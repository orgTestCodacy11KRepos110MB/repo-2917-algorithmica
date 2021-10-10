---
title: Деревья в STL
weight: -1
---

В STL конкретная реализация бинарного дерева представлена структурой `set`, поддерживающей упорядоченное множество уникальных элементов.

### Основные операции

Структуру `set<T>` можно объявить от любого типа, для которого реализован оператор сравнения — в частности, для пар и тюплов он реализован автоматически как лексикографическая сортировка.

```cpp
set<int> s;

s.insert(3); // s = {3}
s.insert(2); // s = {2, 3}
s.size();    // вернет |s| = 2

s.insert(3); // 3 не будет добавлено ещё раз, так как уже присутствует в множестве
s.size();    // |s| = 2

// присутствует ли в множестве элемент:
s.count(3);  // вернет 1
s.count(5);  // вернет 0

s.erase(3);  // s = {2}
s.insert(6); // s = {2, 6}
```

Так как `set` реализован как сбалансированное двоичное дерево поиска — а конкретно как [красно-черное дерево](https://neerc.ifmo.ru/wiki/index.php?title=%D0%9A%D1%80%D0%B0%D1%81%D0%BD%D0%BE-%D1%87%D0%B5%D1%80%D0%BD%D0%BE%D0%B5_%D0%B4%D0%B5%D1%80%D0%B5%D0%B2%D0%BE) — все операции с его элементами работают за $O(\log n)$.

### Итераторы

Также `set`, как и все контейнеры STL, поддерживает итераторы.

Начало `set` (наименьший элемент) можно получить через `.begin()`, конец — через `.end()`. Обратите внимание, что `.end()`, как и все итераторы, указывает на конец полуинтервала — то есть на несуществующий элемент, идущий после последнего.

```cpp
auto it = s.find(2); // возвращает итератор на элемент или `end`, если элемента нет
++it;                // найти следующий элемент
int x = *it;         // x = 6 

s.lower_bound(1); // вернет 2, так как это первый элемент >= 1
s.upper_bound(2); // вернет 6, так как это первый элемент > 2

auto it = s.upper_bound(10);
if (it == s.end()) {
    // аккуратно: если разыменуете it, получите undefined behavior!
}

// вывод всех элементов сета в порядке возрастания с использованием итераторов
for (auto it = s.begin(); it != s.end(); ++it)
    cout << *it << " ";

// но для таких целей лучше использовать range-based for loop
for (int x : s)
    cout << x << " ";
```

Инкремент и декремент итераторов работает за логарифмические время.

### Связанные структуры

В STL есть несколько структур со схожей функциональностью:

- `map` ассоциирует с ключами значения и позволяет получать их как из бесконечных массивов: `m[x] = y`.
- `multiset` поддерживает дубликаты элементов. `.count(x)` у него возвращает количество элементов с заданным ключом, а не просто 0 или 1. Его можно реализовать как `map`, в котором в качестве ключей хранятся количества элементов.
- `multimap` возвращает по ключу много различных значений вместо одного и позволяет итерироваться по ним (то же самое, что использовать). Его можно реализовать как `map<A, vector<B>>`.

Также у всех этих контейнеров есть аналоги, работающие на хеш-таблицах вместо деревьев: `unordered_set`, `unordered_map`, `unordered_set` и `unordered_multimap`. В них поиск, удаление и вставка в среднем работают за константное время, но нет `lower_bound` и упорядоченного итерирования.