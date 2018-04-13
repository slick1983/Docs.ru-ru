---
uid: web-api/overview/web-api-routing-and-actions/create-a-rest-api-with-attribute-routing
title: Создать REST API с маршрутизацией атрибутов в ASP.NET Web API 2 | Документы Microsoft
author: MikeWasson
description: ''
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/26/2013
ms.topic: article
ms.assetid: 23fc77da-2725-4434-99a0-ff872d96336b
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/web-api-routing-and-actions/create-a-rest-api-with-attribute-routing
msc.type: authoredcontent
ms.openlocfilehash: 1f1e90544c9dd8439a522f2196d81d020ea2f4f2
ms.sourcegitcommit: 7f92990bad6a6cb901265d621dcbc136794f5f3f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
<a name="create-a-rest-api-with-attribute-routing-in-aspnet-web-api-2"></a>Создание интерфейса API REST с атрибутом маршрутизации в ASP.NET Web API 2
====================
по [Mike Wasson](https://github.com/MikeWasson)

Веб-API 2 поддерживает новый тип маршрутизации, вызывается *маршрутизацией атрибутов*. Общие сведения о маршрутизации атрибута, в разделе [атрибут маршрутизации в веб-API 2](attribute-routing-in-web-api-2.md). В этом учебнике используется маршрутизацией атрибутов для создания REST API для коллекции книг. API-Интерфейс поддерживает следующие действия:

| Действие | Пример URI |
| --- | --- |
| Получение списка всех книг. | / api/книг |
| Получение книги по идентификатору. | /api/books/1 |
| Получите сведения о книге. | /API/Books/1/Details |
| Получение списка книг по жанру. | /API/Books/FANTASY |
| Получите список книг, дата публикации. | /API/Books/Date/2013-02-16 /api/books/date/2013/02/16 (альтернативный способ) |
| Получение списка книг определенного автора. | /API/authors/1/Books |

Все методы являются только для чтения (HTTP-запросы GET).

Для уровня данных мы будем использовать Entity Framework. Записи книги будут иметь следующие поля:

- ID
- Заголовок
- Жанр
- Дата публикации.
- Цены
- Описание
- AuthorID (внешний ключ к таблице Authors)

Для большинства запросов тем не менее, API вернет подмножество этих данных (название, автор и жанр). Чтобы получить полную запись клиент запрапрашивает `/api/books/{id}/details`.

## <a name="prerequisites"></a>Предварительные требования

[Visual Studio 2017 г](https://www.visualstudio.com/vs/) Community, Professional или Enterprise edition.

## <a name="create-the-visual-studio-project"></a>Создание проекта Visual Studio

Запустить Visual Studio. Из **файл** последовательно выберите пункты **New** , а затем выберите **проекта**.

В **шаблоны** выберите **установленные шаблоны** и разверните **Visual C#** узла. В разделе **Visual C#**выберите **Web**. В списке шаблонов проектов выберите **веб-приложение ASP.NET MVC 4**. Назовите проект &quot;BooksAPI&quot;.

![](create-a-rest-api-with-attribute-routing/_static/image1.png)

В **новый проект ASP.NET** диалогового окна выберите **пустой** шаблона. В разделе «Добавление папок и основные ссылки для» выберите **веб-API** флажок. Нажмите кнопку **создания проекта**.

![](create-a-rest-api-with-attribute-routing/_static/image2.png)

Это создает каркас проекта, который настроен для функциональных возможностей веб-API.

### <a name="domain-models"></a>Модели домена

Добавьте классы для модели домена. В обозревателе решений щелкните правой кнопкой мыши папку модели. Выберите **добавить**, а затем выберите **класса**. Присвойте классу имя `Author`.

![](create-a-rest-api-with-attribute-routing/_static/image3.png)

Замените код в Author.cs следующее:

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample1.cs)]

Теперь добавьте класс с именем `Book`.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample2.cs)]

### <a name="add-a-web-api-controller"></a>Добавить контроллер Web API

На этом шаге будет добавлен контроллер веб-API, использующий Entity Framework, в качестве данных.

Для сборки проекта нажмите CTRL+SHIFT+B. Платформа Entity Framework использует отражение для обнаружения свойств моделей, поэтому для него требуется скомпилированную сборку создать схему базы данных.

В обозревателе решений щелкните правой кнопкой мыши папку Controllers. Выберите **добавить**, а затем выберите **контроллера**.

![](create-a-rest-api-with-attribute-routing/_static/image4.png)

В **Добавление формирования шаблонов** диалогового окна выберите «веб-API 2 контроллер с действиями чтения и записи, использующий Entity Framework.»

