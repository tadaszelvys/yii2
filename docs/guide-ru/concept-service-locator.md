Service Locator
===============

Service Locator является объектом, который знает, как обеспечить всевозможные службы (или компоненты), которые могут понадобиться в приложении. 
В пределах Service Locator'а, каждый компонент имеет только один экземпляр, который уникально определяется  с помощью идентификатора (ID). 
Уникальный идентификатор (ID) может быть использован для извлечения компонента из Service Locator'а. 

В Yii Service Locator является экземпляром класса [[yii\di\ServiceLocator]] или его дочернего класса.

Наиболее часто используемый Service Locator в Yii - это объект *приложения*, который можно получить через 
`\Yii::$app`. Обеспечиваемые им службы называют *компонентами приложения*, такие, как компоненты `запрос`, `ответ`, `UrlManager`. 
Вы легко можете настроить эти компоненты или даже заменить их собственными реализациями, 
благодаря функциональным службам, предоставляемым  Service Locator'ом.

Помимо объекта приложения, объект каждого модуля так же является Service Locator'ом.

Для использования Service Locator'а первым шагом является регистрация компонентов. 
Компонент может быть зарегистрирован с помощью [[yii\di\ServiceLocator::set()]]. 
Следующий код демонстрирует различные способы регистрации компонентов:

```php
use yii\di\ServiceLocator;
use yii\caching\FileCache;

$locator = new ServiceLocator;

// Зарегистрирует "cache", используя имя класса, которое может быть использовано для создания компонента.
$locator->set('cache', 'yii\caching\ApcCache');

// Зарегистрирует "db", используя конфигурационный массив, который может быть использован для создания компонента.
$locator->set('db', [
    'class' => 'yii\db\Connection',
    'dsn' => 'mysql:host=localhost;dbname=demo',
    'username' => 'root',
    'password' => '',
]);

// Зарегистрирует "search", используя анонимную функцию, которая создаёт компонент
$locator->set('search', function () {
    return new app\components\SolrService;
});

// Зарегистрирует "pageCache", используя компонент
$locator->set('pageCache', new FileCache);
```

После того, как компонент зарегистрирован, вы можете получить к нему доступ, используя уникальный идентификатор (ID), 
одним из двух следующих способов:

```php
$cache = $locator->get('cache');
// или альтернативный
$cache = $locator->cache;
```

Как видно выше, [[yii\di\ServiceLocator]] позволяет обратиться к компоненту, как к свойству, 
при помощи идентификатора (ID) компонента.
При обращении к компоненту впервые, [[yii\di\ServiceLocator]] будет использовать информацию о регистрации компонента, 
что бы создать новый экземпляр компонента и вернёт его. 
В дальнейшем  при обращении к компоненту  снова, Service Locator вернёт тот же экземпляр. 


Что бы проверить, был ли идентификатор (ID) компонента уже зарегистрирован, можно использовать  [[yii\di\ServiceLocator::has()]].
Если вы вызовете [[yii\di\ServiceLocator::get()]] с недопустимым идентификатором (ID), тогда будет выброшено исключение.



Поскольку Service Locator`ы зачастую создаются с [конфигурациями](concept-configurations.md), 
записываемое свойство с именем [[yii\di\ServiceLocator::setComponents()|components]] предоставляется так, 
что Вы можете его настроить и зарегистрировать несколько компонентов одновременно.
Следующий код демонстрирует конфигурационный массив, 
который может использоваться для настройки приложения и регистрации компонентов  "db", "cache" и "search" :

```php
return [
    // ...
    'components' => [
        'db' => [
            'class' => 'yii\db\Connection',
            'dsn' => 'mysql:host=localhost;dbname=demo',
            'username' => 'root',
            'password' => '',
        ],
        'cache' => 'yii\caching\ApcCache',
        'search' => function () {
            return new app\components\SolrService;
        },
    ],
];
```
