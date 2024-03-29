## Структура документів

Передбачається, що обмін документами відбувається в форматі JSON. Документ включає дві частини:

- **описові властивості** - що місять загальну інформацію про документ, незалежно від його типу та призначення  
- **змістові властивості** - задають зміст документу який може змінюватися при редагуванні; усі змістові властивості знаходяться у властивості `data`.

### Описові властивості

Документи мають мати наступні обов'язкові **описові властивості** (якщо властивості відсутні вони вважають за замовченням або `false`):

- `id` кожен документ має унікальний `id` , за яким він зберігається в базі даних, правила формування ID описується у описі документу
- `name` - назва документу українською мовою
- `signatory` - підписант
  - `position` - посада
  - `degree` - звання, посада і т.п.
  - `fistname`
  - `lastname`
  - `middlename`

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

## Створення та запис документів

Створення документів робиться на базі шаблону базової частини з описовими властивостями та змістової частини документу з змістовими властивостями. Шаблон запитується через запит відповідного типу `doctype` 

```http
POST api/v1/docstmplt/doctype
```

При запиті шаблону усі значення властивостей з шаблону будуть автоматично виставлені за замовченням. 

При створенні документу він буде записаний в базу даних відповідно до вказаного типу документу `doctype`

```http
POST api/v1/docs/doctype
Host: host
Content-Type: application/json
{
Тіло документу
}
```

При зміні документу він використовується наступний запит API

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