# Завдання 5 — Архітектура (Component Diagram)

**Студент:** Чакун Олександр  
**Група:** ІМ-о51  
**Тема:** бронювання навчальних аудиторій

Компонентна UML-діаграма описує архітектурне рішення для **UC-02 «Створити бронювання»**. Основна вимога — повторна перевірка доступності й запис у межах однієї транзакції (REQ-02A).

## Основні частини

- Web UI, вхідний HTTP-порт і Booking API — приймання запиту.
- BookingService — координує сценарій; AvailabilityService — попередня перевірка.
- AuthenticationAdapter і вихідний порт Auth — зв'язок із зовнішнім університетським компонентом `<<external>>` через `ISessionValidation`.
- BookingRepository — блокування, перевірка та запис/відкат у транзакції; RoomRepository — читання аудиторій.
- Booking Database — окрема БД із сутностями Lab1, зокрема `Building`.

## Файли

| Файл | Призначення |
|---|---|
| [`model/components.puml`](model/components.puml) | Джерело UML Component Diagram |
| [`renders/components.png`](renders/components.png) | Зображення, яке слід експортувати з поточної версії PlantUML |
| [`spec.md`](spec.md) | Намір, компоненти, контракти, критерії прийняття та межі обсягу |
| [`AUDIT.md`](AUDIT.md) | Перевірка відповідності і розбіжності |
| [`DEFENSE.md`](DEFENSE.md) | Аргументація архітектурних рішень |
| [`ai/prompts.md`](ai/prompts.md) | Документація фактичного використання ChatGPT |

## Зв'язок із Lab1–Lab4

- **Lab1:** сутності `User`, `Building`, `Room`, `Booking`, `BookingStatus`, `Equipment`, `RoomEquipment`.
- **Lab2:** UC-02 та REQ-02A. UC-03–UC-05, NFR-04 та REQ-07 тут поза обсягом.
- **Lab3:** BookingService керує `beginAtomicBooking` і потім `insertBooking` або `rollbackTransaction`; додаткові компоненти в Lab5 — нове архітектурне деталізування, а не учасники старої sequence-діаграми.
- **Lab4:** враховано `valid(userId)`, `invalid`, `timeout`.

## Аудит

Виправлення AUD-01 (`Building`), AUD-02 (окремі інтерфейси) та AUD-03 (транзакційна межа) описано в [`AUDIT.md`](AUDIT.md). Зовнішня інтеграція й порти уточнені за відгуком перевірки.
