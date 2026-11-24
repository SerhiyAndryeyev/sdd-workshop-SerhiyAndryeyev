# Нотатки PoC: що я побачив у запущеному застосунку

## Проба 1: обов'язковий телефон

### Очікувані файли (День 2, «спершу своє»)

За таблицею правил і аналізом впливу (RULE-08, RULE-09, RULE-19, RULE-20, AC-01, AC-03) очікував змінити:

- `app/shared/components/forms/contact-form/ContactForm.tsx` — прибрати `.optional()` і допуск порожнього телефону в Zod; додати текст помилки «обов’язкове поле».
- `app/lib/utils/validateContactData.ts` — відхиляти відсутній або порожній `phoneNumber`, а не пропускати перевірку.
- `app/constants/errors.ts` — повідомлення API на кшталт `Phone number is required.` (AC-03).
- `i18n/messages/en.json` і `i18n/messages/uk.json` — зірочка в мітці телефону та ключ `phoneNumberRequired` у `contactForm.errors`.
- Тести `validateContactData.test.ts` і `ContactForm.test.tsx` — можуть впасти, якщо сценарії розраховані на необов’язковий телефон (це окрема знахідка, не обов’язково правити в PoC).

Не чіпав би в першій ітерації: `useHandlePhoneInput.ts`, `validation.ts` (regex/довжини вже для валідного номера), шаблони листів (`emails.ts`) — там телефон уже опційно в листі, поки поле заповнене.

Що змінив: ті самі 5 файлів, що очікував (`ContactForm.tsx`, `validateContactData.ts`, `errors.ts`, `en.json`, `uk.json`): обов’язковий телефон на UI й API, текст `Phone number is required.`, мітка «Phone number *».

Що побачив у браузері: на `/en/contacts` біля поля — «Phone number *». Без телефону, з іншими полями заповненими, після «Send request» форма не відправилась; під телефоном червоне «This field is required». Вікна подяки не було.

Що показали тести: `validateContactData.test.ts` — 1 червоний: `should return no errors for valid input` (дані без `phoneNumber`, тепер `Phone number is required.`). `ContactForm.test.tsx` — 1 червоний: `should call onSubmit callback when submit button is clicked` (submit без телефону, `onSubmit` 0 разів). Решта зелені.

## Проба 2: мінімальна довжина повідомлення

Очікував 4 місця (орієнтир інструкції): `validation.ts`, `ContactForm.tsx`, `en.json`, `uk.json` — мінімум 10 → 5.

Що змінив: `MESSAGE_MIN_LENGTH: 5` у `app/constants/validation.ts`; `.min(5, …)` у `ContactForm.tsx`; тексти `messageMinLength` у `en.json` і `uk.json` (тепер «at least 5» / «щонайменше 5»). Сервер підхопив константу; окремо `validateContactData.ts` не чіпав.

Що побачив у браузері: повідомлення з 6 символів (`123456`), валідний телефон і згода — форма відправилась, з’явилось вікно «ThAnK YoU / Your request has been sent». Помилки довжини для 6 символів не було.

Що показали тести: у `app/lib/utils` — 2 червоні в `validateContactData.test.ts`: (1) `should return no errors for valid input` — без телефону (від проби 1); (2) `should return error if message is too short` — тест передає `Too short` (9 символів), після зниження мінімуму до 5 це вже «валідна» довжина, тест очікує помилку, її немає; додатково спливає `Phone number is required.`.