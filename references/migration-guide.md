# Руководство по миграции с Strapi 4 на Strapi 5

## Ключевые изменения

### 1. Document Service API (Новое в Strapi 5)

**Strapi 4:**
```typescript
await strapi.entityService.findMany('api::article.article', {
  filters: { status: 'published' }
});
```

**Strapi 5:**
```typescript
await strapi.documents('api::article.article').findMany({
  filters: { status: 'published' }
});
```

### 2. Document ID вместо ID

**Strapi 4:**
```typescript
const article = await strapi.entityService.findOne('api::article.article', id);
```

**Strapi 5:**
```typescript
const article = await strapi.documents('api::article.article').findOne({
  documentId: 'abc123' // documentId instead of numeric id
});
```

### 3. Структура событий Lifecycle

**Strapi 4:**
```typescript
export default {
  async beforeCreate(event) {
    const { data } = event.params;
  }
};
```

**Strapi 5:** (Same structure, but event.result uses documentId)
```typescript
export default {
  async afterCreate(event) {
    const { result } = event;
    console.log(result.documentId); // Use documentId
  }
};
```

### 4. Методы контроллеров

**Strapi 4:**
```typescript
async find(ctx) {
  const entities = await strapi.entityService.findMany('api::article.article', ctx.query);
  return entities;
}
```

**Strapi 5:**
```typescript
async find(ctx) {
  await this.validateQuery(ctx);
  const sanitizedQuery = await this.sanitizeQuery(ctx);

  const documents = await strapi.documents('api::article.article').findMany(sanitizedQuery);

  const sanitizedResults = await this.sanitizeOutput(documents, ctx);
  return this.transformResponse(sanitizedResults);
}
```

### 5. Публикация/Снятие с публикации

**Strapi 4:**
```typescript
await strapi.entityService.update('api::article.article', id, {
  data: { publishedAt: new Date() }
});
```

**Strapi 5:**
```typescript
await strapi.documents('api::article.article').publish({
  documentId: 'abc123'
});
```

## Чеклист миграции

### Контроллеры

- [ ] Replace `strapi.entityService` with `strapi.documents()`
- [ ] Add `validateQuery()` before queries
- [ ] Add `sanitizeQuery()` for input
- [ ] Add `sanitizeOutput()` for results
- [ ] Use `transformResponse()` for final output
- [ ] Update `id` to `documentId` in parameters

### Сервисы

- [ ] Replace `strapi.entityService` with `strapi.documents()`
- [ ] Update method signatures to use `documentId`
- [ ] Update return types if needed

### Lifecycles

- [ ] Update references from `result.id` to `result.documentId`
- [ ] Test all lifecycle hooks
- [ ] Verify data transformations still work

### Роуты

- [ ] No changes needed (routes remain the same)

### Схемы

- [ ] No changes needed (schema format is the same)

## Общие паттерны

### Поиск по Slug

**Strapi 4:**
```typescript
const articles = await strapi.entityService.findMany('api::article.article', {
  filters: { slug: 'my-article' }
});
return articles[0];
```

**Strapi 5:**
```typescript
const article = await strapi.documents('api::article.article').findFirst({
  filters: { slug: 'my-article' }
});
return article;
```

### Создание со связями

**Strapi 4:**
```typescript
await strapi.entityService.create('api::article.article', {
  data: {
    title: 'Title',
    author: 1 // ID
  }
});
```

**Strapi 5:**
```typescript
await strapi.documents('api::article.article').create({
  data: {
    title: 'Title',
    author: 1 // Still works
  }
});
```

### Обновление

**Strapi 4:**
```typescript
await strapi.entityService.update('api::article.article', id, {
  data: { title: 'New Title' }
});
```

**Strapi 5:**
```typescript
await strapi.documents('api::article.article').update({
  documentId: 'abc123',
  data: { title: 'New Title' }
});
```

### Удаление

**Strapi 4:**
```typescript
await strapi.entityService.delete('api::article.article', id);
```

**Strapi 5:**
```typescript
await strapi.documents('api::article.article').delete({
  documentId: 'abc123'
});
```

## Breaking Changes

### 1. Больше нет числовых ID в API

Strapi 5 uses string-based `documentId` instead of numeric `id`.

**Влияние:** Обнови весь код, который ссылается на `id`, чтобы использовать `documentId`.

### 2. Обязательная санитизация

Controllers must use sanitize methods for security.

**Влияние:** Добавь `sanitizeQuery()`, `sanitizeInput()` и `sanitizeOutput()` во все кастомные контроллеры.

### 3. Entity Service устарел

`strapi.entityService` still works but is deprecated.

**Влияние:** Мигрируй на `strapi.documents()` для совместимости с будущими версиями.

## Тестирование после миграции

1. **CRUD операции**
   - Создай новые документы
   - Прочитай документы по documentId
   - Обнови существующие документы
   - Удали документы

2. **Связи**
   - Протестируй все типы связей
   - Проверь работу populate
   - Проверь вложенные связи

3. **Lifecycles**
   - Убедись, что все хуки срабатывают корректно
   - Проверь трансформации данных
   - Протестируй каскадные обновления

4. **Кастомные endpoints**
   - Протестируй все кастомные роуты
   - Проверь аутентификацию
   - Проверь права доступа

5. **Фильтры и запросы**
   - Протестируй сложные фильтры
   - Проверь работу сортировки
   - Проверь пагинацию

## Ресурсы

- [Official Migration Guide](https://docs.strapi.io/dev-docs/migration/v4-to-v5)
- [Document Service API Docs](https://docs.strapi.io/dev-docs/api/document-service)
- [Breaking Changes List](https://docs.strapi.io/dev-docs/migration/v4-to-v5/breaking-changes)
