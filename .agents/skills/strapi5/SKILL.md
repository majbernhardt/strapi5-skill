---
name: strapi5
description: Expert Strapi 5 development - content types, controllers, services, lifecycles, components, and API customization
---

# Strapi 5 Development Skill

Ты эксперт по разработке на Strapi 5. Используй этот скилл для создания и модификации content types, контроллеров, сервисов, lifecycles, компонентов и кастомизации API.

## Версия и стек

- **Strapi**: 5.42.0
- **Node**: >=20.0.0 <=24.x.x
- **Database**: PostgreSQL (pg 8.20.0)
- **TypeScript**: полная поддержка
- **Plugins**: users-permissions, cloud, nodemailer, aws-s3, combobox, sortable-entries

## Структура проекта

```
src/
├── api/                          # API endpoints (collection types)
│   └── {entity}/
│       ├── content-types/{entity}/
│       │   └── schema.json       # Схема content type
│       ├── controllers/
│       │   └── {entity}.ts       # Контроллеры
│       ├── services/
│       │   └── {entity}.ts       # Сервисы
│       ├── routes/
│       │   └── {entity}.ts       # Роуты
│       └── lifecycles/
│           └── {entity}.ts       # Lifecycle hooks (опционально)
├── components/                   # Переиспользуемые компоненты
│   └── {category}/
│       └── {component}.json      # Схема компонента
├── admin/                        # Кастомизация админ-панели
├── extensions/                   # Расширения плагинов
└── index.ts                      # Entry point
```

## 1. Content Types (Collection Types)

### Создание нового Content Type

**Шаг 1:** Создай директорию и schema.json

```bash
mkdir -p src/api/{entity}/content-types/{entity}
```

**Шаг 2:** Создай `schema.json`

```json
{
	"kind": "collectionType",
	"collectionName": "{entities}",
	"info": {
		"singularName": "{entity}",
		"pluralName": "{entities}",
		"displayName": "Название на русском"
	},
	"options": {
		"draftAndPublish": true
	},
	"pluginOptions": {},
	"attributes": {
		"title": {
			"type": "string",
			"required": true
		},
		"slug": {
			"type": "uid",
			"targetField": "title"
		},
		"description": {
			"type": "text"
		}
	}
}
```

### Типы полей (attributes)

**Базовые типы:**

```json
{
	"string_field": { "type": "string" },
	"text_field": { "type": "text" },
	"richtext_field": { "type": "richtext" },
	"email_field": { "type": "email" },
	"integer_field": { "type": "integer" },
	"biginteger_field": { "type": "biginteger" },
	"float_field": { "type": "float" },
	"decimal_field": { "type": "decimal" },
	"boolean_field": { "type": "boolean", "default": false },
	"date_field": { "type": "date" },
	"time_field": { "type": "time" },
	"datetime_field": { "type": "datetime" },
	"timestamp_field": { "type": "timestamp" }
}
```

**UID (slug):**

```json
{
	"slug": {
		"type": "uid",
		"targetField": "title",
		"required": true
	}
}
```

**Enum:**

```json
{
	"status": {
		"type": "enumeration",
		"enum": ["draft", "published", "archived"],
		"default": "draft"
	}
}
```

**JSON:**

```json
{
	"metadata": {
		"type": "json"
	}
}
```

**Media (файлы/изображения):**

```json
{
	"image": {
		"type": "media",
		"multiple": false,
		"allowedTypes": ["images"]
	},
	"files": {
		"type": "media",
		"multiple": true,
		"allowedTypes": ["images", "files", "videos", "audios"]
	}
}
```

**Relation (связи):**

```json
{
	"author": {
		"type": "relation",
		"relation": "manyToOne",
		"target": "plugin::users-permissions.user"
	},
	"categories": {
		"type": "relation",
		"relation": "manyToMany",
		"target": "api::category.category"
	}
}
```

**Component (компонент):**

```json
{
	"buttons": {
		"type": "component",
		"component": "ui.button",
		"repeatable": true,
		"max": 2
	}
}
```

**Dynamic Zone:**

```json
{
	"page_blocks": {
		"type": "dynamiczone",
		"components": ["blocks.hero", "blocks.services", "blocks.faq"]
	}
}
```

### Опции полей

