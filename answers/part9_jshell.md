# Часть 9 — Эксперименты в jshell

## Как запустить jshell

Откройте терминал IntelliJ (View → Tool Windows → Terminal) и введите:
```
jshell
```
Для выхода: `/exit`

---

## Задание 9.1: Sealed-классы

### Команды (скопируйте и вставьте в jshell)

```
sealed interface Shape permits Circle, Square {}
record Circle(double r) implements Shape {}
record Square(double side) implements Shape {}
Shape s = new Circle(5)
s instanceof Circle c ? "Круг r=" + c.r() : "Не круг"
```

### Фактический вывод:

```
sealed interface Shape permits Circle, Square {}
|  created interface Shape

record Circle(double r) implements Shape {}
|  created record Circle

record Square(double side) implements Shape {}
|  created record Square

Shape s = new Circle(5)
s ==> Circle[r=5.0]

s instanceof Circle c ? "Круг r=" + c.r() : "Не круг"
$4 ==> "Круг r=5.0"
```

### Вопрос: Что произойдёт при попытке создать `record Triangle(double a) implements Shape {}`?

**Ваш ответ:**
Будет ошибка компиляции. Sealed-интерфейс Shape разрешает только два наследника: Circle и Square. 



---

## Задание 9.2: Цепочка лямбд

### Команды

```
import java.util.function.*
Function<String, String> trim = String::trim
Function<String, String> upper = String::toUpperCase
Function<String, String> exclaim = s -> s + "!"
var pipeline1 = trim.andThen(upper).andThen(exclaim)
var pipeline2 = exclaim.compose(upper).compose(trim)
pipeline1.apply("  hello world  ")
pipeline2.apply("  hello world  ")
```

### Фактический вывод:

```
import java.util.function.*

trim ==> $Lambda$...
upper ==> $Lambda$...
exclaim ==> $Lambda$...

pipeline1 ==> java.util.function.Function$$Lambda$...
pipeline2 ==> java.util.function.Function$$Lambda$...

pipeline1.apply("  hello world  ")
$7 ==> "HELLO WORLD!"

pipeline2.apply("  hello world  ")
$8 ==> "HELLO WORLD!"
```

### Вопрос: Дают ли `andThen()` и `compose()` одинаковый результат? В каком случае результаты будут различаться?

**Ваш ответ:**
В данном случае результат совпадает, но результат различался бы, если бы выводили функции в одинаковом порядке.


---

## Задание 9.3: Сравнение EnumSet и HashSet

### Команды

```
enum Color { RED, GREEN, BLUE, YELLOW, CYAN, MAGENTA, WHITE, BLACK }
var enumSet = java.util.EnumSet.of(Color.RED, Color.GREEN, Color.BLUE)
var hashSet = new java.util.HashSet<>(java.util.Set.of(Color.RED, Color.GREEN, Color.BLUE))
enumSet.contains(Color.RED)
hashSet.contains(Color.RED)
enumSet.getClass().getSimpleName()
hashSet.getClass().getSimpleName()
```

### Фактический вывод:

```
enum Color { RED, GREEN, BLUE, YELLOW, CYAN, MAGENTA, WHITE, BLACK }
|  created enum Color

var enumSet = java.util.EnumSet.of(Color.RED, Color.GREEN, Color.BLUE)
enumSet ==> [RED, GREEN, BLUE]

var hashSet = new java.util.HashSet<>(java.util.Set.of(Color.RED, Color.GREEN, Color.BLUE))
hashSet ==> [RED, GREEN, BLUE]

enumSet.contains(Color.RED)
$6 ==> true

hashSet.contains(Color.RED)
$7 ==> true

enumSet.getClass().getSimpleName()
$8 ==> "RegularEnumSet"

hashSet.getClass().getSimpleName()
$9 ==> "HashSet"
```

### Вопрос: Почему внутренний класс EnumSet называется `RegularEnumSet`? Что произойдёт, если enum будет иметь больше 64 констант?

**Ваш ответ:**
`RegularEnumSet` работает быстро для маленьких enum (до 64 значений). Если enum большой (больше 64), Java сама использует другой тип — `JumboEnumSet`.
