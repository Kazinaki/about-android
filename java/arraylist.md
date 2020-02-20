- [Назад](/./java.md)

## Реализация ArrayList

### Внутренности

Внутри элементы хранятся в массиве `Object[]`, в перменной `elementData`. Так же имеется переменная `size` - текущий размер массива.

При создании, конструктором без указания размера, создается массив из десяти элементов заполненных `null`. 

### Добавление элементов в конец

При добавлении происходят следущие вещи:

1. Проверяется есть ли место в массиве для добавления.
1. Если места нет, то создается новый массив размера `(oldCapacity * 3) / 2 + 1`. В него копируются старый массив методом `System.arraycopy()` - он нативный, написан не на Java. Быстрый.
1. Вставляется новый элемент.

### Добавление элементов не в конец

Тут алгоритм такой:

1. Проверяется есть ли место в массиве для добавления.
1. Если места нет, то создается новый массив размера `(oldCapacity * 3) / 2 + 1`. В него копируются старый массив методом `System.arraycopy()`.
1. Создается новый массив, с  помощью `System.arraycopy()`, копируются элементы с вставляемого индекса и до конца, смещаясь на один.
1. Вставляется новый элемент по индексу.

Т.е. если вставляем в середину и при этом размер массива максимальный, то копирование массива происходит два раза.

### Удаление элементов по индексу

1. Используя тот же `System.arraycopy()` копируются элементы с удаляемого индекса+1 и до конца, свигаясь к началу.
1. Последний элемент присваиваем `null`
1. Уменьшаем `size--`

### Удаление элементов по значению

Все тоже самое, но сначала находим индекс элемента по его значению, перебирая элементы с начала.

### Заключение

1. Быстрый доступ к элементу по индексу
1. Доступ к элементу по значению за линейное время.
1. Медленная вставка и удаление элементов из середины.
1. Не синхронизирован