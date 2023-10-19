### Общие сведения

https://www.npmjs.com/package/json-server

JSON Server - это библиотека, позволяющая "получить полный фейковый REST API без предварительной настройки менее чем за 30 секунд". Также имеется возможность создания полноценного сервера. Данная библиотека реализована с помощью lowdb и express. Наиболее известным примером ее использования является JSON Placeholder. Поддерживаются HTTP-запросы GET, POST, PUT, PATCH и DELETE.

CLI
--port, -p - выбор порта
--watch, -w - отслеживание файла
--routes, -r - путь к файлу с маршрутами
--middlewares, -m - путь к файлу с посредниками
--static, -s - путь к директории со статическими файлами
--delay, -d - добавление задержки к ответу в мс


### Шаг 1: Установка JSON Server
Перед тем, как начать, убедитесь, что у вас установлен Node.js и npm (Node Package Manager). Откройте командную строку или терминал. Установите JSON Server, выполнив следующую команду:

$ npm install -g json-server

### Шаг 2: Создание JSON-файла с данными
Создайте новый файл db.json и откройте его в текстовом редакторе. Вставьте ваш массив данных например в файл db.json:

// db.json
{
  "todos": [
    {
      "id": "1",
      "text": "Eat",
      "completed": true,
      "meta": {
        "author": "John",
        "createdAt": "today"
      }
    },
    {
      "id": "2",
      "text": "Code",
      "completed": true,
      "meta": {
        "author": "Jane",
        "createdAt": "yesterday"
      }
    },
    {
      "id": "3",
      "text": "Sleep",
      "completed": false
    },
    {
      "id": "4",
      "text": "Repeat",
      "completed": false
    }
  ]
}

### Шаг 3: Запуск JSON Server
Откройте командную строку или терминал. Перейдите в папку, где находится файл db.json. Запустите JSON Server с помощью следующей команды:

$ json-server --watch db.json

JSON Server будет запущен и будет следить за файлом, посмотреть это можно по адресу http://localhost:3000.

### Шаг 4: Проверка фейкового API
Теперь ваше фейковое API готово к использованию! Вы можете выполнять запросы к эндпоинтам с помощью HTTP-клиента (например, Postman или curl) или из вашего фронтенд-приложения.

### Шаг 5: Возможности фейкового API сервера

https://my-js.org/docs/cheatsheet/json-server/ - Шпаргалка по json-server

// Адрес нашего сервера
const SERVER_URL = 'http://localhost:3000/todos'

// Основная функция для получения всех задач.
// Она принимает строку запроса и адрес сервера
async function getTodos(query, endpoint = SERVER_URL) {
  try {
    // Определяем наличие строки запроса
    query ? (query = `?${query}`) : (query = '')

    const response = await fetch(`${endpoint}${query}`)

    if (!response.ok) throw new Error(response.statusText)

    const json = await response.json()
    console.log(json)
    return json
  } catch (err) {
    console.error(err.message || err)
  }
}
getTodos()

// Получение задачи по ключу
const getTodoByKey = (key, val) => getTodos(`${key}=${val}`)

// По `id`
const getTodoById = (id) => getTodoByKey('id', id)
getTodoById('2')

// По имени автора
const getTodoByAuthorName = (name) => getTodoByKey('meta.author', name)
getTodoByAuthorName('John')

// Получение активных задач
const getActiveTodos = () => getTodoByKey('complete', false)
getActiveTodos()

// Более сложный пример -
// получение задач по массиву `id`
const getTodosByIds = (ids) => {
  let query = `id=${ids.splice(0, 1)}`
  ids.forEach((id) => {
    query += `&id=${id}`
  })
  getTodos(query)
}
getTodosByIds([2, 4])

// Получение нечетных задач
const getOddTodos = async () => {
  const todos = await getTodos()
  const oddIds = todos.reduce((arr, { id }) => {
    id % 2 !== 0 && arr.push(id)
    return arr
  }, [])
  getTodosByIds(oddIds)
}
getOddTodos()

/* Пагинация */
// _page &| _limit - у нас нет страниц, так что...
const getTodosByCount = (count) => getTodos(`_limit=${count}`)
getTodosByCount(2)

/* Сортировка */
// _sort & _order - asc по умолчанию
const getSortedTodos = (field, order) =>
  getTodos(`_sort=${field}&_order=${order}`)
getSortedTodos('id', 'desc')

/* Часть */
// _start & _end | _limit
// _start не включается
const getTodosSlice = (min, max) => getTodos(`_start=${min}&_end=${max}`)
getTodosSlice(1, 3)

/* Операторы */
// Диапазон
// _gte | _lte
// _gte включается
const getTodosByRange = (key, min, max) =>
  getTodos(`${key}_gte=${min}&${key}_lte=${max}`)
getTodosByRange('id', 1, 2)

// Исключение
// _ne
const getTodosWithout = (key, val) => getTodos(`${key}_ne=${val}`)
getTodosWithout('id', '3')

// Фильтрация
// _like
const getTodosByFilter = (key, filter) => getTodos(`${key}_like=${filter}`)
getTodosByFilter('text', '^[cr]') // Code & Repeat - начинаются с 'c' & 'r', соответственно

/* Полнотекстовый поиск */
// q
const getTodosBySearch = (str) => getTodos(`q=${str}`)
getTodosBySearch('eat') // Eat & Repeat - включают 'eat'



/* Отношения */
// _embed - дочерние ресурсы
GET /posts?_embed=comments
GET /posts/1?_embed=comments

// _expand - родительские ресурсы
GET /comments?_expand=post
GET /comments/1?_expand = post

/* База данных */
GET /db

/* Домашняя страница */
GET /


