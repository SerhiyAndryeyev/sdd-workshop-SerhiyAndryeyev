# Правила контактної форми, щоб порівняти код із вимогою замовника

Цей файл для людини, яка звіряє контактну форму з вимогою. Таблиця фіксує правила, які вже є в коді. Порожня клітинка означає, що рішення немає ні в коді, ні в словах замовника.

Шар — це місце правила в системі. Схема — це перевірка форми. Літерал — це число або текст, записані прямо в коді. Regexp — це шаблон для тексту. Запис «Замовник (issue #1)» означає, що рішення стоїть у вимозі `Original request` в issue #1.

## Таблиця правил

| ID | Правило простою мовою | Файл | Джерело істини | Шар | Є зараз | Має бути | Хто вирішив |
| --- | --- | --- | --- | --- | --- | --- | --- |
| RULE-01 | Ім’я обов’язкове. Форма зрізає пробіли по краях. Далі ім’я має від 2 до 50 символів. | `app/shared/components/forms/contact-form/ContactForm.tsx`, 58–63 | схема, літерали 1, 2 і 50 | UI | Так | | |
| RULE-02 | Сервер приймає ім’я лише як рядок від 2 до 50 символів. Пробіли він не зрізає. | `app/lib/utils/validateContactData.ts`, 15–17 | константи `NAME_MIN_LENGTH` і `NAME_MAX_LENGTH` (`app/constants/validation.ts`, 2–3) | API | Так | | |
| RULE-03 | Пошта на формі обов’язкова. Вона має вигляд адреси пошти. | `ContactForm.tsx`, 64 | схема, метод `email` | UI | Так | | |
| RULE-04 | Сервер приймає пошту лише за власним шаблоном. Шаблон дозволяє літери, цифри та знаки `.`, `_`, `-` перед `@`. Знак `+` він не дозволяє. | `validateContactData.ts`, 19–21 | regexp `EMAIL_PATTERN` (`validation.ts`, 11) | API | Так | | |
| RULE-05 | Повідомлення обов’язкове. Форма зрізає пробіли по краях. Далі текст має від 10 до 1000 символів. | `ContactForm.tsx`, 66–71 | схема, літерали 1, 10 і 1000 | UI | Так | | |
| RULE-06 | Сервер приймає повідомлення лише як рядок від 10 до 1000 символів. | `validateContactData.ts`, 34–40 | константи `MESSAGE_MIN_LENGTH` і `MESSAGE_MAX_LENGTH` (`validation.ts`, 6–7) | API | Так | | |
| RULE-07 | Браузер не дає ввести в ім’я більше 50 символів, а в повідомлення більше 1000. | `ContactForm.tsx`, 133 і 166 | літерал `maxLength` | UI | Так | | |
| RULE-08 | Форма пропускає порожній телефон. | `ContactForm.tsx`, 44–55 і 65 | схема, `z.literal('')` і `.optional()` | UI | Так. Телефон необов’язковий. | Телефон обов’язковий. | Замовник (issue #1) |
| RULE-09 | Сервер перевіряє телефон лише тоді, коли значення непорожнє. | `validateContactData.ts`, 23 | гілка `if (phoneNumber)` | API | Так. Порожній телефон проходить. | Телефон обов’язковий. | Замовник (issue #1) |
| RULE-10 | Непорожній телефон на сервері починається з `+`, далі йдуть лише цифри. Довжина від 11 до 16 символів разом із `+`. | `validateContactData.ts`, 24–29 | regexp `PHONE_NUMBER_PATTERN`, константи `PHONE_NUMBER_MIN_LENGTH` і `PHONE_NUMBER_MAX_LENGTH` (`validation.ts`, 4–5 і 12) | API | Так | | |
| RULE-11 | Маска телефону шукає код країни довжиною від 3 до 1 цифри. Невідомий код дає помилку. | `app/shared/hooks/use-handle-phone-input/useHandlePhoneInput.ts`, 7, 80–86 і 181–183 | константа `PHONE_COUNTRY_CODES` (`app/constants/phoneCountryCodes.ts`), літерал 3 | UI | Так | | |
| RULE-12 | Кожна країна має мінімальну довжину номера. Для України це 12 цифр. Понад 16 цифр дає помилку. | `useHandlePhoneInput.ts`, 8 і 137–141; `phoneCountryCodes.ts`, 26 | константа `PHONE_COUNTRY_CODES`, літерал 16 | UI | Так | | |
| RULE-13 | Номер, що починається з `0`, отримує префікс `38`. | `useHandlePhoneInput.ts`, 75 | літерал `'38'` | UI | Так | | |
| RULE-14 | Помилка маски блокує відправку форми. | `ContactForm.tsx`, 108 | стан `hasError` | UI | Так | | |
| RULE-15 | Перед відправкою номер стає `+` і цифри. Порожній номер стає `undefined`. | `app/lib/utils/normalizePhoneNumberFromMask.ts`, 1–7 | функція лишає лише цифри | shared | Так | | |
| RULE-16 | Форма не має поля для документа. | `ContactForm.tsx`, 24–33 і 57–76 | у схемі немає поля файлу | UI | Ні | Відвідувач може додати документ (CV або портфоліо). | Замовник (issue #1) |
| RULE-17 | Клієнт шле запит як JSON з полями `name`, `email`, `phoneNumber`, `message`. Файл у запит не входить. | `app/shared/components/forms/offer-collaboration-form/OfferCollaborationForm.tsx`, 30–41 | літерал тіла запиту | UI | Так | | |
| RULE-18 | Обидва маршрути читають тіло запиту як JSON. | `app/api/contact/route.ts`, 6; `app/api/collaboration/route.ts`, 7 | виклик `request.json()` | API | Так | | |
| RULE-19 | Текст помилки на формі береться зі словника мови сторінки. Окремого тексту «телефон обов’язковий» у словнику `uk` немає. | `ContactForm.tsx`, 37–38 і 58–74; `i18n/messages/uk.json`, 39–50 | літерали в словнику `contactForm.errors` | UI | Так | | |
| RULE-20 | Мітка «Номер телефону» не має зірочки. Підпис «* – поля обов’язкові до заповнення» стоїть над формою. | `uk.json`, 31–35 | літерали в словнику `contactForm` | UI | Так | | |
| RULE-21 | Текст помилки сервера завжди англійський. Числа він бере з констант. | `app/constants/errors.ts`, 4–7 | літерали, константи `lengths` | API | Так | | |
| RULE-22 | Сервер повертає список помилок із кодом 400. Маршрут контакту бере код за замовчуванням. | `app/lib/utils/apiResponse.ts`, 12–17; `collaboration/route.ts`, 11–13; `contact/route.ts`, 10–12 | функція `errorResponse`, літерал 400 | API | Так | | |
| RULE-23 | Маршрут співпраці ловить несподівану помилку. Він повертає код 500 і загальний англійський текст. | `collaboration/route.ts`, 25–28 | літерал тексту | API | Так | | |
| RULE-24 | Маршрут `/api/contact` не має блока `try/catch`. Помилка розбору JSON не оброблена. | `contact/route.ts`, 5–17 | відсутність обробки | API | Так | | |
| RULE-25 | Клієнт не показує помилку відповіді API. Він пише її в консоль. | `OfferCollaborationForm.tsx`, 45–58 | блоки `if (!response.ok)` і `catch` | UI | Так | | |
| RULE-26 | Перевірка форми працює під час введення. | `ContactForm.tsx`, 88 | літерал `mode: 'onChange'` | UI | Так | | |
| RULE-27 | Поля і кнопка увімкнені, доки батько не передасть `disabled`. Під час відправки `OfferCollaborationForm` вимикає форму. | `ContactForm.tsx`, 36; `OfferCollaborationForm.tsx`, 18 і 71 | літерал `disabled = false`, стан `isSubmitting` | UI | Так | | |
| RULE-28 | Підказка про максимум імені чи повідомлення зникає через 3 секунди. | `ContactForm.tsx`, 102–103; `app/shared/hooks/use-auto-hide-message/useAutoHideMessage.ts`, 3 | літерал `3000` | UI | Так | | |
| RULE-29 | Після завершення `onSubmit` форма показує вікно подяки. Закриття вікна скидає форму і прапорець згоди. | `ContactForm.tsx`, 105–116 | стан `isModalOpen` | UI | Так | | |
| RULE-30 | `OfferCollaborationForm` сам ловить помилку відправки. Тому вікно подяки з’являється і тоді, коли API відмовив. | `OfferCollaborationForm.tsx`, 55–58; `ContactForm.tsx`, 109–110 | блок `catch` без повторного викидання | UI | Так | | |
| RULE-31 | `GetNotesModal` використовує ту ж форму. Дані нікуди не йдуть. Вікно лише перемикає екран. | `app/shared/components/get-notes-modal/GetNotesModal.tsx`, 64 | літерал функції `onSubmit` | UI | Так | | |
| RULE-32 | Жодна сторінка не викликає `/api/contact`. Форма шле запит лише на `/api/collaboration`. | `OfferCollaborationForm.tsx`, 30 | літерал адреси | API | Так | | |
| RULE-33 | Згода з політикою на старті вимкнена. Без згоди форма не відправляється. | `ContactForm.tsx`, 72–74 і 89–91 | схема, значення `policy: false` | UI | Так | | |
| RULE-34 | Текст згоди містить посилання на сторінку політики. | `ContactForm.tsx`, 190; `uk.json`, 36–37 | константа `ROUTES.PRIVACY_POLICY`, літерали в словнику | UI | Так | | |
| RULE-35 | Сервер згоду не перевіряє. Запит співпраці це поле не надсилає. | `validateContactData.ts`, 4–9; `OfferCollaborationForm.tsx`, 35–41 | тип `ContactFormData` без поля, літерал тіла запиту | API | Так | | |
| RULE-36 | Маршрути контакту і співпраці лист не викликають. Вони повертають успіх із перевіреними даними. | `contact/route.ts`, 14–16; `collaboration/route.ts`, 15–24 | відповідь `successResponse` без виклику пошти | API | Так. Лист не йде. | | |
| RULE-37 | Методи листа будують текст, відкидають його і повертають успіх. Лист нікуди не йде. | `app/services/email/emailService.ts`, 11–13, 15–23 і 25–34 | літерал `{ success: true }` | shared | Так | | |
| RULE-38 | Шаблон листа додає рядок телефону лише тоді, коли номер є. | `app/services/email/emails/emails.ts`, 19–25 і 88–94 | умова `phoneNumber` у шаблоні | shared | Так | | |
| RULE-39 | Шаблон листа не має поля для документа. | `emails.ts`, 1–14, 16–82 і 85–151 | поля інтерфейсів `CollaborationEmailData` і `ContactEmailData` | shared | Ні | Сповіщення має відображати нове поле. | Замовник (issue #1) |
| RULE-40 | Шаблон вставляє ім’я, пошту, телефон і повідомлення в HTML без екранування. Переноси рядка стають `<br>`. | `emails.ts`, 23, 51, 59, 92, 120 і 128 | літерали шаблону | shared | Так | | |
| RULE-41 | Лист англійською. Тип листа за замовчуванням `Contact`. Час у листі залежить від мови сервера. | `emails.ts`, 64, 79, 86, 133 і 148 | літерали, виклик `toLocaleString()` | shared | Так | | |
| RULE-42 | Успішна відповідь повертає назад усі дані форми, включно з телефоном. | `contact/route.ts`, 16; `collaboration/route.ts`, 17–23 | літерал `data: value` | API | Так | | |
