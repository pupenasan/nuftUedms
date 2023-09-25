# Компетентності та результати навчання

Описові властивості є стандартними. Нижче наводяться тільки змістові властивості.

## Компетентності

`doctype=competency`

- `compettype` - тип: загальна (`basic`), спеціальна `special` (фахова, предметна), інтегральна - (`integral`)
- `isstandard` - є в стандарті: `true/false`
- `tags` - перелік тегів для пошуку
- `departments` - посилання на кафедри, на яких надають компетентність 
- `standards` - посилання на стандарти, де вони є
- `ops` - посилання на освітні програми, де вони є
- `oks` - посилання на освітні компоненти, де вони є
- `text` - додатковий текст з деталізованим описом  

### Створення та зміна

Запит шаблону

```http
POST api/v1/docstmplt/competency
```

Створення

```http
POST api/v1/docs/competency
Host: host
Content-Type: application/json
{
Тіло документу
}
```

Зміна

```http
PUT api/v1/docs/competency
Host: host
Content-Type: application/json
{
Тіло документу
}
```

## Результати навчання

`doctype=learnoutcome`

Усі поля ті самі, що вище, окрім `compettype`, який відсутній