# Тестирование

## [Интеграционные тесты](http://devdocs.magento.com/guides/v2.0/test/integration/integration_test_execution.html)

### The TESTS\_CLEANUP Constant

Значение по умолчанию

```xml
<const name="TESTS_CLEANUP" value="enabled"/>
```

При любом запуске тестирования, даже если тестов нет, сбрасывается состояние magento и запускается полная установка. Для разработки можно прогнать один раз и переключить в _disable_.

### Запуск интеграционных тестов

1. Создвть из dist файлов конфигурационные файлы :
   config-global.php в директории %MAGENTO\_ROOT%/dev/tests/integration/etc
   install-config-mysql.php в директории %MAGENTO\_ROOT%/dev/tests/integration/etc
   phpunit.xml в директории %MAGENTO\_ROOT%/dev/tests/integration
2. Прописать настройки в файлы  
   Параметры DB и AMPQ обязательны  
   В файле phpunit.xml есть настройка

   ```xml
   <const name="TESTS_CLEANUP" value="disabled"/>
   ```

   Она отвечает за подготовку окружения, в частности она создает структуру и заполняет БД.  
   Также создает песочницу в dev/tests/integration/tmp/sandbox-0-%random\_hash%  
   Развертывание окружения - долгая задача. Чтобы, при разработке, каждый раз не ждать,  
   лучше развернуть один раз, проставив параметр "enabled", а потом отключить.  
   Тесты должны очищать свои изменения в БД, поэтому можно просто чистить кэш в песочнице.

3. Запустить тесты

   ```bash
   bin/magento dev:test:run integration  -c'--color=always'
   ```



### Подмена экземпляра класса в DI

Если нужно подставить mock объект в DI для последующего его ав вызова в конструкторах классов, заменять нужно конкретную реализацию, а не интерфейс.

Например, у нас прописана зависимость:

```xml
<preference for="Vendor\Service\Gateway\API\RestManagementInterface" type="Vendor\Service\Gateway\RestManager" />
```

Мы хотим подменить реализацию вот так:

```php
$restManagerMock = $this->createMock(\Vendor\Service\Gateway\API\RestManagementInterface::class);
$restManagerMock->method('someMethod')->will('doAnything');
$this->objectManager->addSharedInstance($serviceManagerMock, \Vendor\Service\Gateway\API\RestManagementInterface::class);
```

Это не сработает. В менеджере объектов сначала по интерфейсу определеяется класс конкретной реализации, а потом ищеться сама реализация.

Поэтому нужно делать так:

```php
$this->objectManager->addSharedInstance($serviceManagerMock, \Vendor\Service\Gateway\RestManager::class);
```



