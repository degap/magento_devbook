## Маркетинг - специальные акции \(Promotions\)

Скидки определяются ценовыми правилами, основанными на расличных условиях. В magento предусмострены следующие варианты:

* купон на скидку на конкретный товар для лучших покупателей
* бесплатная доставка при стоимости заказа выше заданной
* задание расписания действия скидок

### [Ценовые правила каталога](http://docs.magento.com/m2/ee/user_guide/marketing/price-rules-catalog.html)

Используются для выборочного предоставления скидки на товары. Не используют купоны, поскольку применяются до попадания товара в корзину.

##### Добавление нового правила

Контроллер:  **\Magento\CatalogRule\Controller\Adminhtml\Promo\Catalog\Save**

Репозиторий: **\Magento\CatalogRule\Api\CatalogRuleRepositoryInterface**

Модель: **\Magento\CatalogRule\Model\Rule**

Сохранение стандартное. Создается и загружается по id модель, заполняется данными, сохраняется через репозиторий.

При установленном параметре _auto\_apply_ переадресовывается на действие _applyRules._

Изменение поведения модели, проверяемое методом _isRuleBehaviorChanged_ вызывает создание и сохранение [флага](/magento/architecture/flagi-modelei.md) _catalog\_rules\_dirty, _который сбрасывается при применении правил \(_applyRules\)_

Данные, передаваемые с из админки добавления ценовых правил

```json
{
  "row_id": "1",
  "rule_id": "1",
  "created_in": "1",
  "updated_in": "1519209417",
  "name": "название правила",
  "description": "описание",
  "from_date": "",
  "to_date": "",
  "is_active": "0",
  "conditions_serialized": "{\"type\":\"Magento\\\\CatalogRule\\\\Model\\\\Rule\\\\Condition\\\\Combine\",\"attribute\":null,\"operator\":null,\"value\":\"1\",\"is_value_processed\":null,\"aggregator\":\"all\",\"conditions\":[{\"type\":\"Magento\\\\CatalogRule\\\\Model\\\\Rule\\\\Condition\\\\Product\",\"attribute\":\"sku\",\"operator\":\"==\",\"value\":\"2785\",\"is_value_processed\":false},{\"type\":\"Magento\\\\CatalogRule\\\\Model\\\\Rule\\\\Condition\\\\Product\",\"attribute\":\"attribute_set_id\",\"operator\":\"==\",\"value\":\"4\",\"is_value_processed\":false},{\"type\":\"Magento\\\\CatalogRule\\\\Model\\\\Rule\\\\Condition\\\\Product\",\"attribute\":\"category_ids\",\"operator\":\"==\",\"value\":\"3\",\"is_value_processed\":false},{\"type\":\"Magento\\\\CatalogRule\\\\Model\\\\Rule\\\\Condition\\\\Product\",\"attribute\":\"sku\",\"operator\":\"==\",\"value\":\"3356\",\"is_value_processed\":false}]}",
  "actions_serialized": "{\"type\":\"Magento\\\\CatalogRule\\\\Model\\\\Rule\\\\Action\\\\Collection\",\"attribute\":null,\"operator\":\"=\",\"value\":null}",
  "stop_rules_processing": "0",
  "sort_order": "10",
  "simple_action": "by_fixed",
  "discount_amount": "200.0000",
  "website_ids": [
    "1"
  ],
  "customer_group_ids": [
    "1"
  ],
  "related_banners": [],
  "rule": {
    "conditions": {
      "1": {
        "type": "Magento\\CatalogRule\\Model\\Rule\\Condition\\Combine",
        "aggregator": "all",
        "value": "1",
        "new_child": ""
      },
      "1--1": {
        "type": "Magento\\CatalogRule\\Model\\Rule\\Condition\\Product",
        "attribute": "sku",
        "operator": "==",
        "value": "2785"
      },
      "1--2": {
        "type": "Magento\\CatalogRule\\Model\\Rule\\Condition\\Product",
        "attribute": "attribute_set_id",
        "operator": "==",
        "value": "4"
      },
      "1--3": {
        "type": "Magento\\CatalogRule\\Model\\Rule\\Condition\\Product",
        "attribute": "category_ids",
        "operator": "==",
        "value": "3"
      },
      "1--4": {
        "type": "Magento\\CatalogRule\\Model\\Rule\\Condition\\Product",
        "attribute": "sku",
        "operator": "==",
        "value": "3356"
      }
    }
  },
  "form_key": "teybhXRwdR0LZ1bC"
}
```



