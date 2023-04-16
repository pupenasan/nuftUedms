# Підрозділи

## Факультети та інститути

- `id` - ідентифікатор  
- `name` - назва
- `url` - офіційна сторінка
- `logo` - url логотипу
- `signatory` - підписант
  - `fistname`
  - `lastname`
  - `middlename`


Отримати перелік всіх факультетів

```
GET api/v1/facultets
```

Прочитати інформацію про факультет `idfac` (id)

```
GET api/v1/facultets/idfac
```



## Кафедри

- `id` - ідентифікатор  
- `name` - назва
- `url` - офіційна сторінка
- `logo` - url логотипу
- `signatory` - підписант
  - `fistname`
  - `lastname`
  - `middlename`
- `facultet` - інститут/факультет, до якого належить кафедра

Отримати перелік кафедр

```
GET api/v1/departments
```

Отримати перелік кафедр прив'язаних до факультету/інституту

```
GET api/v1/departments?facultet=idfac
```

Прочитати інформацію про кафедру за `id`  `iddep`

```
GET api/v1/departments/iddep
```



## Довідка



