[system.xml](http://www.mageplaza.com/magento-2-module-development/create-system-xml-configuration-magento-2.html) конфигурационный файл использумый для создания элементов на странице конфигурации Store -&gt; Settings -&gt; Configuration

Сама страница конифгурации разделена на несколько логических частей:

Tabs, Sections, Groups, Fields![](https://cdn.mageplaza.com/media/general/P8E2i4k.png)Располагается в директории etc/adminhtml модуля.

Элемент может содержать следующие дочерние узлы:

* Class - класс элемента
* Label - заголовок 
* Tab - идентификатор Tab, к которому относится элемент
* Resource - определеяет правило ACL для доступа к элементу
* Group - элемент может иметь множество полей и аттрибуты, которые группируются в Section
* Fields поля, в которых хранятся данные, которые требуется конфигурировать



