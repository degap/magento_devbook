## Редактирование товара в административной панели

Контроллер: **Magento\Catalog\Controller\Adminhtml\Product\Save**

Поскольку у модели товара есть [плагины](/magento/podsistemi/plagini.md), сохранение происходит не через `Magento\Catalog\Model\Product::save()`, а через его перехватчик `Magento\Catalog\Model\Product\Interceptor::save()`.

Список плагинов в commerce-версии

```xml
<!-- vendor/magento/module-catalog-permissions/etc/di.xml -->
<type name="Magento\Catalog\Model\Product">
    <plugin name="catalogPermissionsProductChange" type="Magento\CatalogPermissions\Model\Indexer\Plugin\Product"/>
</type>
<!-- vendor/magento/module-catalog-inventory/etc/di.xml -->
<type name="Magento\Catalog\Model\Product">
    <plugin name="catalogInventoryAfterLoad" type="Magento\CatalogInventory\Model\Plugin\AfterProductLoad"/>
</type>
<!-- vendor/magento/module-bundle/etc/di.xml -->
<type name="Magento\Catalog\Model\Product">
    <plugin name="bundle" type="Magento\Bundle\Model\Plugin\Product" sortOrder="100" />
</type>
<!-- vendor/magento/module-swatches/etc/di.xml -->
<type name="Magento\Catalog\Model\Product">
    <plugin name="exclude_swatch_attribute" type="Magento\Swatches\Model\Plugin\Product" />
</type>
<!-- vendor/magento/module-catalog-rule/etc/webapi_rest/di.xml -->
<type name="Magento\Catalog\Model\Product">
    <plugin name="apply_catalog_rules_after_product_save_and_reindex" type="Magento\CatalogRule\Plugin\Indexer\Product\Save\ApplyRulesAfterReindex"/>
</type>
```

**Magento\CatalogPermissions\Model\Indexer\Plugin\Product**

afterSave - переиндексация разрешений

afterDelete - переиндексация разрешений

**Magento\CatalogInventory\Model\Plugin\AfterProductLoad**

afterLoad - установка идентификатора магазин в расширенные атрибуты товара

**Magento\Bundle\Model\Plugin\Product**

afterGetIdentities - сбор identities для родительских объектов

**Magento\Swatches\Model\Plugin\Product**

afterGetMediaAttributes - убирает доступ к swatch из списка ролей для простых и виртуальных продуктов 

**Magento\CatalogRule\Plugin\Indexer\Product\Save**

afterReindex - применение правил цен каталога после сохранения ресурсной модели.

Такое поведение описано в комментарии к методу. Сам метод выполняется после reindex метода модели. Но фактическое сохранение происходит в ресурсной модели, в одном из плагинов которого \(или дочерних\) вызывается метод reindex