```json
{
	"field": {
		"type": "string",
		"required": true, // Обязательное поле
		"unique": true, // Уникальное значение
		"private": true, // Не возвращается в API
		"default": "value", // Значение по умолчанию
		"minLength": 3, // Мин. длина (string/text)
		"maxLength": 255, // Макс. длина (string/text)
		"min": 0, // Мин. значение (number)
		"max": 100, // Макс. значение (number)
		"regex": "^[A-Z]" // Валидация regex
	}
}
```

## 2. Components

### Создание компонента

**Шаг 1:** Создай файл компонента

```bash
mkdir -p src/components/{category}
```

**Шаг 2:** Создай `{component}.json`

```json
{
	"collectionName": "components_{category}_{component}",
	"info": {
		"displayName": "Название компонента",
		"icon": "stack",
		"description": "Описание"
	},
	"options": {},
	"attributes": {
		"type": {
			"type": "string",
			"default": "{component}",
			"unique": false
		},
		"visibility": {
			"type": "boolean",
			"default": true
		},
		"title": {
			"type": "text"
		}
	},
	"config": {}
}
```

**Пример: UI Button**

```json
{
	"collectionName": "components_ui_button",
	"info": {
		"displayName": "Кнопка",
		"icon": "cursor"
	},
	"options": {},
	"attributes": {
		"label": {
			"type": "string",
			"required": true
		},
		"url": {
			"type": "string"
		},
		"variant": {
			"type": "enumeration",
			"enum": ["primary", "secondary", "outline"],
			"default": "primary"
		},
		"icon": {
			"type": "string"
		}
	}
}
```

## 3. Controllers

### Базовый контроллер (Core Router)

```typescript
// src/api/{entity}/controllers/{entity}.ts
import { factories } from '@strapi/strapi'

export default factories.createCoreController('api::{entity}.{entity}')
```

### Кастомный контроллер

```typescript
import { factories } from '@strapi/strapi'

export default factories.createCoreController('api::{entity}.{entity}', ({ strapi }) => ({
	// GET /api/{entities}
	async find(ctx) {
		await this.validateQuery(ctx)
		const sanitizedQuery = await this.sanitizeQuery(ctx)

		const documents = await strapi.documents('api::{entity}.{entity}').findMany({
			...sanitizedQuery,
			populate: {
				image: {
					fields: ['alternativeText', 'width', 'height', 'url', 'formats'],
				},
				relation: true,
			},
		})

		const sanitizedResults = await this.sanitizeOutput(documents, ctx)
		return this.transformResponse(sanitizedResults)
	},

	// GET /api/{entities}/:id
	async findOne(ctx) {
		const { id } = ctx.params
		await this.validateQuery(ctx)
		const sanitizedQuery = await this.sanitizeQuery(ctx)

		const document = await strapi.documents('api::{entity}.{entity}').findOne({
			documentId: id,
			...sanitizedQuery,
			populate: '*',
		})

		const sanitizedResult = await this.sanitizeOutput(document, ctx)
		return this.transformResponse(sanitizedResult)
	},

	// POST /api/{entities}
	async create(ctx) {
		const sanitizedData = await this.sanitizeInput(ctx.request.body, ctx)

		const document = await strapi.documents('api::{entity}.{entity}').create({
			data: sanitizedData,
		})

		const sanitizedResult = await this.sanitizeOutput(document, ctx)
		return this.transformResponse(sanitizedResult)
	},

	// PUT /api/{entities}/:id
	async update(ctx) {
		const { id } = ctx.params
		const sanitizedData = await this.sanitizeInput(ctx.request.body, ctx)

		const document = await strapi.documents('api::{entity}.{entity}').update({
			documentId: id,
			data: sanitizedData,
		})

		const sanitizedResult = await this.sanitizeOutput(document, ctx)
		return this.transformResponse(sanitizedResult)
	},

	// DELETE /api/{entities}/:id
	async delete(ctx) {
		const { id } = ctx.params

		const document = await strapi.documents('api::{entity}.{entity}').delete({
			documentId: id,
		})

		const sanitizedResult = await this.sanitizeOutput(document, ctx)
		return this.transformResponse(sanitizedResult)
	},
}))
```

### Фильтрация данных в контроллере

