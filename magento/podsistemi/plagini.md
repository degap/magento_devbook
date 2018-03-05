# [Плагины \(перехватчики\)](http://devdocs.magento.com/guides/v2.2/extension-dev-guide/plugins.html)

Плагин - класс, изменяющие поведение открытых методов других классов, перехватывая вызов метода и выполняя код до, после и "оборачивая" \(т.е. и до и после\) метод. Такой подход позволяет заменять или расширять поведение первоначальных публичных методов любого класса или интерфейса.

Расширения, которые хотят перехватить и изменить певедение публичного метода могут создать класс _Plugin. _Это вариант перехвата уменьшает возможность конфликта между расширениями ,которые хотят имзенить поведение одного и того же класс или метода. Ваша реализация класса _Plugin_ изменяет поведение метода класса, но не меняет сам класс. Magento вызывает перехватчики последовательно в порядке, заданном в конфигурационном файле, так что он не конфликтуют между собой.

### Ограничения

Плагины не могут использованы для изменения:

* финальных методов
* финальных классов
* непубличных методов
* методов класса \(статических\)
* конструктора
* виртуальных типов \(?\)
* объектов, иницилиализирующихся до момента инициации Magento\Framework\Interception

### Объявление плагина

Нужно создать следующие элементы в di.xml модуля:

* **type name** - имя изменяемого класса или интерфейса
* **plugin name** - произвольное имя, идентифицирующее плагин. Также используется при слиянии конфигураций плагина
* **plugin type** - название класса плагина или его виртуальный тип. Используется соглашение о наименовании: \Vendor\Module\Plugin\&lt;ClassName&gt;

Следующие элементы не обязательны:

* **plugin sortOrder** - плагины, вызывающие тот-же метод используют этот параметр для определения очередности
* **plugin disabled** - отключение плагина _true_, по умолчанию _false_

### Реализация плагина

Первый аргумент для методов плагина before, after, around объект, обеспечивающий доступ ко всем публичным методам класса изменяемого метода.

#### Методы "до" \(before\)

Magento выполняет все методы "до" перед вызовом изменяемого метода. Эти методы должны называться так же, как и изменяемый метод с приставкой 'before'

Можно использовать методы "до" для изменения аргументов изменяемого метода возвращая модифицированный аргумент. Если аргументов больше одного, метод должен вернуть массив таких аргументов. Если метод не изменяет аргументы исходного метода, он должен вернуть null.

#### Методы "после" \(after\)

Magento запускает все методы "после" по окончании выполнения изменяемого метода. Magento требует возврата значения из этих методов и они должны именоваться также как и изменяемый с приставкой "after".

Можно использовать методы "после" для изменения результата изменяемого метода модифицируя исходное значение и возвращай его в конце метода "после".

Метода "после" имеют доступ ко всем аргументам изменяемого метода. Magento передает результат и аргументы изменяемого метода в следующий по порядку метод "после". Если изменяемый метод не возвращает значения, метод "после" должен вернуть null.

Метод "после" не должен объявлять все аргументы исходного метода, кроме тех, которые им используются и любых аргументов изменяемого метода , объявленных до используемого.

#### Методы-обертки

Magento выполняет эти методы до и после изменяемого метода. Использование методов-оберток позволяет переопределить поведение изменяемого метода. Наименование методов должно быть таким же как у изменяемого с приставкой "around".

Перед списком аргументов оригинального метода метод-обертка получает функцию обратного вызова callable, которая позволяет выполнить следующий метод в цепочке. Когда ваш код выполняет callable, magento вызывает следующий плагин или изменяемую функцию.

Если метод-обертка не вызывает callable, вызова других плагинов и исходного метода не происходит.

#### Приоритизация плагинов

Свойство sortOrder для плагинов определяет в каком порядке будут вызываться их методы "до", "после", "вокруг" в ситуации, когда конкретный метод класса переопределяется несколькими плагинами.

Правила приоритетов:

* перед выполнение изменяемого метода, magento задействует плагины от меньшего к большему sortOrder
  * во время обработки каждого плагина magento выполняет его метод "до"
  * после этого выполняется метод-обертка ,после чего запускается следующий плагин или изменяемый метод
* после выполнения изменяемого метода magento запускает выполнение плагинов от больше к меньшему sortOrder
  * во время каждого выполнения плагина, текущий плагин сначала закончит выполнение метод-обертку 
  * после его окончания, плагин выполнит метод "после" и вызовет следующий плагин

#### Настройка наследования

Классы и интерфейсы, который реализуют или наследуют классы, имеющие плагины, также наследуют плагины родительского класса.

Magento использует плагины, определенные в глобальной области система находится в определенном пространстве \(frontend, backend и т.п.\) Можно расширять или переопределять глобальную настройку плагинов через di.xml пространств.

## Development

При вызове методов многих моделей вместо самой модели вызывается класс Interceptor. Какой класс использовать определяется наличием плагинов. Если в di.xml есть секции plugin для этого класса, значит есть плагины. 

```php
namespace Magento\Framework\Interception\ObjectManager\Config

class Developer
{
...
    public function getInstanceType($instanceName)
    {
        $type = parent::getInstanceType($instanceName);
        if ($this->interceptionConfig && $this->interceptionConfig->hasPlugins($instanceName)
            && $this->interceptableValidator->validate($instanceName)
        ) {
            return $type . '\\Interceptor';
        }
        return $type;
    }
...
}
```


