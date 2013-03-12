# Задача 7

Чем фреймворки отличаются от библиотек? Фреймворки забирают часть контроля
над тем, что происходит в программе, но зато взамен дают набор инструментов
для управления сложностью больших проектов. Чтобы не получалось лапши.

Между библиотеками и фреймворками иногда достаточно сложно провести черту,
но можно сказать, что если вы замечаете, что при программировании части
приложения чаще не вы зовёте какой-то код, а это ваш код вызывается какой-то
внешней к нему системой, то это — фреймворк. Иначе это библиотека.

Так вот, jQuery — это библиотека, а Backbone — довольно минимальный,
но фреймворк. Они часто используются вместе. Более того, часто вместе
используются Backbone, jQuery и [Underscore](http://underscorejs.org) —
это ещё одна библиотека, дающая некоторое количество интересных функций.

Фреймворки в JavaScript строятся вокруг и около концепции MVC
(Model — View — Controller). MVC — это не какая-то конкретная вещь. Это
*шаблон проектирования*. Шаблон проектирования — набор лучших программистских
практик, который на деле показал свою полезность в схожих проектах.
Фреймворков много, и некоторые из них могут и не быть построены
на основе концепции MVC, но мы их рассматривать пока не будем.

Так вот, MVC — это шаблон, который говорит, что приложение состоит
из небольших компонентов (например, кнопка, или поле для ввода, или
движущийся график, или что-то типа `Wobbler` из предыдущего задания).
В свою очередь, каждый компонент не является монолитом,
таким как Wobbler, а сам также разделён на сущности: Model, View и Controller.

1. Model — это описание одного элемента предметной области.
Например, для модель кнопки может содержать её состояние: нажата она
сейчас, или отжата. Модель ракеты может содержать её текущее положение,
её скорость и направление полёта. И реагировать на команды «поверни
налево», «поверни направо». Модель не содержит никаких знаний о том,
как представить (визуализировать, отобразить, нарисовать) ракету или
кнопку — она содержит и позволяет управлять только сутью объекта.
Суть кнопки — иногда уметь быть нажатой. Суть ракеты — лететь в нужном
направлении. Или пока не лететь. Один из ключевых моментов модели в
том, что модель может реагировать на запросы к ней («поверни!»), и обязана
изменять внутреннее состояние в соответствии с ними, но так, чтобы внутреннее
состояние продолжало оставаться непротиворечивым (Вопрос: как отреагировать на
команду «поверни!» ракете, которая ещё не взлетела?). Внутреннее состояние
в контроллере описывается произвольным числом переменных, таких как
направление полёта или факт того, находится ли ракета в воздухе.

2. View — это способ отобразить объект. Представление. Например, объект,
реализующий View для ракеты, умеет её нарисовать в соответствии с её
текущими свойствами.

3. Controller — занимается захватом действий пользователя, их интерпретацией,
и вызовом соответствующих методов Модели, чтобы менять её состояние.
В вебе это самый «слабый» компонент MVC-подхода. Иногда из MVC контроллер
исключают, получается что View должен реагировать на пользовательский ввод.

Главное, что нужно запомнить — это то, что Model моделирует (имитирует суть)
какой-то вещи (кнопки, ракеты, чайника), а View — отображает этот чайник.

Зная это можно перейти к Backbone.JS, как к одному из самых распространённых,
но, одновременно, одному из самых простых MVC-фреймворков.

## Задача

Ваша задача — прорешать статью на habrahabr, самостоятельно повторив
и повторив все шаги её автора. Для этого можно использовать
только её, или заодно посмотреть на документацию Backbone.JS и Underscore.JS,
либо пройти какой-нибудь туториал. Ссылки ниже.

Эта задача может суммарно занять от двух до десяти часов. Постарайтесь
не торопиться, а акцент делать на том, чтобы как можно больше действий
попробовать (повторить, поэкспериментировать) своими руками.

Результат сохраните в каталоге task-7 на своём Гитхабе.

### Ссылки

* http://en.wikipedia.org/wiki/Model–view–controller
* http://backbonejs.org
* http://habrahabr.ru/post/127049/
* http://www.codeschool.com/courses/anatomy-of-backbonejs