```typescript
// Фильтрация скрытых блоков
const filterHiddenBlocks = (documents) => {
  const filterPageBlocks = (document) => {
    if (!document || !Array.isArray(document.page_blocks)) {
      return document;
    }

    return {
      ...document,
      page_blocks: document.page_blocks.filter((block) => block?.visibility !== false)
    };
  };

  return Array.isArray(documents)
    ? documents.map(filterPageBlocks)
    : filterPageBlocks(documents);
};

// Использование в контроллере
async find(ctx) {
  const documents = await strapi.documents('api::page.page').findMany({...});
  const sanitizedResults = await this.sanitizeOutput(filterHiddenBlocks(documents), ctx);
  return this.transformResponse(sanitizedResults);
}
```

### Populate для Dynamic Zone

```typescript
populate: {
  page_blocks: {
    on: {
      'blocks.hero': {
        fields: ['type', 'visibility', 'title', 'subtitle'],
        populate: {
          image: {
            fields: ['alternativeText', 'width', 'height', 'url', 'formats']
          },
          buttons: true
        }
      },
      'blocks.services': {
        fields: ['type', 'visibility', 'title'],
        populate: {
          services_list: {
            fields: ['menutitle', 'slug', 'short_description'],
            populate: {
              image: {
                fields: ['alternativeText', 'width', 'height', 'url', 'formats']
              }
            }
          }
        }
      },
      'blocks.faq': {
        populate: {
          faq_list: {
            fields: ['question', 'answer']
          }
        }
      }
    }
  }
}
```

## 4. Services

### Базовый сервис

```typescript
// src/api/{entity}/services/{entity}.ts
import { factories } from '@strapi/strapi'

export default factories.createCoreService('api::{entity}.{entity}')
```

### Кастомный сервис

```typescript
import { factories } from '@strapi/strapi'

export default factories.createCoreService('api::{entity}.{entity}', ({ strapi }) => ({
	// Кастомный метод
	async findBySlug(slug: string) {
		const documents = await strapi.documents('api::{entity}.{entity}').findMany({
			filters: { slug },
			populate: '*',
		})

		return documents[0] || null
	},

	// Переопределение базового метода
	async find(params) {
		const documents = await strapi.documents('api::{entity}.{entity}').findMany({
			...params,
			populate: {
				image: true,
				author: {
					fields: ['username', 'email'],
				},
			},
		})

		return documents
	},

	// Бизнес-логика
	async publish(documentId: string) {
		const document = await strapi.documents('api::{entity}.{entity}').update({
			documentId,
			data: {
				publishedAt: new Date(),
			},
		})

		return document
	},
}))
```

## 5. Routes

### Базовый роутер

```typescript
// src/api/{entity}/routes/{entity}.ts
import { factories } from '@strapi/strapi'

export default factories.createCoreRouter('api::{entity}.{entity}')
```

### Кастомный роутер

```typescript
export default {
	routes: [
		{
			method: 'GET',
			path: '/{entities}',
			handler: '{entity}.find',
			config: {
				policies: [],
				middlewares: [],
			},
		},
		{
			method: 'GET',
			path: '/{entities}/:id',
			handler: '{entity}.findOne',
		},
		{
			method: 'POST',
			path: '/{entities}',
			handler: '{entity}.create',
		},
		{
			method: 'PUT',
			path: '/{entities}/:id',
			handler: '{entity}.update',
		},
		{
			method: 'DELETE',
			path: '/{entities}/:id',
			handler: '{entity}.delete',
		},
		// Кастомный роут
		{
			method: 'GET',
			path: '/{entities}/slug/:slug',
			handler: '{entity}.findBySlug',
		},
	],
}
```

## 6. Lifecycles

Lifecycle hooks выполняются автоматически при операциях с документами.

### Структура event объекта

```typescript
// event.params содержит:
{
  data: {},      // Данные для создания/обновления
  where: {},     // Условия для поиска (update/delete)
  select: [],    // Выбранные поля
  populate: {}   // Связи для загрузки
}

// event.result содержит результат операции (в after* хуках)
{
  id: number,
  documentId: string,
  ...fields
}
```

### Доступные хуки

```typescript
// src/api/{entity}/content-types/{entity}/lifecycles.ts
export default {
	// Перед созданием
	async beforeCreate(event) {
		const { data } = event.params
	},

	// После создания
	async afterCreate(event) {
		const { result } = event
	},

	// Перед обновлением
	async beforeUpdate(event) {
		const { data, where } = event.params
	},

	// После обновления
	async afterUpdate(event) {
		const { result } = event
	},

	// Перед удалением
	async beforeDelete(event) {
		const { where } = event.params
	},

	// После удаления
	async afterDelete(event) {
		const { result } = event
	},

	// Массовые операции
	async beforeCreateMany(event) {},
	async afterCreateMany(event) {},
	async beforeUpdateMany(event) {},
	async afterUpdateMany(event) {},
	async beforeDeleteMany(event) {},
	async afterDeleteMany(event) {},

	// Публикация (если draftAndPublish: true)
	async beforePublish(event) {},
	async afterPublish(event) {},
	async beforeUnpublish(event) {},
	async afterUnpublish(event) {},
}
```

