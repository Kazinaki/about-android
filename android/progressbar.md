- [Назад](/./android.md)

## Градиентный прогресс бар

Делаем файл разметки `progress_bar.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<rotate xmlns:android="http://schemas.android.com/apk/res/android"
    android:pivotX="50%"
    android:pivotY="50%"
    android:toDegrees="1440">
    <shape
        android:shape="ring"
        android:thickness="4dp"
        android:useLevel="false">
        <gradient
            android:endColor="@android:color/transparent"
            android:startColor="@color/accent"
            android:type="sweep"
            android:useLevel="false" />
        <size
            android:width="36dp"
            android:height="36dp" />
    </shape>
</rotate>
```

Тут вроде все понятно: крутящийся drawable:

 `android:pivotX` и `android:pivotY` указывают центр вращения,

 `android:toDegrees="1440"` указывает на сколько градусов крутится в секунду - так регулируем скорость.

И в разметке у самого прогресс бара устанавливаем:

```xml  
    <ProgressBar
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:indeterminateDrawable="@drawable/progress_bar" />
```