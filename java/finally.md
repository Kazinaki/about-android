- [Назад](/./java.md)

## Выполнения блока finally

### Интересные примеры кода

  ```java
    public static void main(final String[] args) {
        System.out.println(test());
    }

    public static int test() {
        int i = 0;
        try {
            i = 2;
            return i;
        } finally {
            i = 12;
            System.out.println("Выполнение finally.");
        }
    }
  ```

Этот код выведет

>Выполнение finally.
>
>2

Это происходит потому, что при выполнении `return i;` `i` имеет значение 2. После этого выполняется блок `finally`, где выполняется `i = 12`, а затем выполняется `System.out.println`.

После выполнения блока `finally` блок `try` возвращает `2`, а не возвращает `12`, потому что этот оператор возврата не выполняется снова.

### В каких случаях блок finally не выполниться

1. Если вы вызываете `System.exit()`;
1. Если сначала произойдет сбой JVM;
1. Если JVM достигает бесконечного цикла (или другого не прерывающегося, не заканчивающегося оператора) в блоке `try` или `catch`;
1. Если ОС принудительно завершает процесс JVM; например, `kill -9` в UNIX.
1. Если хост-система умирает; например, сбой питания, аппаратная ошибка, паника ОС и т.д.
1. Если, наконец, блок будет выполняться потоком демона, а все остальные не-демонные потоки выходят до того, как, наконец, вызывается.