[![](create-a-rest-api-with-attribute-routing/_static/image6.png)](create-a-rest-api-with-attribute-routing/_static/image5.png)

В **добавления контроллера** диалогового окна для **имя контроллера**, введите &quot;BooksController&quot;. Выберите &quot;использовать асинхронные действия контроллера&quot; флажок. Для **класс модели** выберите &quot;книги&quot;. (Если вы не видите `Book` класса в списке в раскрывающемся списке, убедитесь, что при построении проекта.) Затем нажмите кнопку «+».

![](create-a-rest-api-with-attribute-routing/_static/image7.png)

Нажмите кнопку **добавить** в **новый контекст данных** диалогового окна.

![](create-a-rest-api-with-attribute-routing/_static/image8.png)

Нажмите кнопку **добавить** в **добавить контроллер** диалогового окна. Формирование шаблонов добавляет класс с именем `BooksController` , определяющий контроллер API. Он также добавляет класс с именем `BooksAPIContext` в папку Models, который определяет контекст данных Entity Framework.

![](create-a-rest-api-with-attribute-routing/_static/image9.png)

### <a name="seed-the-database"></a>Начальное значение базы данных

Меню "Сервис" выберите **диспетчер пакетов библиотеки**, а затем выберите **консоль диспетчера пакетов**.

В окне консоли диспетчера пакетов введите следующую команду:

[!code-powershell[Main](create-a-rest-api-with-attribute-routing/samples/sample3.ps1)]

Эта команда создает папку Migrations и добавляет новый файл кода с именем Configuration.cs. Откройте этот файл и добавьте следующий код в `Configuration.Seed` метод.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample4.cs)]

В окне консоли диспетчера пакетов введите следующие команды.

[!code-powershell[Main](create-a-rest-api-with-attribute-routing/samples/sample5.ps1)]

Эти команды создают локальную базу данных и вызвают метод для заполнения ее начальными значениями.

![](create-a-rest-api-with-attribute-routing/_static/image10.png)

## <a name="add-dto-classes"></a>Добавление классов DTO

Если вы запускаете приложение и отправляете запрос GET /api/books/1, ответ выглядит примерно следующим. (Я добавил отступы для удобства чтения).

[!code-json[Main](create-a-rest-api-with-attribute-routing/samples/sample6.json)]

Вместо этого я хочу, чтобы этот запрос вернул подмножество полей. Кроме того необходимо вернуть имя автора, а не идентификатор автора. Чтобы сделать это, мы изменим методы контроллера для возврата *объект передачи данных* (DTO) вместо EF модели. Объект DTO — это объект, предназначенный только для доставки данных.

В обозревателе решений щелкните правой кнопкой мыши проект и выберите **добавить** | **новую папку**. Назовите папку &quot;DTO&quot;. Добавьте класс с именем `BookDto` в папку DTO со следующим определением:

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample7.cs)]

Добавьте еще один класс с именем `BookDetailDto`.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample8.cs)]

Затем обновите `BooksController` класс, чтобы вернуть `BookDto` экземпляр. Мы будем использовать [Queryable.Select](https://msdn.microsoft.com/library/system.linq.queryable.select.aspx) метод приведения экземпляра `Book` к `BookDto` экземпляру. Вот обновленный код для класса контроллера.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample9.cs)]

> [!NOTE]
> Я удалил `PutBook`, `PostBook`, и `DeleteBook` методы, поскольку они не нужны для этого учебника.


Теперь запустите приложение и запроса /api/books/1, текст ответа должен выглядеть следующим образом:

[!code-json[Main](create-a-rest-api-with-attribute-routing/samples/sample10.json)]

## <a name="add-route-attributes"></a>Добавление маршрутизации атрибутов

Далее мы будем преобразовать контроллер для использования маршрутизации атрибута. Сначала добавьте **RoutePrefix** атрибут к контроллеру. Этот атрибут определяет начальный сегмент URI-адресов для всех методов на этом контроллере.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample11.cs?highlight=1)]

Затем добавьте **[маршрута]** атрибуты действия контроллера, следующим образом:

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample12.cs?highlight=1,7)]

Шаблон маршрута для каждого метода контроллера — это префикс, а также строки, указанные в **маршруте** атрибута. Для `GetBook` метод шаблон маршрута включает параметризованная строка &quot;{id: int}&quot;, которой соответствует, если сегмент URI содержит целочисленное значение.

| Метод | Шаблон маршрута | Пример URI |
| --- | --- | --- |
| `GetBooks` | «api/books» | `http://localhost/api/books` |
| `GetBook` | «api электронной / {id: int}» | `http://localhost/api/books/5` |