### Универсальные паттерны

#### 1. Auto-slug generation

```typescript
// src/utils/generateSlug.ts
import slugify from 'slugify'

export const generateSlug = (data: any, sourceField: string = 'title'): void => {
	if (!data.slug && data[sourceField]) {
		data.slug = slugify(data[sourceField].trim(), {
			lower: true,
			strict: true,
			locale: 'ru',
		})
	}
}
```

```typescript
// src/api/product/content-types/product/lifecycles.ts
import { generateSlug } from '../../../../utils/generateSlug'

export default {
	async beforeCreate(event: any) {
		generateSlug(event.params.data, 'title')
	},
}
```

#### 2. Автоматическая установка timestamp

```typescript
export default {
	async beforeCreate(event: any) {
		const { data } = event.params

		if (data.status === 'published' && !data.publishedAt) {
			data.publishedAt = new Date()
		}
	},

	async beforeUpdate(event: any) {
		const { data } = event.params

		if (data.status === 'published' && !data.publishedAt) {
			data.publishedAt = new Date()
		}
	},
}
```

#### 3. Генерация уникального идентификатора после создания

```typescript
function generateUniqueCode(id: number): string {
	const year = new Date().getFullYear().toString().slice(-2)
	const idPadded = String(id).padStart(6, '0')
	return `DOC${year}-${idPadded}`
}

export default {
	async afterCreate(event: any) {
		const { result } = event

		if (result.id && !result.code) {
			const code = generateUniqueCode(result.id)

			await strapi.entityService.update('api::document.document', result.id, {
				data: { code },
			})

			event.result.code = code
		}
	},
}
```

#### 4. Загрузка связанных данных

```typescript
export default {
	async beforeUpdate(event: any) {
		const { data, where } = event.params

		// Загрузка существующего документа с populate
		if (where?.id) {
			const existing = await strapi.db.query('api::entity.entity').findOne({
				where: { id: where.id },
				populate: {
					relation: {
						select: ['id', 'name'],
					},
				},
			})

			// Используй данные для валидации или расчетов
			if (existing) {
				// Логика
			}
		}
	},
}
```

#### 5. Обработка форматов связей (админка vs API)

```typescript
// Универсальная функция для извлечения ID связи
function extractRelationId(relationData: any): number | undefined {
	if (!relationData) return undefined

	// Админка: { connect: [{ id: 1 }] }
	if (relationData.connect && Array.isArray(relationData.connect)) {
		return relationData.connect[0]?.id
	}

	// REST API: { set: [{ id: 1 }] }
	if (relationData.set && Array.isArray(relationData.set)) {
		return relationData.set[0]?.id
	}

	// Прямой ID: { id: 1 }
	if (relationData.id) {
		return relationData.id
	}

	return undefined
}

export default {
	async beforeCreate(event: any) {
		const { data } = event.params

		const categoryId = extractRelationId(data.category)

		if (categoryId) {
			const category = await strapi.db.query('api::category.category').findOne({
				where: { id: categoryId },
				select: ['name'],
			})

			if (category) {
				data.category_name = category.name
			}
		}
	},
}
```

#### 6. Каскадное обновление родителя

```typescript
// Триггер пересчета родительской сущности
async function triggerParentUpdate(parentId: number, parentModel: string) {
	try {
		// Пустой update вызовет beforeUpdate родителя
		await strapi.entityService.update(parentModel, parentId, {
			data: {},
		})
	} catch (error) {
		strapi.log.error(`Failed to trigger parent update:`, error)
	}
}

export default {
	async afterCreate(event: any) {
		const { id } = event.result

		// Загружаем связь с родителем
		const child = await strapi.db.query('api::child.child').findOne({
			where: { id },
			populate: { parent: true },
		})

		if (child?.parent?.id) {
			await triggerParentUpdate(child.parent.id, 'api::parent.parent')
		}
	},
}
```

#### 7. Сохранение данных между хуками

