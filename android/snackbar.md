- [Назад](/./android.md)

## Выезжающий снакбар в любом месте

Чтобы снакбар показывался ему нужен родительский `CoordinatorLayout`. Добавим на верх еще один `CoordinatorLayout`, высота будет `wrap_content`, а цвет прозрачный:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:layout_behavior="@string/appbar_scrolling_view_behavior">

    <androidx.coordinatorlayout.widget.CoordinatorLayout
        android:id="@+id/top_snackbar_layout"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:background="@android:color/transparent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

Создаем и показываем снакбар:

```kotlin
Snackbar
    .make(binding.root, "Test", Snackbar.LENGTH_LONG)
    .setAnimationMode(ANIMATION_MODE_SLIDE)
    .show()
```

Получаем такое появление:

![Появление снакбара](/./img/snackbar-01.gif)

Появляется там где нужно, но анимация странная. Можно просто поменять анимацию на `ANIMATION_MODE_FADE` и остановиться на этом. Но нам надо чтобы снакбар выезжал сверху. Повернем в разметке `CoordinatorLayout` на 180 градусов

```xml
android:rotation="180"
```

Получаем такое появление:

![Появление снакбара](/./img/snackbar-02.gif)

Выезжает из правильно места, но содержимое перевернуто. Повернём содержимое снакбара

```kotlin
        val snackbar = Snackbar.make(binding.topSnackbarLayout, "Test Top", Snackbar.LENGTH_LONG)
        snackbar.animationMode = ANIMATION_MODE_SLIDE
        val snackBarLayout = snackbar.view as Snackbar.SnackbarLayout
        for (i in 0 until snackBarLayout.childCount) {
            val parent = snackBarLayout.getChildAt(i)
            if (parent is LinearLayout) {
                parent.setRotation(180f)
                break
            }
        }
        snackbar.show()
```

И получаем нужный результат:

![Появление снакбара](/./img/snackbar-03.gif)