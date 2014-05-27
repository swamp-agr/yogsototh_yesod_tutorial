-----
isHidden:       false
theme: scientific
image: img/01_flying_neo.jpg
menupriority:   1
kind:           article
published: 2012-01-15
title: Web-программирование на Haskell
subtitle: Туториал по Yesod
author: Yann Esposito
authoruri: yannesposito.com
tags:  yesod, haskell, programming, web
-----
blogimage("img/01_flying_neo.jpg","Neo Flying at warp speed")

<div class="intro">

_update_: обновлено для Yesod 1.2

%tldr Простое пособие по Yesod. 
Yesod - это web-фреймворк на Haskell. 
Вам даже не нужно знать Haskell.

</div>

Почему Haskell?

blogimage("img/02_haskell-benchmark.png","Impressive Haskell Benchmark")

- Из-за его производительности (см. <a href="http://snapframework.com/blog/2010/11/17/snap-0.3-benchmarks">бенчмарк Snap</a> и <a href="http://www.yesodweb.com/blog/2011/03/preliminary-warp-cross-language-benchmarks"/>).
- Haskell на порядок быстрее, чем такие интерпретируемые языки, как <a href="http://shootout.alioth.debian.org/u64q/benchmark.php?test=all&lang=ghc&lang2=yarv">Ruby</a> и <a href="http://shootout.alioth.debian.org/u64q/benchmark.php?test=all&lang=ghc&lang2=python3">Python</a> <a href="fn#2">[2]</a>.
- Haskell - высокоуровневый язык программирования, с помощью которого гораздо тяжелее выстрелить себе в ногу по сравнению с `C`, `C++` или `Java`. 
- Одно из лучших свойств Haskell:
> Если оно компилируется, значит, оно работает.

- Web-фреймворк на Haskell идеально обрабатывает распараллеленные задачи, даже лучше, чем, например, node.js <a href="fn#3">[3].</a>

leftblogimage("img/03_thousands_smiths.jpg","Thousands of Agent Smith")

С чисто технической точки зрения Haskell кажется идеальным инструментом для web-разработки.
Слабая сторона Хаскелля не техническая: 

- тяжёлый порог вхождения в Haskell;
- непросто найти Haskell-программиста;
- Сообщество Haskell меньше, чем для `/.*/`;
- <strike>Пока ещё не существует <a href="http://heroku.com">heroku</a> для Haskell. Даже принимая во внимание тот факт, что я использую heroku для того, чтобы хостить свои сайты, это не так-то просто (смотри <a href="https://github.com/yesodweb/yesod/wiki/Deploying-Yesod-Apps-to-Heroku">HOW-TO</a>).</strike>
   <a href="http://fpcomplete.com/">FPComlete</a> теперь закрыло и эту дыру. 
   И они предоставляют не только облачный хостинг, но ещё и полноценную IDE и окружение Haskell для работы с ним.

Я не говорю о том, что эти недостатки несущественны. Но с Haskell ваше web-приложение будет способно как безопасно поглощать внушительное число параллельных запросов, так и оставаться открытым к изменениям.

Существует три основных web-фреймворка на Haskell:

