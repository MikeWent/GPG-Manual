# GPG для самых маленьких

## Дисклеймер

Это руководство — шпаргалка и быстрый how-to. Не является официальным документом и не претендует на 100% правильность. Официальные руководсва и документацию можно найти [тут](https://www.gnupg.org/documentation/index.html).

## Определения

### Приватный ключ

Штука, с помощью которой производится _дешифрование отправленных вам_ файлов. **❗️ Приватный ключ давать никому нельзя**

### Публичный ключ

Противоположность приватному, с помощью него производится _шифрование отправляемых вам_ файлов. Публичный ключ можно давать кому угодно, более того, это _следует делать_.

### PGP

Pretty Good Privacy — штука, которая использует ассиметричное и симметричное шифрование для обмена файлами, создания подписи и прочих ништяков. [Wikipedia](https://en.wikipedia.org/wiki/Pretty_Good_Privacy)

### GPG

GnuPG — GNU Privacy Guard — софтина, которая умеет в PGP. Опен-сорц, бесплатно, во всех традициях. Из оф. рук-ва:
> GnuPG is cryptographic software that helps people ensure the confidentiality, integrity and assurance of their data...
[Wikipedia](https://en.wikipedia.org/wiki/GNU_Privacy_Guard) [GnuPG.org](https://www.gnupg.org/faq/gnupg-faq.html#whats_gnupg)

## Создание ключа

Для использования GPG/PGP необходимо создать пару приватный-публичный ключ. Они нераздельны. Для начала установите `gpg`. Как это сделать расказанно подробно в оф. рук-ве [вот тут](https://www.gnupg.org/faq/gnupg-faq.html#get_gnupg).

Создаётся ключ командой `gpg --gen-key` или более подробной (**рекомендуется**) `gpg --full-gen-key`

Рассмотрим второй вариант. Выполняем в терминале:

```sh
gpg --full-gen-key
```

В терминале появится примерно следующее:

```
gpg (GnuPG) 2.1.15; Copyright (C) 2016 Free Software Foundation, Inc. бла-бла-бла...

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
Your selection?
```

Предлагается выбрать тип ключа. Сразу два отступления про RSA, DSA и Elgamal: это три разных алгоритма шифрования.

### RSA

> RSA is the world’s premier asymmetric cryptographic algorithm, and is built on the difficulty of factoring extremely large composites. GnuPG supports RSA with key sizes of between 1024 and 4096 bits.

### DSA

> The United States’ National Institute for Standards and Technology (NIST) established the Digital Signature Algorithm (DSA) as a government standard for digital signatures. Originally, it supported key lengths between 512 and 1024 bits. Recently, NIST has declared 512-bit keys obsolete: now, DSA is available in 1024, 2048 and 3072-bit lengths. DSA belongs to the Elgamal family of algorithms, and is very well-regarded.

### Elgamal

> Elgamal may refer to either a family of cryptographic algorithms built around the difficulty of computing discrete logarithms in a finite field, or one particular asymmetric encryption algorithm based on that problem. The former is normally referred to as “the Elgamal family,” and the latter is normally referred to as simply “Elgamal.” GnuPG supports the Elgamal asymmetric encryption algorithm in key lengths ranging from 1024 to 4096 bits. There is also an Elgamal signature algorithm, which GnuPG no longer supports.

Взято [отсюда](https://www.gnupg.org/faq/gnupg-faq.html#define_rsa).

Я выбрал RSA из-за распространённости и большей максимальной теоретической длины в сравнении с DSA (4096 против 3072).

Нажимаем Enter.

```
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048)
```

По-умолчанию RSA-ключ длины 2048 бит, чего часто вполне достаточно. Длина ключа влияет на скорость операций и надёжность шифрования. Больше длина = медленнее и надёжнее, меньше длина = быстрее и ненадёжнее, соотвественно.

Оставим 2048. Enter.

```
Requested keysize is 2048 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0)
```

Далее предлагают выбрать время действия ключа. Ключ может быть как бесконечно валидным (0), так и иметь срок действия, после которого необходимо генерировать другой ключ.

Установка срока действия ключа обычно увеличивает степень защищённости, т.к. даже если кто-то найдёт случайно ваш старый ключ, то не сможет им воспользоваться по причине истчения "срока годности".

Ставим 1 год: `1y`

```
Key expires at Mon 29 Oct 2018 08:53:37 PM +03
Is this correct? (y/N)
```

Подтверждаем: `y`

Далее начинается самое интересное.

```
GnuPG needs to construct a user ID to identify your key.

Real name:
```

Тут необходимо вести либо своё реальное имя, либо никнейм, либо что-то, что идентифицирует вас. Возьмём для примера имя `John Dude`

`Real name: John Dude`

Далее email. Вводим свой основной email, по которому с вами можно связаться

`Email address: jdude@example.com`

И комментарий. В нём можно указать какую-то информацию или не указывать вовсе. Тут следует заметить, что изменить её можно будет только модификацией ключа, после которой новый публичный ключ придётся заново раздать всем знакомым.

Оставляем пустым.

`Comment:`

В итоге получится вот так:
```
Real name: John Dude
Email address: jdude@example.com
Comment:
You selected this USER-ID:
    "John Dude <jdude@example.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit?
```

Проверяем все данные, если введено правильно — нажмаем `O` и Enter.

GPG запросит пароль для дополнительной защиты приватного ключа. Этот пароль нужно будет водить при операциях с приватным ключом, и **его нельзя забывать**. Если забудете пароль — выходом будет только создать новый ключ.

Рекомендуется установить пароль. Хороший генератор стойких паролей, которые легко запомнить, есть на [Mouseware.org](https://mouseware.org)

```
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
```

После ввода пароля GPG выведет сообщение о необходимости увеличить энтропию в системе, т.е. создать разные случайные действия, такие как движения мышкой, нажатия на клавиатуру и др. Это нужно для увеличения стойкости ключа.

Через несколько секунд (процесс может быть небыстрым) появится сообщение о том, что ключ успешно создан и добавлен в хранилище ключей GPG.

```
gpg: key 5BEFCBB79BE2BE79 marked as ultimately trusted
gpg: revocation certificate stored as '/home/user/.gnupg/openpgp-revocs.d/116FA3241C977446BE5D2B5F5BEFCBB79BE2BE79.rev'
public and secret key created and signed.

pub   rsa2048 2017-10-29 [SC] [expires: 2018-10-29]
      116FA3241C977446BE5D2B5F5BEFCBB79BE2BE79
uid                      John Dude <jdude@example.com>
sub   rsa2048 2017-10-29 [E] [expires: 2018-10-29]
```

Поздравляю! Ключ успешно создан и готов к использованию.
