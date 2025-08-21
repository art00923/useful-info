# Шпаргалка по JavaScript DOM

## Что такое DOM?

**DOM** (Document Object Model) — это программный интерфейс для веб-документов. Он представляет страницу так, что программы могут изменять структуру, стиль и содержимое документа. Когда браузер загружает веб-страницу, он создает модель документа в памяти. DOM представляет этот документ в виде дерева.

Каждый элемент, атрибут и текстовый узел на HTML-странице является **узлом** (node) в этом дереве. Например, тег `<body>` является узлом, тег `<p>` внутри него — дочерним узлом, а текст внутри `<p>` — еще одним дочерним узлом.

JavaScript может взаимодействовать с этим деревом: находить узлы, изменять их, создавать новые и удалять существующие. Это и называется **управлением DOM**. Эта шпаргалка поможет вам разобраться с основными методами и свойствами для этого.

## Поиск и выбор элементов

Это первый шаг в управлении DOM. Прежде чем что-то изменить, нужно найти нужный элемент.

### `document.getElementById(id)`

Самый быстрый способ найти элемент. Он ищет элемент с уникальным `id`.

- **Возвращает**: один элемент или `null`, если элемент не найден.

```html
<div id="main">Привет, мир!</div>
```

```javascript
const mainDiv = document.getElementById('main');
console.log(mainDiv.textContent); // "Привет, мир!"
```

### `document.getElementsByTagName(tagName)`

Ищет все элементы с указанным тегом.

- **Возвращает**: "живую" HTML-коллекцию элементов. Это означает, что если вы добавите на страницу новый элемент с таким тегом, коллекция автоматически обновится.

```html
<p>Первый параграф.</p>
<p>Второй параграф.</p>
```

```javascript
const paragraphs = document.getElementsByTagName('p');
for (let p of paragraphs) {
  p.style.color = 'blue';
}
```

### `document.getElementsByClassName(className)`

Ищет все элементы с указанным классом.

- **Возвращает**: "живую" HTML-коллекцию элементов.

```html
<div class="item">Элемент 1</div>
<span class="item">Элемент 2</span>
```

```javascript
const items = document.getElementsByClassName('item');
console.log(items.length); // 2
```

### `document.querySelector(cssSelector)`

Универсальный и мощный метод. Ищет **первый** элемент, соответствующий указанному CSS-селектору. Можно использовать любые селекторы: по тегу, классу, id, атрибутам и их комбинациям.

- **Возвращает**: первый найденный элемент или `null`.

```html
<div class="container">
  <p class="text">Какой-то текст.</p>
  <p class="text">Еще текст.</p>
</div>
```

```javascript
// Найдет первый элемент с классом "text" внутри элемента с классом "container"
const firstText = document.querySelector('.container .text');
console.log(firstText.textContent); // "Какой-то текст."
```

### `document.querySelectorAll(cssSelector)`

Похож на `querySelector`, но находит **все** элементы, соответствующие CSS-селектору.

- **Возвращает**: статическую (не "живую") коллекцию `NodeList`. Это значит, что если после вызова этого метода на странице появятся новые подходящие элементы, в коллекции они не появятся.

```html
<ul>
  <li class="list-item">Один</li>
  <li class="list-item">Два</li>
  <li>Три</li>
</ul>
```

```javascript
const listItems = document.querySelectorAll('.list-item');
listItems.forEach(item => {
  item.style.fontWeight = 'bold';
});
// Только "Один" и "Два" станут жирными
```

## Перемещение по DOM-дереву (Навигация)

После того как вы выбрали один элемент, вы можете перемещаться от него к другим — родительским, дочерним или соседним.

Для навигации существуют две группы свойств:
- **`...Child` и `...Sibling`**: работают со всеми **узлами** (nodes), включая текстовые узлы (пробелы, переносы строк) и комментарии.
- **`...ElementChild` и `...ElementSibling`**: работают только с **узлами-элементами** (тегами). Это почти всегда то, что вам нужно, поэтому лучше использовать их.

