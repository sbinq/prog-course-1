# Backbone.JS

От предыдущего задания у вас должно было остаться довольно расплывчатое
представление о том, что на самом деле происходит в Backbone, и какие
роли есть у его компонентов. Это задание и сопроводительный текст
призван придать структуру вашим представлениям о предмете.

Чтобы отвлечься от ненужных деталей, мы будем рассматривать компоненты
Backbone по-отдельности, затем постепенно будем из них собирать рабочую
систему. Другие туториалы могут рассматривать компоненты сразу в связке,
чем затрудняют первоначальное восприятие материала.

## Backbone.Model

Самый базовый и простой для понимания компонент фреймворка [Backbone.JS](http://backbone.js/) — это Backbone.Model, который позволяет создавать модели.

Модель — это упрощённое представление какой-то сущности. Модель в MVC-подходе
нужна для того, чтобы хранить важные свойства и методы моделируемой сущности,
и держать их в непротиворечивом виде.

Например, если мы моделируем ракету, то модель может содержать её состояние —
летит она или лежит. А если она летит, то модель может иметь направление
её полёта. Методами ракеты можно сделать функции «запустись» и
«поверни на такой-то угол». Непротиворечивость модели может обеспечиваться
тем, что мы не будем давать ракете повернуть на какой-то угол до того, как
ракету запустили. То есть, метод «поверни на такой-то угол» должен проверять,
запущена ли ракета, и если нет, то игнорировать команду поворота.

Создать можно шаблон (или класс) модели и экземпляр модели.
Шаблон модели аналогичен классу: мы его делаем для того, чтобы потом
порождать экземпляры. Например, у ракеты может быть всего лишь один шаблон
Rocket, но множество экземпляров: rocket1, rocket2, rocket3.

Давайте создадим шаблон модели ракеты, и две ракеты впридачу:

```javascript
var Rocket = Backbone.Model.extend();

var rocket1 = new Rocket();
var rocket2 = new Rocket();
```

Если нам нужно иметь ракеты с разным типом топлива, мы можем задавать
атрибуты ракетам прямо в конструкторе экземпляра модели Rocket.

```javascript
var Rocket = Backbone.Model.extend();

var rocket1 = new Rocket({ fuelType: 1 });
var rocket2 = new Rocket({ fuelType: 2 });

console.log(  rocket1.get("fuelType")   );
console.log(  rocket2.get("fuelType")   );
```

Любые атрибуты модели можно читать и менять функциями модели `.get()` и `.set()`.

Если мы хотим иметь какой-то тип топлива по-умолчанию, мы можем воспользоваться
возможностью задания атрибутов по умолчанию в шаблоне модели Rocket:

```javascript
var Rocket = Backbone.Model.extend({
                defaults: { fuelType: 1 }
            });

var rocket1 = new Rocket(); // Получит fuelType по-умолчанию.
var rocket2 = new Rocket({ fuelType: 2 });

console.log(  rocket1.get("fuelType")   );
rocket1.set("fuelType", 3);
console.log(  rocket1.get("fuelType")   );
```

Но давайте в дальнейшем в качестве примера предметной области будем
использовать не ракеты, а рестораны. Ресторан может иметь название.
В ресторане могут находиться посетители — но не больше какого-то
максимального количества. Ресторан может быть открыт в какое-то время,
и закрыт в другое время.

Опишем модель ресторана с разумными умолчаниями.

```javascript
var Restaurant = Backbone.Model.extend({
                    defaults: {
                        openAt: 9,      // Рабочее время обычно с 9
                        closeAt: 22     // ...до 10.
                    }
            });
```

Backbone.Model.extend кроме defaults принимает ещё несколько интересных
параметров. Например, если определить функцию initialize, она будет вызываться
сразу после создания нового экземпляра модели (то есть, после создания
нового ресторана), позволяя нам создать и добавить ещё какие-то атрибуты
в экземпляр. Сделаем так, чтобы при создании объекта
одним из его атрибутов было текущее количество человек в ресторане.

```javascript
var Restaurant = Backbone.Model.extend({
                    defaults: { openAt: 9, closeAt: 22 },

                    initialize: function() {
                        this.set("currentOccupancy", 0);
                    }
            });
```

Одним из свойств модели должно являться поддержание собственной
непротиворечивости. В ресторане не может быть отрицательное количество
посетителей. В ресторане не должно быть посетителей, если он закрыт.
Можно определить функцию `validate`, которая помогает убедиться в
непротиворечивости всего объекта. Функция `validate` вызывается всегда,
когда модель сохраняется на сервере через `.save()` (об этом позже),
или тогда, когда мы меняем атрибуты у экземпляра модели через функцю
`.set(..., {validate:true})`.

```javascript
var Restaurant = Backbone.Model.extend({
                    defaults: { openAt: 9, closeAt: 22,
                                currentOccupancy: 0 },

                    validate: function() {
                        if(this.get("currentOccupancy") < 0)
                            return "Negative occupancy is not permitted";
                    }
            });

var r = new Restaurant();
r.set({"currentOccupancy": 4}, {validate: true});
console.log(r.get("currentOccupancy"));      // Выведет единичку.
r.set({"currentOccupancy": -4}, {validate: true});
console.log(r.get("currentOccupancy"));      // Выведет тоже единичку.
```

Мы устанавливаем атрибуты модели через `.set()` не только потому, что
это помогает нам держать модель в непротиворечивом состоянии. Ещё одно
свойство моделей и других компонентов Backbone.JS — умение вызывать
наши обработчики, навешиваемые на разные события.

Например, можно навеситься на событие "change", и тогда любое изменение
любого атрибута в модели, сделанное через функцию `.set()`, повлечёт
за собой вызов этого коллбека.

```javascript
r.on("change", function() { alert("Something changed") });
```

Как может выглядеть более крупная модель ресторана вы можете посмотреть
в файле [simple-restaurant-model.html](simple-restaurant-model.html).
Склонируйте себе этот проект, затем откройте в браузере и проштудируйте
этот файл сейчас. Вы будете полностью понимать, что в нём происходит.

## Backbone.Collection

Ещё один крупный компонент Backbone — Коллекции. Они позволяют хранить
множество экземпляров какой-то модели, искать средь них, и хранить
коллекции на сервере целиком. Вместо того, чтобы в каких-то местах
делать простые JavaScript-массивы (arrays) экземпляров моделей, лучше
воспользоваться коллекциями.

В нашем примере с рестораном мы можем сказать, что один посетитель
ресторана может быть представлен моделью Visitor, а набор посетителей
ресторана может быть представлен коллекцией VisitorsList.

```javascript
var Visitor = Backbone.Model.extend({});
var VisitorsList = Backbone.Collection.extend({});

var visitors = new VisitorsList();
var v1 = new Visitor();
visitors.add(v1);
```

Менее примитивный пример коллекции из посетителей ресторана смотрите
в файле [simple-visitor-collection.html](simple-visitor-collection.html).
В этом примере мы увидим, как Модели хранятся внутри Коллекций.

## Backbone.Collection внутри Backbone.Model

Усложняем композицию. Сделаем так, чтобы наш ресторан умел не только
считать количество посетителей в своём помещении, но и умел хранить
самих этих посетителей.

То есть, нам нужно сделать модель Restaurant, одним атрибутом которой
является коллекция VisitorsList, которая может хранить множество
посетителей, представленных моделью Visitor.

Модель, в которой есть Коллекция, в которой есть Модели.

Restaurant, в котором есть VisitorsList, в котором есть много Visitor.

Кроме того, сделаем так, чтобы визитор мог чекинится в Foursquare как только
попадёт в ресторан. Это лучше всего сделать, навесив коллбэк на событие
"add" от коллекции, если у нас определена наша функция `checkin()`
у модели Visitor:

```javascript
var visitors = new VisitorsList;
visitors.on("add", function(visitor) {
    visitor.checkin("Ресторан «Весёлый овчар»");
});
```

Подробнее в файле [model-collection-model.html](model-collection-model.html).

Ещё заметьте, что мы уже не создаём модель Restaurant как прямого потомка
Backbone.Model, а делаем потомка от [GenericRestaurant](my/generic-restaurant-model.js).
Что нам даёт такое непрямое наследование? Мы можем определить
общие функции для всех типов ресторанов, затем иметь возможность создавать
рестораны Грузинской, Армянской, Эльфийской кухни.

В этом файле мы также переопределяем функции `visitorCame()` и `visitorLeft()`,
но продолжаем пользоваться старыми определениями этих функций.
Мы добавляем новую функциональность по ведению коллекции посетителей, не
удаляя старой функциональности по ведению счётчика `currentOccupancy`,
которая была определена в GenericRestaurant.

#### Задание на понимание сущности модели

Скопируйте код из [my/generic-restaurant-model.js](my/generic-restaurant-model.js), и перепишите его так, чтобы ресторан нельзя было закрыть (вызовом метода
`closeRestaurant()`) до последнего посетителя.
С уходом последнего посетителя ресторан должен закрываться автоматически.


## Underscore templates

Backbone.JS не имеет своей функциональности по порождению HTML.
Для того, чтобы выводить что-то в HTML, надо пользоваться или примитивными
функциями DOM, или использовать jQuery, или использовать функцию `_.template()`
от Underscore, которая в любом случае будет присутствовать в любом проекте,
использующем Backbone.

Шаблонизация позволяет взять объект, и его свойства вставить в нужные
места уже наполовину готового HTML.

```javascript
// Попробуйте запустить этот код в консоли.
_.template("<div><%= foo %></div>", { foo: "Здесь рыбы нет!" });
```

Подробнее про шаблонизацию с использованием функции `_.template()` читайте здесь: [underscore-template.html](underscore-template.html).


## Backbone.View

Представления (Views, вьюхи) — это способ отобразить модель. В Backbone.JS
это практически всегда значит использование HTML-шаблонов вместе с атрибутами
модели, которые с помощью этого шаблона будут отображаться.


