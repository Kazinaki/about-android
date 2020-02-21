- [Назад](/./java.md)

## PECS

PECS - правило по которому надо делать ограниченные подстановочные типы.
т.е. когда писать:
```java
<? super T>
```
а когда:
```java
<? extends T>
```

Расшифровывается: **P**roducer - **E**xtend, **C**omsumer - **S**uper.
Если у нас что-то производит объекты на надо `extends`, если поглощает то `super`.

```java
public void pushAll(Iterable<? extends E> src) {
    for (E e : src)
        push(e);
}
```

Здесь `scr` производит объекты с которыми что-то делаем, значит пишем `extends`, чтобы могли использовать и подтипы `T`.

```java
public void popAll(Collection<? super E> dst) {
    while (!isEmpty())
        dst.add(pop());
}

```

Тут `dst` поглощает объекты - используем `super`.
