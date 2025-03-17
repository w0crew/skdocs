+++
title = "Игровые команды"
archetype = "default"
url = "commands"
weight = 11
+++

## Игровые команды
<gray>Тук-тук я в домике</gray>

<hundred-empty-line></hundred-empty-line>

Создавать команды в Skript очень просто.\
Базовый шаблон определения команды следующий:
```
command /<имя команды> <аргументы>:
  aliases:
  executable by:
  usage:
  description:
  permission:
  permission message:
  cooldown: <timespan>
  cooldown message:
  cooldown bypass:
  cooldown storage: <переменная>
  trigger:
  　#ваш код
```
Все параметры, находящиеся между самой командой и trigger - опциональны (необязательны)\

Разберем каждый раздел\
\
Имя команды - это в сама команда. В имени команды можно использовать любой символ, кроме символа пробела. Если вы используете пробел в имени команды, текст после пробела становится аргументом. Символ косой черты (/) перед именем команды является необязательным (это не означает, что вы можете выполнять команду без косой черты).\
\
Аргументы - это все, что пользователь пишет после команды через пробел. У аргументов может быть тип данных, например Number, Text, Player. Аргументы могут быть обязательными и опциональными, могуть иметь базовое значение, а могут не иметь:\
Команда без аргументов
```
command /mycommand:
  trigger:а
    broadcast "Hello world!"
```
Команда с обязательными аргументами
```
command /mycommand <number> <number>:
  trigger:
    broadcast "%arg-1+arg-2%"
    #сложит два числа указанных пользователем через пробел
```
Команда с необязательными аргументами
```
command /mycommand [<text>]:
  trigger:
    if arg-1 is set:
      broadcast arg-1
    else:
      broadcast "Hello world!"
    # Выведет указанный текст, если он указан,
    # иначе 'Hello world!'
```
Команда с базовым значением
```
command /mycommand [<text="Hello world!">]:
  trigger:
      broadcast arg-1
    # Выведет указанный текст, если он указан,
    # иначе 'Hello world!'
```
В блоке trigger для получения значений аргументов используются следующие выражения:
```
[the] last arg[ument][s]
[the] arg[ument][s](-| )%number%
[the] (1st|2nd|3rd|4-90th) arg[ument][s]
[the] arg[ument][s]
[the] %type%( |-)arg[ument][( |-)%number%]
[the] arg[ument]( |-)%type%[( |-)%number%]
```
Нумерация аргументов происходит в том же порядке, как они указаны в самом описании команды\
\
Aliases - псевдонимы команды; дополнительные имена, при помощи которых она может быть вызвана. Пишутся через запятую на этой же строке.\
Пример:
```
command /mycommand [<text="Hello world!">]:
  aliases: /helloworld,/hello
  trigger:
```
Executable by - определяет кто может выполнять данную команду, в текущих реалиях это либо игрок, либо консоль
Console
```
command /mycommand [<text="Hello world!">]:
  executable by: console
  trigger:
```
Player
```
command /mycommand [<text="Hello world!">]:
  executable by: player
  trigger:
```
Usage - сообщение которое будет отправлено, если команда используется неправильно. Например, когда вы не вводите обязательные аргументы или когда вы  используете неверный тип в качестве аргумента.
```
command /mycommand <text>:
  usage: Используйте: /mycommand <текст для отправки>
  trigger:
```
Description - описание команды, выводится при отправке команды /help, либо может быть получено при помощи Bukkit API другими плагинами.
```
command /mycommand <text>:
  description: отправляет всем указанное сообщение
  trigger:
```
Permission - право необходимое игроку для выполнения команды. 
```
command /mycommand <text>:
  permission: mycommand.send
  trigger:
```
Permission message - сообщение получаемое отправителем, если у него отсутствует право на выполнение команды.
```
command /mycommand <text>:
  permission message: Недостаточно прав для выполнения данной команды
  trigger:
```
Cooldown - время восстановления для повторного использования команды. Обратите внимание, что время восстановления команд сбрасывается при остановке сервера.
```
command /mycommand <text>:
  cooldown: 1 hour
  trigger:
```
Перезарядку можно отменить, используя выражения:
```
(cancel|ignore) [the] [current] [command] cooldown 
un(cancel|ignore) [the] [current] [command] cooldown
```
Первое отменяет перезарядку команды после ее выполнения, вторая иначе, принудительно ее устанавливает. \
\
Cooldown message - сообщение, которое будет написано отправителю, если время восстановления команды еще не прошло. Вы можете использовать выражения  elapsed time и remaining time для получения времени последнего применения команды и времени до окончания перезарядки соответственно.\
\
Cooldown bypass - право, при наличии которого у отправителя команда не будет уходить на перезарядку.
```
command /mycommand <text>:
  cooldown: 1 hour
  cooldown bypass: hello.bypass
  trigger:
```
Cooldown storage - тут указывается переменная для сохранения информации о перезарядки. Если ее указать, то перезарядка будет сохранятся даже после выключения сервера. 
```
command /mycommand <text>:
  cooldown: 1 hour
  cooldown bypass: hello.bypass
  cooldown storage: {mycommand::%player's uuid%}
  trigger:
```
Trigger - в этом блоке непосредственно находится выполняемый командой код.\
\
Примеры команд:
```
command /speed [<player>] [<number>]:
  permission: sk.speed
  permission message: &c→ &fНедостаточно прав для выполнения данной команды
  usage: &c→ &fИспользуйте: &c/speed &f<&cник игрока&f> &f<&cскорость&f>
  trigger:
    if (arg-1 is set):
      if (sender have permission "sk.speed.any"):
        set {_player} to arg-1        
        if (arg-2 is set):
          set {_speed} to arg-2
        else:
          set {_speed} to 1
        set {_player}'s walking speed to {_speed}/5        
        
        send "&a→ &fСкорость вашего передвижения установлена на &a%{_speed}%" to arg-1
        if {_player} is not player:
          send "&a→ &fСкорость передвижения &a%{_player}% &fустановлена на &a%{_speed}%"
        stop
    if (sender is player):
      if (arg-2 is set):
        set {_speed} to arg-2
      else:
        set {_speed} to 1
      set player's walking speed to {_speed}/5
      send "&a→ &fСкорость вашего передвижения установлена на &a%{_speed}%"
    else:
      send "&c→ &fИспользуйте: &c/speed &f<&cник игрока&f> &f<&cскорость&f>"
```
```
command /heal [<player>] [<text>]:
  permission: sk.heal
  permission message: &c→ &fНедостаточно прав для выполнения данной команды
  cooldown: 5 minutes
  cooldown message: &c→ &fПодождите перед следующим выполнением команды
  cooldown bypass: sk.heal.bypass
  trigger:
    if (arg-1 is set):
      if sender have permission "sk.heal.any":
        heal arg-1
        feed arg-1
        set {_player} to arg-1
        send "&a→ &fИгрок &a%{_player}%&f был исцелен"
        stop
    if (sender is player):
      heal player
      feed player
      send "&a→ &fВы были исцелены"
    else:
      send "&c→ &fИспользуйте: &c/heal &f<&cник игрока&f>"
```