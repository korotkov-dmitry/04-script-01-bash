# Домашнее задание к занятию "4.1. Командная оболочка Bash: Практические навыки"

## Обязательные задания

## 1. Есть скрипт:
	```bash
	a=1
	b=2
	c=a+b
	d=$a+$b
	e=$(($a+$b))
	```
	* Какие значения переменным c,d,e будут присвоены?
	* Почему?
	
Результат:

	`./lesson.sh
	a+b
	1+2
	3`
	
	`c = a+b` - так все переменный - строки;
	`d = 1+2` - переменные определены как строки, арифметической операции не произошло; 
	`e = 3`   - мы явно указали, что нужно сложить переменные, за счет скобок
## 2. На нашем локальном сервере упал сервис и мы написали скрипт, который постоянно проверяет его доступность, записывая дату проверок до тех пор, пока сервис не станет доступным. В скрипте допущена ошибка, из-за которой выполнение не может завершиться, при этом место на Жёстком Диске постоянно уменьшается. Что необходимо сделать, чтобы его исправить:
	```bash
	while ((1==1)
	do
	curl https://localhost:4757
	if (($? != 0))
	then
	date >> curl.log
	fi
	done
	```
Результат:

	`while ((1==1))
	do
	  curl https://localhost:4757
		if (($? != 0))
		  then
		    date >> curl.log
		  else break
		fi
		sleep 5
	done`
Доработки: 
- не хватало закрывающей скобки в условии цикла;
- для уменьшения частоты выполнения добавлен `sleep`;
- при успешном выполнении, добавлен оператор `break` для выхода из цикла.
## 3. Необходимо написать скрипт, который проверяет доступность трёх IP: 192.168.0.1, 173.194.222.113, 87.250.250.242 по 80 порту и записывает результат в файл log. Проверять доступность необходимо пять раз для каждого узла.
	`vagrant@vagrant:~$ cat checkhost.sh
	#!/usr/bin/env bash
	hosts=(192.168.0.1 173.194.222.113 87.250.250.242)
	timeout=5
	for i in {1..5}
	do
	date >>hosts.log
	    for h in ${hosts[@]}
	    do
		curl -Is --connect-timeout $timeout $h:80 > /dev/null
		echo "check" $h status=$? >>hosts.log
	    done
	done
	vagrant@vagrant:~$ cat hosts.log
	Sun 19 Dec 2021 05:27:37 AM UTC
	check 192.168.0.1 status=28
	check 173.194.222.113 status=0
	check 87.250.250.242 status=0
	Sun 19 Dec 2021 05:27:42 AM UTC
	check 192.168.0.1 status=28
	check 173.194.222.113 status=0
	check 87.250.250.242 status=0
	Sun 19 Dec 2021 05:27:48 AM UTC
	check 192.168.0.1 status=28
	check 173.194.222.113 status=0
	check 87.250.250.242 status=0
	Sun 19 Dec 2021 05:27:54 AM UTC
	check 192.168.0.1 status=28
	check 173.194.222.113 status=0
	check 87.250.250.242 status=0
	Sun 19 Dec 2021 05:27:59 AM UTC
	check 192.168.0.1 status=28
	check 173.194.222.113 status=0
	check 87.250.250.242 status=0`
## 4. Необходимо дописать скрипт из предыдущего задания так, чтобы он выполнялся до тех пор, пока один из узлов не окажется недоступным. Если любой из узлов недоступен - IP этого узла пишется в файл error, скрипт прерывается
	`vagrant@vagrant:~$ cat checkhost_break.sh
	#!/usr/bin/env bash
	hosts=(192.168.0.1 173.194.222.113 87.250.250.242)
	timeout=5
	br=0
	while (($br == 0))
	do
	date >>error.log
	    for h in ${hosts[@]}
	    do
		curl -Is --connect-timeout $timeout $h:80 > /dev/null
		br=$?
		if (($br != 0))
			then
				echo "error" $h status=$br >>error.log
				break
		fi
	    done
	done
	vagrant@vagrant:~$ cat error.log
	Sun 19 Dec 2021 06:53:22 AM UTC
	error 192.168.0.1 status=28`

