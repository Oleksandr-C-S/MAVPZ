# Специфікація архітектури — Завдання 5

**Студент:** Чакун Олександр  
**Група:** ІМ-о51  
**Предметна область:** бронювання навчальних аудиторій

## 1. Для чого потрібна діаграма

У завданні моделюється архітектура для UC-02 «Створити бронювання». Діаграма потрібна, щоб відокремити інтерфейс користувача, логіку бронювання, роботу з БД та зовнішню автентифікацію. Вона показує контракти між компонентами, а не реалізацію всіх методів.

## 2. Компоненти

| Компонент | Відповідальність |
|---|---|
| Web UI | Отримання даних від користувача й показ результату |
| Booking API | Прийом запитів UI та передача їх сервісу |
| BookingService | Керування UC-02: перевірка сесії, доступності, вибір гілки створення або відмови |
| AvailabilityService | Попередня перевірка вільних аудиторій для пошуку |
| AuthenticationAdapter | Виклик зовнішнього університетського сервісу автентифікації |
| BookingRepository | Початок транзакції, блокування аудиторії, повторна перевірка, INSERT, commit/rollback |
| RoomRepository | Читання інформації про аудиторії |
| Booking Database | Збереження User, Building, Room, Booking, BookingStatus, Equipment, RoomEquipment |

Користувач працює через Web UI. Зовнішній університетський сервіс автентифікації перебуває за межею нашої системи.

## 3. Інтерфейси

| Контракт | Надає (provided) | Використовує (required) |
|---|---|---|
| `IBookingApi` | Booking API | Web UI |
| `IBookingService` | BookingService | Booking API |
| `IAvailabilityService` | AvailabilityService | BookingService |
| `IAuthentication` | AuthenticationAdapter | BookingService |
| `IBookingRepository` | BookingRepository | BookingService, AvailabilityService |
| `IRoomRepository` | RoomRepository | BookingService, AvailabilityService |

У PlantUML `..|>` позначає реалізацію контракту (provided), а `..>` — залежність від контракту (required).

## 4. Як проходить UC-02

Після підтвердження даних `BookingService` перевіряє сесію через `IAuthentication`. `AuthenticationAdapter` викликає `validateSession(session)` і повертає `valid(userId)`, `invalid` або `timeout`. При відмові чи тайм-ауті бронювання не створюється.

Попередній пошук доступних аудиторій не резервує місце. Для підтвердження `BookingService` викликає `BookingRepository.beginAtomicBooking(roomId, start, end)`. Репозиторій виконує `beginTransactionAndLockRoom(roomId)` та `findBlockingBookings(...)`, повертаючи `availabilityResult`. Якщо вільно, `BookingService` окремо викликає `insertBooking(..., Pending)`, після чого репозиторій записує бронювання й виконує commit. Якщо конфлікт — `BookingService` викликає `rollbackTransaction()`. Це відповідає послідовності Lab3, а не єдиній операції `createBookingAtomically(...)`.

Блокуючі статуси визначаються через `BookingStatus.blocks_room`. Інтервали часу трактуються як `[start, end)`: 10:00–11:00 і 11:00–12:00 не конфліктують. Блокування рядка Room — один із можливих способів виконати REQ-02A, а не вибір конкретної СУБД.

## 5. Критерії прийняття

- Є всі основні компоненти, БД і зовнішня система за межею застосунку.
- Шість інтерфейсів мають одного постачальника та явних споживачів.
- Створення бронювання у Lab5 узгоджується з викликами Lab3.
- Уточнені результати автентифікації з Lab4.
- БД охоплює сутності з Lab1, включно з Building.
- Для REQ-02A повторна перевірка та запис залишаються в одній транзакції.
- `components.puml` компілюється в PlantUML, а `components.png` згенерований із тієї ж версії файлу.

## 6. Межі моделі

Це компонентна схема, тому вона не визначає конкретну СУБД, HTTP-маршрути, повні сигнатури всіх методів або спосіб розгортання. Напряму зовнішню автентифікацію Lab3 показує на рівні сценарію, а тут для неї виділений окремий адаптер.
