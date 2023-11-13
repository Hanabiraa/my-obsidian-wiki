---
created:
  - " 13-11-2023 18:11"
aliases: 
tags:
  - article/
---

# Gradient boosting

## Recap
![[Pasted image 20231113181430.png]]
- gradient of the loss w.r.t. output of previous model


![[Pasted image 20231113181459.png]]

![[Pasted image 20231113181524.png]]
 - one-dimensional optimization

## Gradient boosting of trees
![[Pasted image 20231113182136.png]]

$t$-th iteration:
![[Pasted image 20231113182249.png]]

### Scheme:
1) Learn tree $b_t \implies$ the structure was set
2) Relearn leaves $\implies$ no need $\gamma_t$ and $b_{tk}$

![[Pasted image 20231113182548.png]]


- обучаем дерево
- забываме ответы в листьях
- получаем новые ответы в листьях
- вырастает новый лист грубо говоря

Получаем более четкие ответы в листьях


**Дополнительная точность** от целевой функции потерь (см. картинку


### Перенастройка в листьях

![[Pasted image 20231113183102.png]]

Она аналитически не решается, только итерационные методы:

![[Pasted image 20231113183118.png]]

## Пространство $\mathbf{R}^n$
При построении градиентног обустинга рассмотрим градиентный спуск в пространстве $\mathbf{R}^n$.

Соответствие обнозначений:

![[Pasted image 20231113184308.png]]

### Построение $b_t$
![[Pasted image 20231113184445.png]]
Распишем в терминаз пространства:


![[Pasted image 20231113184515.png]]

Почему задача логична? 
$s$ - предсказание предыдущей композиции, $\delta s$ - текущее то что добавляем
![[Pasted image 20231113184551.png]]

![[Pasted image 20231113184718.png]]
Задача оптимизации:

![[Pasted image 20231113184733.png]]