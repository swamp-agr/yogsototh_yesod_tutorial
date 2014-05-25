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

Из-за его эффективности (см. <a href="http://snapframework.com/blog/2010/11/17/snap-0.3-benchmarks">бенчмарк Snap</a> и <a href="http://www.yesodweb.com/blog/2011/03/preliminary-warp-cross-language-benchmarks"/>).
Haskell на порядок быстрее, чем такие интерпретируемые языки, как <a href="http://shootout.alioth.debian.org/u64q/benchmark.php?test=all&lang=ghc&lang2=yarv">Ruby</a> и <a href="http://shootout.alioth.debian.org/u64q/benchmark.php?test=all&lang=ghc&lang2=python3">Python</a> <a href="fn#2">[2]</a>.
Haskell - высокоуровневый язык программирования, с помощью которого гораздо тяжелее выстрелить себе в ногу по сравнению с `C`, `C++` или `Java`, например. 
Одно из лучших свойств Haskell:
> Если ваше приложение скомпилировано, то его работа очень близка к той, что закладывал в неё программист.

Web-фреймворк на Haskell идеально обрабатывает распараллеленные задачи, даже лучше, чем, например, node.js <a href="fn#3">[3].</a>

leftblogimage("img/03_thousands_smiths.jpg","Thousands of Agent Smith")

С чисто технической точки зрения Haskell кажется идеальным инструментом для web-разработки.
Слабая сторона Хаскелля не техническая: 

- тяжёлый порог вхождения в Haskell;
- непросто найти Haskell-программиста;
- Сообщество Haskell меньше, чем для `/.*/`;
- <strike>Пока ещё не существует <a href="http://heroku.com">heroku</a> для Haskell. Даже принимая во внимание тот факт, что я использую heroku для того, чтобы хостить свои сайты, это не так-то просто (смотри <a href="https://github.com/yesodweb/yesod/wiki/Deploying-Yesod-Apps-to-Heroku">HOW-TO</a>).</strike>
   <a href="http://fpcomplete.com/">FPComlete</a> теперь закрыло и эту дыру. 
   И они не только предоставляют облачный хостинг, но ещё и полноценную IDE и окружение Haskell для работы с ним.

Я не говорю о том, что эти недостатки несущественны. Но с Haskell ваше web-приложение будет способно как безопасно поглощать внушительное число параллельных запросов, так и приспосабливаться к изменениям.

Вообще-то, существует три основных web-фреймворка на Haskell:

