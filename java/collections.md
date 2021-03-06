- [Назад](/./java.md)

## Вопросы про коллекции

### Различия между Set и List

Set - множество неупорядоченных значений, List - упорядоченный список, где каждый элемент имеет индекс, начинающийся с нуля. List могут содержать хоть сколько одинаковых значений, а Set не может. Соответственно, null-ов в списке может быть хоть сколько, в множестве только один.

### Различия между List и Map

List - это коллекция элементов, а Map - коллекция пар ключ/значение. List наследует интерфейс Colleciton, а Map не наследует никакой интерфейс. У них разные методы и представление данных.

### Различия между HashMap и HashTable

- HashTable потокобезопасна, а HashMap нет
- HashTable не может содержать элементы null, тогда как HashMap может содержать один ключ null и любое -количество значений null
- Третье ключевое отличие между ними — итератор у HashMap, в отличие от перечислителя HashTable, работает по принципу fail-fast (выдает исключение при любой несогласованности данных)

### Различия между HashMap и HashSet

HashMap — коллекция пар ключ/значение, а HashSet это упорядоченная коллекция уникальных элементов.

### Различия между ArrayList и LinkedList

- LinkedList хранит элементы в двусвязном списке, тогда как ArrayList хранит их в массиве, размер которого может динамически меняться.
- LinkedList поддерживает добавление/удаление элементов за фиксированное время, но только последовательный доступ к элементам. То есть, вы можете перебрать список с начала в конец и с конца в начало, но получение элемента в середине списка займет время пропорциональное размеру списка. ArrayList же позволяет получить любой элемент по его индексу за фиксированное время. Но добавление/удаление элементов туда требует затрат времени пропорциональных размеру, потому что нужно подвинуть все элементы с места вставки/удаления и до конца списка, либо чтобы освободить место для вставляемого либо чтобы убрать пропуск на месте удаленного.
- LinkedList требует больше памяти для хранения такого же количества элементов, потому что кроме самого элемента хранятся еще указатели на следующий и предыдущий элементы списка, тогда как в ArrayList элементы просто идут по порядку.

### Разница между fail-fast и fail-safe

Это свойства итераторов коллекций.

`fail-fast` - означает, что при переборе итератор выкинет исключение, если коллекция была изменена тут же или в другом потоке.

`fail-safe` интераторы не вызывают никаких исключений при изменении структуры, потому что они работают с клоном коллекции вместо оригинала.

Итератор коллекции CopyOnWriteArrayList и итератор представления keySet коллекции ConcurrentHashMap являются примерами итераторов fail-safe.