Представим себе такую структуру:
```html
<div class="parent">
  <!-- комментарий -->
  <p class="child-1">Первый дочерний элемент</p>
  <span class="child-2">Второй дочерний элемент</span>
</div>
```

### `element.parentElement`
Позволяет получить родительский элемент.

```javascript
const child2 = document.querySelector('.child-2');
const parent = child2.parentElement;
console.log(parent.className); // "parent"
```

### `element.children`
Возвращает "живую" коллекцию дочерних элементов.

```javascript
const parent = document.querySelector('.parent');
console.log(parent.children.length); // 2
console.log(parent.children[0].className); // "child-1"
```

### `element.firstElementChild` и `element.lastElementChild`
Возвращают первый и последний дочерний элемент соответственно.

```javascript
const parent = document.querySelector('.parent');
console.log(parent.firstElementChild.className);  // "child-1"
console.log(parent.lastElementChild.className);   // "child-2"
```

### `element.previousElementSibling` и `element.nextElementSibling`
Возвращают предыдущий и следующий соседний элемент.

```javascript
const child1 = document.querySelector('.child-1');
console.log(child1.nextElementSibling.className); // "child-2"

const child2 = document.querySelector('.child-2');
console.log(child2.previousElementSibling.className); // "child-1"
```

## Создание, вставка и удаление элементов

JavaScript позволяет не только находить, но и создавать новые элементы, а также изменять их положение на странице.

### `document.createElement(tagName)`
Создает новый элемент с указанным тегом, но **не добавляет** его на страницу.

```javascript
// Просто создает <li> в памяти
const newLi = document.createElement('li');
newLi.textContent = 'Новый элемент';
```

### Вставка элементов
После создания элемент нужно вставить в DOM. Для этого существуют удобные современные методы.

- `element.append(item1, item2, ...)` — вставляет в конец `element`.
- `element.prepend(item1, item2, ...)` — вставляет в начало `element`.
- `element.before(item1, item2, ...)` — вставляет перед `element`.
- `element.after(item1, item2, ...)` — вставляет после `element`.

Во все эти методы можно передавать как созданные элементы, так и просто строки.

```html
<ul id="list">
  <li>Пункт 1</li>
</ul>
```

```javascript
const list = document.getElementById('list');
const newLi = document.createElement('li');
newLi.textContent = 'Пункт 2';

// Вставим новый элемент в конец списка
list.append(newLi);

// Вставим еще один элемент и текст в начало
const firstLi = document.createElement('li');
firstLi.textContent = 'Пункт 0';
list.prepend(firstLi, 'Начало списка:');
```

### `element.remove()`
Удаляет элемент со страницы.

```html
<p id="to-delete">Этот текст нужно удалить.</p>
```

```javascript
const p = document.getElementById('to-delete');
p.remove();
```

### `element.cloneNode(deep)`
Клонирует (создает копию) элемента.
- `cloneNode(false)` (по умолчанию) — поверхностное копирование, без дочерних элементов.
- `cloneNode(true)` — глубокое копирование, со всем содержимым.

```html
<div class="block">
  <p>Текст</p>
</div>
```

```javascript
const block = document.querySelector('.block');
const deepClone = block.cloneNode(true); // Клонируем вместе с <p>
const shallowClone = block.cloneNode(false); // Клонируем только сам <div>

console.log(deepClone.innerHTML);   // "<p>Текст</p>"
console.log(shallowClone.innerHTML); // ""
```

## Изменение содержимого и атрибутов

После нахождения элемента, чаще всего, требуется изменить его содержимое, классы или атрибуты.

### `element.innerHTML` vs `element.textContent`

Оба свойства позволяют читать и изменять содержимое элемента, но делают это по-разному.

- **`innerHTML`**: Возвращает и устанавливает содержимое элемента в виде HTML-строки. **Небезопасно** использовать со строками, полученными от пользователя, так как это может привести к XSS-атакам.
- **`textContent`**: Возвращает и устанавливает только текстовое содержимое, игнорируя все HTML-теги. Это **безопасный** способ работы с текстом.

