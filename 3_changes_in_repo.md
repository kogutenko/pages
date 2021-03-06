# Изменения в репозитории

В каждом `git`-репозитории по соседству располагаются два типов файлов: неотслеживаемые `git` и, соответственно,
отслеживаемые.

Неотслеживаемые файлы — такие, о существовании которых `git` знает, но не более. Изменения в таких файлах не
отслеживаются вовсе. При создании нового репозитория все файлы изначально являются неотслеживаемыми.

За отслеживаемыми файлами, наоборот, `git` наблюдает очень пристально, и любое изменение содержимого или 
метаданных фиксируется. Здесь файлы делятся на три подтипа: не изменённые с последнего зафиксированного коммита, 
изменённые с последнего зафиксированного коммита и индексированные, то есть подредактированные и готовые к коммиту. 
При клонировании репозитория все файлы изначально будут неизменёнными и отслеживаемыми.

Ещё раз, более подробно:

1) Любой созданный или изменённый файл, до внесения его в `git`-индекс, по умолчанию (_[JSO]: можно настроить?_) является неотслеживаемым.

2) Любой созданный или изменённый файл, внесённый в индекс, является готовым к коммиту (фиксации).

3) Любой файл, перешедший из состояния готовности к коммиту в зафиксированное состояние, становится неизменённым.

4) Любой другой файл, уже внесённый в `git`-индекс, но пока не готовый к коммиту, является изменённым.


## Информация о текущем статусе репозитория

`git status [<путь>]` является крайне полезной информационной командой о том, какому (под)типу (неотслеживаемому, 
неизменённому, индексированному или редактированному) соответствует сейчас тот или иной файл, а также — мы это 
разберём в последующих главах — показывает ёмкую информацию о ветках.

Выглядеть информация будет примерно так:
```
$ git status

На ветке main
Ваша ветка обновлена в соответствии с «origin/main».
```
В этой части выводится текущее название ветки, а также её статус относительно удалённого хранилища (например, 
"Ваша ветка <…> опережает удалённую <…> на N коммитов").

```
Изменения, которые не в индексе для коммита:
  (используйте «git add <файл>…», чтобы добавить файл в индекс)
  (используйте «git checkout -- <файл>…», чтобы отменить изменения в рабочем каталоге)

    изменено:   3_changes_in_repo.md
```
Здесь речь ведётся об изменённых, переименованных и удалённых файлах — то есть тех, которые находились в 
индексе `git` и не совпали с предыдущим зафиксированным снимком.

```
Неотслеживаемые файлы:
  (используйте «git add <файл>…», чтобы добавить в то, что будет включено в коммит).

    #4_something_about.md

нет изменений добавленных для коммита
(используйте «git add» и/или «git commit -a»)
```
Тут будут описаны все файлы, которые появились со времени последнего коммита в отслеживаемом каталоге.

**Пара полезных ключей**: 

`-s` или `--short` выводит информацию в кратком виде.

`-vv` (`--verbose --verbose`) показывает все изменения файлов в текстовом виде, аналогична `git diff`.
Одиночный `-v` или `--verbose` требуются реже.


## Добавление файлов в индекс

Стандартная команда добавления созданного или обновления изменённого файла в индексе — `git add`. Она — 
на низком уровне — сохраняет снимок с добавляемого файла и его метаданных и обновляет индекс. 

При добавлении файла в индекс `git` он становится готовым к коммиту и, если ещё не был, отслеживаемым.

Синтаксис: `git add [<опции>] <файл/путь>…`. Для добавления всех изменённых каталогов и файлов нужно 
набрать `git add .` (с этого пути и ниже). Для конкретных файлов нужно через пробел указывать их пути, 
например, `git add *INFO main.zsc ZScript/*.zsc` добавит `main.zsc`, все файлы, заканчивающиеся на 
`INFO`, а также все файлы с расширением `zsc` в каталоге `ZScript`.

**Пара полезных ключей**: 

`-i` или `--interactive`: позволяет выбирать файлы для добавления во встроенной интерактивной оболочке.

`-p` или `--patch`: интерактивная оболочка для добавления отдельных частей файла. Может быть вызвана 
с использованием предыдущего ключа. Может быть полезна для разбития множества не связанных между 
собой изменений в одном файле на отдельные коммиты.

`-f` или `--force`: переопределяет игнорируемые файлы (см. раздел "Дополнение: `.gitignore`" ниже).


## Создание коммитов

Команда `git commit` сохраняет все файлы из текущего индекса в слепок во внутреннюю базу данных, а также 
сдвигает указатель текущей ветви и HEAD-указатель на этот новый коммит. Каждому коммиту соответствует 
заполняемое сообщение с описанием того, что было сделано (если не указано обратное, загружается текстовый 
редактор по умолчанию). Хорошим тоном считается подробно расписывать каждое среднемасштабное сделанное 
изменение. Мельчить не надо, описывая каждую строку, но и запихивать в один коммит половину ото всей работы 
за месяц также нежелательно.

**Полезные ключи**:

`-a` или `--all`: добавить все изменённые файлы, пропустив, "перепрыгнув" команду `git add`.

`--amend`: исправить предыдущий коммит. Вся информация в предыдущем коммите, что конфликтует с новыми 
изменениями, замещается.

`-m "<msg>"` или `--message "<msg>"`: вместо загрузки стороннего текстового редактора сохранить коммит с 
сообщением `<msg>`.

`-s` или `--short`: выводить краткую информацию о добавленных файлах.

Часто используется дуплет ключей `git commit -am "Message here"`, который добавляет все файлы и вместе с тем 
сразу записывает нужное сообщение в коммит, без посредников.


## Удаление файлов из индекса

`git rm <файл(ы)>` является `git`-удобной заменой утилиты `rm` из UNIX: удаление файла с автоматической 
привязкой такого изменения к индексу. Однако она, как и любая `git`-утилита, работающая с реальным каталогом, 
индексом и базой данных, умеет делать чуть больше. И самое её частое применение, по моему опыту — как раз не 
в удалении файлов из рабочего каталога, а в удалении файла из индекса.

Итак, `git rm --cached <файл в индексе>` удаляет файл из списка изменений на фиксацию (из индекса). 
Пригождается сравнительно часто: ошибка человеческого фактора и, как следствие, добавления лишнего файла в 
индекс довольно популярна.


## Дополнение: `.gitignore`

Если некоторые файлы Вам необходимы, а их отслеживание — совсем нет, то в корень репозитория можно добавить 
файл "`.gitignore`". В нём построчно записываются маски файлов, которые `git` не нужно отслеживать.

Пример `.gitignore`:
```
# Temporal files:
*.rez
*.log

# Dev launchers:
launch_2.bat
emergencyTerminateProgram.sh
launch_3_testificate.bat

# Error and crash files:
crash*
err*
```
В таком репозитории не будут учитываться никакие изменения в файлах с расширением `.rez`, `.log`, в файлах, 
начинающихся на `crash` и на `err`, а также в нескольких исполняемых пакетных файлах. Строки, начинающиеся с 
`#`, считаются коммментариями.
