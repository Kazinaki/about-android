- [Назад](/./java.md)

## Блоки инициализации

* Блоки инициализации используются для инициализации переменных внутри класса.
* Есть два типа блоков - статический и нестатический.
* Синтаксис - просто выражение внутри скобок `{}`

Нестатический:
  ```java
    {
        //...
    }
  ```

Статический:
  ```java
    static {
        //...
    }
  ```
* Статический блок используется для установки статических переменных, нестатический - для нестатических (внезапно)

### Выполнение статических блоков инициализации

Статический блок выполняется один раз, при загрузке класса загрузчиком. Он выполняется до вызова контруктора.

При разработке языка Java был установлен постоянный порядок действий при загрузке. Во время загрузки класса порядок выглядит следующим образом:

1. Определения статических полей родительских классов.
1. Инициализация статических полей и выполнение статических блоков родительских классов.
1. Определения статических полей класса.
1. Инициализация статических полей и выполнение статических блоков класса.

Пример, создадим класс с логированием и такой же его потомок:

  ```java
    public class InitBlocksParent {
        static String parentI = "parent static field";

        static {
            System.out.println("parent static init block");
        }

        {
            System.out.println("parent non static init block");
        }

        public InitBlocksParent() {
            System.out.println("parent constructor");
        }
    }

    public class InitBlocks extends InitBlocksParent {
        static String i = "static field";

        static {
            System.out.println("static init block");
        }

        {
            System.out.println("non static init block");
        }

        public InitBlocks() {
            System.out.println("constructor");
        }
    }
  ```

Обращаемся к потомку так:

  ```java
    System.out.println(InitBlocks.i);
    InitBlocks initBlocks = new InitBlocks();
  ```

В логах:

> parent static init block
> 
> static init block
> 
> static field
> 
> parent non static init block
> 
> parent constructor
> 
> non static init block
> 
> constructor