```html
<div id="example">
  <p>Это <strong>важный</strong> текст.</p>
</div>
```

```javascript
const exampleDiv = document.getElementById('example');

// Чтение
console.log(exampleDiv.innerHTML);   // "  <p>Это <strong>важный</strong> текст.</p>"
console.log(exampleDiv.textContent); // "  Это важный текст."

// Запись
const newContent = 'Новый <span>простой</span> текст.';
// exampleDiv.innerHTML = newContent;   // Вставит HTML
// exampleDiv.textContent = newContent; // Вставит текст "Новый <span>простой</span> текст."
```

### Работа с атрибутами

Для управления атрибутами (`id`, `href`, `src`, `alt` и др.) существуют специальные методы.

- `element.getAttribute(name)` — получает значение атрибута.
- `element.setAttribute(name, value)` — устанавливает или изменяет значение атрибута.
- `element.hasAttribute(name)` — проверяет наличие атрибута, возвращает `true` или `false`.
- `element.removeAttribute(name)` — удаляет атрибут.

```html
<a id="link" href="/home">Старая ссылка</a>
```

```javascript
const link = document.getElementById('link');

console.log(link.getAttribute('href')); // "/home"
link.setAttribute('href', '/new-page');
console.log(link.hasAttribute('target')); // false
link.removeAttribute('id');
```

### Работа с классами: `element.classList`

`classList` — это специальный объект для удобной работы с классами элемента. Он гораздо удобнее, чем прямое изменение `element.className`.

- `element.classList.add('className')` — добавляет класс.
- `element.classList.remove('className')` — удаляет класс.
- `element.classList.toggle('className')` — добавляет класс, если его нет, и удаляет, если есть.
- `element.classList.contains('className')` — проверяет наличие класса, возвращает `true` или `false`.

```html
<div id="profile" class="user active"></div>
```

```javascript
const profile = document.getElementById('profile');

profile.classList.add('online');      // Добавит класс 'online'
profile.classList.remove('active');   // Удалит класс 'active'
profile.classList.toggle('visible');  // Добавит класс 'visible'
profile.classList.toggle('visible');  // Удалит класс 'visible'

console.log(profile.classList.contains('user')); // true
```

## Работа со стилями

С помощью JavaScript можно динамически изменять CSS-стили элементов.

### `element.style`

Свойство `style` дает доступ к **инлайн-стилям** элемента (тем, что находятся в атрибуте `style="..."`). Через него можно легко изменять стили.

- CSS-свойства, содержащие дефис (например, `font-size`), преобразуются в camelCase (`fontSize`).
- `element.style` читает только инлайн-стили, он "не знает" о стилях из CSS-файлов.

```html
<div id="box"></div>
```
```css
/* style.css */
#box {
  width: 100px;
  background-color: blue; /* Этот стиль JS не увидит через .style */
}
```
```javascript
const box = document.getElementById('box');

// Запись стилей
box.style.width = '200px';
box.style.height = '100px';
box.style.backgroundColor = 'red';
box.style.border = '2px solid black';

// Чтение
console.log(box.style.backgroundColor); // "red"
// Не сработает, т.к. background-color был задан в CSS-файле, а не в style=""
console.log(box.style.color); // ""
```
**Важно**: `element.style.cssText` позволяет установить всю строку инлайн-стилей за раз:
`box.style.cssText = 'width: 200px; height: 100px; background-color: red;';`

### `getComputedStyle(element)`

Чтобы получить **итоговый** стиль элемента (с учетом всех CSS-правил, классов и инлайн-стилей), используется функция `getComputedStyle`.

- Она возвращает объект только для чтения. Изменить стили через него нельзя.

```javascript
const box = document.getElementById('box');
const styles = getComputedStyle(box);

console.log(styles.width);          // "100px" (из CSS)
console.log(styles.backgroundColor); // "blue" (из CSS)
```

