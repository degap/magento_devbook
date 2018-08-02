# API
- Magento поддерживает REST и SOAP. Обращение к WebAPI может осуществляться обоими сопособами.  
- Включает три типа аутентификации
  - сторонние приложения аутентифицируются при помощи [OAuth 1.0a](http://devdocs.magento.com/guides/v2.0//get-started/authentication/gs-authentication-oauth.html)
  - мобильные приложения используют [токен](http://devdocs.magento.com/guides/v2.0//get-started/authentication/gs-authentication-token.html)   
  - администраторы и пользователи посредством пары логин/пароль
- Для всех аккаунтов и интеграций определяются доступные привязанные ресурсы. Фреймворк API авторизует каждый запрос.
- Любой сервис magento или сторонний может быть [сконфигурирован как webapi](webapi_config) за несколько строк в webapi.xml
- API основано на модели CRUD и поиске. 
- Поддерживается фильтрация полей для экономии мобильного трафика
- Стиль интеграции web API позволяет вызовом одного api запускать несколько сервисов сразу 
  
## Пользовательский api
- Создаем модуль обычным способом
- Заполняем /etc/webapi.xml
- Прописываем зависимости в /etc/di.xml
- Определяем интерфейс и реализацию
- Добавляем права доступа, если нужно

### <a name="webapi_config"></a> [Конфигурирование webapi.xml](http://devdocs.magento.com/guides/v2.0/extension-dev-guide/service-contracts/service-to-web-service.html)
Для валидации xml используется схема расположенная по адресу:
```bash
app/code/<Vendor>/Webapi/etc/webapi.xsd
```
или можно использовать webapi.xsd по-умолчанию

Создаем файл /etc/webapi.xml и добавлем описание метода api  
Например, нам нужно получать дату доставки  

```xml
<?xml version="1.0"?>
<routes xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Webapi:etc/webapi.xsd">
    <route url="/V1/guest-carts/{cartId}/shipping-delivery-date" method="POST">
        <service class="<Vendor>\Quote\Api\ShippingManagementInterface" method="estimateDate"/>
        <resources>
            <resource ref="anonymous"/>
        </resources>
    </route>
</routes>
```
 На основании этого класса magento динамически создает сервисный метод, доступный через web API. 
 Поэтому класс должен отформатирован специальным образом. Это имеет смысл, если метод принимает на вход объекты конкретного класса 
 или возвращает результат как объект или массив объектов. В случае результатов, то magento автоматически преобразует в нужный формат,
  JSON или SOAP, используя рефлексию. 
Для обеспечения такого поведения приложение должно получить информацию об этих параметрах из класса. 
Для php 7 достаточно задать тип агрументов, для php 5 указать в аннотациях @param и @return  
