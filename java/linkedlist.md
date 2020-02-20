- [Назад](/./java.md)

## Реализация LinkedList

LinkedList - двунаправленный список. Каждый элемент содержит указатели на предыдущий и последующий элемент. Интератор поддерживает обход в обе стороны.

### Внутренности

Внутри имеет статический внутренний класс `Node` - элемент списка.

```java
    private static class Node<E> {
        E item;
        LinkedList.Node<E> next;
        LinkedList.Node<E> prev;

        Node(LinkedList.Node<E> prev, E element, LinkedList.Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```

Объект `LinkedList` имеет свойства `first`, `last` (оба типа `Node`) и `size`.

### Добавление элемента в конец

```java
void linkLast(E e) {
    LinkedList.Node<E> l = this.last;
    LinkedList.Node<E> newNode = new LinkedList.Node(l, e, (LinkedList.Node)null);
    this.last = newNode;
    if (l == null) {
        this.first = newNode;
    } else {
        l.next = newNode;
    }

    ++this.size;
}
```

### Добавление элемента в середину

```java
void linkBefore(E e, LinkedList.Node<E> succ) {
    LinkedList.Node<E> pred = succ.prev;
    LinkedList.Node<E> newNode = new LinkedList.Node(pred, e, succ);
    succ.prev = newNode;
    if (pred == null) {
        this.first = newNode;
    } else {
        pred.next = newNode;
    }

    ++this.size;
}
```

### Удаление элемента

Тут все ясно, удаляем, переделываем ссылк `first`, `last` в соседних. В удаляемом элементе зануляем поле `item`.


### Заключение

1. На вставку, удаление элемента время линейное.
1. Можно сделать стэк, очередь.
1. Не синхронизирован.
