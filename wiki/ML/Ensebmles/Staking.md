---
created:
  - " 13-11-2023 19:19"
aliases: 
tags:
  - article/
---

# Staking

##  Общая идея

![[Pasted image 20231113192024.png]]

- ученики выдали свои ответы - предсказания моделей как новые признаки
- учитель аггрегирует и выдает финальное предсказкние (мета-модель)


## как обучать

самый простой - обучить мета-модель на той же выборке:
![[Pasted image 20231113192202.png]]
- переобучится (много информации на трейне но не на тесте)

**Правильно** - обучать базовые и мета-модель на разных выборках
+: просто для понимания и реализации
-: из-за разделения на 2 части меньше данных

Другой вариант - разделим обучающую выборук на $K$ блоков

![[Pasted image 20231113192407.png]]
- при обучении мета-модели используются юазовые модели, которые не видели этот объект при обучении

- разнообразие базовых моделей очень важно
- мета-модель необзятательно должна быть сложной


## Блендинг
> Частный случай стекинга, в которой мета-модель является линейной

Варианты весов:
![[Pasted image 20231113192735.png]]