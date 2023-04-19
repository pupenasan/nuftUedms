# Університетська система електронного документообігу НУХТ

https://github.com/pupenasan/nuftUedms

## Мета проекту

Мета проекту - переведення документів в дата-центричний електронний формат та створення внутрішньо-університетського стандарту представлення даних при обміні електронними документами між інформаційними системами університету задля автоматизації та інтенсифікації процесів створення, верифікації, валідації та аналітики документів.   

Переведення в електронний дата-центричний формат з їх збереженням в БД та обміном між інформаційними системами університету наступних документів:

- стандарт вищої освіти на бакалавра/магістра/доктора філософії
- [освітня програма](OP.md): ОП, ОНП
- освітня компонента для формування документів:
  - робоча програма освітньої компоненти
  - силабус
- методичне забезпечення ОК:
  - текст лекцій
  - лабораторний практикум
  - практичних занять
  - вивчення дисципліни
  - курсової роботи
  - курсового проекту
  - кваліфікаційної роботи
  - проведення практики
- вкладені документи

## Терміни та скорочення

- ІС - інформаційні системи
- БД - база даних
- СКБД - система керування базами даних
- ОК - освітня компонента 
- URI - уніфікований ідентифікатор ресурсу

## Основні ідеї

- самостійні структури наведені вище є документами (наприклад JSON, XML)
- документи пов'язані між собою і з іншими даними та процесами, які вже автоматизовані в інформаційних системах тому вони повинні бути оцифровані до рівня даних, оскільки це дає можливість:
  - зменшити кількість помилок при створенні
  - витягувати дані в інші системи
  - робити аналітичні дослідження як з даними
  - спростити порівняння версій (якщо використовувати системи типу Git)
- документи в теперішньому вигляді (pdf, docx) - це звіти, вони друкуються після усіх верифікацій
- дані можуть зберігатися в БД ІС в зручному для них вигляді

- мова розмітки документів при передачі - JSON
- обмін між ІС -- HTTP API

## Основні компоненти

- **Документ** - самодостатня сутність в електронному вигляді як структури даних,  JSON-сумісні. Не варто їх плутати з документами типу pdf чи docx, в даному контексті ті варто називати **звітним документом**.  Документ містить все що необхідно для його перевірки, може мати посилання на пов'язані документи для витягування з нього даних та перевірки.   
- **Звітний документ** - паперова та/або електронна версія документу (наприклад скан копія в pdf), який оформлений відповідно до вимог форми та змісту, та передбачає читання користувачем-людиною та нанесення на нього печатей та підписів.   
- **Верифікатор** - частина програми, або окремий сервіс, який проводить перевірку відповідності документу заданим правилам:
  - на вході верифікатору документ, або його частина, що потребує перевірки
  - на виході змінений документ або його частина з примітками щодо валідності та пропозицій змін
- **ІС обробки документів**  - інформаційна система з WEB API, яка забезпечує доступ до документів та до інших сервісів   

## Структура документів

Передбачається, що обмін документами відбувається в форматі JSON. Документ включає дві частини:

- **описові властивості** - що місять загальну інформацію про документ, незалежно від його типу та призначення  
- **змістові властивості** - задають зміст документу який може змінюватися при редагуванні; усі змістові властивості знаходяться у властивості `data`.

### Описові властивості

Документи мають мати наступні обов'язкові **описові властивості** (якщо властивості відсутні вони вважають за замовченням або `false`):

- `id` кожен документ має унікальний `id` , за яким він зберігається в базі даних, правила формування ID описується у описі документу
- `name` - назва документу українською мовою
- `status` - загальний стан документу
  - `version`:
    - `new` - не проходила жодного разу верифікацію
    - `active` - активна
    - `dprecated` - застарівша   

  - `valid` - якщо документ пройшов повну процедуру верифікації без зауважень
  - `dtcreate` - дата та час створення
  - `dtverif` - дата та час останнього проходження верифікації
  - `approve` - звітний документ пройшов підтвердження, і даний документ є незмінним
  - `dtapprove` - дата та час, яким було змінена властивість `approve` 
  - `approve_url` - місце, де знаходиться електронна копія звітного документу  
  - `regid` - реєстраційний номер звітного документу
- `metadata` - документи містить розділ з іншими метаданими `metadata`, в якому міститься:
  - `baseurl` - місце розміщення API ІС обробки документів
  - `rules` - правила верифікації, які за якихось причин не можуть відображатися у відповідних властивостях 
  - `doctype` - тип документу:
    - `base` - документ неозначеного типу 
    - `stndrd` - стандарт вищої освіти
    - `op` - освітня програма
    - `rp` - робоча програми
    - `ok` - освітня компонента 
    - `competency` - компетентність
    - `learnoutcome` - програмний результат навчання 
    - `mr_txtlec` - методичні рекомендації (МР) текст лекцій
    - `mr_pract` - МР до практичних занять
    - `mr_lab` - МР до лабораторних робіт
    - `mr_kursrob` - МР до курсової роботи
    - `mr_prct` - МР до практичних робіт
    - `mr_kvalrob` - МР до кваліфікаційної роботи 
  - інші властивості за необхідності
