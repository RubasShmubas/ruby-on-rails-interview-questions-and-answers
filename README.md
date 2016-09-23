# Ruby on Rails interview questions and answers.

- [ ] Что такое Rack?
- [ ] Что такое Singleton Class в руби?

## Что такое класс в руби?
```ruby
Классы хранят данные, имеют методы, которые могут взаимодействовать с этими данными
и используются для инстанцирования объектов:

class WhatAreClasses
  def initialize
    @data = "i'm instance data of this object. Hello."
  end

  def method
    puts @data.gsub('instance', 'altered')
  end
end

object = WhatAreClasses.new
object.method
# => “I’m altered data of this object. Hello.”
```

## Что такое модуль в руби?
```ruby
Модули служат механизмом для неймспейсов:

module ExampleNamespace
  class ExampleClass
    def initialize
      puts ‘la-la-la’
    end
  end
end

ExampleNamespace::ExampleClass.new
# => 'la-la-la'

Еще модули используются для множественного наследования через mixin-ы:

module AMixIn
  def who_am_i?
    puts "An existentialist, that's who."
  end
end

class Huass
  extend AMixIn
end

Huass.who_am_i?
# => An existentialist, that's who.

Модули не могут быть использованы для инстанцирования объектов так, как классы:

AMixIn.new
# => NoMethodError: undefined method ‘new’ for AMixIn:Module
```

## Разница между классом и модулем в руби?

              |Класс         | Модуль
------------- | ------------ | -------------
Инстанциация  | Может быть инстанцирован | Не может быть инстанцирован
Использование | Создание объектов        | Mixin-ы, namespace-ы
Суперкласс    | module           | object
Методы        | методы класса, методы инстанса        | методы модуля, методы инстанса
Наследование  | Может наследоваться и может быть используемым для наследования | Нет наследования
Включение(Include) | Нет | Может быть за-include-жен в класс или модуль. 
Расширение(Extend) | Нет  | Может быть за-extend-жен в класс или модуль.

## Как реализовано множественное наследование в руби? (multiple inheritance)

```ruby
С помощью mixin-ов модулей
```

## В чем разница между include и extend?

```ruby
include — добавляет методы модуля как методы инстанса в модуль/класс.

class A
  include M
end

A.ancestors
#=> [A, M, Object, Kernel, BasicObject]

extend — добавляет методы и константы модуля к метаклассу цели(синглтон классу)
Если мы вызываем Klazz.extend(Mod), то Klazz получает доступ к методам Mod-а(как к классовым методам)
Если мы вызываем obj.extend(Mod), то obj получает доступ к методам Mod-a(как к инстанс методам),
но остальные инстансы obj.class-а этих методов не имеют.

class A
  extend M
end

A.singleton_class.ancestors
#=> [M, Class, Module, Object, Kernel, BasicObject]

Каждый вызов include проверяет подключаемый модуль на наличие метода included.
Этот метод исполняется, когда модуль подключается, используя include.
Это как конструктор (initialize) для подключений.
У extend для этих целей есть собственный метод — extended.

Подробнее: https://habrahabr.ru/post/143483/,
http://stackoverflow.com/questions/156362/what-is-the-difference-between-include-and-extend-in-ruby
```

## Что такое объект в руби?

```ruby
Объект - это инстанс класса. Также в руби есть рутовый класс Object. Классы наследуются от него.
```

## Как объявляется и используется конструктор в руби?

```ruby
Это метод initialize.

class Woman
  def initialize(hair_color, breast_size)
    @hair_color = hair_color
    @breast_size = breast_size
  end
end

Вызывается он методом new, аргументы, переданные в new попадают в initialize.

olya = Woman.new('black', 4)
olya.hair_color
# => 'black'
olya.breast_size
# => 4
```

- [ ] В чем разница между строкой и символом?


- [ ] Что такое и в чем разница между лямбдой, проком и блоком в руби?


- [ ] Для чего нужен yield?


- [ ] Собственная реализация метода each, map и inject (и других популярных методов)


- [ ] В чем разница между and(or) и &&(||)?


- [ ] Как реализован Hash в руби?


- [ ] Что такое REST?


- [ ] В чем разница между Hash(Ruby) и HashWithIndifferentAccess(ActiveSupport)?


- [ ] Что такое CSRF(Cross-Site Request Forgery)?


- [ ] Для чего используется constraints опция в роутах?


- [ ] В чем разница между find и find_by_что-то?


- [ ] Что означает a ||= b?


- [ ] Для чего нужны методы attr_reader, attr_writer, attr_accessor?


- [ ] Для чего нужна полиморфная связь?


- [ ] Что такое assets pipeline?


- [ ] MVC в рельсах


- [ ] Что такое Filter методы? before/after


- [ ] Какие переменные бывают в руби?


- [ ] Как объявить глобальную переменную?


- [ ] В чем разница между классовыми и инстансовыми переменными?


- [ ] Разница между == и ===?


- [ ] Что делает метод #send?


- [ ] Какие есть модули в рельсах?


- [ ] Какие есть три уровня доступа к методам класса или модуля?


- [ ] Какие есть синтаксические структуры для вызова метода в руби?


- [ ] Что означает self?


- [ ] Какая система типов в руби?


- [ ] В чем различие между Date.today и Date.current?


- [ ] Что такое транзакции в базе данных и как они реализованы в рельсах?


- [ ] Кеширование в рельсах


- [ ] Как используется сессия и куки в рельсах?


- [ ] Какие типы ассоциаций есть в рельсах?


- [ ] Что такое ORM(Object-Relationship-Model) в рельсах?


- [ ] В чем разница между false и nil в руби?


- [ ] Что может сделать миграция?


- [ ] Что происходит после запуска рубишного файла? (ruby test.rb)


- [ ] Что за super() и super?


- [ ] Как работает array.map(&:method_name)?


- [ ] В чем разница между одиночной и двойной кавычкой?


- [ ] Какие методы для итерирования есть в руби?


- [ ] Define_method 


- [ ] Что за метапрограммирование?


- [ ] Что за n+1 проблема?


- [ ] Какие есть коллбеки?


- [ ] Зачем нужен #lazy метод


- [ ] Почему у некоторых методов на конце “!”, а у других “?” ?



# Разное
- [ ] Регулярные выражения
- [ ] Что такое ООП?
- [ ] Принципы ООП
- [ ] Что такое CDN?
- [ ] Сети (https://toster.ru/q/84380)
- [ ] Как реализовано удаление в постгресе?


