# Атомы и кортежи

*Атомы* типичны для функциональных языков, но редко встречаются в языках императивных. 

TODO: symbols in Ruby

Это некие константные значения, которые можно сравнивать друг с другом. Собственно, сравнивать — это единственное, что с ними можно делать. Сами по себе они не очень полезны, но становятся полезны в комплекте с кортежами и сопоставлением с образцом (pattern matching).

Эффективны по памяти. Независимо от длины имени в рантайме занимают 32 бита, потому что представляют собой ссылку на таблицу атомов. 

Их можно рассматривать как человекочитаемые синонимы для 32-х битных чисел. Используются как имена констант. Операция сравнения работает очень быстро.

```elixir
:user
:point
:ip_address
```

TODO примеры с большой буквы. 
Имена модулей являются атомами.
MyModule в рантайме разворачивается в :"Elixir.MyModule".

*Кортеж (tuple)* — это структура данных, объединяющая несколько разных значений. Кортеж похож на список, но в отличие от списка имеет фиксированную длину.

```elixir
{"Bob", :male, 23}
{1, 2}
{127, 0, 0, 1}
```

В кортежах на первой позиции часто ставят атом, чтобы обозначить, что за данные собраны в кортеже. Таким образом кортеж помечается тэгом, и это называется **тэгированный кортеж (tagged tuple)**.

```elixir
{:user, "Bob", :male, 23}
{:point, 1, 2}
{:ip_address, 127, 0, 0, 1}
```

Кортежи могут быть вложенными:

```elixir
{:rectangle, {:point, 0, 0}, {:point, 10, 10}}
{:ip4, {127, 0, 0, 1}}
```

Небольшие объекты, состоящие из 2-4 полей, удобно представлять в виде кортежей, если роль полей понятна из контекста. В ином случае нужно использовать *словарь (map)* или *структуру (struct)*.

Атомы и кортежи — это легковесные объекты, они используют меньше памяти, чем словари и структуры, и операции над ними выполняются быстрее.

Сопоставление с образцом мы будем изучать подробно. Сейчас важно знать, что это способ извлечь отдельные значения из кортежа:

```elixir
my_point = {:point, 5, 10}
{:point, x, y} = my_point
IO.puts(x) # => 5
IO.puts(y) # => 10
```

TODO: elem/2
TODO: more examples with user, get name, get age

Рассмотрим реализацию функции distance, которая вычисляет расстояние между двумя точками:

```elixir
def distance(point1, point2) do
  {:point, x1, y1} = point1
  {:point, x2, y2} = point2
  x_dist = abs(x1 - x2)
  y_dist = abs(y1 - y2)
  :math.sqrt(:math.pow(x_dist, 2) + :math.pow(y_dist, 2))
end
```
TODO: templating, extract code from lib/*.exs
TODO: abs not needed, but logical to use

Функция принимает в аргументах две точки, извлекает их координаты с помощью сопоставления с образцом, и по теореме Пифагора вычисляет расстояние между точками.

Для этого применяется модуль *:math* из языка Эрланг, потому что у Эликсир нет своего такого модуля в стандартной библиотеке (есть в сторонних библиотеках). Если бы такой модуль был, то код выглядел бы так:

```elixir
Math.sqrt(Math.pow(x_dist, 2) + Math.pow(y_dist, 2))
```

Обычно извлечение значений из кортежа с помощью сопоставления с образцом делают прямо в аргументах функции:

```elixir
def distance({:point, x1, y1}, {:point, x2, y2}) do
  x_dist = abs(x1 - x2)
  y_dist = abs(y1 - y2)
  :math.sqrt(:math.pow(x_dist, 2) + :math.pow(y_dist, 2))
end
```
TODO: need another lib/exs for this

Результат работы функции:

```elixir
distance({:point, 0, 0}, {:point, 0, 5})  # 5.0
distance({:point, 2, 2}, {:point, 10, 12})  # 12.806248474865697
distance({:point, -5, -5}, {:point, 10, 10})  # 21.213203435596427
```

```
iex(6)> c "lib/lesson_03/task_03_04_atom_tuple.exs"
[Lesson_03.Task_03_04_AtomTuple]
iex(7)> import Lesson_03.Task_03_04_AtomTuple
Lesson_03.Task_03_04_AtomTuple
iex(8)> p1 = {:point, 1, 1}
{:point, 1, 1}
iex(9)> p2 = {:point, 10, 10}
{:point, 10, 10}
iex(10)> distance(p1, p2)
12.727922061357855
```

```
elixir lib/lesson_03/task_03_04_atom_tuple.exs
```

Реализовать функцию is_point_inside_figure для прямоугольника и круга.


Точка представлена кортежем `{:point, x, y}`.

Окружность представлена кортежем `{:circle, center, radius}`, где center — это кортеж `:point`.

Прямоугольник представлен кортежем `{:rect, left_top, right_bottom}`, где left_top и right_bottom — это кортежи `:point`.

TODO: в скринкасте я использовал @type и @spec. Стоит ли их вводить на данном этапе? Собственно, @spec уже был на самом первом занятии. А @type удобный способ формально описать, что такое point, circle, rect.

TODO: я сперва сделал `point_inside_cirle?` и `point_inside_rect?`, а потом обобщил до `point_inside_figure?`. Есть смысл это сохранить.

TODO: code and sessions from templates