```typescript
// Используй переменную модуля для передачи данных
let tempStorage: Map<number, any> = new Map()

export default {
	async beforeDelete(event: any) {
		const { id } = event.params.where

		if (id) {
			const entity = await strapi.db.query('api::entity.entity').findOne({
				where: { id },
				populate: { relation: true },
			})

			tempStorage.set(id, entity)
		}
	},

	async afterDelete(event: any) {
		const { id } = event.result

		const savedData = tempStorage.get(id)

		if (savedData) {
			// Используй сохраненные данные
			strapi.log.info('Deleted entity:', savedData)

			// Очистка
			tempStorage.delete(id)
		}
	},
}
```

### Доступ к данным в lifecycles

#### strapi.db.query (низкоуровневый API)

```typescript
// Поиск одного
const entity = await strapi.db.query('api::entity.entity').findOne({
	where: { id: entityId },
	select: ['field1', 'field2'],
	populate: {
		relation: {
			select: ['id', 'name'],
		},
	},
})

// Поиск многих
const entities = await strapi.db.query('api::entity.entity').findMany({
	where: { status: 'active' },
	limit: 10,
})

// Подсчет
const count = await strapi.db.query('api::entity.entity').count({
	where: { status: 'active' },
})

// Обновление
await strapi.db.query('api::entity.entity').update({
	where: { id: entityId },
	data: { field: 'value' },
})
```

#### strapi.entityService (высокоуровневый API)

```typescript
// Поиск
const entity = await strapi.entityService.findOne('api::entity.entity', entityId, {
	populate: ['relation'],
})

// Обновление
await strapi.entityService.update('api::entity.entity', entityId, {
	data: { field: 'value' },
})
```

## 7. Document Service API

Основной API для работы с документами в Strapi 5.

### findMany - получение списка

```typescript
const documents = await strapi.documents('api::{entity}.{entity}').findMany({
	filters: {
		title: { $contains: 'search' },
		status: 'published',
		publishedAt: { $notNull: true },
	},
	sort: { createdAt: 'desc' },
	pagination: {
		page: 1,
		pageSize: 10,
	},
	populate: {
		image: true,
		author: {
			fields: ['username'],
		},
	},
	fields: ['title', 'slug', 'description'],
	locale: 'ru',
	status: 'published', // draft | published
})
```

### findOne - получение одного документа

```typescript
const document = await strapi.documents('api::{entity}.{entity}').findOne({
	documentId: 'abc123',
	populate: '*',
	locale: 'ru',
	status: 'published',
})
```

### findFirst - первый найденный

```typescript
const document = await strapi.documents('api::{entity}.{entity}').findFirst({
	filters: { slug: 'my-page' },
	populate: '*',
})
```

### create - создание

```typescript
const document = await strapi.documents('api::{entity}.{entity}').create({
	data: {
		title: 'New Document',
		description: 'Description',
		status: 'draft',
	},
	populate: '*',
})
```

### update - обновление

```typescript
const document = await strapi.documents('api::{entity}.{entity}').update({
	documentId: 'abc123',
	data: {
		title: 'Updated Title',
	},
	populate: '*',
})
```

### delete - удаление

```typescript
const document = await strapi.documents('api::{entity}.{entity}').delete({
	documentId: 'abc123',
})
```

### publish/unpublish - публикация

```typescript
// Публикация
const published = await strapi.documents('api::{entity}.{entity}').publish({
	documentId: 'abc123',
})

// Снятие с публикации
const unpublished = await strapi.documents('api::{entity}.{entity}').unpublish({
	documentId: 'abc123',
})
```

### count - подсчет

```typescript
const count = await strapi.documents('api::{entity}.{entity}').count({
	filters: { status: 'published' },
})
```

## 8. Фильтры и операторы

### Операторы сравнения

```typescript
filters: {
  // Равенство
  field: 'value',
  field: { $eq: 'value' },

  // Не равно
  field: { $ne: 'value' },

  // Больше/меньше
  price: { $gt: 100 },
  price: { $gte: 100 },
  price: { $lt: 1000 },
  price: { $lte: 1000 },

  // В списке
  status: { $in: ['draft', 'published'] },
  status: { $notIn: ['archived'] },

  // Null проверки
  publishedAt: { $null: true },
  publishedAt: { $notNull: true },

  // Строковые операторы
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
  $not: {
    status: 'archived'
  }
}
```

## 9. Populate стратегии

### Базовый populate

