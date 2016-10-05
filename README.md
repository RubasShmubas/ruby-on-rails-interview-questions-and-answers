# Ruby on Rails interview questions and answers.

## Что такое Rack?

```ruby
Rack создан для обеспечения минимального API для подключения веб-серверов,
поддерживающих Ruby (WEBrick, Mongrel и т.д.) и
ruby веб-фреймворками (Rails, Sinatra и др.)

Rack позволяет с легкостью обрабатывать HTTP-запросы.

HTTP-запрос — тройка, состоящая из пары метод-ресурс, набора заголовков и тела запроса,
в то время как HTTP-ответ HTTP состоит из кода ответа, набора заголовков и опционального тела ответа.

Мэппинг Rack близок к этому. Rack-приложение — это ruby-объект, у которого есть метод call,
принимающий единственный аргумент — environment, и возвращающий массив трех элементов:
статус, заголовки и тело ответа.

Rack включает в себя обработчики и адаптеры. Первые соединяют его с ruby веб-серверами,
вторые — связывают с фреймворками.
```
![Rack](https://habrastorage.org/storage1/aba99564/dcf02d0e/f8cbd4f2/7fcf6b93.jpeg)
```ruby
Используя middleware, можно изменить rack под нужды вашего приложения.
Основная идея rack middleware — обработка запроса до того, как он попал в приложение,
и обработка ответа перед возвратом его клиенту.

```
####Быстрый экскурс в объект proc
```ruby
Помните объект proc? В руби блоки — это не объекты,
но они могут быть преобразованы в объекты класса Proc.
Это можно сделать, вызвав метод lambda класса Object.
Блок, созданный с помощью lambda ведет себя как метод ruby.
А у класса Proc есть метод call, который вызывает блок на исполнение.

>> my_rack_proc = lambda {puts 'Rack Intro'}
=> #<Proc:0x1fc9038@(irb):2(lambda)>
>> # method call invokes the block
?> my_rack_proc.call
Rack Intro
=> nil

Как уже упоминалось, наше rack приложение — это объект (не класс), реагирующий на метод call
и принимающий один аргумент — окружение (он же environment — прим. пер.).
Окружение должно быть экземпляром класса Hash. Приложение должно возвращать массив трех значений:
код состояния (больший или равный 100), заголовки (тоже хэш) и тела (тело обычно является массивом строк,
экземпляром приложения или файлом. Тело ответа должно отзываться на метод each и выводить только строковые значения).
Создадим наш новый proc объект.

>> my_rack_proc = lambda { |env| [200, {"Content-Type" => "text/plain"}, ["Hello. The time is #{Time.now}"]] }
=> # <Proc:0x1f4c358@(irb):5(lambda)>

Теперь можно вызвать my_rack_proc:

>> my_rack_proc.call({})
=> [200, {"Content-Type" => "text/plain"}, ["Hello. The time is 2011-10-24 09:18:56 +0530"]]

my_rack_proc — наш однострочный Rack.

Мы можем запустить наше rack-приложение с использованием любого из обработчиков.

>> Rack::Handler.constants
=> [:CGI, :FastCGI, :Mongrel, :EventedMongrel, :SwiftipliedMongrel, :WEBrick, :LSWS, :SCGI, :Thin]

Все эти обработчики имеют метод run, запускающий rack-приложение:
>> Rack::Handler::WEBrick.run my_rack_proc
[2011-10-24 10:00:45] INFO WEBrick 1.3.1
[2011-10-24 10:00:45] INFO ruby 1.9.2 (2011-07-09) [i386-mingw32]
[2011-10-24 10:00:45] INFO WEBrick::HTTPServer#start: pid=1788 port=80

Откройте в браузере страничку localhost и вы должны увидеть строчку типа:

Hello. The time is 2011-10-24 10:02:20 +0530

Rack-application — это не обязательно lambda. Оно так же может являтся методом

>> def my_method env
>> [200, {}, ["method called"]]
>> end
=> nil
>> Rack::Handler::WEBrick.run method(:my_method)
[2011-10-24 14:32:05] INFO WEBrick 1.3.1
[2011-10-24 14:32:05] INFO ruby 1.9.2 (2011-07-09) [i386-mingw32]
[2011-10-24 14:32:05] INFO WEBrick::HTTPServer#start: pid=1644 port=80

По адресу localhost вы снова увидите:

method called

Объекты методов создаются с помощью Object#method. Они связаны с конкретным объектом (а не только с классом).
Они могут быть использованы для вызова метода в пределах объекта.
Method.call — это метод, вызывающий метод (Ох, руби, руби… — прим. пер.)
с указанными аргументами и возвращающий значение, которое возвращает вызываемый метод.
```
####Использование rackup
```ruby

Гем rack идет с пачкой полезных штук, упрощающий жизнь разработчика. Одной из этих штук является rackup.

Rackup — полезный инструмент для запуска rack-приложений.
Rackup сам определяет среду, в которой он запущен и запускает ваше приложение как FastCGI, CGI,
или автономно с Mongrel или WEBrick — всех с одной и той де конфигурацией.

Для использования rackup, необходимо создать его конфигурационный файл.
По соглашению необходимо использовать расширение .ru у этого конфига. По умолчанию, rackup стартует на порту 9292.

Давайте создадим файл config.ru, содержащий:

run lambda { |env| [200, {"Content-Type" => "text/plain"}, ["Hello. The time is #{Time.now}"]] }

Этот файл содержит слово run, который может быть вызван всем, что отвечает на метод .call.

Для запуска rack-приложения в папке с файлом config.ru введите:

$ rackup config.ru
[2011-10-24 15:18:03] INFO WEBrick 1.3.1
[2011-10-24 15:18:03] INFO ruby 1.9.2 (2011-07-09) [i386-mingw32]
[2011-10-24 15:18:03] INFO WEBrick::HTTPServer # start: pid = 3304 port = 9292

Теперь по адресу localhost:9292/ нам будет снова будет отдаваться следующая страничка:

Hello. The time is 2011-10-24 15:18:10 +0530
Давайте переместим наше приложение из файла config.ru в my_app.rb:


# my_app.rb
class MyApp
  def call(env)
    [200, {"Content-Type" => "text/html"}, ["Hello Rack Participants"]]
  end
end

Так же изменим config.ru:

require './my_app'
run MyApp.new

Теперь снова можно запустить приложение (с помощью «rackup config.ru») и проверить, отдается ли страничка браузеру.

Подробнее: https://habrahabr.ru/post/131429/
```

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

## В чем разница между строкой и символом?

```ruby
Символы - immutable(неизменные) объекты, строки - mutable.

Mutable объеты могут быть изменены после назначения. Immutable объекты могут быть только перезаписаны.

var1 = "New String"
 => "New String" 
2.1.1 :010 > var1.object_id
 => 20297400 
2.1.1 :011 > var1 << ' appended text'
 => "New String appended text" 
2.1.1 :012 > var1.object_id
 => 20297400
 
Несколько символов с одинаковым значением всегда идентичны:
irb(main):007:0> :test.object_id
=> 83618
irb(main):008:0> :test.object_id
=> 83618
irb(main):009:0> :test.object_id
=> 83618

Строки различны:
irb(main):010:0> "test".object_id
=> -605770378
irb(main):011:0> "test".object_id
=> -605779298
irb(main):012:0> "test".object_id
=> -605784948

Символы сравниваются быстрее строк из-за возможности сравнения по object_id, в строках же приходится сравнивать значения.
```

## Что такое и в чем разница между лямбдой, проком и блоком в руби?

```
Блок - это кусок кода между do и end или между { и }.
do и end используются в многострочном блоке, {} используются в однострочном.
Блоки не являются объектами.

[1, 2, 3].each {|n| puts "Number #{n}"}
Number 1
Number 2
Number 3
 => [1, 2, 3]
```

#### Как работает yield?

```ruby
def my_method
  puts "reached the top"
  yield
  puts "reached the bottom"
end

my_method do
  puts "reached yield"
end

reached the top
reached yield
reached the bottom
 => nil
 
Код блока выполняется тогда, когда my_method доходит до yield.
Когда блок выполнен продолжает выполняться метод.
```

#### Передача блока методу

```ruby
Методу не обязательно что-то прописывать, чтобы получать блок как параметр.
В любой метод в руби можно передать блок.
Однако если метод не использует yield, то блок не будет выполнен.
С другой стороны, если в теле метода присутствует yield, то при вызове метода
произойдет ошибка, если блок не был передан.
Если хочется, чтобы блок был опционален, можно использовать метод block_given?

def my_method
  yield if block_given?
end

Любой параметр, переданный в yield, будет служить параметром блоку. Важен порядок аргументов.

def my_method
 yield('John', 2)
end

my_method do |name, age|
  puts "#{ name }  is #{ age } years old" # => John is 2 years old
end

Блоки не могут быть сами по себе, они являются частью синтаксиса вызова метода:
{ puts "Hello World"}       # syntax error  
a = { puts "Hello World"}   # syntax error
[1,2,3].each {|x| puts x*2} # only works as part of the syntax of a method call
```
#### Что значит &block (амперсанд параметр)

```
Руби позволяет отправлять любой объект методу как будто это блок.
Если метод получает не блок, то он вызывает метод to_proc на нем, чтобы преобразовать его к блоку.

def my_method(&block)
  puts block
  block.call
end

my_method { puts "Hello!" }

#<Proc:0x0000010124e5a8@tmp/example.rb:6>
Hello!

Метод call вызванный для блока это аналог yield.

```

#### Как работает .map(&:something)?

```ruby
.map(&:capitalize) в развернутом виде это .map { |title| title.capitalize }
Это происходит из-за метода to_proc класса Символ
```

#### Разница между проком и лямбдой:

```ruby
И прок, и лямбда - это объекты класса Proc.
proc = Proc.new { puts "Hello world" }
lam = -> { puts "Hello World" }

proc.class # returns 'Proc'
lam.class  # returns 'Proc'

Разница первая: Лямбды проверяют кол-во входных аргументов, а проки - нет:
lam = -> { |x| puts x }    # creates a lambda that takes 1 argument
lam.call(2)                    # prints out 2
lam.call                       # ArgumentError: wrong number of arguments (0 for 1)
lam.call(1,2,3)                # ArgumentError: wrong number of arguments (3 for 1)

proc = Proc.new { |x| puts x } # creates a proc that takes 1 argument
proc.call(2)                   # prints out 2
proc.call                      # returns nil
proc.call(1,2,3)               # prints out 1 and forgets about the extra arguments

Разница вторая: проки и лямбды по-разному реагируют на return. 
Прок выполняется в той области, где был определен. Лямбда - в той, где была вызвана.
def lambda_test
  lam = lambda { return }
  lam.call
  puts "Hello world"
end

lambda_test                 # calling lambda_test prints 'Hello World'

def proc_test
  proc = Proc.new { return }
  proc.call
  puts "Hello world"
end

proc_test                 # calling proc_test prints nothing
```

## Собственная реализация метода each, map и inject (и других популярных методов)

```ruby
module Enumerable
  def my_map
    each_with_object([]) do |el, arr|
      arr << (yield el)
    end
  end

  def my_each
    i = 0
    while i < size
      yield self[i]
      i += 1
    end
  end
end
```

## В чем разница между and(or) и &&(||)?

```ruby
and это то же самое, что &&, но с низким приоритетом.
У and и or приоритет ниже, чем у =

foo = :foo
bar = nil

a = foo and bar
# => nil
a
# => :foo

a = foo && bar
# => nil
a
# => nil

a = (foo and bar)
# => nil
a
# => nil

(a = foo) && bar
# => nil
a
# => :foo

Некие правила:

Use &&/|| for boolean expressions, and/or for control flow. (Rule of thumb: If you have to use outer parentheses, you are using the wrong operators.)

# boolean expression
if some_condition && some_other_condition
do_something
end

# control flow
document.saved? or document.save!
```



## В чем разница между Hash(Ruby) и HashWithIndifferentAccess(ActiveSupport)?

```ruby
Hash класс из рубишной кор библиотеки сравнивает значения с помощью оператора ==.
Это означает, что символьный ключ(:my_value) не может быть извлечен эквивалентной строкой('my_value').
HashWithIndifferentAccess в свою очередь считает символьные и строковые ключи эквивалентными:

h = HashWithIndifferentAccess.new
h[:my_value] = 'foo'
h['my_value'] #=> вернет "foo"
```

## Что такое CSRF(Cross-Site Request Forgery)?

```ruby
CSRF stands for Cross-Site Request Forgery.
This is a form of an attack where the attacker submits a form on your behalf
to a different website, potentially causing damage or revealing sensitive information.
Since browsers will automatically include cookies for a domain on a request,
if you were recently logged in to the target site, the attacker’s request will
appear to come from you as a logged-in user (as your session cookie will be sent with the POST request).

In order to protect against CSRF attacks, you can add protect_from_forgery to your ApplicationController.
This will then cause Rails to require a CSRF token to be present before accepting any
POST, PUT, or DELETE requests. The CSRF token is included as a hidden field in every
form created using Rails’ form builders. It is also included as a header in GET
requests so that other, non-form-based mechanisms for sending a POST can use it as well.
Attackers are prevented from stealing the CSRF token by browsers’ “same origin” policy.
```

## Что означает a ||= b?

```ruby
# a = b when a == false
# иначе a остается неизменным
a || a = b

a = 1
b = 2
a ||= b #=> a = 1

a = nil
b = 2
a ||= b #=> a = 2

a = false
b = 2
a ||= b #=> a = 2
```

## Для чего нужны методы attr_reader, attr_writer, attr_accessor?

```ruby
attr_accessor - это attr_writer + attr_reader.
attr_writer - это сеттер, attr_reader - геттер

attr_writer :age

эквивалентно

def age=(value)
  @age = value
end

attr_reader :age

эквивалентно

def age
  @age
end

attr_accessor :age

эквивалентно

def age=(value)
  @age = value
end

def age
  @age
end
```

## Равенство в руби (методы equal?, eql?, == и ===)

```ruby
equal? определен у класса Object.
Все, что он делает — это проверка, указывают ли переменные на один и тот же объект.

eql? также определен у класса Object.
Однако переопределяется в подклассах(в частности всех Numeric и String) — возвращает true,
если объекты имеют одинаковые значения, однако проверяется еще и принадлежность к одному классу.
Используется классом Hash.

== на уровне класса Object делает то же самое, что метод equal?, т.е. проверяет указывают ли переменные на один объект.
В подклассах(в частности строковых и числовых) он переписан, чтобы проверять только значения.

=== используется в case.
проверяет принадлежность не только классу, но и принадлежит ли объект потомку этого класса, т.е.
Numeric === 42
Integer === 42
Fixnum === 42
# все true
# аналог kind_of?
```

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

## Что означает self?

```ruby
self всегда отсылает к текущему объекту. Этот вопрос немного усложняется тем, что Классы сами по себе тоже объекты.

class WhatIsSelf
  def test
    puts "At the instance level, self is #{ self }"
  end

  def self.test
    puts "At the class level, self is #{ self }"
  end
end

WhatIsSelf.test 
# => At the class level, self is WhatIsSelf

WhatIsSelf.new.test 
# => At the instance level, self is #<WhatIsSelf:0x28190>

На уровне класса self это сам класс, в данном случае WhatIsSelf
На уровне инстанса self это инстанс, в данном случае инстанс класса WhatIsSelf 
```

## Какая система типов в руби?

```ruby
Руби обладает динамической, сильной, неявной системой типов.
Подробно: https://habrahabr.ru/post/161205/

Статическая / динамическая типизация.
Статическая определяется тем, что конечные типы переменных и
функций устанавливаются на этапе компиляции.
Т.е. уже компилятор на 100% уверен, какой тип где находится.
В динамической типизации все типы выясняются уже во время выполнения программы. 

1) Dynamic Vs Static

Example of a language statically typed :

int x;
x = 3;
x = "hello"; //ERROR!

Example of a language dynamically typed :

x = 3
x = "Transformers seems to be a boring movie"
x = :why_are_we_here
x = /is this a reg ex/
x = "enough already... I think we understand"

Сильная / слабая типизация (также иногда говорят строгая / нестрогая).
Сильная типизация выделяется тем, что язык не позволяет смешивать в выражениях
различные типы и не выполняет автоматические неявные преобразования,
например нельзя вычесть из строки множество.
Языки со слабой типизацией выполняют множество неявных преобразований автоматически,
даже если может произойти потеря точности или преобразование неоднозначно.

2) Strong Vs Weak

In ruby, you CAN do :
x = "3"
y = x + "ho!" #result : "3ho!"

but you CANNOT do:
x = "3"
y = x + 3 #Wooo! Ruby won't like that

Явная / неявная типизация.
Явно-типизированные языки отличаются тем, что тип новых переменных / функций /
их аргументов нужно задавать явно. Соответственно языки с неявной типизацией
перекладывают эту задачу на компилятор / интерпретатор.
```

## В чем различие между Date.today и Date.current?

```ruby
def current
  ::Time.zone ? ::Time.zone.today : ::Date.today
end

Если определена таймзона, то вернется дата согласно таймзоне, иначе Date.today
```

## Что такое транзакции в базе данных и как они реализованы в рельсах?

```ruby
ActiveRecord’s transaction метод принимает блок и завершается в том случае, если все вложенные в блок sql
стейтменты завершились без ошибок. В случае рейза эксепшенов, будет вызвал rollback, который
возвращает бд к изначальному состоянию.

ActiveRecord::Base.transaction do
  david.withdrawal(100)
  mary.deposit(100)
end
Это отработает в том случае, если ни withdrawal, ни deposit методы не вызовут эксепшенов.\

Т.к. метод определен в ActiveRecord::Transactions модуле, заинклюженном в ActiveRecord::Base,
этот метод доступен для любого класса, наследуемого от ActiveRecord::Base

Методы save и destroy сами по себе уже обернуты в транзакцию, поэтому следующий код не имеет смысла:
ActiveRecord::Base.transaction do
  @client.save
end
т.к. метод save в случае ошибки вернет false, которого не достаточно для того, чтобы транзакция не выполнилась
Необходимо рейзить эксепшен, т.е. вместо метода save или destroy надо использовать save! или destroy!
ActiveRecord::Base.transaction do
  @client.save!
end
```

## Как используется сессия и куки в рельсах?

```ruby
Для каждого пользователя сессия хранит в себе некоторые данные, которые будут сохранены между запросами.
Сессии используют куки для хранения id сессии.
Доступ к сессии осуществляется через метод session, который доступен в контроллере и вьюхе.
Значения храняется в сессии с помощью пары ключ/значение.

Куки хранят небольшое кол-во данных на клиенте. Данные сохраняются между запросами и между сессиями.
Доступ к кукам осуществляется через метод cookies.
Как и в сессии значения храняется с помощью пары ключ/значение.
```

## Какие типы ассоциаций есть в рельсах?

```ruby
belongs_to
has_one
has_many
has_many :through
has_one :through
has_and_belongs_to_many

Отдельно стоит упомянуть полиморфную связь и STI(single table inheritance)
```

## В чем разница между false и nil в руби?

```ruby
nil - инстанс NilClass-а, false - инстанс FalseClass-а
false - boolean datatype
```

## Что происходит после запуска рубишного файла? (ruby test.rb)

```ruby
ruby code -> tokenize -> parse -> compile -> YARV instructions

Сначала руби токенизирует код, посимвольно идя по коду и преобразовывая 
рубишные синтаксические конструкции в токены.
Затем токены парсятся и преобразовываются в ноды abstract syntax tree(AST)
Затем ноды AST компиляется в YARV instructions. YARV - Yet Another Ruby Virtual Machine

Подробнее в книге "руби под микроскопом"
```

## Что за super() и super?

```ruby
super обращается к родительскому классу, чтобы вызвать метод с таким же названием,
как то, откуда super вызывается. В этом случае в родительский метод передаются те же аргументы, что
и в текущий метод.
super() делает то же самое, но не передает аргументы

Итого: super - передает все аргументы, super() не передает аргументы.
```

## В чем разница между одиночной и двойной кавычкой?

```ruby
Двойные ковычки позволяют использовать строковую интерполяцию:
world_type = 'Mars'
"Hello #{world_type}"
```

- [ ] Define_method 


- [ ] Что за метапрограммирование?


- [ ] Что за n+1 проблема?


- [ ] Какие есть коллбеки?


- [ ] Зачем нужен #lazy метод


- [ ] Почему у некоторых методов на конце “!”, а у других “?” ?


- [ ] Что такое assets pipeline?


- [ ] MVC в рельсах


- [ ] Что такое Filter методы? before/after


- [ ] Какие переменные бывают в руби?


- [ ] Как объявить глобальную переменную?


- [ ] В чем разница между классовыми и инстансовыми переменными?


- [ ] Для чего используется constraints опция в роутах?


- [ ] В чем разница между find и find_by_что-то?


- [ ] Что делает метод #send?


- [ ] Какие есть модули в рельсах?


- [ ] Как реализован Hash в руби?


- [ ] Что такое REST?


- [ ] Кеширование в рельсах


# Разное
- [ ] Регулярные выражения
- [ ] Что такое ООП?
- [ ] Принципы ООП
- [ ] Что такое CDN?
- [ ] Сети (https://toster.ru/q/84380)
- [ ] Как реализовано удаление в постгресе?


