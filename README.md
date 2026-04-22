# Strapi 5 Development Skill

> Экспертное руководство по разработке на Strapi 5 - content types, контроллеры, сервисы, lifecycles, компоненты и кастомизация API

## 📋 Обзор

Этот скилл предоставляет comprehensive паттерны и best practices для разработки на Strapi 5. Охватывает всё от создания базовых content types до продвинутых lifecycle hooks и кастомизации API.

**Версия:** Strapi 5.42.0
**Язык:** Русский (с примерами кода на английском)

## 🎯 Что включено

- **Content Types** - Создание схем, типы полей, связи, dynamic zones
- **Controllers** - Базовые и кастомные контроллеры, фильтрация данных, sanitization
- **Services** - Бизнес-логика, кастомные методы, паттерны сервисов
- **Routes** - Базовая и кастомная конфигурация роутинга
- **Lifecycles** - Event hooks, трансформация данных, каскадные обновления
- **Document Service API** - Основной API Strapi 5 для работы с документами
- **Фильтры и операторы** - Построение запросов, паттерны поиска
- **Populate стратегии** - Эффективная загрузка данных, вложенные связи
- **Best Practices** - Использование TypeScript, оптимизация, безопасность
- **Утилиты** - Переиспользуемые хелперы для типичных задач

## 🚀 Быстрый старт

### Установка

Для Claude Code / Copilot CLI:

```bash
# Клонируй в директорию скиллов
git clone https://github.com/majbernhardt/strapi5-skill.git ~/.agents/skills/strapi5
```

### Использование

В Claude Code:

```
используй скилл strapi5
```

В Copilot CLI:

```
use skill strapi5
```

## 📚 Ключевые особенности

### Универсальные паттерны

Все примеры универсальны и применимы к любому проекту на Strapi 5:

- ✅ Автогенерация slug
- ✅ Управление timestamp
- ✅ Генерация уникальных кодов
- ✅ Обработка связей (админка + API форматы)
- ✅ Каскадные обновления
- ✅ Сохранение данных между хуками

### Production-Ready

Основано на реальных production кодбейсах с проверенными паттернами:

- Type-safe TypeScript примеры
- Правильная обработка ошибок
- Предотвращение утечек памяти
- Оптимизация производительности
- Best practices безопасности

### Полное покрытие

От основ до продвинутых техник:

- Дизайн схем
- CRUD операции
- Сложные запросы
- Автоматизация через lifecycle
- Кастомные endpoints
- Техники отладки

## 🛠️ Структура

```
strapi5-skill/
├── SKILL.md          # Основное содержание скилла
├── GENERATION.md     # Метаданные и changelog
├── README.md         # Этот файл
├── LICENSE           # MIT лицензия
└── references/       # Справочные материалы
    ├── api-quick-reference.md
    ├── field-types.md
    └── migration-guide.md
```

## 📖 Разделы документации

1. **Content Types** - Collection types, типы полей, опции
2. **Components** - Создание переиспользуемых компонентов
3. **Controllers** - Обработка запросов, трансформация данных
4. **Services** - Слой бизнес-логики
5. **Routes** - Конфигурация endpoints
6. **Lifecycles** - Event hooks и автоматизация
7. **Document Service API** - Основной API Strapi 5
8. **Фильтры и операторы** - Построение запросов
9. **Populate стратегии** - Паттерны загрузки данных
10. **Best Practices** - Рекомендации для production
11. **Утилиты** - Вспомогательные функции
12. **Типичные задачи** - Пошаговые руководства

## 🎓 Путь обучения

1. Начни с **Content Types** для понимания моделирования данных
2. Изучи **Controllers** для обработки запросов
3. Освой **Lifecycles** для автоматизации
4. Овладей **Document Service API** для операций с данными
5. Применяй **Best Practices** для production кода

## 🤝 Вклад в проект

Contributions приветствуются! Пожалуйста:

1. Форкни репозиторий
2. Создай feature branch
3. Добавь свои улучшения
4. Отправь pull request

## 📝 Лицензия

MIT License - свободно используй в своих проектах

## 🔗 Ресурсы

- [Документация Strapi 5](https://docs.strapi.io)
- [Strapi GitHub](https://github.com/strapi/strapi)
- [Strapi Community](https://discord.strapi.io)

## 💬 Поддержка

- Открой issue для багов или вопросов
- Присоединяйся к Strapi Discord для поддержки сообщества
- Проверь официальную документацию для детального API reference

## 🏷️ Теги

`strapi` `strapi5` `cms` `headless-cms` `typescript` `nodejs` `api` `backend` `skill` `claude-code` `copilot-cli`

---

**Сделано с ❤️ для Strapi сообщества**