- інші властивості за необхідності

Прототипу документу можна отримати за запитом:

```http
GET api/v1/docs/tmplts/base
```

Прототип документу з типом `doctype`

```http
GET api/v1/docs/tmplts/doctype
```



### Змістові властивості

Інші властивості є **змістовими властивостями** задають зміст документу який може змінюватися при редагуванні. Для них діють наступні правила:

- усі змістові властивості розміщуються у властивості `data`;
- змістові властивості завжди мають вкладені властивості:
  - `value` - плинне значення, яке заповнюється
  - `pvalue` - значення яке пропонується під час верифікації (тільки у властивості `tmp.propos`)
  - `pvalues` - альтернативні значення які пропонуються під час верифікації, або оновленні звязків (тільки у властивості `tmp.propos`)  
  - `status` - набір статусних полів, які говорять про валідність властивості або іншу інформацію
- змістові властивості, що розраховуються мають вкладену властиість  `formula` :
  - для властивості вказується `formula`, яка задає значення для якого розраховується `value`: `value = formula`
  - для властивості є форсоване значення `fvalue`
  - для властивості є `status.isforce` яке вказує що значення не розрахункове а береться з `fvalue`
- змістові властивості можуть мати правила `rules`, яким повинні задовольняти
  - правила формуються як залежності між властивостями
  - для властивості вказуються правила `rules` яким воно повинно задовольняти
  - для властивості вказується `status.valids`, якщо вказує на істинність конкретного виразу `rules`
  - для властивості вказується `status.valid = status.valids[i] по OR ` 
- пов'язані змістові властивості мають властивість `lnk`, яка включає властивості (див нижче правила):
  - `url` - відносне посилання на зв'язану властивість через API
  - `type` - тип зв'язку  `orig` або `prototype`
- якщо текстові властивості мають інші варіанти мов, то вони мають приймати суфікс мови, наприклад `name_EN_`

Документи можуть мати тимчасову властивість `tmp`, яка використовується тільки для обміну службової інформації та інформування і при збереженні документу видаляється. Вона може містити наступні частини:   

- `propos` - копія всього документу, в якій містяться запропоновані варіанти після валідації
-  

## Створення та запис документів

Створення документів робиться на базі шаблону базової частини з описовими властивостями та змістової частини документу з змістовими властивостями. Назва шаблону описової частини є типом документу, тобто що міститься у властивості `doctype`. При створенні документу усі значення властивостей з шаблону будуть автоматично присвоєні відповідним значенням в документі. Якщо значення властивості містить код, він буде виконаний а результат буде записаний в цю властивість. 

Приклад запиту API для створення документу

```http
POST api/v1/docs
Host: host
Content-Type: application/json
{
  "name": "Назва документу",
  "doctype": "doctype"
}
```

При створенні документу він повертається в результаті запиту, але не зберігається в базу документів. Для запису документу в базу (існуючого або нового) використовується наступний запит API

```http
PUT api/v1/docs/doctype
Host: host
Content-Type: application/json
{
Тіло документу
}
```

При цьому перед розміщенням документу в базі буде перевірятися тип документу `doctype` вони повинні співпадати.

  

## Правила заповнення, зміни та верифікації документів

Змістові властивості різних документів можуть бути пов'язані. Якщо правило зв'язку для змістової властивості існує, воно задається дочірньою властивістю `lnk`. Посилання на зв'язану властивість задається властивістю `url`. Зв'язки можуть бути різних типів, що задається полем `type`, яке може приймати значення:

- `orig`: оригінальна властивість (джерело даних) -> копія частин властивостей, зв'язок односторонній від копії до оригіналу
- `prototype`: прототип властивості -> екземпляр, зв'язок односторонній від екземпляру до прототипу 

Наявність зв'язку має підсвічуватися візуально в редакторі, для того щоб користувач її бачив. Також має відображатися статус. 

Будь які зв'язки автоматично не впливають на значення властивостей, вони перезаписуються у властивості тільки за команди користувача. Користувач має право змінити значення `value` будь якої властивості на власний розсуд. При запиті до звязаних значень а також при верифікації змінюються `pvalue` або/та  `pvalues` у властивості `tmp.propos`. Також при верифікації якщо вони не співпадають, то це вказується в статусі. Має бути можливість команди синхронізації, коли усі значення пов'язаних властивостей змінюються автоматично.    

Прототип містить усі необхідні властивості та правила для них `rules`. Кожна властивість в прототипі має мати значення за замовченням `defvalue`. 

## Щоб не забути

- керування версіями (порівняння тексті як в Git) ?
- підтвердження 
