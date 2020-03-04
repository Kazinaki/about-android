- [Назад](/./android.md)

## Handler

### Основные определения

1. android.os.**Looper** - диспетчер сообщений, связанный только с одним потоком-потребителем.
1. android.os.**Handler** - обработчик сообщений для потока-потербителя и соответствующий интерфейс для потока-производителя, позволяющий помещать сообщения в очередь. Объект типа **Looper** может иметь несколько связанных с ним обработчиков, но все они помещают сообщения в одну и ту же очередь.
1 android.os.**MessageQueue** - неограниченный по размеру связанный список сообщений, который должен обрабатываться в потоке-потребителе. Каждый объект типа **Looper* (и **Thread**) имеет не более одной очереди.
1. android.os.**Message** - сообщение, которое должен обработать поток-потребитель.

### Механизм передачи-приема сообщений

**Handler** позволяет отправлять и обрабатывать сообщения (**Message**) и объекты **Runnable** в связанную с потоком очередь сообщений (**MessageQueue**). Каждый объект **Handler** связан с одним потоком (у потока же может быть несколько **Handler**-ов) и очередью сообщений этого потока. Когда создается **Handler** он связывается с объектом **Looper**. Он передает сообщения и объекты **Runnable** в очередь сообщений этого **Looper**-а и обрабатывает их в потоке **Looper**-а. Сам **Looper** работает в потоке-потребителе, извлекает сообщения из очереди, для передачи их соответствующему **Handler**-у.

![](/./img/handler01.png)

Каждое сообщение содержит ссылку на следующее в очереди.

### Класс MessageQueue

Очередь сообщений представляет собой неограниченный однонаправленный список. Сообщения сортируются по отметкам времени (timestamp). Если эта отметка меньше текущего времени, оно отправляется потоку-получателю на обработку. Потоки-производители могут вставлять сообщения в любое время и на любую позицию.

### Интерфейс MessageQueue.IdleHandler

Предназначен для обработки ситуаций, когда в очереди нет сообщений готовых для отправки (их время не пришло). По умолчанию поток-потребитель просто ждет новых сообщений, но вместо этого он может использоваться для других задач. Этот интерфейс определяет слушателя, который связывается с очередью **MessageQueue**

```kotlin
MessageQueue mq = Looper.myQueue()
val idleHandler = MessageQueue.IdleHandler()
mq.addIdleHandler(idleHandler) // регистрация наблюдателя
mq.removeIdlehandler(idleHandler) // отписка наблюдателя
```

Интерфейс имеет один метод
```kotlin
interface IdleHandler {
    fun queueIdle() : Boolean
}
```
В реализации этого метода надо возвращать **Boolean**
- **true** - если слушатель после обработки остается активным и принимаем следующие вызовы.
- **false** - если слушатель после обработки считается не активным. Это равно отмене подписки.

### Класс Looper

По-умолчанию объект типа **Looper** имеется только в UI-потоке. Другие потоки должны явно создавать его. При создании **Looper** автоматически подключает очередь сообщений, с которой и работает. Связь с объектом **Looper** устанавливается в методе `run()` потока:
```kotlin
class CustomThread : Thread() {
    override fun run() {
        Looper.prepare()
        // ...
        Looper.loop()
    }
}
```
1. `Looper.prepare()` - статический метод создает **Looper**. после этого очередь готова принимать сообщения, но пока они не будут передаваться потоку-потребителю
1.  `Looper.loop()` - начало обработки сообщений в очереди. Этот метод блокирующий - он не позволяет завершиться методу `run()`.

Поток может иметь только один объект **Looper**. При попытке создать второй будет вызвано исключение.

Для завершения работы **Looper**-а используются методы `quit()` и `quitSafely()`. Первый останавливает **Looper** и удаляет все сообщения, даже те время которых пришло и они ожидают обработки. Метод `quitSafely()` удаляет только сообщения время которых не пришло.
Завершение работы объекта **Looper** не завершает работу потока, оно лишь выходит из цикла `Looper.loop()`. Но после этого не возможно ни восстановить работу старого **Looper**-а, ни создать новый.

