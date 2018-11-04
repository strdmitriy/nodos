# Роутинг в Nodos

Это руководство охватывает открытые для пользователя функции роутинга Nodos.

После прочтения этого руководства вы узнаете:

1. Как интерпретировать код в package/routes.js
2. Как создавать свои собственные маршруты, используя встроенные команды
3. Как определять параметры маршрута, которые передаются в обработчик события
4. Как автоматически создавать пути и URL, используя маршрутные хелперы

## 1. Цель роутера Nodos
Роутер Nodos анализируют HTTP медоты (GET, POST, PUT, DELETE), а так же распознает URL и сопоставляет его с определенным методом обработки для передачи управления.

### 1.1. Соединение URL с кодом
Когда ваше приложение на Nodos получает входящий запрос, например:
```
GET /articles/17
```
Тут начинаются у меня проблемы с пониманием.
посмотрел примеры в папке example, немного теряюсь в определениях. Я так понял что у нас есть 6 методов обработки входящих запросов (index, build, edit, show, create, destroy). В доке пишут что “приходит запрос GET /patients/17, он опрашивает роутер на предмет соответствия экшну(это наш обработчик) контроллера(что из себя представляет контроллер?)“. Пришел запрос, мы определили какой экшен будет его обрабатывать и дальше мы куда направляемся, в темплейт где разметка?

## 1.2 Создание URL из кода
Не могу описать, т.к не разобрался, что из себя представлет контроллер в нодос(я так понял его нет), но куда тогда нас должен перекинуть экшен?
## 1.3 Настройка маршрутизатора Nodos
Маршруты для приложения располагаются в файле package/routes.js и обычно выглядят так:
```
const routesDefaultOnly = ['index', 'build', 'create', 'show', 'edit', 'update', 'destroy'];
const routesDefaultExcept = [];

const buildHandlerNames = (routeItem) => {
  const onlyNames = _.get(routeItem, 'only', routesDefaultOnly);
  const exceptNames = _.get(routeItem, 'except', routesDefaultExcept);
  return _.difference(onlyNames, exceptNames);
};

```
Поскольку это обычный исходный файл Nodos, можно использовать все его особенности, чтобы помочь определять маршруты, но необходимо быть осторожным с именами переменных, так как они могут конфликтовать с методами DSL маршрутизатора.

# 2. Ресурсный роутинг
Ресурсный роутинг позволяет быстро объявлять все общие маршруты для заданного обработчика страницы. Вместо объявления отдельных маршрутов для экшнов index, show, new, edit, create, update и destroy, ресурсный маршрут объявляет их одной строчкой кода.
## 2.1. Ресурсы в вебе

Браузеры запрашивают страницы от Nodos, выполняя запрос по URL, используя определенный метод HTTP, такой как GET, POST, PATCH, PUT и DELETE. Каждый метод - это запрос на выполнение операции с ресурсом. Ресурсный маршрут соединяет несколько родственных запросов с экшнами в одном файле.

Когда приложение на Nodos получает входящий запрос для:
```
DELETE /photos/17
```
оно просит роутер соединить его с экшном контроллера. Если первый соответствующий маршрут такой:
```
resources :photos
```
 Nodos будет направлять этот запрос в экшн destroy файла photos с { id: '17' } в params.