```ruby
ActiveRecord Query Interface
```
Сохранить в базу
```ruby
test = Test.new 
test.title = 'hello'
test.save
```
 Сохраниться в базу сразу
```ruby
 test = Test.create(title: 'world',level:1,category_id:1)
```
Проверка сохранен ли объект в базе данных? 
```ruby
test.persisted?
```
Проверка на отсутсвие объекта в базе данных?
```ruby
test.new_record? 
```
Несколько строчек в базе данных
```ruby
Test.create([{title:'rails',level:2,category_id:1},{title:'qwerty',level:3,category_id:1}]) 
```
поиск строки по id (Выбрасывает исключение если не найдено)
```ruby
test = Test.find(4) или ([4,5])
```
без исключения, поиск по любому столбцу и по нескольким условиям
```ruby
test = Test.find_by(id:4) (id:4,title:"fwef')
```
выбрасывает исключение, если есть несколько строк то возвращается одна но порядок неопределен 
```ruby
test = Test.find_by?(id:4) (id:4,title:"fwef')
```
вызов всех строк(вернет все, использовать на маленьких базах)
```ruby
Test.all
```
вернет первый элемент, поиск по id
```ruby
Test.first
```
Поиск по условию
возвращает массив в любом случае, 
даже если ничего не вернется
```ruby
Test.where(title:'hello') 
```
аналог 
```ruby
WHERE SQL
```
а так же можно писать так:
```ruby
(title: :hello)
```
и так тоже можно писать
```ruby
(title:[:hello,:world]) и (level:0..2)
```
поиск по времени от вчерашнего до текущего
```ruby
Test.where(created_at: 1.day.ago..Time.current) 
```
или 
```ruby
2.days.ago
```
если множественное число
https://api.rubyonrails.org/classes/ActiveSupport/Duration.html

вывод в красивом формате
```ruby
pp
```
поиск больше,меньше 
```ruby
Test.where("level >=1")
```

Поиск по нескольким условиям 
```ruby
Test.where("level >=1 AND title='hello'")
title = 'hello'
level = 1
```
если придет sql(от юзера хакера) запрос в переменную level или title то он будет превращен в строку ()
```ruby
Test.where("level >= ? AND title= ?",level,title)
```
что бы не было путаницы что и куда идет(хз чем поможет)
```ruby
Test.where("level >= :level AND :title", level: level, title: title) 
```
все кроме этого(отрицание)
```ruby
Test.where.not("level >=1")
```
 можно и так
```ruby
Test.where.not(title: ['rails','ruby'])
Test.where.not(title: ['rails','ruby']).or(Test.where.not(title: ['wdqd','rubqdy']))
```
покажет только указанные столбцы 
```ruby
Test.select(:id,:title)
```
вернет именно массив значений title но не объекты
```ruby
Test.pluck(:title)
```
обнулит все title и вернет количество задетых стро
```ruby
.update_all(:title 0)
```
удалит напрямую sql, минуя ActiveRecord
```ruby
.delete_all(:title 0)
```
пройдет через модели потом удалит, удалит 
каждый объект по отдельности, то есть использует модель
```ruby
.destroy_all(:title 0)
```
по цепочке найдет строки где уровень больше 1 и обнулит их в  обход моделей, напрямую через sql
```ruby
Test.where("level >=1").update_all(:title 0) 
```
Сортировка по id(по дефолту по возрастанию)
```ruby
Test.order('id')
```
по Убыванию
```ruby
Test.order('id DESC')
```
по Возрастанию(явно указали)
```ruby
Test.order('id ASC')
```
самая ранняя запись
```ruby
Test.order('id ASC').limit(1)
```
Сортировка по опреденному полю или по нескольким( если будут два одинаковых то дальше пойдет сортировка по второму условию)
```ruby
Test.order(created_at: :desc)
Test.order(level: :desc,created_at: :desc)
```

Для пагинации
```ruby
Test.order(created_at: :desc).limit(1).offset(1)
```
аналог
```ruby
Test.find_each(batch_size:1)
```
Опция 
```ruby
:batch_size
```
позволяет определить число записей, подлежащих получению в одном пакете, до передачи отдельной записи в блок. Например, для получения 5000 записей в пакете:

```ruby
{|test| puts test}
```
но позволяет не указывать каждый раз начальные значения(как я это делал в php), проще говоря сам итерирует offset пока есть из чего брать :start стартовая точка для итерации 
:finish позволяет указать последний ID последовательности, когда наибольший ID не тот, что вам нужен. Это может быть полезно, например, если хотите запустить процесс пакетирования, используя подмножество записей на основании
```ruby
:start и :finish
User.find_each(start: 2000, finish: 10000) do |user|
  NewsMailer.weekly(user).deliver_now
end
```
[Дополнительная информация][1]

```ruby
Test.joins('JOIN categories ON tests.category_id = categories.id')

Test.count
Test.sum(:level)
Test.minimum(:level)

test = Test.new(level: 0,title: :hello,category_id: 1)
```
создать объект
```ruby
test.save
```
сохранить объект в базе
```ruby
test.update(level:0,title: :sad)
```
обновит объект а если такого нет в бд то создаст и сохранит
```ruby
test.destroy удален в базе но не в памяти
```
обнуляет все изменения сделанные в памяти и по новой показывает то что есть в базе
```ruby
test.reload
```

[1]: http://rusrails.ru/active-record-query-interface

