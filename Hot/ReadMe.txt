Пометки по работе
CMD
Отключение гибернации [экономия места]
powercfg -h off

Карантин почтового ящика [служит для защиты почтового сервера от каскадной аварии, вызванной падением одного ящика] 
Причины:
1) Дал сбой поток, который работает для почтового ящика.
2) 5 потоков, обрабатывающие почтовый ящик, никуда не сдвинулись за долгое время.
По умолчанию, если почтовый ящик был определен в качестве угрозы он будет находиться на карантине в течение 6 часов.
Что происходит, когда клиенты пытаются получить доступ к почтовому ящику находящемуся в карантине?

1)  Outlook с ящиком, который находистся в карантине перестает запускаться.
2)  При подключении через OWA выходит  сообщение A problem occurred while you were trying to use your mailbox .
3)  При перемещении ящика в другой mailbox происходит ошибка
4)    В логах можно увидеть следующую ошибку:
The mailbox for user {guid} : has been quarantined. Access to this mailbox will be restricted to administrative logons for the next 6 hours.
или
Почтовый ящик пользователя {guid} помещен в карантин. Доступ к этому почтовому ящику будет ограничен административными входами на следующие 6 часов.

Для оперделения находится ли почтовый ящик в карантине или нет используйте команду Get – MailboxStatistics.

Get-MailboxStatistics -identity Mail | FL Isquarantined
IsQuarantined : True
Mail – проверяемый эл. ящик

Как убрать почтовый ящик из карантина?

Подключаемся на сервер с ролью mailbox, открываем реестр:
HKLM\SYSTEM\CCS\Services\MSexchangeIS\{Servername}\Private-{dbguid}\Quarantined Mailboxes\

удаляем {Mailbox guid}
Перезапускаем службу Microsoft Exchange Information Store.

Что бы узнать {guid} почтового ящика, необходимо выполнить команду Get-MailboxStatistics -identity "<mail>" | FL

Что бы узнать {guid} почтовой базы, необходимо выполнить команду Get-MailboxDatabase -Identity "<database name>" | fl GUID

Что бы в дальнейшем такой ситуации не возникало заходим в реестр по пути HKLM\SYSTEM\CCS\Services\MSexchangeIS\{Servername}\Private-{dbguid}\QuarantinedMailboxes создаем параметр DWORD (32-bit) "MailboxQuarantineDurationInseconds"и указываем в нем время которое проведет ящик в карантине (в секундах).

Тем самым если ящик с этого mailbox попадет в карантин он пробудет там указное вами время (в данном примере 500 сек).

