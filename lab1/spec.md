# Spec: Завдання 1 — ER-модель бронювання аудиторій

## Намір
Змоделювати дані, необхідні для системи бронювання навчальних аудиторій університету.

## Сутності
- User: user_id, full_name, email
- Building: building_id, name, address
- Room: room_id, building_id, room_number, capacity
- Booking: booking_id, user_id, room_id, starts_at, ends_at, purpose, status
- Equipment: equipment_id, name
- RoomEquipment: room_id, equipment_id, quantity

## Зв'язки
- Building 1:N Room
- User 1:N Booking
- Room 1:N Booking
- Room 1:N RoomEquipment
- Equipment 1:N RoomEquipment

Зв'язок Room M:N Equipment реалізується через RoomEquipment.

## Обмеження
- User.email повинен бути унікальним.
- У межах одного корпусу номер аудиторії не повинен повторюватися.
- starts_at повинен бути раніше за ends_at.
- RoomEquipment.quantity повинно бути більше нуля.
- Бронювання однієї аудиторії не повинні перетинатися в один і той самий час.

## Критерії прийняття
- [x] У моделі присутні всі сутності зі spec.
- [x] Для кожної сутності визначено первинний ключ.
- [x] Зовнішні ключі відповідають зв'язкам між сутностями.
- [x] Кардинальності відповідають словесному опису.
- [x] Зв'язок M:N між Room та Equipment реалізований через RoomEquipment.
- [x] Типи первинних та відповідних зовнішніх ключів узгоджені.
- [x] ER-модель не містить повторюваних груп або багатозначних атрибутів.