## <a name="get-book-details"></a>Получение сведений о книги

Чтобы получить сведения о книге, клиент отправит запрос GET к `/api/books/{id}/details`, где *{id}* идентификатор книги.

Добавьте следующий метод в класс `BooksController`.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample13.cs)]

Если вы запрашиваете `/api/books/1/details`, ответ выглядит следующим образом:

[!code-json[Main](create-a-rest-api-with-attribute-routing/samples/sample14.json)]

## <a name="get-books-by-genre"></a>Получение книги по жанру

Чтобы получить список книг определенного жанра, клиент отправит запрос GET к `/api/books/genre`, где *жанр* имя жанр. (Например, `/api/books/fantasy`.)

Добавьте следующий метод `BooksController`.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample15.cs)]

Здесь мы определении маршрут, который содержит параметр {жанр} в шаблоне URI. Обратите внимание, что веб-API может различать эти два URI и перенаправлять их в различные методы:

`/api/books/1`

`/api/books/fantasy`

Причина этого заключается в `GetBook` метод включает ограничение, что сегмент «id» должно быть целым числом:

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample16.cs?highlight=1)]

Если вы запрашиваете /api/books/fantasy, ответ выглядит следующим образом:

`[ { "Title": "Midnight Rain", "Author": "Ralls, Kim", "Genre": "Fantasy" }, { "Title": "Maeve Ascendant", "Author": "Corets, Eva", "Genre": "Fantasy" }, { "Title": "The Sundered Grail", "Author": "Corets, Eva", "Genre": "Fantasy" } ]`

## <a name="get-books-by-author"></a>Получение книг по автору

Чтобы получить список книг по конкретному автору, клиент отправит запрос GET к `/api/authors/id/books`, где *идентификатор* идентификатор автора.

Добавьте следующий метод `BooksController`.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample17.cs)]

В этом примере представляет интерес из-за &quot;электронной&quot; — обрабатываются дочерний ресурс &quot;авторы&quot;. Этот шаблон является довольно часто в API RESTful.

Тильда (~) в шаблоне маршрута переопределяет префикс маршрута в **RoutePrefix** атрибута.

## <a name="get-books-by-publication-date"></a>Получить книги по дате публикации.

Чтобы получить список книг по дате публикации, клиент отправит запрос GET к `/api/books/date/yyyy-mm-dd`, где *гггг мм дд* дата.

Вот один из способов сделать это.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample18.cs)]

`{pubdate:datetime}` Параметр должен соответствовать **DateTime** . Это работает, но на самом деле это более допустимо, чем нам хотелось бы. Например эти URI также будет соответствовать маршрутам:

`/api/books/date/Thu, 01 May 2008`

`/api/books/date/2000-12-16T00:00:00`

Нет ничего плохого в том, чтобы разрешить эти URI. Тем не менее можно ограничить маршрут к определенному формату путем добавления ограничения регулярных выражений в шаблоне маршрута.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample19.cs?highlight=1)]

Теперь только даты в виде &quot;гггг мм дд&quot; будет соответствовать. Обратите внимание, что мы не используем регулярное выражение для проверки того, что у нас есть фактическая дата. При попытке преобразовать сегмент URI в веб-API, обрабатывается **DateTime** экземпляра. Обнаружении неверной даты, например "2012-47-99' не удается преобразовать, а клиент получит ошибку 404.

Можно также использовать разделитель косая черта (`/api/books/date/yyyy/mm/dd`) путем добавления другого **[маршрута]** атрибута с помощью различных регулярного выражения.

[!code-html[Main](create-a-rest-api-with-attribute-routing/samples/sample20.html)]

Здесь есть тонкая, но важная деталь. Второй шаблон маршрута присутствует подстановочный знак (\*) в начале параметра {pubdate}:

[!code-json[Main](create-a-rest-api-with-attribute-routing/samples/sample21.json)]

Подсистема обработки маршрутизации {pubdate} должен совпадать с остальной частью URI. По умолчанию параметр шаблона соответствует один сегмент URI. В этом случае мы хотим {pubdate} занимать несколько сегментов URI-адреса:

`/api/books/date/2013/06/17`

## <a name="controller-code"></a>Код контроллера

Ниже приведен полный код для класса BooksController.

[!code-csharp[Main](create-a-rest-api-with-attribute-routing/samples/sample22.cs)]

## <a name="summary"></a>Сводка

Атрибут маршрутизации предоставляет больший контроль и большую гибкость при разработке URI для API.
