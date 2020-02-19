- [Назад](/./android.md)

## View

Жизненный цикл View:

![](/./img/view01.png)

Методы жизненного цикла:

### onAttachedToWindow()

Выполняется после того как родительский View вызовет `addView(view)`, этот view будет присоединен к окну. На этой стадии этот вью уже знает о всех вью расположенных в родительском, можно их найти по идентификатору и сохранить, например, если надо взаимодействовать с ними.

### onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int)

Вью определяет свой размер. В приходящих параметрах зашифровано значение и режим. Достать из можно так:
```java
override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
    val width = MeasureSpec.getSize(widthMeasureSpec)
    val widthMode = MeasureSpec.getMode(widthMeasureSpec)
    // ...
}

```
Режим может быть:
- **MeasureSpec.EXACTLY** - размеры четко заданы, лучше их не менять.
- **MeasureSpec.AT_MOST** - размеры заданы по размеру родителя, больше их делать не стоит.
- **MeasureSpec.UNSPECIFIED** - размеры не определены.

Если требуется изменить размер вью, то надо вызвать `setMeasuredDimension(int width, int height)`.

### onLayout(changed: Boolean, left: Int, top: Int, right: Int, bottom: Int)

Позволяет присваивать размер и позицию дочерних вью.

### onDraw(canvas: Canvas?)

Тут уже происходит отрисовка. Объект `canvas` отвечает за рисование фигур. Для их раскраски надо создать объект `Paint`. Есть нюанс, тут создавать объект `Paint` не надо, т.к. это затратно, а `onDraw` может вызываться часто. Лучше создать `Paint`, например, в конструкторе и сохранить в приватной переменной.

### invalidate()

Вызываем, когда надо просто перерисовать вью не меняя размер.

### requestLayout()

Вызываем, когда надо изменить размер вью и перерисовать.
