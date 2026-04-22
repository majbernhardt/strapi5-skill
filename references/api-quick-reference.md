# Strapi 5 API - Быстрый справочник

## Document Service API

### Базовые операции

```typescript
// Поиск многих
await strapi.documents('api::entity.entity').findMany({
  filters: { status: 'published' },
  sort: { createdAt: 'desc' },
  pagination: { page: 1, pageSize: 10 },
  populate: { relation: true },
  fields: ['title', 'slug'],
  locale: 'ru',
  status: 'published'
});

// Поиск одного
await strapi.documents('api::entity.entity').findOne({
  documentId: 'abc123',
  populate: '*'
});

// Поиск первого
await strapi.documents('api::entity.entity').findFirst({
  filters: { slug: 'my-page' }
});

// Создание
await strapi.documents('api::entity.entity').create({
  data: { title: 'New', status: 'draft' }
});

// Обновление
await strapi.documents('api::entity.entity').update({
  documentId: 'abc123',
  data: { title: 'Updated' }
});

// Удаление
await strapi.documents('api::entity.entity').delete({
  documentId: 'abc123'
});

// Публикация/Снятие с публикации
await strapi.documents('api::entity.entity').publish({ documentId: 'abc123' });
await strapi.documents('api::entity.entity').unpublish({ documentId: 'abc123' });

// Подсчет
await strapi.documents('api::entity.entity').count({
  filters: { status: 'published' }
});
```

## Database Query API

```typescript
// Поиск одного
await strapi.db.query('api::entity.entity').findOne({
  where: { id: 1 },
  select: ['field1', 'field2'],
  populate: { relation: { select: ['id', 'name'] } }
});

// Поиск многих
await strapi.db.query('api::entity.entity').findMany({
  where: { status: 'active' },
  limit: 10,
  offset: 0
});

// Подсчет
await strapi.db.query('api::entity.entity').count({
  where: { status: 'active' }
});

// Создание
await strapi.db.query('api::entity.entity').create({
  data: { field: 'value' }
});

// Обновление
await strapi.db.query('api::entity.entity').update({
  where: { id: 1 },
  data: { field: 'value' }
});

// Удаление
await strapi.db.query('api::entity.entity').delete({
  where: { id: 1 }
});
```

## Entity Service API

```typescript
// Поиск одного
await strapi.entityService.findOne('api::entity.entity', id, {
  populate: ['relation']
});

// Поиск многих
await strapi.entityService.findMany('api::entity.entity', {
  filters: { status: 'published' },
  populate: ['relation']
});

// Создание
await strapi.entityService.create('api::entity.entity', {
  data: { field: 'value' }
});

// Обновление
await strapi.entityService.update('api::entity.entity', id, {
  data: { field: 'value' }
});

// Удаление
await strapi.entityService.delete('api::entity.entity', id);
```

## Операторы фильтрации

```typescript
filters: {
  // Равенство
  field: 'value',
  field: { $eq: 'value' },
  field: { $ne: 'value' },

  // Сравнение
  price: { $gt: 100 },
  price: { $gte: 100 },
  price: { $lt: 1000 },
  price: { $lte: 1000 },

  // Списки
  status: { $in: ['draft', 'published'] },
  status: { $notIn: ['archived'] },

  // Проверки на null
  publishedAt: { $null: true },
  publishedAt: { $notNull: true },

  // Строковые операции
  title: { $contains: 'search' },
  title: { $notContains: 'spam' },
  title: { $startsWith: 'Hello' },
  title: { $endsWith: 'World' },

  // Логические операторы
  $and: [
    { status: 'published' },
    { price: { $gt: 100 } }
  ],
  $or: [
    { status: 'draft' },
    { status: 'review' }
  ],
  $not: { status: 'archived' }
}
```

## Паттерны Populate

```typescript
// Все связи (1 уровень)
populate: '*'

// Конкретные поля
populate: ['image', 'author']

// С выбором полей
populate: {
  image: {
    fields: ['url', 'alternativeText']
  },
  author: {
    fields: ['username', 'email']
  }
}

// Глубокий populate
populate: {
  author: {
    fields: ['username'],
    populate: {
      avatar: {
        fields: ['url']
      }
    }
  }
}

// Dynamic zone
populate: {
  blocks: {
    on: {
      'blocks.hero': {
        fields: ['title', 'subtitle'],
        populate: {
          image: {
            fields: ['url', 'formats']
          }
        }
      },
      'blocks.text': {
        fields: ['content']
      }
    }
  }
}
```

## Lifecycle Hooks

```typescript
export default {
  async beforeCreate(event) {
    const { data } = event.params;
  },

  async afterCreate(event) {
    const { result } = event;
  },

  async beforeUpdate(event) {
    const { data, where } = event.params;
  },

  async afterUpdate(event) {
    const { result } = event;
  },

  async beforeDelete(event) {
    const { where } = event.params;
  },

  async afterDelete(event) {
    const { result } = event;
  },

  async beforePublish(event) {},
  async afterPublish(event) {},
  async beforeUnpublish(event) {},
  async afterUnpublish(event) {}
};
```

## Controller Methods

```typescript
export default factories.createCoreController('api::entity.entity', ({ strapi }) => ({
  async find(ctx) {
    await this.validateQuery(ctx);
    const sanitizedQuery = await this.sanitizeQuery(ctx);
    const documents = await strapi.documents('api::entity.entity').findMany(sanitizedQuery);
    const sanitizedResults = await this.sanitizeOutput(documents, ctx);
    return this.transformResponse(sanitizedResults);
  },

  async findOne(ctx) {
    const { id } = ctx.params;
    await this.validateQuery(ctx);
    const sanitizedQuery = await this.sanitizeQuery(ctx);
    const document = await strapi.documents('api::entity.entity').findOne({
      documentId: id,
      ...sanitizedQuery
    });
    const sanitizedResult = await this.sanitizeOutput(document, ctx);
    return this.transformResponse(sanitizedResult);
  },

  async create(ctx) {
    const sanitizedData = await this.sanitizeInput(ctx.request.body, ctx);
    const document = await strapi.documents('api::entity.entity').create({
      data: sanitizedData
    });
    const sanitizedResult = await this.sanitizeOutput(document, ctx);
    return this.transformResponse(sanitizedResult);
  },

  async update(ctx) {
    const { id } = ctx.params;
    const sanitizedData = await this.sanitizeInput(ctx.request.body, ctx);
    const document = await strapi.documents('api::entity.entity').update({
      documentId: id,
      data: sanitizedData
    });
    const sanitizedResult = await this.sanitizeOutput(document, ctx);
    return this.transformResponse(sanitizedResult);
  },

  async delete(ctx) {
    const { id } = ctx.params;
    const document = await strapi.documents('api::entity.entity').delete({
      documentId: id
    });
    const sanitizedResult = await this.sanitizeOutput(document, ctx);
    return this.transformResponse(sanitizedResult);
  }
}));
```
