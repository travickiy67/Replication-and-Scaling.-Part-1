# Травицкий Сергей
# Домашнее задание к занятию «Репликация и масштабирование. Часть 1»

### Инструкция по выполнению домашнего задания

1. Сделайте fork [репозитория c шаблоном решения](https://github.com/netology-code/sys-pattern-homework) к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw).
2. Выполните клонирование этого репозитория к себе на ПК с помощью команды `git clone`.
3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
   - впишите вверху название занятия и ваши фамилию и имя;
   - в каждом задании добавьте решение в требуемом виде: текст/код/скриншоты/ссылка;
   - для корректного добавления скриншотов воспользуйтесь инструкцией [«Как вставить скриншот в шаблон с решением»](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md);
   - при оформлении используйте возможности языка разметки md. Коротко об этом можно посмотреть в [инструкции по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md).
4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`).
5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
6. Любые вопросы задавайте в чате учебной группы и/или в разделе «Вопросы по заданию» в личном кабинете.

Желаем успехов в выполнении домашнего задания.

---

### Задание 1

На лекции рассматривались режимы репликации master-slave, master-master, опишите их различия.

*Ответить в свободной форме.*

<details>
<summary>Ответ 1</summary>

**Режим master-slave, подразумевает наличие главного сервера, на котором хранитца эталонная копия БД. Это подразумевает, что изменения могут вносится только на этом сервере и после этого реплицироваться на все slave сервера**

**Режим master-master, это когда несколько серверов являююются и master и slave одновремено. На всех можно вносить изменения в БД, но вслучае если изменения вносятся одновременно и меняются одни и теже данные, не понятно что сохранять, может возникнуть конфликт.**
- Плюс: изменения можно вносить на разных серверах.  
- Минус: не возможно вносить изменения синхронно.  

<details>


---

### Задание 2

Выполните конфигурацию master-slave репликации, примером можно пользоваться из лекции.

*Приложите скриншоты конфигурации, выполнения работы: состояния и режимы работы серверов.*

<details>
<summary>Ответ 2</summary>

*Зарвботала далеко не с первого раза*  
 
**На сервере master**  

```
CREATE USER 'replication'@'%'IDENTIFIED WITH mysql_native_password BY '24101967cO';
GRANT REPLICATION SLAVE ON *.* TO 'replication'@'%'
SHOW GRANTS FOR replication@'%';   # Проверяем права
```

**Редактируем файл /etc/mysql/my.cnf и перезагружаем сервер**  

```
bind-address=0.0.0.0
server_id = 1
log_bin = mysql-bin
```
**На сервере slave, Редактируем файл /etc/mysql/my.cnf и перезагружаемся**  

```
bind-address=0.0.0.0
log_bin = mysql-bin
server_id = 2
relay-log = /var/lib/mysql/mysql-relay-bin
relay-log-index = /var/lib/mysql/mysql-relay-bin.index
read_only = 1
```

```
CHANGE MASTER TO MASTER_HOST='192.168.0.4', MASTER_USER='replication', MASTER_PASSWORD='24101967cO', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=157;
START SLAVE;
SHOW SLAVE STATUS\G;
```
*И наконец заработало. В первых вариантах я не указывал в конфигурации bind-address=0.0.0.0*

**Скрины**

*Наытройка на master*

![img](https://github.com/travickiy67/Replication-and-Scaling.-Part-1/blob/main/img.1.7.png)  

*На slave*  

![img](https://github.com/travickiy67/Replication-and-Scaling.-Part-1/blob/main/img.1.8.png)  

*Остальные скрины демонстрирующие создание и удаление вазы и таблицы*

![img](https://github.com/travickiy67/Replication-and-Scaling.-Part-1/blob/main/img.1.1.png)  
---

![img](https://github.com/travickiy67/Replication-and-Scaling.-Part-1/blob/main/img.1.2.png)  
---

![img](https://github.com/travickiy67/Replication-and-Scaling.-Part-1/blob/main/img.1.4.png)  
---

![img](https://github.com/travickiy67/Replication-and-Scaling.-Part-1/blob/main/img.1.3.png)  
---

![img](https://github.com/travickiy67/Replication-and-Scaling.-Part-1/blob/main/img.1.5.png)  
---

![img](https://github.com/travickiy67/Replication-and-Scaling.-Part-1/blob/main/img.1.6.png)  

</details>

---

## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

---

### Задание 3* 

Выполните конфигурацию master-master репликации. Произведите проверку.

*Приложите скриншоты конфигурации, выполнения работы: состояния и режимы работы серверов.*
