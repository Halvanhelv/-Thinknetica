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

[migration]:  https://i.imgur.com/BoAvWQZ.png