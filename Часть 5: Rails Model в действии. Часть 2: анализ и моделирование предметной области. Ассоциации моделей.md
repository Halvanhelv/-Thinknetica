class Test < ApplicationRecord

has_many :questions добавляет в класс Test метод questions и любой объект класса Test получит метод questions который будет возвращать массив вопросов связанный с текущим тестом(объектом класса Test)

По соглашению Rails у родительской Модели(Test) название ассоциации 

has_many должно быть таким же как название  модели(Question)  во множественном числе и в нижнем регистре, можно передать хэш опций что бы все работало не по соглашению Rails

end

class Question < ApplicationRecord

belongs_to : test любой объект класса Question получит метод test который вернет связанный с текущим вопросом(объектом класса Question) объект класса Test

По соглащению Rails будет искать в таблице questions внешний ключ test_id, это все настраиваемо 

end



test.questions вернет не просто массив объектов а специальный объект класса ActiveRecord::Associations_CollectionProxy он ведет себя как массив но добавлять дополнительные методы

1. reload перезагружает коллекцию актуальными данными из БД

2. build позволяет создать новый связанный объект

3. build имеет alias new

   если написать test.questions.build или test.questions.new то внешний ключ у новосозданного объекта questions.build уже будет указан

   4. так же build или new помещает в коллекцию(массив) questions новосозданный с помощью build объект 

   4. build и new принимает хэш со значениями для записи в бд

      test.questions.build(body: 'test', level: 10)

   5. для сохранения а бд нужно применить метод save

test.questions.build(body: 'test', level: 10).save

4. test.questions.create(body: 'test', level: 10) сразу сохранит в бд не нужно будет использовать save

5. test.questions.ids вернет все id связанных с тестом вопросов в ввиде массива [3, 5, 6]

6. question = Question.new(body: 'fwefwef')

    test.questions.push(question) можно  и так написать

есть еще много методов для объекта ассоциаций смотреть в доках

так же можно искать связанные объекты по условию

test.questions.where('created_at > ?', 1.hour.ago).order(created_at: :desc)

если сделать question.test то вернется сам объект теста( при условии что у нас таблица и связь моделей (one to many) )

все тоже самое и для one to one связи, в моделях указываем has_one :question и belongs_to : test

Для связи many to many

has_and_belongs_to_many :tests  в модели Question и has_and_belongs_to_many в модели Test и конечно нужно соеденительная таблица к примеру questions_users

 используем rails g migration CreateQuestionsTestsJoinTable questions tests(спецом пишу так что бы Rails поняли что делаем соеденительную таблицу но это не обязательно, можно и самому все написать в миграции что это соеденительная таблица)

в миграции 

![Миграция][migration]

unique: true только уникальные значения с двумя такими полями

rails db:migrate провести миграцию 

и теперь можно писать user.tests и наоборот test.users

Если создать новый объект test или question то он запишется не только в таблицу но и в промежуточную таблицу question_tests

у промежуточной таблицы так же может быть модель(необязательно но может быть полезна если у промежуточной таблицы будут доп поля и что бы была возможность к ним обращаться), она должна называться по соглащению Rails  в единственном числе TestsUser( Это можно настроить)

![Главные модели][Many_to_many]

![Промежуточная модель][JoinTable]

модели соеденяются через промеждуточную модель TestsUser и нужно указать ей с помощью through: tests_users во множественном числе промежуточную модель (это настраиваемо) и все то же самое и для второй модели

теперь для доступа к промежуточной таблице нужно указать test.tests_users

но если просто указать test.users то столбцы из .tests_users не будут доступны, для решения проблемы нужно использовать, test1 = user.tests.select('tests * , tests.* '), то есть с помощью select указываем что хотим получить все колонки из tests и tests_users или указать конкретные колонки user.tests.select('tests * , tests.progress ') звездочка означает включить все( стандартая вещь для всего), но столбцы не будут показаны но будут загружены и к ним уже можно будет обратиться test1.progress но что бы поменять progress придется работать через модель TestsUser

[migration]:  https://i.imgur.com/BoAvWQZ.png
[Many_to_many]: https://i.imgur.com/vp4xj1W.png
[JoinTable]:   https://i.imgur.com/D5B2AZo.png