# Ruby on Rails interview questions and answers.

- [ ] Что такое Rack?
- [ ] Что такое Singleton Class в руби?
- [x] Что такое класс в руби?
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

- [x] Что такое модуль в руби?
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

- [ ] Разница между классом и модулем в руби?


- [ ] Как реализовано множественное наследование в руби? (multiple inheritance)


- [ ] В чем разница между include и extend?


- [ ] Что такое объект в руби?


- [ ] Как объявляется и используется конструктор в руби?


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


