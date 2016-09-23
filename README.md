# Ruby on Rails interview questions and answers.

- [ ] Что такое Rack?

## Что такое Singleton Class в руби?
```ruby
Классы — это тоже объекты. От "обычных" объектов их отличает две вещи:
они служат образцом для создания новых объектов и они являются частью иерархии классов.
Классы могут иметь экземпляры себя(объекты), суперклассы(родители) и подклассы(дети).
Если классы - объекты, то у них должен быть свой собственный класс.
В руби это класс Class.

Dog = Class.new
Dog.class
# => Class
```

#### Путь поиска метода:

```ruby
Когда вы вызываете метод для какого-то объекта, сначала Ruby ищет этот метод в классе объекта.
Если там метод найти не удается, то следующим будет проверен суперкласс класса объекта
и так далее по всей иерархии классов до самого первого класса,
которым в Ruby 1.9 является BasicObject.

Путь поиска метода для экземпляра класса Dog:
fido -> Dog -> Mammal -> Object...

Путь поиска метода для объекта Dog:
Dog -> Class -> Module -> Object...
```

#### Синглтон-методы:

```ruby
Основная функция классов — определять поведение своих экземпляров.
Например, общее поведение собак расположено в классе Dog.
Однако в руби мы можем добавлять уникальное поведение отдельным объектам.
Т.е. мы можем добавить методы отдельному объекту класса Dog,
которые не будут доступны другим экземплярам этого класса.
Чаще всего такие методы называют синглтон-методами,
потому что они принадлежат только одному единственному объекту.

snoopy = Dog.new
def snoopy.alter_ego
  "Red Baron"
end

snoopy.alter_ego
# => "Red Baron"

fido.alter_ego
# => NoMethodError: undefined method `alter_ego' for #<Dog:0x0000000190cee0>
```

#### Метаклассы:

```ruby
Как было отмечено выше, когда мы вызываем метод для какого-то объекта, то Ruby ищет его в классе объекта.
В случае синглтон-методов очевидно, что они находятся вне класса объекта,
потому что они недоступны другим экземплярам этого класса.
Так где же они? И каким образом Ruby умудряется находить синглтон-методы наравне
с обычными методами экземпляра класса без нарушения нормального пути поиска метода?

Оказывается, Ruby реализует это в характерном ему изящном стиле.
Сначала создается анонимный класс, чтобы разместить в нем синглтон-методы объекта.
Затем этот анонимный класс занимает роль класса объекта,
а класс объекта становится суперклассом анонимного класса.
```

#### Методы класса:

```ruby
Являясь объектами, классы тоже могут иметь синглтон-методы.
По правде говоря, то что мы привыкли считать методами класса на самом деле
является ничем иным как синглтон-методами — методами, которые принадлежат
одному единственному объекту, который, так уж получилось, является классом.
Как и любые синглтон-методы, методы класса располагаются внутри метакласса.

Методы класса могут быть определены различными способами:

class Dog
  def self.closest_relative
    "wolf"
  end
end

class Dog
  class << self
    def closest_relative
      "wolf"
    end
  end
end

def Dog.closest_relative
  "wolf"
end

Подробнее: https://habrahabr.ru/post/143990/
```

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


## Какие есть три уровня доступа к методам класса или модуля?

```ruby
Любые методы, вне зависимости от их уровня доступа, доступны внутри класса.

Public методы не имеют ограничений, они доступны откуда угодно.
Protected методы доступны только другим объектам данного класса.
Private методы доступны только внутри данного класса

class AccessLevel
  def something_interesting
    another = AccessLevel.new
    another.public_method
    another.protected_method
    another.private_method
  end

  def public_method
    puts "Public method. Nice to meet you."
  end

  protected

  def protected_method
    puts "Protected method. Sweet!"
  end

  private 

  def private_method
    puts "Incoming exception!"
  end
end

AccessLevel.new.something_interesting
# => Public method.  Nice to meet you.
# => Protected method.  Sweet!
# => NoMethodError: private method ‘private_method’ called for
# => #<AccessLevel:0x898c8>
```

## Какие есть синтаксические структуры для вызова метода в руби?

```ruby
точка-оператор, Object#send метод или method(:foo).call

object = Object.new
puts object.object_id
 #=> 282660

puts object.send(:object_id)
 #=> 282660

puts object.method(:object_id).call
 #=> 282660
```

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


