# Вступ

Головне, що потрібно зрозуміти: це просто інструмент, а не якийсь протокол чи стандарт, якому треба слідувати і завчати.

В книжках та вікі пишуть багато, а пояснити нормально не можуть. Я спробую.

# Важливо знати

### Тип

int, float, String, Set, Map ...

### Клас

Розповідь комп'ютеру про те, як буде виглядати певний об'єкт

### Інтерфейс

Тип класу

### Реалізація

Синонім до класу, але розповідь повинна відповідати зараннє розказаному типу

### Поле

Об'єкт що належить інтерфейсу

### Метод

Функція що належить інтерфейсу

### Наслідування

Коли нова реалізація розширює попередню

### Композиція

Коли один інтерфейс вставляється як поле іншого
```
Kernel {
    ....
}

OperationSystem {
    Kernel kernel;
    int version;
    ....
}
```

# Структурний

Структурний шаблон означає, що це підхід для роботи з інтерфейсом.

## Фасад
Найпопулярніший структурний шаблон.

**Фасад об'єднує декілька інтерфейсів в один.**

```
class Music {...}
class Lyrics {...}

class Song {
    Music music;
    Lyrics lyrics;
    
    play() {
        music.start();
        lyrics.slow();
    }
}

```

## Адаптер
Або "***мінімальний фасад***".

Перетворює лише **1 інтерфейс** в інший.

## Декоратор

**Відокремлення складного методу в окремий інтерфейс**
через наслідування

> схожий шаблон - [Стратегія](#стратегія)

```
interface SomeObject {
    init();
}

class Initializable implements SomeObject {
    SomeObject parent;

    Initialize (SomeObject obj) {
        parent= obj;
    }

    init() {
        parent.init();
    }
}

class Model extends Initializable {
    Model (SomeObject obj) {
        super(obj);
    }

    init() {
        ...
        super.init();
    }
}

class Data extends Initializable {
    Data (SomeObject obj) {
        super(obj);
    }

    init() {
        ...
        super.init();
    }
}

main {
   algorithm=  Algorithm(Data(Model()));
}
```

## Композитор

**Композитор робить композицію із власного інтерфейсу**

```
interface Widget {
    data
    data
    method
    ...
    Widget child;
}
```


## Заступник

**Заступник робить композицію із реалізації власного інтерфейсу**

```
interface UriProtocol {
    String address;
    Object action(String command);
    ...
}

class Http implements UriProtocol {...}

class Https implements UriProtocol {
    UriProtocol http= Http();
    Crypt crypto= SSL();

    String get address => http.address;
    void set address(String toSet) => http.address = toSet;
    ...
    Object action(String command) => http.action(crypto.decrypt(command));
}
```

## Пристосуванець

**Композиція статичного об'єкту**

## Міст

Чиста, правильна композиція.

**Міст розділяє реалізацію певної композиції і самого інтерфейсу**

```
interface Enchanting {
    enum ENCHANTING_TYPE;
    int magnitude;
}

interface Sword {
    int get damage;
    int get value;
    String get name;
    ...
    Enchanting enchanting;
}

class Freezing implements Enchanting {
    enum ENCHANTING_TYPE= OFFENSIVE_ELEMENENTAL;
    int magnitude= 42;
}

class MagicSteelSword implements Sword {
    ...
    // Constructor
    MagicSteelSword(Enchanting enchanting)
    {
        ...
        this.enchanting = enchanting;
    }
}

main {
    Sword SwordOfFreezingBlow= MagicSteelSword(Freezing());
}
```

# Поведінки

Поведінки означає, що це шаблон для роботи з полями та методами певного класу.

## Стратегія

**Відокремлення складного методу в окремий інтерфейс**
через композицію

> схожий шаблон - [Декоратор](#декоратор)

Роблячи це ви починаєте користуватися [мост](#міст)ом, бо розділюєте реалізацію самого інтерфейсу, і нового інтерфейсу, що раніше був методом.

Було:
```
class Algorithm {
    Data
    Data
    Data
    ...
    calculate(data,data,data..);
}
```
Стало:
```
interface Calculate {
    calculate(data,data,data..);
}

class OldStrategy implements Calculate {
    calculate(data,data,data..) {...}
}

class NewStrategy implements Calculate {
    calculate(data,data,data..) {...}
}

class Algorithm {
    Calculate calc= OldStrategy();
    ...
    Data
    Data
    Data
    ...
    calculate(data,data,data..) {
        calc.calculate(data,data,data..);
    }

    makeFaster() {
        calc= NewStrategy();
    }
}

main {
    if (user.wants()) {
        algorithm.makeFaster();
    }
}
```

## Стан

**Відокремлення декількох методів в окремий інтерфейс**

Реалізація якого називається станом.

## Хранитель

**Відокремлення спільних полів декількох інтерфейсів в окремий інтерфейс**

Схожий на [відвудувача](#відвідувач), але відвідувач відокремлює методи.

## Відвідувач

**Відокремлення спільних методів декількох інтерфейсів в окремий інтерфейс**

Він реалізіується таким чином:

у вас є, скажімо 2+ різних класи із спільними методами
```
class Dog {
    ...
    toFeed();
    toWash();
}
class Cat {
    ...
    toFeed();
    toWash();
}
```
Ви використовуєте ось таку махінацію
```
interface Care {
    takeCare(Dog dog);
    takeCare(Cat cat);
}

class Dog {
    ...
    be(Care care) {
        care.takeCare(this);
    }
}

class Cat {
    ...
    be(Care care) {
        care.takeCare(this);
    }
}
```
І додаєте кожен метод реалізацією нашого нового інтерфейсу
```
Feeded implements Care {
    takeCare(Dog dog) {...}
    takeCare(Cat cat) {...}
}
Washed implements Care {
    takeCare(Dog dog) {...}
    takeCare(Cat cat) {...}
}

main {
    for (pet in pets) {
        pet.be(Feeded());
        if (pet.isDirty)
            pet.be(Washed());
    }
}
```