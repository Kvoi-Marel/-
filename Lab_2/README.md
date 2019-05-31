## Лабораторная работа 2

### Задание 1 (Установка ОС и настройка LVM, RAID)
1.Создание новой виртуальной машины, выдав ей следующие характеристики:
* 1 gb ram
* 1 cpu
* 2 hdd (назвав их ssd1, ssd2 и назначил равный размер, поставив галочки hot swap и ssd)
* SATA контроллер настроен на 4 порта
![](Image1/Screenshot_1.png)

2.Начало установки Linux:

![](Image1/NPgiMGkrVoo.jpg)
* Настройка отдельного раздела под /boot: Выбрав первый диск, создал на нем новую таблицу разделов
* Partition size: 512M
* Mount point: /boot
* Повторил настройки для второго диска, выбрав mount point:none
![](Image1/VirtualBox_ded_06_04_2019_10_53_27.png)
* Настройка RAID
* Выбрал свободное место на первом диске и настроил в качестве типа раздела physical volume for RAID
* Выбрал "Done setting up the partition"
* Повторил настройку для второго диска
![](Image1/VirtualBox_ded_06_04_2019_11_01_28.png)
* Выбрал пункт "Configure software RAID"
* Create MD device
* Software RAID device type: Выберал зеркальный массив
* Active devices for the RAID XXXX array: Выбрал оба диска
* Spare devices: Оставил 0 по умолчанию
* Active devices for the RAID XX array: Выбрал разделы, которые создавал под raid
* Finish
* В итоге получил: 
![](Image1/VirtualBox_ded_06_04_2019_11_02_11.png)
* Настройка LVM: Выбрал Configure the Logical Volume Manager
* Keep current partition layout and configure LVM: Yes
* Create volume group
* Volume group name: system
* Devices for the new volume group: Выбрал созданный RAID
* Create logical volume
* logical volume name: root
* logical volume size: 2\5 от размера диска
* Create logical volume
* logical volume name: var
* logical volume size: 2\5 от размера диска
* Create logical volume
* logical volume name: log
* logical volume size: 1\5 от размера диска
* Завершив настройку LVM увидел следующее: 
![](Image1/VirtualBox_ded_06_04_2019_11_09_24.png)
* Разметка разделов: по-очереди выбрал каждый созданный в LVM том и разметил их, например, для root так:
* Use as: ext4
* mount point: /
* повторил операцию разметки для var и log выбрав соответствующие точки монтирования (/var и /var/log), получив следующий результат:
![](Image1/VirtualBox_ded_06_04_2019_11_16_33.png)
3.Закончил установку ОС, поставив grub на первое устройство (sda) и загрузил систему.
4.Выполнил копирование содержимого раздела /boot с диска sda (ssd1) на диск sdb (ssd2)
5.Выполнил установку grub на второе устройство: 
* Посмотрел информацию о текущем raid командой cat /proc/mdstat (не было своего скриншота): 
![](Image1/VirtualBox_ded_06_04_2019_11_24_38.png)
Увидел, что активны два raid1 sda2[0] и sdb2[1]
* Выводы команд: pvs, vgs, lvs, mount: 
![](Image1/VirtualBox_ded_06_04_2019_11_29_29.png)
* С помощью этих команд увидел информацию об physical volumes, volume groups, logical volumes, примонтированных устройств.
#### Вывод
В этом задании научился устанавливать ОС Linux, настраивать LVM и RAID, а также ознакомился с командами:
* lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT
* fdisk -l
* pvs,lvs,vgs
* cat /proc/mdstat
* mount
* dd if=/dev/xxx of=/dev/yyy
* grub-install /dev/XXX
* В результате получил виртуальную машину с дисками ssd1, ssd2.
### Задание 2 (Эмуляция отказа одного из дисков)
1.Удаление диска ssd1 в свойствах машины.

2.Проверка работоспособности виртуальной машины.

3.Проведена перезагрузка.

4.Проверка статуса RAID-массива командой cat /proc/mdstat. 
![](Image2/CBGJrpuzskU.jpg)
5.Добавление в интерфейсе VM нового диска такого же размера с названием ssd3.

6.Выполнение операций:
* Просмотр нового диска, что он приехал в систему командой fdisk -l
* Копирование таблиц разделов со старого диска на новый: sfdisk -d /dev/XXXX | sfdisk /dev/YYY
* Добавление в рейд массив нового диска: mdadm --manage /dev/md0 --add /dev/YYY
* Результат:
![](Image2/CBGJrpuzskU.jpg)
7.Выполение синхронизации разделов, не входящих в RAID

8.Установка grub на новый диск 
![](Image2/rcqWGFEyOQA.jpg)

9.Перезагрузка ВМ и проверка, что все работает 

![](Image2/4qUEwnIoSAc.jpg)
#### Вывод
В этом задании научился:

* Удалять диск ssd1
* Проверять статус RAID-массива
* Копировать таблицу разделов со старого диска на новый
* Добавлять в рейд массив новый диск
* Выполнять синхронизацию разделов, не входящих в RAID
Изучил новые команды:

* sfdisk -d /dev/XXXX | sfdisk /dev/YYY
* mdadm --manage /dev/md0 --add /dev/YYY
Результат: Удален диск ssd1, добавлен диск ssd3, ssd2 сохранили