1. [Happstack](http://happstack.com)
2. [Snap](http://snapframework.com)
3. [Yesod](http://yesodweb.com)

Не думаю, что среди них найдётся один фреймворк-победитель.
Выбор, который я сделал в пользу Yesod, был очень субъективным.
Я немного поискал и попробовал несколько пособий.
Я чувствовал, что Yesod делает больше для помощи новичкам.
Более того, команда разработчиков Yesod выглядела наиболее активной.
Конечно, я могу ошибаться, ведь это всего лишь впечатление.

blogimage("img/04_owl_draw.png","1. Draw some circles. 2. Draw the rest of the fucking owl")

Зачем я пишу эту статью?
Документация Yesod и, в частности, - книга бесподобны.
Однако я не нашёл промежуточный туториал.
Данный туториал не объясняет всех деталей.
Я попытался пошагово описать переход от пятиминутных туториалов к построению полноценных приложений.
Более того, донести что-то до других, хороший способ научиться самому.
Если вы уже используете Haskell и Yesod, этот туториал многому вас не научит.
Но если вы - абсолютный новичок в Haskell и Yesod, то, надеюсь, вам это будет полезно.
Также если вас будет смущать синтаксис, то рекомендую ознакомиться с этой <a href="http://blog.ezyang.com/2011/11/how-to-read-haskell/">статьёй</a>.

В течение этой статьи вы установите, инициализируете и сконфигурируете ваш первый Yesod проект.
Затем будет пятиминутный маленький туториал по Yesod для того, чтобы разогреться и ощутить всю удивительность Yesod.
Затем мы вычистим пятиминутный туториал, использовав `best practices`.
Напоследок останется минимальный прототип платформы блога - более стандартный пример из реального мира.

[snapbench]: http://snapframework.com/blog/2010/11/17/snap-0.3-benchmarks

[warpbench]: http://www.yesodweb.com/blog/2011/03/preliminary-warp-cross-language-benchmarks

[^benchmarkdigression]: One can argue these benchmark contains many problems.  But the benchmarks are just here to give the basic idea, namely that Haskell is very fast.

[^speeddigression]: Generally _high level_ Haskell is slower than C, but _low level_ Haskell is equivalent to C speed. It means that even if you can easily link C code with Haskell, this is not needed to reach the same speed. Furthermore writing a web service in C/C++ seems to be a very bad idea. You can take a look at a [discussion on HN about this](http://news.ycombinator.com/item?id=3449388).

[^nodejstroll]: If you are curious, you can search about [the Fibonacci node.js troll](http://www.unlimitednovelty.com/2011/10/nodejs-has-jumped-shark.html). Without any tweaking, [Haskell handled this problem perfectly](http://mathias-biilmann.net/posts/2011/10/is-haskell-the-cure). I tested it myself using Yesod instead of Snap.

[haskellvsruby]: http://shootout.alioth.debian.org/u64q/benchmark.php?test=all&lang=ghc&lang2=yarv

[haskellvspython]: http://shootout.alioth.debian.org/u64q/benchmark.php?test=all&lang=ghc&lang2=python3

## Перед реальным стартом

### Установка

Рекомендуемый путь установки Haskell - скачать <a href="http://www.haskell.org/platform">Haskell Platform</a>.

[haskell]: http://www.haskell.org

[haskellplatform]: http://www.haskell.org/platform

Как только это будет сделано, нужно будет поставить Yesod.
Откроем терминальную сессию и сделаем следующее:

~~~~~~ {.bash}
~ cabal update
~ cabal install yesod yesod-bin cabal-dev
~~~~~~

Здесь несколько шагов, и их выполнение займёт какое-то время до окончания.

### Инициализация

Теперь вы готовы инициализировать ваш первый Yesod проект.
Откройте терминал и наберите там:

~~~~~~ {.bash}
~ yesod init
~~~~~~

Введите ваше имя, выберите `yosog` в качестве названия вашего проекта и введите `Yosog` для наименования основания (Foundation).
Наконец, выберите `sqlite`.
Теперь начнём цикл разработки:

~~~~~~ {.bash}
~ cd yosog
~ cabal-dev install && yesod --dev devel
~~~~~~

Эти команды скомпилируют ваш проект целиком. Наберитесь терпения, поскольку процесс в первый раз займёт достаточно времени.
Как только процесс закончится, запустится сервер, и вы сможете зайти на него, перейдя по ссылке:

[`http://localhost:3000`](http://localhost:3000)

Поздравляю! Yesod работает!

> Примечание: если что-то пошло не так, используйте следующую команду внутри директории с проектом:

~~~~~~ {.bash}
\rm -rf dist/* ; cabal-dev install && yesod --dev devel
~~~~~~

Для оставшейся части туториала используйте другой терминал, а этот поместите 
в угол экрана и оставьте открытым, чтобы наблюдать за происходящим.

### Настройка git

> Конечно, этот шаг не обязателен для туториала, но это хорошая практика.

К счастью, файл `.gitignore` уже существует в директории `yosog`.
Поэтому достаточно инициализировать ваш репозиторий git.

~~~~~~ {.bash}
~ git init .
~ git add .
~ git commit -a -m "Initial yesod commit"
~~~~~~

Мы почти готовы начать.

### Несколько слов перед стартом

На текущий момент у нас есть директория, содержащая несколько файлов, 
и локальный web-сервер, слушающий порт 3000.
Если мы модифицируем файл из этой директории, Yesod попробует пересобрать изменения как можно быстрее.
Вместо объяснения роли каждого файла, давайте сосредоточимся лишь на самых важных файлах/директориях в этом туториале:

1. `config/routes`
2. `Handler/`
3. `templates/`
4. `config/models`

Очевидно, что:

<table><tr><td> `config/routes`      </td><td>: здесь конфигурируется отображение %url → Код.
</td></tr><tr><td> `Handler/`        </td><td>: содержатся файлы, в которых будет лежать исполняемый код, когда к нему обратятся по соответствующему %url.
</td></tr><tr><td> `templates/`      </td><td>: содержит %html, js и %css шаблоны.
</td></tr><tr><td> `config/models`   </td><td>: здесь настраиваются персистентные объекты (таблицы БД).
</td></tr></table>

В течение этого туториала мы будем также модифицировать и другие файлы, 
но не станем на них детально останавливаться.

Также замечу, что команды для эмулятора терминала запускаются в рутовой директории вашего проекта по умолчанию, если, конечно, не сказано другое.

Теперь мы готовы к старту!

## Эхо

Для того, чтобы подтвердить безопасность фреймворка Yesod,
давайте сделаем минимальное приложение-эхо.

> Цель:
>
> Сделать такой сервер, чтобы когда к нему обращались `/echo/[какой-то текст]`, он возвращал страницу, содержащую "какой-то текст" внутри `h1` блока.

~~~ {.no-highlight}
~/Sites/yosog $ {-hi-}yesod add-handler{-/hi-}
Name of route (without trailing R): {-hi-}Echo{-/hi-}
Enter route pattern (ex: /entry/#EntryId): {-hi-}/echo/#String{-/hi-}
Enter space-separated list of methods (ex: GET POST): {-hi-}GET{-/hi-}
~~~

Почти вся работа сделана за нас. `add-handler` выполняет следующие действия:

Обновляет `config/route` файл, добавляя в конец:

~~~
/echo/#String EchoR GET
~~~

эта строка содержит три элемента: шаблон %url, наименование обработчика, метод %http

- создаёт файл `Handler/Echo.hs`
- импортиует `Handler.Echo` в основной файл приложения `Application.hs`
- объявляет `Handler.Echo` в файле `.cabal` для сборки приложения.

А теперь попробуем перейти по этому адресу: [`localhost:3000/echo/foo`](http://localhost:3000/echo/foo).
Вы должны увидеть сообщение, объясняющее, что `getEchoR` пока не реализована.

Так давйте взглянем на `Handler/Echo.hs`:

~~~ {.haskell}
module Handler.Echo where

import Import

getEchoR :: String -> Handler Html
getEchoR = error "Not yet implemented: getEchoR"
~~~

Вот это мы и увидим.
Теперь мы можем заменить это таким образом: 

~~~ {.haskell}
module Handler.Echo where

import Import

getEchoR :: String -> Handler Html
getEchoR theText = defaultLayout [whamlet|<h1>#{theText}|]
~~~

Не беспокойтесь, если вы найдёте код выше немного загадочным.
Вкратце, здесь просто объявляется функция с именем `getEchoR` с одним аргументом (`theText`) типа `String`.
Когда происходит вызов этой функции, она возвращает `Handler Html`, чем бы это ни было.
Но вообще она инкапсулирует наш ожидаемый результат внутрь %html текста.

После сохранения файла, вы должны увидеть, как Yesod пересобирает приложение.
Когда компиляция завершится, вы увидите сообщение: `Starting devel application`.

Теперь перейдите по следующей ссылке: [`http://localhost:3000/echo/Yesod%20rocks!`](http://localhost:3000/echo/Yesod%20rocks!)

ТА-ДАМ! Работает!

### Пуленепробиваемый?

blogimage("img/05_neo_bullet_proof.jpg","Neo stops a myriad of bullets")

Даже это невероятно маленькое web-приложение обладает некоторыми впечатляющими свойствами.
Например, представим хакера, который пробует такой %url:

<div class="small">

[`http://localhost:3000/echo/I'm <script>alert(\"Bad!\");</script>`][bad]

</div>

[bad]: http://localhost:3000/echo/I'm%20%3Cscript%3Ealert(%22Bad!%22);%3C%2Fscript%3E

Перейдите по ссылке и протестируйте приложение.

Специальные символы защищены так, что сокрытие злоумышленником вредоносного кода внутри %url предотвращается.

Это поведение - прямое следствие _безопасности типов_.
%url строка помещается внутрь типа %url.
Затем идёт интересная часть, когда %url переводится в тип `String`.
Во время перевода происходят некоторые трансформации.
Например, все экземпляры "`%20`" заменяются пробелами.
Затем, для того, чтобы показать строку внутри %html документа, строка помещается внутрь типа %html.
Производятся такие трансформации, как, например, замена "<code><</code>" на "`%lt;`".
Спасибо Yesod, эту нудную работу он делает за нас.

~~~~~~ {.bash}
"http://localhost:3000/echo/some%20text%3Ca%3E" :: URL
                    ↓
              "some text<a>"                    :: String
                    ↓
          "some text &amp;lt;a&amp;gt;"         :: Html
~~~~~~

Yesod - не только быстрый, но он ещё и помагает приложениям оставаться безопасными.
Он защищает нас от множества общих ошибок в других парадигмах.
Да, я смотрю на тебя, PHP!

### Доводим до блеска

Даже этот очень маленький пример может быть улучшен.
Мы произведём следующие модификации:

- Используем `Data.Text` вместо `String`
- Положим наши "представления"[^explainviewwidget] в директорию `template`

[^explainviewwidget]: Под представлением я подразумеваю файлы виджетов yesod (гамлет, люциус и джулиус).

#### `Data.Text`

Использование `Data.Text` вместо `String` является хорошей практикой.

Добавим следующую инструкцию в `Foundation.hs` (сразу после остальных):

~~~~~~ {.diff}
import Data.Text
~~~~~~

Мы должны изменить `config/routes` и наш обработчик соответственно.
Заменим `#String` на `#Text` в `config/routes`:

<pre>
/echo/{-hi-}#Text{-/hi-} EchoR GET
</pre>

И сделаем то же для `Handler/Echo.hs`:

~~~~~~ {.haskell}
module Handler.Echo where

import Import

getEchoR :: {-hi-}Text{-/hi-} -> Handler Html
getEchoR theText = defaultLayout [whamlet|<h1>#{theText}|]
~~~~~~

#### Использование шаблонов

Некоторый %html (более точно - гамлет) записан прямо внутрь нашего обработчика.
Мы должны убрать его внутрь дорого файла.
Создадим новый файл `templates/echo.hamlet`, содержащий:

~~~~~~ {.haskell}
<h1> #{theText}
~~~~~~

и отредактируем обработчик `Handler/Echo.hs`:

~~~~~~ {.haskell}
module Handler.Echo where

import Import

getEchoR :: Text -> Handler Html
getEchoR theText = defaultLayout {-hi-}$(widgetFile "echo"){-/hi-}
~~~~~~

Теперь наше web приложение прекрасно структурировано.
Мы используем `Data.Text` и наши представления лежат в шаблонах.
Самое время перейти к немного более сложным примерам.

## Зеркало

leftblogimage("img/06_mirror.jpg","Neo touching a mirror")

Давайте сделаем другое небольшое приложение.
Вы увидите форму, содержащую поле для ввода текста и кнопку для валидации.
Когда вы введёте некоторый текст, (например, "Главрыба") и нажмёте на кнопку, 
на следующей странице отобразится введённая вами строка, к которой будет присоединена та же, только перевёрнутая строка.
В нашем примере должна вернуться "ГлаврыбаабырвалГ".

Сначала добавим новый обработчик:

~~~ {.no-highlight}
 ~/Sites/yosog (master) $ {-hi-}yesod add-handler{-/hi-}
Name of route (without trailing R): {-hi-}Mirror{-/hi-}
Enter route pattern (ex: /entry/#EntryId): {-hi-}/mirror{-/hi-}
Enter space-separated list of methods (ex: GET POST): {-hi-}GET POST{-/hi-}
~~~

С этого момента путь `/mirror` будет принимать GET и POST запросы.
Обновим файл, соответствующий обработчику (`Handler/Mirror.hs`):

~~~~~~ {.haskell}
module Handler.Mirror where

import Import
import qualified Data.Text as T

getMirrorR :: Handler Html
getMirrorR = defaultLayout $(widgetFile "mirror")

postMirrorR :: Handler Html
postMirrorR =  do
        postedText <- runInputPost $ ireq textField "content"
        defaultLayout $(widgetFile "posted")
~~~~~~

Нам понадобится функция `reverse` из пакета `Data.Text`,
для этого наб понадобился дополнительный импорт.

Единственным незнакомым моментом в новом коде является получение параметра POST-запроса "content". 
Более полное описание методов доступа к параметрам запроса и работы с формами вы найдёте в  [the Yesod book](http://www.yesodweb.com/book/forms).

Создадим два соответствующих шаблона
(`templates/mirror.hamlet` и `templates/posted.hamlet`):

~~~~~~ {.html}
<h1> Enter your text
<form method=post action=@{MirrorR}>
    <input type=text name=content>
    <input type=submit>
~~~~~~

~~~~~~ {.html}
<h1>You've just posted
<p>#{postedText}#{T.reverse postedText}
<hr>
<p><a href=@{MirrorR}>Get back
~~~~~~

И всё.
В этот раз нам не нужно ничего вычищать.
Мы могли бы сгенерировать форму по-другому,
но об этом пойдёт речь в следующем разделе.
Попробуйте [перейти сюда](http://localhost:3000/mirror).

Мы также можем попробовать ввести странные значения (как, например `<script>alert('Bad');</script>`).
Как и раньше, наше приложение по-прежнему безопасно.

## Блог

Мы узнали, как доставать %http параметры запросы.
Самое время узнать, как сохранять данные в базу данных.

Этот пример будет очень небольшим:

- `GET`  по адресу `/blog` должен отображать список статей,
- `POST` по адресу `/blog` должен создавать новую статью,
- `GET`  по адресу `/blog/<код статьи>` должен отображать содержимое статьи по его коду.

Как и ранее, начнём с добавления некоторых обработчиков:

~~~
~/Sites/yosog (master) $ yesod add-handler
Name of route (without trailing R): Blog
Enter route pattern (ex: /entry/#EntryId): /blog
Enter space-separated list of methods (ex: GET POST): GET POST

~/Sites/yosog (master) $ yesod add-handler
Name of route (without trailing R): Article
Enter route pattern (ex: /entry/#EntryId): /blog/#ArticleId
Enter space-separated list of methods (ex: GET POST): GET
~~~

Затем объявим объект модели данных.
Допишем следующий код в конец файла `config/models`:

~~~
Article
    title   Text
    content Html
    deriving
~~~

Поскольку `Html` не является экземпляром классов `Read`, `Show` и `Eq`,
добавим строку `deriving` после объявления.
Если вы забудете это сделать, то увидите сообщение об ошибке.

После настройки маршрутизации и модели мы напишем обработчик 
для блога `Handler/Blog.hs`.
Начнём с объявления модуля и импорта некоторых пакетов для валидации форм.

~~~~~~ {.haskell}
module Handler.Blog
    ( getBlogR
    , postBlogR
    )
where

import Import

-- для использования Html в формах
import Yesod.Form.Nic (YesodNic, nicHtmlField)
instance YesodNic App
~~~~~~

_Замечание_: хорошей практикой считается добавление импорта YesodNic в `Foundation.hs`.
Я положил определение здесь для того, чтобы было нагляднее, однако при компиляции вы должны увидеть предупреждение вида `orphan instance`.
Устранение замечания оставлено в качестве упражнения для читателя.

_Подсказка_: Не забудьте указать `YesodNic` и `nicHtmlField` среди экспортируемых объектов модуля.

~~~~~~ {.haskell}
entryForm :: Form Article
entryForm = renderDivs $ Article
    <$> areq   textField "Title" Nothing
    <*> areq   nicHtmlField "Content" Nothing
~~~~~~

Функция `entryForm` определяет форму для добавления новой статьи.
Не обращайте внимания на её синтаксис.
Если вам интересно - посмотрите в сторону Аппликативных функторов.
Сейчас вам достаточно запомнить, что `areq` необходим для элементов ввода данных формы.
Её аргументы идут в таком порядке: `areq тип ссылка значение_по_умолчанию`.

~~~~~~ {.haskell}
-- Представление, показывающее список статей
getBlogR :: Handler Html
getBlogR = do
    -- Запросить список статей из базы
    articles <- runDB $ selectList [] [Desc ArticleTitle]
    -- Для построения формы (см. templates/articles.hamlet)
    -- нас интересуют два "объекта": `articleWidget` и `enctype`
    (articleWidget, enctype) <- generateFormPost entryForm
    defaultLayout $ do
        $(widgetFile "articles")
~~~~~~

Этот обработчик должен отобразить список статей.
Мы получим список из базы и создадим форму.
Только взгляните на соответствующие шаблоны:

~~~~~~ {.html}
<h1> Articles
$if null articles
    <p> There are no articles in the blog
$else
    <ul>
        $forall Entity articleId article <- articles
            <li>
                <a href=@{ArticleR articleId} > #{articleTitle article}
<hr>
  <form method=post enctype=#{enctype}>
    ^{articleWidget}
    <div>
        <input type=submit value="Post New Article">
~~~~~~

Заметьте, что мы добавили немного логики внутрь шаблона.
Здесь есть проверка и "цикл".

Ещё одна очень интересная часть - это создание формы.
С помощью Yesod создаётся `articleWidget`.
Нами были переданы корректные параметры 
(обязательный - input, а также по желанию ссылки и значения по умолчанию),
и теперь для нас создана защищенная форма.
Но помимо этого нам нужно создать кнопку для отправки данных.

Можете взглянуть на то, что уже есть, [кликнув сюда](localhost:3000/blog).
Конечно, вы ещё не сможете ничего отправить в блог.

Вернёмся к `Handler/Blog.hs`.

~~~~~~ {.haskell}
postBlogR :: Handler Html
postBlogR = do
    ((res,articleWidget),enctype) <- runFormPost entryForm
    case res of
         FormSuccess article -> do
            articleId <- runDB $ insert article
            setMessage $ toHtml $ (articleTitle article) <> " created"
            redirect $ ArticleR articleId
         _ -> defaultLayout $ do
                setTitle "Please correct your entry form"
                $(widgetFile "articleAddError")
~~~~~~

Эта функция должна отрабатывать создание новой статьи.
Мы обработаем ответ для формы.
Если возникла ошибка (например, мы оставили какое-то поле незаполненным) - покажем страницу с ошибкой.
А если всё хорошо, то:

- поместим новую статью в БД (`runDB $ insert article`)
- добавим сообщение, которое отобразится на странице (`setMessage $ ...`)
- переадресуем на страницу со статьёй.

Ниже содержимое страницы с ошибкой:

~~~~~~ {.haskell}
<form method=post enctype=#{enctype}>
    ^{articleWidget}
    <div>
        <input type=submit value="Post New Article">
~~~~~~

Наконец, нам нужно отобразить статью.
Для этого изменим `Handler/Article.hs`

~~~~~~ {.haskell}
getArticleR :: ArticleId -> Handler Html
getArticleR articleId = do
    article <- runDB $ get404 articleId
    defaultLayout $ do
        setTitle $ toHtml $ articleTitle article
        $(widgetFile "article")
~~~~~~

Функция `get404` пытается совершить запрос к БД.
Если из базы ничего не вернётся, покажем пользователю страницу c 404 кодом.
Остальное должно быть понятным.
Ниже содержимое шаблона статьи `templates/article.hamlet`:

~~~~~~ {.html}
<h1> #{articleTitle article}
<article> #{articleContent article}
<hr>
<a href=@{BlogR}>
    Go to article list.
~~~~~~

Платформа для минимального блога завершена.
Вы можете перейти к ней, нажав [сюда](http://localhost:3000/blog).

По приколу вы можете создать статью со следующим содержанием:

~~~~~~ {.html}
Cross Script:
   <script>alert("Ну ты попал!");</script>

SQL injection: "); DROP TABLE ARTICLE;;
~~~~~~

## Заключение

Это конец туториала.
Я сделал его очень маленьким.

Если вы уже знакомы с Haskell и хотите идти двигаться дальше,
обязательно взгляните на недавний [i18n blog tutorial](http://yesodweb.com/blog/2012/01/blog-example).
Нетрудно заметить, на чём основан мой туториал.
Вы изучите очень простой способ, как прикрутить к блогу авторизацию,
время и интернализацию.

А если, с другой стороны, вы не знаете Haskell, тогда не спешите погружаться в web программирование на нём.
Haskell - очень сложный и необычный язык.
Мой вам совет: для того, чтобы как можно быстрее начать использовать Haskell в web программировании, нужно:

1. [Попробовать Haskell прямо в браузере](http://tryhaskell.org)
2. Прочитать мой туориал [Learn Haskell Fast and Hard on School of Haskell](https://www.fpcomplete.com/school/haskell-fast-hard) или прямо [в моём блоге](http://yannesposito.com/Scratch/en/blog/Haskell-the-Hard-Way/)
3. Затем прочесть чудеснейшую книгу [Learn you a Haskell for Great Good](http://learnyouahaskell.com)
4. И если вы испытываете затруднение в понимании таких концепций, как монады, вам стоит прочесть ещё и [эти статьи](http://homepages.inf.ed.ac.uk/wadler/topics/monads.html). С их помощью я обрёл просветление.
5. Если вы чувствуете себя уверенно, тогда вы в состоянии прочесть [Yesod book](http://yesodweb.com/book), но если вам она покажется трудноватой, то сначала уделите время [Real world Haskell](http://book.realworldhaskell.org).

Также нельзя не заметить, что:

- [haskell.org](http://haskell.org) - кладезь бесценной информации.
- [hoogle](http://www.haskell.org/hoogle/) будет очень полезен.
- Используйте [hlint](http://community.haskell.org/~ndm/hlint/) как можно быстрее для приобретения хороших привычек.

Как вы могли понять, если вы ещё не знаете Haskell,
то путь для его изучения долог, но я гарантирую вам, вы будете вознаграждены, пройдя по нему.
the path is long but I guarantee you it will be very rewarding!

_ps:_ исходники оригинала статьи:
[github.com/yogsototh/yosog](http://github.com/yogsototh/yosog).

