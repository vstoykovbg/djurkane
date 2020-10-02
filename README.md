Джуркане на думи
================


От проекта [bgoffice](https://sourceforge.net/projects/bgoffice/files/For%20Developers/4.1/) взимаме думите, които не започват с главна буква и са над 4 букви:

```
$ cd bgoffice-4.1/bin/grammar
$ ./build_root_words.pl  | iconv -f WINDOWS-1251 -t UTF-8 | cut -d " " -f 1 | sort -u > bg-words-only-root.txt
$ cat bg-words-only-root.txt | grep -v '^[А-Я]' > думи-без-имена.txt
$ cat думи-без-имена.txt | sed -E 's/\b\w{1,4}\b//g' | sort -u > думи-без-имена-над-4-букви.txt
```

От [тук](https://github.com/hermitdave/FrequencyWords) взимаме 50-те хиляди най-популярни, извлечени от субтитри (може да съдържат и думи от други езици):

```
$ wget https://raw.githubusercontent.com/hermitdave/FrequencyWords/master/content/2016/bg/bg_50k.txt
```

Взимаме само думите от този списък:

```
$ cat bg_50k.txt | cut -d " " -f 1 > 50-хиляди-популярни-думи.txt
```

Добавяме малко квантова случайност в процеса на джуркане:


```
$ curl 'https://qrng.anu.edu.au/API/jsonI.php?length=10&type=hex16&size=256' > /dev/random
```


После вадим общите думи от двата списъка:

```
./intersection.py | sort -u > популярни-думи.txt
```

Със скрипта `random-words.py` може да си извадите случайни думи:

```
$ ./random-words.py популярни-думи.txt 
практичен
напосоки
змийски
окача
осъзнаване
престъпен
угодя
консерва
анулиране
яйцеклетка
издръжлив
масло
повръщане
година
деколте
хангар
химически
императрица
пристигам
ежедневие
```

Може и от по-големия списък:

```
$ ./random-words.py 50-хиляди-популярни-думи.txt 
превръзки
телма
мала
свикнем
приятелско
спестяванията
невидима
свързал
разрешаваме
бум
художникът
почитта
хувър
присъединиш
сканират
съоръжение
превъзходен
нет
влюбено
формуляри

```

Обаче тези думи са една идея по-трудни за запомняне, защото включват и думи от други езици, а също и измислени ad hoc думи, които се срещат само в една книга.

Скриптът за джуркане `random-words.py` разчита, че числата от `/dev/random` са достатъчно случайни (не иска от потребителя въвеждане на резултат от хвърляне на зарчета и т.н.).

Имайте предвид, че ако пускате системата от жив диск е възможно джуркането да не е станало качествено. Особено ако нямате `haveged`, процесорът няма функция да вади случайни числа (или не му вярвате, че ги вади [достатъчно случайни](https://www.google.com/search?channel=fs&client=ubuntu&q=RDRAND+NSA+conspiracy)) и т.н.


По-голям списък
===============

От тук взимаме списък с думи от Читанка:

```
$ wget https://raw.githubusercontent.com/aquilax/bg-words-dict/master/dict.txt
```

В списъка има и думи от други езици, редки думи (съдържащи се само в една книга) както и знакови низове, които трудно биха могли да се нарекат думи.

Филтрираме думите с големи букви и махаме думите с не повече от 4 букви:

```
$ grep '[а-я]' dict.txt | sed -E 's/\b\w{1,4}\b//g' | sort -u > голям-списък.txt
```

И пак има доста странни неща като думи съдържащи повторения на една буква.

```
$ ./random-words.py голям-списък.txt 
сладкохлебчета
многоспектралната
обяздеше
хохловския
налвигс
придобиех
балзакови
помаях
айделбах
бесах
щрайхер
кумии
учудената
зарче
бахарестан
копенхагенската
медпунктът
синкаво
минатото
некоординиране
```

Ако не бях разджуркал `/dev/random` нямаше да излезе думата `сладкохлебчета` сред случайните.

Ето ви начин да внесете квантова случайност в живота си. Разджуркайте `/dev/ranom` с квантова случайност и пуснете генератор на случайни думи.


