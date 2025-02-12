# KSC decoders and rules for Wazuh

### IMPORTANT: ###

> These rules and decoders primary designed for logs from а Kaspersky Security Center with russian localization. However this will work with different localizations anyway but decoded parameters may not be as detailed as it may be

### ВАЖНО: ###
Проверно с актуальными версиями Kaspersky Security Center, Kaspersky Endpoint Security для Windows и Kaspersky Endpoint Security для Linux (язык локализации RU). 

**Работает только при сборе логов с KSC через встроенный в Wazuh syslog server (https://documentation.wazuh.com/current/user-manual/capabilities/log-data-collection/syslog.html). Если вы собираете логи через syslog server в Linux, то параметров в квадратных скобках не будет**

В случае, если поддерживаемых событий недостаточно, можно просто продублировать правила, заменив ID событий на нужные из https://support.kaspersky.ru/kes12/246282

## Поддерживаемые события (Supported events)

**1. Критические (Critical):**

   - GNRL_EV_VIRUS_FOUND // Обнаружен вредоносный объект (локальные базы)
   - 0000003c // Возникла ошибка проверки зашифрованного соединения. Домен добавлен в список исключений
   - GNRL_EV_WEB_URL_BLOCKED // Доступ запрещен (локальные базы)
   - GNRL_EV_WEB_URL_BLOCKED_BY_KSN // Доступ запрещен (KSN)
   - 00000039 // Приложение установило соединение с сайтом с недоверенным сертификатом
   - GNRL_EV_OBJECT_NOTCURED // Лечение невозможно
   - GNRL_EV_ATTACK_DETECTED // Обнаружена сетевая атака
   - GNRL_EV_APPLICATION_LAUNCH_DENIED // Запуск приложения запрещен
   - GNRL_EV_DEVCTRL_DEV_PLUG_DENIED // Операция с устройством запрещена
   - 000000d1 // Автозапуск приложения выключен
   - 000000e7 // Обнаружена активная угроза. Требуется запуск процедуры лечения активного заражения
   - 00000139 // Невозможно удалить
   - GNRL_EV_VIRUS_FOUND_AND_REPORTED // Открыта опасная ссылка
   - GNRL_EV_VIRUS_FOUND_AND_PASSED // Обнаружена ранее открытая опасная ссылка
   - 00000803 // Клавиатура не авторизована
   - 00000898 // AMSI-запрос заблокирован
   - 00000329 // Сетевая активность запрещена | Сетевое соединение заблокировано 
   - 000002c6 // Запрещенный процесс был запущен до старта Kaspersky Endpoint Security для Windows
   - KLSRV_HOST_STATUS_CRITICAL // Статус устройства "Критический"

**2. Отказы функционирования (Functional failure):**

**3. Предупреждения (Warning):**

   - GNRL_EV_OBJECT_BLOCKED // Загрузка объекта запрещена | Запрещено
   - GNRL_EV_SUSPICIOUS_OBJECT_FOUND // Обнаружено легальное приложение, которое может быть использовано злоумышленниками для нанесения вреда компьютеру или данным пользователя (локальные базы | KSN)
   - GNRL_EV_OBJECT_CURED // Объект вылечен
   - GNRL_EV_OBJECT_DELETED // Объект удален
   - GNRL_EV_WEB_URL_WARNING // Предупреждение о нежелательном содержимом (локальные базы | KSN)
   - GNRL_EV_DEVCTRL_DEV_PLUG_DENIED // Подключение устройства заблокировано
   - 000000d3 // Самозащита приложения выключена
   - 000000d2 // Автоматическое обновление выключено
   - 000000d7 // Компьютер работает в безопасном режиме
   - 000000e8 // Запущена процедура лечения активного заражения
   - 000000e9 // Процедура лечения активного заражения завершена
   - 000007d1 // Обнаружена подозрительная сетевая активность
   - 00000abe // Сетевое соединение заблокировано
   - 00000140 // Объект зашифрован
   - 00000804 // Ошибка авторизации клавиатуры
   - 000002f2 // Осуществлен доступ к нежелательному содержимому после предупреждения | Активирован временный доступ к устройству
   - 000003f8 // Операция отменена пользователем
   - 000000d6 // Компоненты защиты выключены
   - KLSRV_EVENT_HOSTS_NOT_VISIBLE // Устройство долго не проявляет активности в сети
   - KLSRV_HOST_STATUS_WARNING // Статус устройства "Предупреждение"

**4. Информационные сообщения (Informational message):**
     
   - GNRL_EV_PASSWD_ARCHIVE_FOUND // Обнаружен защищенный паролем архив
   - GNRL_EV_DEVCTRL_DEV_PLUGGED // Устройство подключено
   - GNRL_EV_DEVCTRL_DEV_UNPLUGGED // Устройство отключено
   - KLAUD_EV_SERVERCONNECT // Аудит (подключение к Серверу администрирования)
   - KLSRV_UPD_BASES_UPDATED // Базы обновлены
   - KLAUD_EV_TASK_STATE_CHANGED // Аудит: Изменение статуса объекта
   - KLSRV_HOST_MOVED_WITH_RULE_EX // Устройство автоматически добавлено в группу
   - KLAUD_EV_ADMGROUP_CHANGED // Аудит: Изменение параметров группы

## Интеграция с MITRE ATT&CK (MITRE ATT&CK integration)
![123](https://github.com/tread-lightly/KSC_decoders_and_rules_for_Wazuh_ru_RU/assets/125260132/dd3dae2f-edee-4d4b-a16c-184ab91edd6d)

## Пример функционирования (processing example):
Для красивой обработки dstuser в соответствующий декодер нужно добавить имя домена:

```<regex>DOMAIN\\\\(\.*)"</regex>```

Исходный лог:

```
1 2024-02-19T13:18:51.000Z EXAMPLE-PC KES|11.0.0.0 - GNRL_EV_OBJECT_BLOCKED [event@11111 p1="1111111111111111111111111111111111111111111111111111111111111111" p2="https://BAD.SITE" p5="not-a-virus:HEUR:AdWare.Script.Pusher.gen" p7="DOMAIN\\USER" p8="65" p9="{\"engine\":3,\"method\":0,\"blacklist\":false,\"cloud_sb\":false,\"md5\":\"11111111111111111111111111111111\"}" et="GNRL_EV_OBJECT_BLOCKED" tdn="Защита от веб-угроз" etdn="Загрузка объекта запрещена" hdn="EXAMPLE-PC" hip="10.10.10.10" gn="Управляемые устройства" kscfqdn="KES.EXAMPLE"] ﻿Описание результата: Запрещено\r\nТип: Содержит рекламное приложение, приложение автодозвона или легальное приложение, которое может быть использовано злоумышленниками для нанесения вреда компьютеру или данным пользователя\r\nНазвание: not-a-virus:HEUR:AdWare.Script.Pusher.gen\r\nПользователь: DOMAIN\USER (Инициатор)\r\nОбъект: https://BAD.SITE\r\nДата выпуска баз: 19.02.2024 13:31:00\r\nSHA256: 1111111111111111111111111111111111111111111111111111111111111111\r\nMD5: 11111111111111111111111111111111
```
Результат обработки:

![a](https://github.com/tread-lightly/KSC_decoders_and_rules_for_Wazuh_ru_RU/assets/125260132/797b7f4d-6b73-4073-8b6c-d9ab54ad5c71)
