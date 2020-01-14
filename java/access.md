- [Назад](/./java.md)
## Модификаторы доступа

От большего к меньшему

| Модификатор      | Class | Package | Subclass (same pkg)| Subclass (diff pkg)| World |
| ---------------- | :----:| :-----: | :------: | :---: | :-: |
| public           | +     | +       | +        | +  	| + |
| protected        | +     | + 		 | +        | + |-     |
| package-private (без модификатора) | +     | +		 | +        | -     | -|
| private          | +     | -		 | -        | -     | -|
