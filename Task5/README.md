# 1. Анализ и выявление проблемы

## Эндпоинты
- `/clients/{id}`
    - Метод: `Get`
    - Возвращает: объект `[Client]`
- `/clients/{id}/documents`
    - Метод: `Get`
    - Возвращает: список `[Document]`
- `/clients/{id}/relatives`
    - Метод: `Get`
    - Возвращает: список `[Relative]`

## Модели данных

### Client
```
**Client**
- id: <string>
- name: <string>
- age: <int32>
```

### Document
```
- id: <string>
- type: <string>
- number: <string>
- issueDate: <string>
- expiryDate: <string>
```

### Relative
```
- id: <string>
- relationType: <string>
- name: <string>
- age: <int32>
```

## Описание проблемы

Для создания одной «карточки клиента» пользователю нужно выполнить несколько REST-запросов.
Это повышает RPS и вызывает задержки.
Передать все сведения одним большим объектом невозможно из-за большого объема данных (до ~500 атрибутов).

# 2. Предлагаемая GraphQL-схема

GraphQL позволяет запрашивать только нужные поля и объединять связанные данные в одном запросе.

``` graphql
schema {
  query: Query
}

type Query {
  client(id: ID!): Client
  clients(ids: [ID!]!): [Client!]!
}

type Client {
  id: ID!
  name: String
  age: Int

  documents: [Document!]!
  relatives: [Relative!]!
}

type Document {
  id: ID!
  type: String
  number: String
  issueDate: String
  expiryDate: String
}

type Relative {
  id: ID!
  relationType: String
  name: String
  age: Int
}
```

REST подход приводил к получению данных клиента + документы + родственников, что в итоге давало:
1. 3 отдельных HTTP-запроса;
2. Больше RPS;
3. Больше сетевых задержек

Благодаря подходу GraphQL - это всё можно реализовать используя всего один запрос.