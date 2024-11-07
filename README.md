Обработчик ошибки для кафки имеет следующзую логику:

При возникновении ошибки при успешном вычитывании сообщения из кафки:
1. Если подключения к БД нет, то оффсет не будет двигаться и сообщение будет повторно обрабатываться до тех пор, пока либо успешно не обработается (с отключенной БД, если это возможно), либо подключение к БД не появится.
2. Если подключение к БД есть, то будет произведено n дополнительных раз обработать сообщение. Если сообщение всё равно не обработается спустя n попыток при наличии подключения к БД, то информация об этом сообщении + само сообщение будет сохранено в таблицу. А оффсет будет сдвинут вперёд, чтобы пропустить такую ззапись

При возникновении ошибки во время вычитывания сообщения из кафки:
Если это ошибка десериализации, то оффсет будет увеличен, а информация об ошибке + само сообщение будет сохранено.
Иначе ошибка не будет обработана, будет лишь выводиться запись в лог.

Можно добавить рест эндпоинт для того, чтобы производить повторные попытки обработки сообщений, сохранённых в таблицу с сообщениями с ошибками.