### Задание 3 (Добавление новых дисков и перенос раздела)
1.Эмулирую отказа диска ssd2, удалив из свойств ВМ диск и перезагрузившись

2.Текущее состояние дисков и RAID: 
![](Image3/Fw0U58DRQIU.jpg)

4.Добавляю ssd4 

![](Image3/Qs6_goPoOvY.jpg)

5.Перенос данных с помощью LVM.
* Копирование файловую таблицу со старого диска на новый
![](Image3/yk65KORmgwI.jpg)
* Копирование данных /boot на новый диск
![](Image3/JIlxrTGDgYY.jpg)
* Перемонтировака /boot на живой диск

![](Image3/GfAqX9vQvJg.jpg)

* Установка grub на новый диск Grub устанавливаем, чтобы могли загрузить ОС с этого диска

![](Image3/hR7C_vBF3Q0.jpg)

* Создание нового RAID-массива с включением туда только одного нового ssd диска:

![](Image3/BJt0CiMqSmI.jpg)

* Результат 

![](Image3/UDs7_NRDpiE.jpg)

Появился /dev/md63

6.Настройка LVM
* Выполнение команды pvs для просмотра информации о текущих физических томах
![](Image3/nF1f_1IJoXE.jpg)
* Создание нового физического тома, включив в него ранее созданный RAID массив:
* После выполнения команд lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT и pvs яувидел, что к md63 добавился FSTYPE - LVM2_member, так же dev/md63 добавился к результату команды pvs.
* Увеличение размера Volume Group system
* Выполнение команд
vgdisplay system -v
pvs
vgs
lvs -a -o+devices
![](Image3/J5ReFDfxEaI.jpg)
![](Image3/WyRRrq6q3cg.jpg)

var,log,root находятся на /dev/md0
* Перемещение данных со старого диска на новый
![](Image3/DeyoQ5ta4ec.jpg)
* Изменение VG, удалив из него диск старого raid.
* Выполнение команд:

![](Image3/01YT1eGHSLQ.jpg)
![](Image3/oJtXancnTWM.jpg)
![](Image3/tGECVu0AdZk.jpg)

В выводе команды pvs у /dev/md0 исчезли VG и Attr. В выводе команды vgs #PV - уменьшилось на 1, VSize, VFree - стали меньше
7.Удаление ssd3 и добавление ssd5,hdd1,hdd2.

8.Полсе добавления дисков:
![](Image3/Xoh9xlz_zkM.jpg)

9.Восстановление работы RAID массива:

* Копирование таблицы разделов:
10.Копирование загрузочного раздела /boot с диска ssd4 на ssd5
![](Image3/7iCu2pWKflk.jpg)

11.Установка grub на ssd5 

12.Изменение размера второго раздела диска ssd5 
![](Image3/VY1d-xxs3jA.jpg)
![](Image3/JOsp1Yi8QEQ.jpg)
![](Image3/9XaVw-pQfmg.jpg)

13.Перечитывание таблицы разделов
* Добавление нового диска к текущему raid массиву
![](Image3/gRcdnnwIqUQ.jpg)
* Расширение количество дисков в массиве до 2-х штук:
14.Увеличение размера раздела на диске ssd4 
![](Image3/ZqKzbum19sI.jpg)
![](Image3/w4xKRcPSs6E.jpg)

15.Перечитаем таблицу разделов

16.Расширение размера raid 

![](Image3/YcBDimZolP0.jpg)

Размер md127 изменился, теперь он равен 5,8G.

17.Расширим размер PV. 
![](Image3/7hJnvZTwoUk.jpg)

18.Добавление вновь появившееся место VG var,root. 
![](Image3/ApI4Vbc1KtQ.jpg)
![](Image3/VtXVyvJJ_ig.jpg)
![](Image3/xGSvePVqn7Y.jpg)

19.Создание LVM на новых hdd.
Имена новых hhd дисков: 
![](Image3/xGSvePVqn7Y.jpg)
* Создание RAID массива 
![](Image3/RohHYYz8DyQ.jpg)
* Создание нового PV на рейде из больших дисков
* Создание в этом PV группу с названием data
* Создание логического тома с размером всего свободного пространства и присвоением ему имени var_log
![](Image3/cmXpJD9oCSE.jpg)
* Отформатирование созданного раздела в ext4
* Результат: 

![](Image3/9Hj3I5GjDyQ.jpg)

20.Перенос данных логов со старого раздела на новый
* Примонтирование временно нового хранилище логов
* Выполнение синхронизации разделов 
![](Image3/UsCyzTZBJA4.jpg)
![](Image3/gI3DEYXMnSs.jpg)
![](Image3/Xn9NAoRi_zU.jpg)
* Процессы работающие с /var/log 
![](Image3/oUONcUcNJFQ.jpg)
* Остановка этих процессов

![](Image3/KQ7WyIbWJBc.jpg)
* Выполнение финальной синхронизации разделов
* Поменяем местами разделы
* Результат: 

![](Image3/B-p792rPZEI.jpg)

21.Правим /etc/fstab. 
![](Image3/u2Ek459s8Kc.jpg)
22.Финальный аккорд(проверка всего)
![](Image3/A-IiRc3RIbs.jpg)
![](Image3/blJxLiWQBs0.jpg)
![](Image3/iIU8TnBMxbI.jpg)