[acl.xml](https://www.magestore.com/magento-2-tutorial/3194-2) файл определения acl ролей.

Узел acl

```xml
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Acl/etc/acl.xsd">
    <acl>
        <resources>
            <resource id="Magento_Backend::admin">
                <resource id="Magento_Backend::stores">
                    <resource id="Magento_Backend::stores_settings">
                        <resource id="Magento_Config::config">
                            <resource id="Magento_Shipping::config_shipping" title="Shipping Settings Section" translate="title" sortOrder="5" />
                            <resource id="Magento_Shipping::shipping_policy" title="Shipping Policy Parameters Section" translate="title" sortOrder="5" />
                            <resource id="Magento_Shipping::carriers" title="Shipping Methods Section" translate="title" sortOrder="5" />
                        </resource>
                    </resource>
                </resource>
            </resource>
        </resources>
    </acl>
</config>
```

Элемент acl содержит дочерний элемент resources с вложенными элементами resource

Каждый ресурс имеет  атрибуты:

* id - идентификатор ресурса
* title - название, отображаемое в дереве ресурсов
* sortOrder - позиция в дереве ресурсов
* translate - указатель атрибуты для отображения языкового ресурса




