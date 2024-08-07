---
created: [" 01-05-2023 23:43"]
aliases: []
tags:
- article/
---

# Модель TCP/IP

Модель TCP/IP (Transmission Control Protocol/Internet Protocol) - это сетевая модель, которая описывает процесс передачи данных в компьютерных сетях. Эта модель была разработана для описания работы Интернета и является основой для его функционирования.

Модель TCP/IP состоит из четырех уровней:

1.  Уровень доступа к сети (Network Access Layer) - этот уровень определяет способ передачи данных между сетевыми устройствами, такими как сетевые карты, маршрутизаторы и коммутаторы. На этом уровне работают такие протоколы, как Ethernet, Wi-Fi, Bluetooth и другие.
    
2.  Интернет-уровень (Internet Layer) - этот уровень отвечает за передачу данных между различными сетями. На этом уровне работает протокол IP (Internet Protocol).
	1. IP адреса - уникальный адрес в сети
	2. физические девайсы
	3. name resolution
	4. subnets
    
3.  Транспортный уровень (Transport Layer) - этот уровень отвечает за доставку данных между приложениями на различных узлах сети. На этом уровне работают два протокола: TCP (Transmission Control Protocol) и UDP (User Datagram Protocol).
	1. TCP разбивает на пакеты и гарантирует то что все пакеты до пользователя отправлены и дошли в том же порядке что и отправили, иначе отправит заново (пример - веб-страница)
	2. UDP - разбивает на пакеты и не гарантирует что они дойдут (пример - передача звука и изображения в видеозвонках)
    
4.  Прикладной уровень (Application Layer) - на этом уровне работают различные приложения, такие как веб-браузеры, почтовые клиенты, FTP-клиенты и другие. На этом уровне работают такие протоколы, как HTTP (HyperText Transfer Protocol), HTTPS, FTP (File Transfer Protocol), SMTP (Simple Mail Transfer Protocol) и другие.
    

Модель TCP/IP является более простой, чем модель OSI, и имеет меньше уровней. Однако она была разработана для работы в реальном мире и является основой для функционирования Интернета и многих других сетей.

