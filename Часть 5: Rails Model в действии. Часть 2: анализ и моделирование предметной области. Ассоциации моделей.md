```ruby
class Test < ApplicationRecord
```

```ruby
has_many :questions
```

добавляет в класс
```ruby
Test
```
метод 
```ruby
questions
```
и любой объект класса 
```ruby
Test
```
получит метод 
```ruby
questions 
```
который будет возвращать массив вопросов связанный с текущим тестом(объектом класса 
```ruby
Test
```
)

По соглашению Rails для one to many ассоциации у родительской  Модели
```ruby
(Test) 
```
название ассоциации 
```ruby
has_many
```
должно быть таким же как название  

во множественном числе и в нижнем регистре, можно передать хэш опций что бы все работало не по соглашению Rails

```ruby
модели(Question) has_many: questions
```


```ruby
belongs_to : test  в классе Question
```
любой объект класса
```ruby
Question
```
получит метод test который вернет связанный с текущим вопросом тест

```ruby
question.test
```



По соглашению Rails будет искать в таблице 

```ruby
questions
```
внешний ключ 

```ruby
test_id
```

, это все настраиваемо 

```ruby

test.questions
```
вернет не просто массив объектов а специальный объект класса 


```ruby
ActiveRecord::Associations_CollectionProxy
```
он ведет себя как массив но добавлять дополнительные методы

```ruby
reload
```
перезагружает коллекцию актуальными данными из БД

```ruby
build
```
позволяет создать новый связанный объект
```ruby
build имеет alias new
```

   если написать 
```ruby
   test.questions.build 
```
   или 
```ruby
test.questions.new
```

   

принимает хэш со значениями для записи в бд

```ruby
test.questions.build(body: 'test', level: 10)
```
для сохранения а бд нужно применить метод 
```ruby
save
```

```ruby
test.questions.build(body: 'test', level: 10).save
```

```ruby
test.questions.create(body: 'test', level: 10) сразу сохранит в бд не нужно будет использовать ***save***
```



```ruby
test.questions.ids
```
вернет все ***id ***

связанных с тестом вопросов в ввиде массива 
```ruby
[3, 5, 6]
```


```ruby
question = Question.new(body: 'fwefwef') а после ввести test.questions.push(question) можно и так написать
```



есть еще много методов для объекта ассоциаций смотреть в доках

так же можно искать связанные объекты по условию
```ruby
test.questions.where('created_at > ?', 1.hour.ago).order(created_at: :desc)
```
если сделать 
```ruby
question.test
```
то вернется сам объект теста( при условии что у нас таблица и связь моделей (one to many) )

все тоже самое и для one to one связи, в моделях указываем
```ruby
has_one :question в модели Test
```
и 
```ruby
belongs_to :test 
```

Для связи 
```ruby
many to many
```

```ruby
has_and_belongs_to_many :tests
```
в модели 
```ruby
Question
```
и
```ruby
has_and_belongs_to_many
```
в модели 
```ruby
Test
```
и конечно нужна соединительная таблица к примеру 
```ruby
questions_users
```
 используем

 ```ruby
 rails g migration CreateQuestionsTestsJoinTable questions tests это что бы рельсы описали миграцию автоматом типо что create_join_table
 ```
 (спецом пишу так что бы Rails поняли что делаем соединительную таблицу но это не обязательно, можно и самому все написать в миграции что это соединительная таблица)

в миграции 

![Миграция][migration]
```ruby
unique: true
```
только уникальные значения с двумя такими полями

```ruby
rails db:migrate
```
провести миграцию 

и теперь можно писать
```ruby
user.tests
```
и наоборот 
```ruby
test.users
```


Если создать новый объект 
```ruby
test
```
или 
```ruby
question
```
то он запишется не только в таблицу но и в промежуточную таблицу 
```ruby
question_tests
```
у промежуточной таблицы так же может быть модель(необязательно но может быть полезна если у промежуточной таблицы будут доп поля и что бы была возможность к ним обращаться), она должна называться по соглашению Rails  в единственном числе

```ruby
TestsUser
```

( Это можно настроить)

![Главные модели][Many_to_many]

![Промежуточная модель][JoinTable]

модели соединяются через промежуточную модель 
```ruby
TestsUser
```
и нужно указать ей с помощью
```ruby
through: tests_users эта запись позволяет делать доступ к другой таблице и модели через промежуточную на картинке сверху показано
```
во множественном числе промежуточную модель (это настраиваемо) и все то же самое и для второй модели

теперь для доступа к промежуточной таблице нужно указать 
```ruby
test.tests_users
```

но если просто указать 
```ruby
test.users
```
то столбцы из таблицы
```ruby
tests_users
```
не будут доступны а только из таблицы users, для решения проблемы нужно использовать, 
```ruby
test1 = user.tests.select('tests * , tests.* '),
```
то есть с помощью
```ruby
select
```
указываем что хотим получить все колонки из 
```ruby
tests и tests_users
```
или указать конкретные колонки 
```ruby
user.tests.select('tests * , tests.progress ') 
```
звездочка означает включить все( стандартная вещь для всего), но столбцы не будут показаны(визуально) но будут загружены и к ним уже можно будет обратиться 
```ruby
test1.progress
```
но что бы поменять 
```ruby
progress
```
придется работать через модель 
```ruby
TestsUser
```

[Дополнительная информация]

[migration]:  https://i.imgur.com/BoAvWQZ.png
[Many_to_many]: https://i.imgur.com/vp4xj1W.png
[JoinTable]:   https://i.imgur.com/D5B2AZo.png
[Дополнительная информация]: http://rusrails.ru/active-record-associations
```

```