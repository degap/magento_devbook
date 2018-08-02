# Ценообразование

## Товар

При редактировании товара задается несколько цен:

В основной форме:

**Цена \(price\) ** - основная цена товара без скидок, акций и т.п.

В диалоге "Advanced pricing":

**Специальная цена \(special price\) ** - предложение по сниженной цене во время рекламных акций

**Cтоимость \(cost\)** - закупочная цена товара

**Цена для групп пользователей \(customer group price\)** - акционные и пакетные цены для различных групп пользователей \(незалогиненных, дилеров и т.д.\), задаются как фиксировнные значения и процент от основной цены. Если указано quantity = 1, цена заменит основную

**Купи Х шт по специальной цене \(customer group price\)** - если quantity &gt; 1, вместо основной цены отобразится специальная или групповая с  quantity = 1, подходящая под условия. В интерфейсе из коробки

под ценой будет предложение купить quantity по указанной цене

**Рекомендованная розничная цена производителя \(manufacturer's suggested retail price \)**

Цены, как и все остальные свойства товара, редактировать напрямую  или задавать расписания обновлений.

Изменение цены производится [переиндексированием](/magento/podsistemi/indeksirovanie.md) данных.

### Акционные цены

Цены для каталога:

* задать цену для одного или нескольких sku \(скидка, фиксированная\) 

Цены для корзины:

* по коду купона \(купоны мождно генерировать пачками\)
* бесплатная доставка
* Купи Х получи Y бесплатно
* Скидка при минимальном заказе на конкретную сумму

Цены на сопуствтующие товары.

### Определение цены

При добавленнии в корзину цена определеяется в методе `getFinalPrice` модели товара:

```php
namespace Magento\Catalog\Model;
class Product 
{
    public function getFinalPrice($qty = null)
    {
        if ($this->_getData('final_price') === null) {
            $this->setFinalPrice($this->getPriceModel()->getFinalPrice($qty, $this));
        }
        return $this->_getData('final_price');
    }
}
```

которая вызывает метод `getFinalPrice` модели цены, где в простом случае цена определяется как минимальная из price, tierPrice и specialPrice.

## Development

### Изменение цены при сохранении товара

Цены переиндексируются в [плагинах](/magento/development/sohranenie-tovara.md):

#### **Magento\CatalogRule\Plugin\Indexer\Product\Save\ApplyRules**

```php
    /**
     * @var Magento\CatalogRule\Model\Indexer\Product\ProductRuleProcessor
     */
    protected $productRuleProcessor;

    ...

    $this->productRuleProcessor->reindexRow($product->getId());

    ...
```

```php
class ProductRuleProcessor extends AbstractProcessor
{
    /**
     * Indexer id
     */
    const INDEXER_ID = 'catalogrule_product';
}
```

AbstractProcessor по INDEXER\_ID получает индексатор из реестра.

Для catalogrule\_product получаем **Magento\Indexer\Model\Indexer\DependencyDecorator** у которого в качестве индекса указан класс **Magento\Indexer\Model\Indexer\Interceptor.  **Плагинов не найдено и вызов переходит к основной модели индексатора **Magento\Indexer\Model\Indexer. **Для вызова переиндексации записи нужно получить класс для выполнения действия actionClass. Вызовы постоянно переходят от перехватчика к основной модули индекстаора, но в итоге вызывается метода \_getData перехватчика, в свойстве которого хранится информация о реальном индексаторе

```php
indexer_id = "catalogrule_product"
primary = null
view_id = "catalogrule_product"
action_class = "Magento\CatalogRule\Model\Indexer\Product\ProductRuleIndexer"
shared_index = "catalogrule"
title = "Catalog Product Rule"
description = "Indexed product/rule association"
dependencies = {array} [0]
fieldsets = {array} [0]
```

У класса **Magento\CatalogRule\Model\Indexer\Product\ProductRuleIndexer** также есть перехватчик, который пытается получить список плагинов:

```xml
<!-- один плагин в разных модулях -->
<!-- vendor/magento/module-catalog-rule-configurable/etc/crontab/di.xml -->
<type name="Magento\CatalogRule\Model\Indexer\Product\ProductRuleIndexer">
    <plugin name="productRuleReindex" type="Magento\CatalogRuleConfigurable\Plugin\CatalogRule\Model\Indexer\ProductRuleReindex"/>
</type>
<!-- vendor/magento/module-catalog-rule-configurable/etc/adminhtml/di.xml -->
<type name="Magento\CatalogRule\Model\Indexer\Product\ProductRuleIndexer">
    <plugin name="productRuleReindex" type="Magento\CatalogRuleConfigurable\Plugin\CatalogRule\Model\Indexer\ProductRuleReindex"/>
</type>
```

**Magento\CatalogRuleConfigurable\Plugin\CatalogRule\Model\Indexer\ProductRuleReindex**

aroundExecuteRow - переиндексирует дочерние элементы конфигурируемого товара, если нет таких, выполняет функцию обратного вызова \(executeRow\) к переданному идентификатору товара

aroundExecuteList - переиндексирует дочерние элементы конфигурируемого товара. Выполняет функцию обратного вызова к не дочерним идентификаторам списка

**Magento\CatalogRule\Plugin\Indexer\Product\Save\ApplyRulesAfterReindex**

afterReindex - применяет правила цен каталога после сохранени ресурсной модели товара

Метод executeRow вызывает Magento\CatalogRule\Model\Indexer\Product\ProductRuleIndexer::doExecuteRow\(\), после которого,  в итоге , вызывается Magento\CatalogRule\Model\Indexer\IndexBuilder::doReindexByIds\(\). В этом методе для каждого переданного идентификатора товара, загружается модель и к нем применяются активные правила.

Итоговый метод применения ценовых правил **Magento\CatalogRule\Model\Indexer\IndexBuilder::applyRule\(\)**

