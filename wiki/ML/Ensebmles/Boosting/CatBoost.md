---
created:
  - " 13-11-2023 19:16"
aliases: 
tags:
  - article/
---

# CatBoost
- дерево строится по уровням
- Для всех вершин одного уровня предикат одинаковый (одинаковый сплит)


Поэтому:
- меньше памяти
- выглядит как регуляризация
- ускоряет обучение


## Выбор сплита


Квантизация
![[Pasted image 20231113200845.png]]

и теперь делаем с новыми значениями


### Subtract split

![[Pasted image 20231113201314.png]]

Гистограмку получаем в другом узле вычитанием текущий и квантизованной

## Выбор в листьях
Средний антиградиент:
![[Pasted image 20231113201527.png]]

![[Pasted image 20231113202440.png]]