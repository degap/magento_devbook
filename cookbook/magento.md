#### После изменения di.xml запустить

```bash
bin/magento setup:di:compile
```

#### Не находит класс в di.xml

```
<item name="slider_simple_listing_data_source" xsi:type="string">
  \<Vendor>\Slider\Model\ResourceModel\Simple\Grid\Collection
</item>
```

Пробельные символы не обрезаются, поэтому без перевода строки

```
<item name="slider_simple_listing_data_source" xsi:type="string">\<Vendor>\Slider\Model\ResourceModel\Simple\Grid\Collection</item>
```

#### Area code not set: Area code must be set before starting a session.

В magento код, как правило, выполняется в конкретной области \(area\), определяющийся заданным URL- frontend, adminhtml, rest, api. В консольной команде URL отсутствует, поэтому нужно задавать область явно.

```php
use Magento\Framework\App\State;

use Symfony\Component\Console\Command\Command;

class ConcreteCommand extends Command
{
    public function __construct(
        State $appState,
        $name = null
    )
    {
        $appState->setAreaCode('adminhtml');
        parent::__construct($name);
    }
}
```

#### We can't find the role for the user you wanted.

```bash
bin/magento module:disable Shopial_Facebook
```



