# VOROTEX AK820 PRO WebHID Lab

Экспериментальный read-only WebHID-инспектор и исследовательский репозиторий для VOROTEX AK820 PRO.

## Цель проекта

Разобрать HID-протокол клавиатуры достаточно глубоко, чтобы со временем получить собственный браузерный конфигуратор без замены штатной прошивки.

На первом этапе работаем только с безопасным чтением и наблюдением:

- VID/PID и HID collections;
- Input/Output/Feature Report inventory;
- Feature Reports 5, 6 и 9 через `receiveFeatureReport()`;
- live input events для исследования энкодера и дополнительных управляющих событий;
- JSON export сессии для последующего сравнения;
- сопоставление поведения с официальным VOROTEX/BYCOMBO4 конфигуратором и открытыми reverse-engineering проектами.

## Safety boundary

До отдельного подтверждения протокола проект считается **read-only**.

Не используем и не добавляем операции записи в клавиатуру без доказанного mapping и recovery path:

- `sendReport()`;
- `sendFeatureReport()`;
- firmware flashing;
- ISP writes.

Не следует нажимать Save/Apply в сторонних конфигураторах, которые лишь совпадают по VID/PID или HID descriptor, пока их flash layout не подтверждён для AK820 PRO.

## Подтверждённая сигнатура тестируемого VOROTEX AK820 PRO

По официальному ПО VOROTEX, сохранённому WebHID descriptor и живым проверкам:

- product name: `AK820 PRO`;
- wired VID: `0x258A`;
- wired PID: `0x019D`;
- vendor collection: Usage Page `0xFF02`, Usage `0x0002`;
- Feature Report `0x09`: 519-byte descriptor payload;
- дополнительные Feature Reports `0x05` и `0x06`;
- официальный конфигуратор относится к OEM/BYCOMBO4 software family;
- аппаратная модель: 81 keys + encoder + 128×128 TFT;
- Chromium/WebHID способен открыть vendor-defined collection устройства.

## Что уже проверено

### SuperFrame Phantom Editor

Phantom Editor видит и открывает AK820 PRO. Совпадает транспортная сигнатура:

`258A:019D + FF02:0002 + Feature Report 9 / 519 bytes`.

Это сильный признак общего USB/HID stack, но **не доказательство одинакового firmware image или flash layout**. UI Phantom рассчитан на другую раскладку и не содержит настройки TFT/энкодера AK820 PRO.

### EPOMAKER Hub / Cypher 81

Cypher 81 визуально и функционально близка к AK820 PRO по форм-фактору, экрану и энкодеру, однако EPOMAKER Hub не предлагает тестируемую AK820 PRO в списке подключаемых устройств. Как готовый drop-in web driver этот путь не подтвердился.

## Рабочие гипотезы

- Feature Report 9 является сильным кандидатом на keymap/config transport из-за совпадения с исследованным SuperFrame Phantom transport.
- Feature Report 6 на AK820 PRO необычно большой и может участвовать в передаче крупных блоков состояния/данных. Связь с TFT пока **не подтверждена**.
- Энкодер можно картографировать безопасно через входящие HID reports без операций записи.
- Общая OEM software family BYCOMBO4/AULA/SinoWealth полезна как источник структуры протокола, но команды и flash layout должны проверяться именно на AK820 PRO.

## План

1. Read-only WebHID inventory и экспорт сессий.
2. Дифф входящих reports: idle / encoder left / encoder right / encoder press.
3. Сопоставление матрицы 81 keys + encoder с официальным `KB.ini`.
4. Mapping Base / FN1 / FN2 / Tap.
5. Mapping RGB.
6. Mapping TFT/GIF transport.
7. Только после подтверждения mapping и recovery path переходить к контролируемым write tests.
8. Итоговая цель: собственный web configurator для AK820 PRO без перепрошивки.

## References / donors

Исследование использует открытые проекты как доноров идей и протокольных наблюдений:

- `NotJustAnna/sfphantom` — WebHID transport для `258A:019D`, `FF02:02`, Feature Report 9 / 519;
- `carlossless/sinowisp` / SinoWealth ecosystem — platform-family identification;
- AULA/BYCOMBO4 reverse engineering — близкая OEM software family;
- официальный VOROTEX AK820 PRO package — model-specific VID/PID, `KB.ini`, экран, слои и параметры устройства.

## Important non-conclusions

- Совпадение VID/PID не означает совместимость firmware.
- Совпадение Report ID и размера не означает совместимость flash layout.
- Не следует прошивать AK820 PRO firmware от AULA, AJAZZ, Cypher 81, Phantom или других родственных устройств.