UI-поток - единственный с которым по-умолчанию связан объект **Looper**. Он связывается с UI-потоком еще до инициализации компонентов приложения. У этого **Looper**-а есть важные отличия от других:
1. **Looper** в UI-потоке доступен везде через вызов метода `Looper.getMainLooper()`
1. Работа этого **Looper**-а не может быть завершена. Попытка вызова `Looper.quit()` вызовет исключение.
1. Среда выполения связывает **Looper** UI-потока с помощью метода `Looper.prepareMainLooper()`, в любом приложении это можно сделать только один раз. Любые попытки связать главный **Looper** с другим потоком вызовут исключение.

### Класс Handler

Собственно он и выполняет вставку сообщений в очередь и их обработку. Делает это он взаимодействуя с объектом **Looper** - напрямую он связан только с ним. Поэтому во время создания **Handler**-а он сразу связывается с **Looper**-ом. Есть два вида конструкторов:
1. Без явной ссылки на **Looper**. Связывает с **Looper**-ом текущего потока
```kotlin
Handler()
Handler(Handler.Callback)

```
Есть нюанс: если в текущем потоке нет **Looper**-а, вызовется исключение.
2. С явной ссылкой на **Looper**
```kotlin
Handler(Looper)
Handler(Looper, Handler.Callback)

```

В потоке может быть несколько **Handler**-ов. Но это само не себе, не гарантирует параллельность выполнения, т.к. очередь все равно одна и сообщения в ней выполняются последовательно.

**Методы создания сообщения**:
```kotlin
obtainMessage() : Message
obtainMessage(what: Int) : Message
obtainMessage(what: Int, obj: Object) : Message
obtainMessage(what: Int, arg1: Int, arg2: Int) : Message
obtainMessage(what: Int, arg1: Int, arg2: Int, obj: Object) : Message

```

**Отправка сообщений** в очередь возможна двумя способами:
1. передавая **Message**
```kotlin
sendEmptyMessage(int) : Boolean
sendMessage(Message) : Boolean
sendMessageAtTime(Message, long) : Boolean
sendMessageDelayed(Message, long) : Boolean
```
2. передавая объект **Runnable**
```kotlin
post(Runnable) : Boolean
postAtTime(java.lang.Runnable, long) : Boolean
postDelayed(Runnable, Object, long) : Boolean
```

**Обработка сообщений** завист от типа сообщения:
1. Сообщения с задачами - содержат только объект **Runnable**. Их обработка определена в методе `run()` переданного объекта **Runnable** - она выполняется автоматически без вызова `Handler.handleMessage()`.
2. Сообщения с данными. Поток-потребитель обрабатывает эти сообщения переопределяя метод `Handler.handleMessage(msg: Message)`

Обрабатывать сообщения с данными можно двумя способами:
1. В объекте **Handler** переопределить метод `Handler.handleMessage(msg: Message)`
1. Реализовать интерфейс `Handler.Callback` и передать эту реализацию в конструктор **Handler**-а

### Удаление сообщений из очереди

**Handler** может удалять сообщения, либо все, либо какие-то отдельные, для чего сообщения надо идентифицировать. Есть 4 идентификатора сообщений:

|Тип идентификатора | Описание | К каким сообщениям применим |
|-|-|-|
| Handler | Получатель сообщения | с задачами и с данными |
| Object | Тег сообщения | с задачами и с данными |
| Integer | Параметр сообщения **what** | с данными |
| Runnable | Задача, которую надо выполнить | с данными |

Идентификатор типа **Handler** обязателен для каждого сообщения, т.к. сообщениям известно для какого обработчика они переданы. Ни один обработчик не может удалять сообщения переданные не для него.

Методы удаления сообщений из очереди:
```kotlin
removeCallbacks(r: Runnable)
removeCallbacks(r: Runnable, token: Object)

removeMessages(what: Int)
removeMessages(what: Int, tokeh: Object)

removeCallbackAndMessages(token: Object)
```

### Отслеживание обработки очереди сообщений

**Looper**-у можно добавить объект реализующий интерфейс `Printer` для логирования обработки очереди.

```kotlin
Looper.myLooper()?.setMessageLogging(printer: Printer)

interface Printer {
    fun println(x: String?)
}
```