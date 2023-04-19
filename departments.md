# Підрозділи

## Факультет або інститут

- `id` - ідентифікатор  
- `name` - назва
- `url` - офіційна сторінка
- `logo` - url логотипу
- `signatory` - підписант
  - `position` - посада
  - `degree` - звання, посада і т.п.
  - `fistname`
  - `lastname`
  - `middlename`


Отримати перелік всіх факультетів: `id` + `name`

```http
GET api/v1/facultets
```

Прочитати інформацію про факультет `idfac` (id)

```http
GET api/v1/facultets/idfac
```



## Кафедра

- `id` - ідентифікатор  
- `name` - назва
- `url` - офіційна сторінка
- `logo` - url логотипу
- `signatory` - підписант
  - `fistname`
  - `lastname`
  - `middlename`
- `facultet` - інститут/факультет, до якого належить кафедра
  - `id` - ідентифікатор  
  - `name` - назва
  - `lnk` 
    - `url` - в форматі `api/v1/facultets/idfac`, якщо не вказано за замовченням `api/v1/facultets`
    - `type = orig`


Отримати перелік кафедр

```http
GET api/v1/departments
```

Отримати перелік кафедр прив'язаних до факультету/інституту

```http
GET api/v1/departments?facultet=idfac
```

Прочитати інформацію про кафедру за `id`  `iddep`

```http
GET api/v1/departments/iddep
```



## Довідка



