# How to prevent XXE vulnerabilities

Практически все уязвимости XXE возникают из-за того, что библиотека синтаксического анализа XML приложения поддерживает потенциально опасные функции XML, которые приложению не нужны или не собираются использоваться. Самый простой и эффективный способ предотвратить атаки XXE — отключить эти функции.

Как правило, достаточно отключить разрешение внешних сущностей и отключить поддержку XInclude. Обычно это можно сделать с помощью параметров конфигурации или путем программного переопределения поведения по умолчанию. Обратитесь к документации по вашей библиотеке синтаксического анализа XML или API, чтобы узнать, как отключить ненужные возможности.