1. [Happstack](http://happstack.com)
2. [Snap](http://snapframework.com)
3. [Yesod](http://yesodweb.com)

Не думаю, что среди них есть реальный победный фреймворк.
Выбор, который я сделал в пользу Yesod, был очень субъективный.
Я немного притаился(?!) и попробовал несколько пособий.
Я чувствовал, что Yesod делает больше для помощи новичкам.
Более того, команда разработчиков Yesod выглядела наиболее активной.
Конечно, я могу ошибаться, ведь это всего лишь впечатление.

blogimage("img/04_owl_draw.png","1. Draw some circles. 2. Draw the rest of the fucking owl")

Зачем я пишу эту статью?
Документация Yesod и, в частности, книга - бесподобны.
Однако я не нашёл промежуточный туториал.
Этот туториал не объясняет всех деталей.
Я пытался дать пошаговую инструкцию о том, как начать из пятиминутного пособия и прийти к вполне готовой для эксплуатации архитектуре.
Более того, донести что-то других - хороший способ научиться самому.
Если вы уже используете Haskell и Yesod - эта статья многому вас не научит.
Но если вы - абсолютный новичок в Haskell и Yesod, то надеюсь, вам это будет полезно.
Также если вас будет смущать синтаксис, то рекомендую ознакомиться с этой <a href="http://blog.ezyang.com/2011/11/how-to-read-haskell/">статьёй</a>.

В течение этой статьи вы установите, инициализируете и сконфигурируете ваш первый Yesod проект.
Затем будет пятиминутный маленький туториал Yesod для того, чтобы разогреться и ощутить всю удивительность Yesod.
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

Здесь несколько шагов, но их выполнение займёт какое-то время до окончания.

### Инициализация

Теперь вы готовы инициализировать ваш первый Yesod проект.
Откройте терминал и наберите там:

~~~~~~ {.bash}
~ yesod init
~~~~~~

Введите ваше имя, выберите `yosog` в качестве названия вашего проекта и введите `Yosog` для наименования основания (Foundation).
Наконец, выберите `sqlite`.
Теперь, начнём цикл разработки:

~~~~~~ {.bash}
~ cd yosog
~ cabal-dev install && yesod --dev devel
~~~~~~

Эти команды скомпилируют ваш проект целиком. Сохраняйте спокойствие, поскольку процесс в первый раз займёт достаточно времени.
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

На текущий момент у нас есть директория, содержащая пучок файлов, 
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
</td></tr><tr><td> `config/models`   </td><td>: здесь настраиваются персистентные объекты (таблиц БД).
</td></tr></table>

В течение этого туториала мы будем также модифицировать и другие файлы, 
но не станем на них детально останавливаться.

Также замечу, что команды для эмулятора терминала запускаются в рутовой директории вашего проекта по умолчанию, если, конечно, не сказано другое.

Теперь мы готовы к старту!

## Эхо

Для того, чтобы подтвердить качество безопасности фреймворка Yesod,
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
- объявляет `Handler.Echo` в файле `.cabal` для билда приложения.

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

Не беспокойтесь, если вы найдёте это немного загадочным.
Вкратце, здесь просто объявляется функция с именем `getEchoR` с одним аргументом (`theText`) типа `String`.
Когда происходит вызов этой функции, она возвращает `Handler Html`, чем бы это ни было.
Но вообще она инкапсулирует наш ожидаемый результат внутрь %html текста.

После сохранения файла, вы должны увидеть, как Yesod пересобирает приложение.
Когда компиляция завершится, вы увидите сообщение: `Starting devel application`.

Теперь перейдите по следующей ссылке: [`http://localhost:3000/echo/Yesod%20rocks!`](http://localhost:3000/echo/Yesod%20rocks!)

ТА-ДАМ! Оно работает!

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
Then the interesting part in the %url is put inside a String type.
To pass from %url type to String type some transformations are made.
For example, all instances of "`%20`" are replaced by space characters.
Then to show the String inside an %html document, the string is put inside an %html type.
Some transformations occurs like replace "<code><</code>" by "`&lt;`".
Thanks to Yesod, this tedious job is done for us.

~~~~~~ {.bash}
"http://localhost:3000/echo/some%20text%3Ca%3E" :: URL
                    ↓
              "some text<a>"                    :: String
                    ↓
          "some text &amp;lt;a&amp;gt;"         :: Html
~~~~~~

Yesod is not only fast, it helps us to remain secure.
It protects us from many common errors in other paradigms.
Yes, I am looking at you, PHP!

### Cleaning up

Even this very minimal example should be enhanced.
We will clean up many details:

- Use `Data.Text` instead of `String`
- Put our "views"[^explainviewwidget] inside the `template` directory

[^explainviewwidget]: By view I mean yesod widget's hamlet, lucius and julius files.

#### `Data.Text`

It is a good practice to use `Data.Text` instead of `String`.

To declare it, add this import directive to `Foundation.hs` (just after the last one):

~~~~~~ {.diff}
import Data.Text
~~~~~~

We have to modify `config/routes` and our handler accordingly.
Replace `#String` by `#Text` in `config/routes`:

<pre>
/echo/{-hi-}#Text{-/hi-} EchoR GET
</pre>

And do the same in `Handler/Echo.hs`:

~~~~~~ {.haskell}
module Handler.Echo where

import Import

getEchoR :: {-hi-}Text{-/hi-} -> Handler Html
getEchoR theText = defaultLayout [whamlet|<h1>#{theText}|]
~~~~~~

#### Use templates

Some %html (more precisely hamlet) is written directly inside our handler.
We should put this part inside another file.
Create the new file `templates/echo.hamlet` containing:

~~~~~~ {.haskell}
<h1> #{theText}
~~~~~~

and modify the handler `Handler/Echo.hs`:

~~~~~~ {.haskell}
module Handler.Echo where

import Import

getEchoR :: Text -> Handler Html
getEchoR theText = defaultLayout {-hi-}$(widgetFile "echo"){-/hi-}
~~~~~~

At this point, our web application is nicely structured.
We use `Data.Text` and our views are in templates.
It is the time to make a slightly more complex example.

## Mirror

leftblogimage("mirror.jpg","Neo touching a mirror")

Let's make another minimal application.
You should see a form containing a text field and a validation button.
When you enter some text (for example "Jormungad") and validate it,
the next page presents the content to you with its reverse appended to it.
In our example, it should return "JormungaddagnumroJ".

First, add a new handler:

~~~ {.no-highlight}
 ~/Sites/yosog (master) $ {-hi-}yesod add-handler{-/hi-}
Name of route (without trailing R): {-hi-}Mirror{-/hi-}
Enter route pattern (ex: /entry/#EntryId): {-hi-}/mirror{-/hi-}
Enter space-separated list of methods (ex: GET POST): {-hi-}GET POST{-/hi-}
~~~

This time the path `/mirror` will accept GET and POST requests.
Update the corresponding new Handler file (`Handler/Mirror.hs`):

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

We will need to use the `reverse` function provided by `Data.Text`
which explains the additional import.

The only new thing here is the line that gets the POST parameter named "content".
If you want to know more detail about it and forms in general you can take a
look at [the Yesod book](http://www.yesodweb.com/book/forms).

Create the two corresponding templates
(`templates/mirror.hamlet` and `templates/posted.hamlet`):

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

And that is all.
This time, we won't need to clean up.
We might have generated the form a different way,
but we'll see how to do this in the next section.

Just try it by [clicking here](http://localhost:3000/mirror).

Also you can try to enter strange values (like `<script>alert('Bad');</script>`).
As before, your application is quite secure.

## A Blog

We saw how to retrieve %http parameters.
It is the time to save things into a database.

This example will be very minimal:

- `GET`  on `/blog` should display the list of articles,
- `POST` on `/blog` should create a new article,
- `GET`  on `/blog/<article id>` should display the content of the article.

As before, we'll start by adding some handlers:

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

Then we declare another model object.
Append the following content to `config/models`:

~~~
Article
    title   Text
    content Html
    deriving
~~~

As `Html` is not an instance of `Read`, `Show` and `Eq`,
we had to add the `deriving` line.
If you forget it, there will be an error.

After the route and the model, we write the handler.
Let's write the content of `Handler/Blog.hs`.
We start by declaring the module and by importing some block necessary to
handle Html in forms.

~~~~~~ {.haskell}
module Handler.Blog
    ( getBlogR
    , postBlogR
    )
where

import Import

-- to use Html into forms
import Yesod.Form.Nic (YesodNic, nicHtmlField)
instance YesodNic App
~~~~~~

_Remark_: it is a best practice to add the YesodNic instance inside `Foundation.hs`.
I put this definition here to make things easier but you should see a warning about this orphan instance.
To put the include inside Foundation.hs is left as an exercice to the reader.

_Hint_: Do not forget to put `YesodNic` and `nicHtmlField` inside the exported objects of the module.

~~~~~~ {.haskell}
entryForm :: Form Article
entryForm = renderDivs $ Article
    <$> areq   textField "Title" Nothing
    <*> areq   nicHtmlField "Content" Nothing
~~~~~~

This function defines a form for adding a new article.
Don't pay attention to all the syntax.
If you are curious you can take a look at Applicative Functor.
You just have to remember `areq` is for required form input.
Its arguments being: `areq type label default_value`.

~~~~~~ {.haskell}
-- The view showing the list of articles
getBlogR :: Handler Html
getBlogR = do
    -- Get the list of articles inside the database.
    articles <- runDB $ selectList [] [Desc ArticleTitle]
    -- We'll need the two "objects": articleWidget and enctype
    -- to construct the form (see templates/articles.hamlet).
    (articleWidget, enctype) <- generateFormPost entryForm
    defaultLayout $ do
        $(widgetFile "articles")
~~~~~~

This handler should display a list of articles.
We get the list from the DB and we construct the form.
Just take a look at the corresponding template:

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

Notice we added some logic inside the template.
There is a test and a "loop".

Another very interesting part is the creation of the form.
The `articleWidget` was created by Yesod.
We have given it the right parameters
(input required or optional, labels, default values),
and now we have a protected form made for us.
But now we have to create the submit button.

You can take a first look by [clicking here](localhost:3000/blog).
Of course, you can't post something yet.

Get back to `Handler/Blog.hs`.

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

This function should be used to create a new article.
We handle the form response.
If there is an error we display an error page, for example if we left some required value blank.
If things go well:

- we add the new article inside the DB (`runDB $ insert article`)
- we add a message to be displayed (`setMessage $ ...`)
- we are redirected to the article web page.

Here is the content of the error Page:

~~~~~~ {.haskell}
<form method=post enctype=#{enctype}>
    ^{articleWidget}
    <div>
        <input type=submit value="Post New Article">
~~~~~~

Finally we need to display an article.
For this we will modify `Handler/Article.hs`

~~~~~~ {.haskell}
getArticleR :: ArticleId -> Handler Html
getArticleR articleId = do
    article <- runDB $ get404 articleId
    defaultLayout $ do
        setTitle $ toHtml $ articleTitle article
        $(widgetFile "article")
~~~~~~

The `get404` function tries to do a get on the DB.
If it fails, it returns a 404 page.
The rest should be clear.
Here is the content of `templates/article.hamlet`:

~~~~~~ {.html}
<h1> #{articleTitle article}
<article> #{articleContent article}
<hr>
<a href=@{BlogR}>
    Go to article list.
~~~~~~

The blog system is finished.
You can jump to it by clicking [here](http://localhost:3000/blog).

Just for fun, you can try to create an article with the following content:

~~~~~~ {.html}
Cross Script:
   <script>alert("You loose");</script>

SQL injection: "); DROP TABLE ARTICLE;;
~~~~~~

## Conclusion

This is the end of this tutorial.
I made it very minimal.

If you already know Haskell and you want to go further,
you should take a look at the
recent [i18n blog tutorial](http://yesodweb.com/blog/2012/01/blog-example).
It will be obvious I based my own tutorial on it.
You'll learn in a very straightforward way how easy it is to use authorization,
Time and internationalization.

If, on the other hand, you don't know Haskell, then you shouldn't jump directly
to web programming with it.
Haskell is a very complex and unusual language.
My advice to go as fast as possible in using Haskell for web programming is:

1. Start by [trying Haskell in your browser](http://tryhaskell.org)
2. Read my tutorial [Learn Haskell Fast and Hard on School of Haskell](https://www.fpcomplete.com/school/haskell-fast-hard) or directly [on this blog](/Scratch/en/blog/Haskell-the-Hard-Way/)
3. Then read the excellent [Learn you a Haskell for Great Good](http://learnyouahaskell.com)
4. If you have difficulty understanding concepts like monads, you should really read [these articles](http://homepages.inf.ed.ac.uk/wadler/topics/monads.html). For me they were enlightening.
5. If you feel confident, you should be able to follow the
   [Yesod book](http://yesodweb.com/book) but if you find it difficult to follow the Yesod book, you should read [real world Haskell](http://book.realworldhaskell.org) first.

Also, note that:

- [haskell.org](http://haskell.org) is full of excellent resources.
- [hoogle](http://www.haskell.org/hoogle/) will be very useful
- Use [hlint](http://community.haskell.org/~ndm/hlint/) as soon as possible to get good habits.

As you should see, if you don't already know Haskell,
the path is long but I guarantee you it will be very rewarding!

_ps:_ You can download the source of this Yesod blog tutorial at
[github.com/yogsototh/yosog](http://github.com/yogsototh/yosog).

