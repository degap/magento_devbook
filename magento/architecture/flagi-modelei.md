## Использование флагов моделей

Для сохранения элементарных данных нет необходимости создавать новую сущность. В magento встроена модель \Magento\Framework\Flag \(\Magento\Framework\Flag\FlagResource\). Она реализует небольшое хранилище ключ-значение в БД.

![](/assets/2018-02-22_13.13.04.png)В модуле CatralogRule этот механизм исользуется для сохранения состояния модели ценового правила при изменении значений свойств.

```php
namespace Magento\CatalogRule\Model;

class Flag extends \Magento\Framework\Flag
{
    protected $_flagCode = 'catalog_rules_dirty';
}
```

```php
$this->_objectManager->create(\Magento\CatalogRule\Model\Flag::class)
                    ->loadSelf()
                    ->setState(1)
                    ->save();
```

Флаг используется при отправке уведомления об изменении данных

```php
$dirtyRules = $this->_objectManager->create(\Magento\CatalogRule\Model\Flag::class)->loadSelf();
if ($dirtyRules->getState()) {
    $this->messageManager->addNotice($observer->getData('message'));
}
```

Флаг сбрасывается при запуске обработчика, изменяющего цены каталога по новым правилам.

```php
$this->_objectManager->create(\Magento\CatalogRule\Model\Flag::class)->loadSelf()->setState(0)->save();
```

Каждому классу, дочернему от \Magento\Framework\Flag, соответствует одна запись в таблице.