## События

JavaScript позволяет "слушать" действия пользователя на странице, такие как клики, нажатия клавиш, движения мыши и т.д. Это называется **событиями**.

### `addEventListener` и `removeEventListener`

Это основной способ назначать и удалять обработчики событий.

- `element.addEventListener(eventName, handler)`: Назначает функцию `handler`, которая будет вызвана, когда на `element` произойдет событие `eventName`.
- `element.removeEventListener(eventName, handler)`: Удаляет ранее назначенный обработчик. Важно: для этого функция-обработчик должна быть объявлена отдельно, а не быть анонимной.

**Основные события**: `click`, `mouseover`, `mouseout`, `keydown`, `keyup`, `submit`, `focus`, `blur`.

```html
<button id="my-button">Нажми меня</button>
<div id="log"></div>
```
```javascript
const button = document.getElementById('my-button');
const log = document.getElementById('log');

function onButtonClick() {
  log.textContent += 'Кнопка была нажата! ';
  // Удалим обработчик после первого нажатия
  button.removeEventListener('click', onButtonClick);
}

button.addEventListener('click', onButtonClick);
```

### Объект `event`

Когда происходит событие, в функцию-обработчик автоматически передается специальный объект — `event`. Он содержит детали о произошедшем событии.

- `event.target`: Самый глубоко вложенный элемент, на котором произошло событие.
- `event.preventDefault()`: Отменяет действие браузера по умолчанию. Например, для события `submit` на форме это предотвратит перезагрузку страницы.
- `event.stopPropagation()`: Останавливает "всплытие" события — его дальнейшую передачу родительским элементам.

```html
<form id="my-form" action="/submit">
  <a href="https://example.com" id="my-link">Перейти по ссылке</a>
</form>
```
```javascript
const form = document.getElementById('my-form');
const link = document.getElementById('my-link');

// Предотвращаем отправку формы
form.addEventListener('submit', function(event) {
  event.preventDefault();
  console.log('Отправка формы отменена.');
});

// Предотвращаем переход по ссылке
link.addEventListener('click', function(event) {
  event.preventDefault();
  console.log('Переход по ссылке отменен.');
  console.log('Цель события (event.target):', event.target.tagName); // A
});
```

---

## Сводная таблица основных методов

| Метод/Свойство | Краткое описание |
| :--- | :--- |
| **Поиск** | |
| `getElementById('id')` | Найти элемент по уникальному `id`. |
| `querySelector('selector')` | Найти первый элемент по CSS-селектору. |
| `querySelectorAll('selector')` | Найти все элементы по CSS-селектору. |
| **Навигация** | |
| `element.children` | Получить коллекцию дочерних элементов. |
| `element.parentElement` | Получить родительский элемент. |
| `element.nextElementSibling` | Получить следующий соседний элемент. |
| **Манипуляция** | |
| `createElement('tag')` | Создать новый элемент (в памяти). |
| `element.append(item)` | Вставить узел или строку в конец элемента. |
| `element.remove()` | Удалить элемент со страницы. |
| `element.cloneNode(true)`| Создать "глубокую" копию элемента. |
| **Содержимое и Атрибуты** | |
| `element.textContent` | Изменить/прочитать текстовое содержимое (безопасно). |
| `element.innerHTML` | Изменить/прочитать HTML-содержимое (опасно с данными от пользователя). |
| `element.setAttribute('attr', 'val')` | Установить или изменить значение атрибута. |
| `element.classList.add('class')` | Добавить CSS-класс элементу. |
| **Стили** | |
| `element.style.property` | Изменить инлайн-стиль (в атрибуте `style`). |
| `getComputedStyle(element)` | Прочитать итоговый CSS-стиль элемента (только чтение). |
| **События** | |
| `addEventListener('event', handler)` | Назначить обработчик события. |
| `removeEventListener('event', handler)` | Снять ранее назначенный обработчик. |
| `event.preventDefault()` | Отменить стандартное действие браузера. |