```typescript
// Все связи на 1 уровень
populate: '*'

// Конкретные поля
populate: ['image', 'author']

// Объект с настройками
populate: {
  image: true,
  author: {
    fields: ['username', 'email']
  }
}
```

### Глубокий populate

```typescript
populate: {
  author: {
    fields: ['username'],
    populate: {
      avatar: {
        fields: ['url']
      }
    }
  },
  categories: {
    populate: {
      parent: true
    }
  }
}
```

### Populate для media

```typescript
populate: {
  image: {
    fields: ['alternativeText', 'width', 'height', 'url', 'formats']
  },
  gallery: {
    fields: ['url', 'formats']
  }
}
```

## 10. Best Practices

### 1. Всегда используй TypeScript для контроллеров/сервисов/lifecycles

```typescript
// ✅ Правильно
export default factories.createCoreController('api::page.page', ({ strapi }) => ({
	async find(ctx) {
		// ...
	},
}))

// ❌ Неправильно - JS файлы
```

### 2. Используй sanitize методы в контроллерах

```typescript
// ✅ Правильно
const sanitizedQuery = await this.sanitizeQuery(ctx)
const sanitizedData = await this.sanitizeInput(ctx.request.body, ctx)
const sanitizedResults = await this.sanitizeOutput(documents, ctx)

// ❌ Неправильно - прямое использование данных
const documents = await strapi.documents('api::page.page').findMany(ctx.query)
```

### 3. Оптимизируй populate - указывай только нужные поля

```typescript
// ✅ Правильно
populate: {
  image: {
    fields: ['url', 'alternativeText', 'width', 'height']
  },
  author: {
    fields: ['username']
  }
}

// ❌ Неправильно - загружает все поля
populate: {
  image: true,
  author: true
}
```

### 4. Используй filters на уровне БД

```typescript
// ✅ Правильно
const documents = await strapi.documents('api::page.page').findMany({
	filters: { status: 'published' },
})

// ❌ Неправильно - фильтрация в коде
const all = await strapi.documents('api::page.page').findMany()
const filtered = all.filter((doc) => doc.status === 'published')
```

### 5. Используй Document Service API (Strapi 5)

```typescript
// ✅ Правильно - Strapi 5
await strapi.documents('api::page.page').findMany({...});

// ❌ Неправильно - Strapi 4 (устаревший)
await strapi.entityService.findMany('api::page.page', {...});
```

### 6. Выноси сложную логику в утилиты

```typescript
// ✅ Правильно
import { generateSlug } from '../../../../utils/generateSlug'

export default {
	async beforeCreate(event: any) {
		generateSlug(event.params.data)
	},
}

// ❌ Неправильно - вся логика в lifecycle
export default {
	async beforeCreate(event: any) {
		// 50+ строк логики прямо здесь
	},
}
```

### 7. Обрабатывай оба формата связей (админка + API)

```typescript
// ✅ Правильно - универсальная функция
function extractRelationId(relationData: any): number | undefined {
	if (!relationData) return undefined

	if (relationData.connect && Array.isArray(relationData.connect)) {
		return relationData.connect[0]?.id // Админка
	}

	if (relationData.set && Array.isArray(relationData.set)) {
		return relationData.set[0]?.id // REST API
	}

	return relationData.id // Прямой ID
}

// ❌ Неправильно - только один формат
const relationId = data.relation?.id
```

### 8. Используй strapi.log для логирования

```typescript
// ✅ Правильно
strapi.log.info('Document created', { documentId: result.documentId })
strapi.log.error('Failed to process', error)
strapi.log.warn('Deprecated field used')
strapi.log.debug('Debug info', data)

// ❌ Неправильно
console.log('Document created', result.documentId)
```

### 9. Очищай временные данные в lifecycles

```typescript
// ✅ Правильно
let tempStorage: Map<number, any> = new Map()

export default {
	async beforeDelete(event: any) {
		const { id } = event.params.where
		tempStorage.set(id, await loadData(id))
	},

	async afterDelete(event: any) {
		const { id } = event.result
		const data = tempStorage.get(id)

		if (data) {
			await processData(data)
			tempStorage.delete(id) // Очистка
		}
	},
}

// ❌ Неправильно - утечка памяти
let tempData: any = null

export default {
	async beforeDelete(event: any) {
		tempData = await loadData()
	},

	async afterDelete() {
		await processData(tempData)
		// tempData не очищается
	},
}
```

