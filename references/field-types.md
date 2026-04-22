# Справочник типов полей в схемах

## Базовые типы

### String
```json
{
  "field_name": {
    "type": "string",
    "required": false,
    "unique": false,
    "minLength": 1,
    "maxLength": 255,
    "default": "",
    "regex": "^[a-zA-Z0-9]*$"
  }
}
```

### Text
```json
{
  "field_name": {
    "type": "text",
    "required": false,
    "minLength": 1,
    "maxLength": 5000
  }
}
```

### Rich Text
```json
{
  "field_name": {
    "type": "richtext",
    "required": false
  }
}
```

### Email
```json
{
  "field_name": {
    "type": "email",
    "required": false,
    "unique": true
  }
}
```

### Password
```json
{
  "field_name": {
    "type": "password",
    "required": false,
    "minLength": 8
  }
}
```

## Числовые типы

### Integer
```json
{
  "field_name": {
    "type": "integer",
    "required": false,
    "min": 0,
    "max": 100,
    "default": 0
  }
}
```

### Big Integer
```json
{
  "field_name": {
    "type": "biginteger",
    "required": false
  }
}
```

### Float
```json
{
  "field_name": {
    "type": "float",
    "required": false,
    "min": 0.0,
    "max": 100.0
  }
}
```

### Decimal
```json
{
  "field_name": {
    "type": "decimal",
    "required": false,
    "min": 0.00,
    "max": 9999.99
  }
}
```

## Типы даты и времени

### Date
```json
{
  "field_name": {
    "type": "date",
    "required": false
  }
}
```

### Time
```json
{
  "field_name": {
    "type": "time",
    "required": false
  }
}
```

### DateTime
```json
{
  "field_name": {
    "type": "datetime",
    "required": false
  }
}
```

### Timestamp
```json
{
  "field_name": {
    "type": "timestamp",
    "required": false
  }
}
```

## Другие типы

### Boolean
```json
{
  "field_name": {
    "type": "boolean",
    "default": false,
    "required": false
  }
}
```

### Enumeration
```json
{
  "field_name": {
    "type": "enumeration",
    "enum": ["option1", "option2", "option3"],
    "default": "option1",
    "required": false
  }
}
```

### JSON
```json
{
  "field_name": {
    "type": "json",
    "required": false
  }
}
```

### UID (Slug)
```json
{
  "field_name": {
    "type": "uid",
    "targetField": "title",
    "required": true
  }
}
```

## Типы медиа

### Single Media
```json
{
  "field_name": {
    "type": "media",
    "multiple": false,
    "required": false,
    "allowedTypes": ["images"]
  }
}
```

### Multiple Media
```json
{
  "field_name": {
    "type": "media",
    "multiple": true,
    "required": false,
    "allowedTypes": ["images", "files", "videos", "audios"]
  }
}
```

## Типы связей

### One-to-One
```json
{
  "field_name": {
    "type": "relation",
    "relation": "oneToOne",
    "target": "api::target.target"
  }
}
```

### One-to-Many
```json
{
  "field_name": {
    "type": "relation",
    "relation": "oneToMany",
    "target": "api::target.target",
    "mappedBy": "inverse_field"
  }
}
```

### Many-to-One
```json
{
  "field_name": {
    "type": "relation",
    "relation": "manyToOne",
    "target": "api::target.target",
    "inversedBy": "inverse_field"
  }
}
```

### Many-to-Many
```json
{
  "field_name": {
    "type": "relation",
    "relation": "manyToMany",
    "target": "api::target.target",
    "inversedBy": "inverse_field"
  }
}
```

### Relation to User
```json
{
  "field_name": {
    "type": "relation",
    "relation": "manyToOne",
    "target": "plugin::users-permissions.user"
  }
}
```

## Типы компонентов

### Single Component
```json
{
  "field_name": {
    "type": "component",
    "component": "category.component-name",
    "repeatable": false,
    "required": false
  }
}
```

### Repeatable Component
```json
{
  "field_name": {
    "type": "component",
    "component": "category.component-name",
    "repeatable": true,
    "required": false,
    "min": 1,
    "max": 10
  }
}
```

## Dynamic Zone

```json
{
  "field_name": {
    "type": "dynamiczone",
    "components": [
      "blocks.hero",
      "blocks.text",
      "blocks.image",
      "blocks.video"
    ],
    "required": false,
    "min": 1,
    "max": 20
  }
}
```

## Опции полей

### Общие опции
- `required`: Boolean - Field is required
- `unique`: Boolean - Value must be unique
- `private`: Boolean - Not returned in API responses
- `default`: Any - Default value

### String/Text Options
- `minLength`: Number - Minimum length
- `maxLength`: Number - Maximum length
- `regex`: String - Validation regex pattern

### Number Options
- `min`: Number - Minimum value
- `max`: Number - Maximum value

### Media Options
- `multiple`: Boolean - Allow multiple files
- `allowedTypes`: Array - Allowed file types
  - `"images"` - Image files
  - `"files"` - Documents
  - `"videos"` - Video files
  - `"audios"` - Audio files

### Component Options
- `repeatable`: Boolean - Allow multiple instances
- `min`: Number - Minimum instances (repeatable only)
- `max`: Number - Maximum instances (repeatable only)

### Dynamic Zone Options
- `components`: Array - List of allowed components
- `min`: Number - Minimum blocks
- `max`: Number - Maximum blocks