### 10. Используй правильный API для доступа к данным

```typescript
// В lifecycles используй strapi.db.query для точного контроля
const entity = await strapi.db.query('api::entity.entity').findOne({
	where: { id },
	select: ['field1', 'field2'],
	populate: { relation: true },
})

// В контроллерах используй strapi.documents (Strapi 5)
const documents = await strapi.documents('api::entity.entity').findMany({
	filters: { status: 'published' },
})

// Для обновлений в lifecycles используй entityService
await strapi.entityService.update('api::entity.entity', id, {
	data: { field: 'value' },
})
```

## 11. Утилиты для lifecycles

### Создание переиспользуемых утилит

```typescript
// src/utils/generateSlug.ts
import slugify from 'slugify'

export const generateSlug = (data: any, sourceField: string = 'title'): void => {
	if (!data.slug && data[sourceField]) {
		data.slug = slugify(data[sourceField].trim(), {
			lower: true,
			strict: true,
			locale: 'ru',
		})
	}
}
```

```typescript
// src/utils/helpers.ts

export function generateUniqueCode(id: number, prefix: string = 'DOC'): string {
	const year = new Date().getFullYear().toString().slice(-2)
	const idPadded = String(id).padStart(6, '0')
	return `${prefix}${year}-${idPadded}`
}

export function parseDecimal(value: any): number {
	return value != null ? parseFloat(String(value)) : 0
}

export function extractRelationId(relationData: any): number | undefined {
	if (!relationData) return undefined

	if (relationData.connect && Array.isArray(relationData.connect)) {
		return relationData.connect[0]?.id // Админка
	}

	if (relationData.set && Array.isArray(relationData.set)) {
		return relationData.set[0]?.id // REST API
	}

	return relationData.id // Прямой ID
}
```

### Использование утилит в lifecycles

```typescript
// src/api/product/content-types/product/lifecycles.ts
import { generateSlug } from '../../../../utils/generateSlug'

export default {
	async beforeCreate(event: any) {
		generateSlug(event.params.data, 'title')
	},
}
```

## 12. Типичные задачи

### Создание новой коллекции

1. Создай schema.json в `src/api/{entity}/content-types/{entity}/`
2. Создай controller в `src/api/{entity}/controllers/{entity}.ts`
3. Создай service в `src/api/{entity}/services/{entity}.ts`
4. Создай routes в `src/api/{entity}/routes/{entity}.ts`
5. (Опционально) Создай lifecycles в `src/api/{entity}/lifecycles/{entity}.ts`

### Добавление нового блока в Dynamic Zone

1. Создай компонент в `src/components/blocks/{block}.json`
2. Добавь компонент в schema.json страницы:

```json
{
	"page_blocks": {
		"type": "dynamiczone",
		"components": [
			"blocks.hero",
			"blocks.new-block" // ← новый блок
		]
	}
}
```

3. Добавь populate в контроллер страницы

### Кастомный endpoint

1. Добавь метод в контроллер
2. Добавь роут в routes файл
3. (Опционально) Добавь логику в сервис

## 12. Debugging

### Логирование

```typescript
// В контроллере/сервисе
strapi.log.info('Info message')
strapi.log.warn('Warning message')
strapi.log.error('Error message')
strapi.log.debug('Debug message')

// С данными
strapi.log.info('Document created', { documentId: result.documentId })
```

### Проверка данных

```typescript
// В lifecycle
async beforeCreate(event) {
  console.log('Event params:', event.params);
  console.log('Data:', event.params.data);
}
```

## Чеклист при создании новой коллекции

- [ ] Создана директория `src/api/{entity}/content-types/{entity}/`
- [ ] Создан `schema.json` с правильной структурой
- [ ] Созданы controller, service, routes файлы
- [ ] Если нужна бизнес-логика - создан lifecycle
- [ ] Если используются компоненты - они созданы в `src/components/`
- [ ] В контроллере настроен правильный populate
- [ ] Используются sanitize методы
- [ ] Добавлены необходимые фильтры
- [ ] Протестированы CRUD операции через API

## Полезные команды

```bash
# Запуск dev сервера
npm run dev

# Сборка для production
npm run build

# Запуск production
npm start

# Обновление Strapi
npm run upgrade

# Dry run обновления
npm run upgrade:dry
```

---

**Помни:** Всегда используй Document Service API (`strapi.documents()`), sanitize методы, TypeScript для кода, и следуй структуре проекта.
