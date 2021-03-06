---
uid: signalr/overview/older-versions/signalr-1x-hubs-api-guide-javascript-client
title: "Руководство API концентраторов 1.x SignalR - клиент JavaScript | Документы Microsoft"
author: pfletcher
description: "В этом документе содержатся вводные сведения с помощью API концентраторов SignalR версии 1.1 в клиентов JavaScript, таких как браузеры и магазина Windows (WinJS) паролей..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/17/2013
ms.topic: article
ms.assetid: dcd4593b-1118-418a-af71-d12ff33fb36d
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/older-versions/signalr-1x-hubs-api-guide-javascript-client
msc.type: authoredcontent
ms.openlocfilehash: f92470b2022f343cfd6d822abb255dc19947b4d1
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
<a name="signalr-1x-hubs-api-guide---javascript-client"></a>Руководство API концентраторов 1.x SignalR - клиент JavaScript
====================
по [Флетчера Патрик](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)

> В этом документе содержатся вводные сведения с помощью API концентраторов SignalR версии 1.1 в клиентов JavaScript, таких как браузеров и приложений для магазина Windows (WinJS).
> 
> API концентраторов SignalR позволяет вносить удаленные вызовы процедур (RPC) с сервера на подключенных клиентов и от клиентов к серверу. В серверном коде определять методы, которые могут быть вызваны клиентов и вызова методов, которые выполняются на клиенте. В клиентском коде определяют методы, которые могут быть вызваны с сервера и вызывать методы, которые выполняются на сервере. SignalR обеспечивает всех коммуникаций клиент сервер для вас.
> 
> SignalR также предлагает API нижнего уровня, который называется постоянные подключения. Общие сведения о SignalR, концентраторов и постоянные подключения или учебник, в котором показано, как построить полное приложение SignalR см. [SignalR — начало работы](../getting-started/index.md).


## <a name="overview"></a>Обзор

Этот документ содержит следующие разделы.

- [Созданный прокси-сервера и его действие автоматически](#genproxy)

    - [Когда следует использовать созданный прокси-сервера](#cantusegenproxy)
- [Установка клиента](#clientsetup)

    - [Способ создания ссылок динамически созданный прокси-сервера](#dynamicproxy)
    - [Как создать физический файл для SignalR созданный прокси-сервера](#manualproxy)
- [Как установить соединение](#establishconnection)

    - [$. connection.hub совпадает с объекта, $.hubConnection() создает](#connequivalence)
    - [Асинхронное выполнение метод start](#asyncstart)
- [Как установить соединение между доменами](#crossdomain)
- [Настройка соединения](#configureconnection)

    - [Как задать параметры строки запроса](#querystring)
    - [Как задать метод транспорта](#transport)
- [Как получить учетную запись-посредник для класса концентратора](#getproxy)
- [Как определить на стороне клиента, сервер может вызывать методы](#callclient)
- [Способ вызова методов сервера от клиента](#callserver)
- [Как обрабатывать события времени жизни соединений](#connectionlifetime)
- [Способ обработки ошибок](#handleerrors)
- [Как включить ведение журнала на стороне клиента](#logging)

Документацию по программированию сервера или клиентов .NET см. следующие ресурсы:

- [Справочник по API концентраторов SignalR - сервера](../guide-to-the-api/hubs-api-guide-server.md)
- [Справочник по API концентраторов SignalR - клиент .NET](../guide-to-the-api/hubs-api-guide-net-client.md)

Ссылки на разделы справки по API, до версии .NET 4.5 API-интерфейса. Если вы используете .NET 4, см. раздел [версии .NET 4 разделов API](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).

<a id="genproxy"></a>

## <a name="the-generated-proxy-and-what-it-does-for-you"></a>Созданный прокси-сервера и его действие автоматически

Вы можете программировать клиента JavaScript для взаимодействия со службой SignalR с или без учетной записи-посредника, SignalR создает для вас. Прокси-сервер может сделать для вас является упрощение синтаксис кода можно использовать для подключения, методы записи, которые сервер вызывает метод, и вызывать методы на сервере.

При написании кода для вызова методов сервера, созданного прокси позволяет использовать синтаксис, впечатление, что выполнялись локальной функции: можно написать `serverMethod(arg1, arg2)` вместо `invoke('serverMethod', arg1, arg2)`. Синтаксис созданный прокси также позволяет немедленно и понятный ошибка клиентского, если введено имя метода сервера. И если вручную создать файл, определяющий прокси-серверы, можно также получить поддержку IntelliSense для написания кода, который вызывает методы сервера.

Например предположим, что имеется следующий класс концентратора на сервере:

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample1.cs?highlight=1,3,5)]

В следующих примерах кода показано, имеет следующий код JavaScript, например для вызова `NewContosoChatMessage` метод на сервере и получать вызовы `addContosoChatMessageToPage` метод с сервера.

**Созданный прокси-сервер**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample2.js?highlight=1-2,8)]

**Без созданный прокси-сервера**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample3.js?highlight=2-3,9)]

<a id="cantusegenproxy"></a>

### <a name="when-to-use-the-generated-proxy"></a>Когда следует использовать созданный прокси-сервера

Если вы хотите зарегистрировать несколько обработчиков событий для, сервер вызывает клиентский метод, нельзя использовать созданный прокси-сервера. В противном случае можно использовать созданный прокси-сервер или не зависит от настроек написания кода. Если вы решили не использовать его, нет необходимости ссылаться на URL-адрес «и концентраторы signalr» в `script` элемент в коде клиента.

<a id="clientsetup"></a>

## <a name="client-setup"></a>Установка клиента

Клиент JavaScript требуются ссылки jQuery и файл JavaScript SignalR core. JQuery версия должна быть 1.6.4 или основных более поздней версии, например 1.7.2, 1.8.2 или 1.9.1. Если вы решили использовать созданный прокси-сервер, необходимо также ссылку на прокси-сервера SignalR созданный файл JavaScript. Следующий пример показывает, как может выглядеть ссылок на HTML-странице, который использует созданный прокси.

[!code-html[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample4.html)]

Эти ссылки должны быть включены в следующем порядке: jQuery, SignalR core после этого и прокси-SignalR Фамилия.

<a id="dynamicproxy"></a>

### <a name="how-to-reference-the-dynamically-generated-proxy"></a>Способ создания ссылок динамически созданный прокси-сервера

В предыдущем примере ссылку на созданный SignalR прокси-сервера — динамически создаваемый код JavaScript, не в физическом файле. SignalR создает код JavaScript для прокси-сервера в режиме реального времени и доставляет его клиенту в ответ на URL-адрес «/ signalr/концентраторов». Если указан другой базовый URL-адрес для подключения SignalR на сервере в вашей `MapHubs` , URL-адрес для динамически создаваемого файла посредника является пользовательский URL-адрес с «/ концентраторов» к нему.

> [!NOTE]
> Для клиентов Windows 8 (магазин Windows) JavaScript используйте прокси физический файл вместо того, динамически создаваемых. Дополнительные сведения см. в разделе [как создать физический файл для SignalR созданный прокси](#manualproxy) далее в этом разделе.


В представлении ASP.NET MVC 4 Razor используйте тильда для обращения к корневому каталогу приложения в ссылке файл прокси-сервера:

[!code-html[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample5.html)]

Дополнительные сведения об использовании SignalR в MVC 4 см. в разделе [Приступая к работе с SignalR и MVC 4](tutorial-getting-started-with-signalr-and-mvc-4.md).

В представлении ASP.NET MVC 3 Razor, используйте `Url.Content` для прокси-сервера файл справки:

[!code-cshtml[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample6.cshtml)]

В приложении веб-форм ASP.NET использовать `ResolveClientUrl` прокси, ссылка на файл или зарегистрируйте его через ScriptManager, с помощью приложения относительный путь от корня (начинающийся с тильды):

[!code-aspx[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample7.aspx)]

Как правило используется тот же метод для указания URL-адрес «/ signalr/концентраторов», используемого для файлов CSS и JavaScript. При указании URL-адрес без использования тильда в некоторых сценариях приложение будет работать правильно при тестирования в Visual Studio с помощью IIS Express, но завершится с ошибкой 404 при развертывании до полной версии IIS. Дополнительные сведения см. в разделе **разрешения ссылки на ресурсы на корневом уровне** в [веб-серверов в Visual Studio для веб-проектов ASP.NET](https://msdn.microsoft.com/library/58wxa9w5.aspx) на сайте MSDN.

При запуске веб-проекта в Visual Studio 2012 в режиме отладки, и при использовании Internet Explorer как браузер, можно просмотреть файл прокси в **обозревателе решений** под **документов скрипта**, как показано в на рисунке.

![Файл созданного прокси JavaScript в обозревателе решений](signalr-1x-hubs-api-guide-javascript-client/_static/image1.png)

Чтобы просмотреть содержимое файла, дважды щелкните **концентраторов**. Если вы не используете Visual Studio 2012 и Internet Explorer, или если вы не в режиме отладки, содержимое файла можно получить, перейдя по URL-адресу «/ signalR/концентраторов». Например, если сайт работает в `http://localhost:56699`, перейдите в меню `http://localhost:56699/SignalR/hubs` в браузере.

<a id="manualproxy"></a>

### <a name="how-to-create-a-physical-file-for-the-signalr-generated-proxy"></a>Как создать физический файл для SignalR созданный прокси-сервера

В качестве альтернативы для динамически создаваемых прокси-сервера можно создать физический файл, содержащий код прокси и ссылку на этот файл. Вы можете сделать это для управления кэшированием или объединении поведение или получить доступ к IntelliSense при написании вызовы методов сервера.

Чтобы создать прокси-файл, выполните следующие действия:

1. Установка [Microsoft.AspNet.SignalR.Utils](https://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) пакет NuGet.
2. Откройте командную строку и перейдите к *средства* папку, содержащую файл SignalR.exe. В папку средств находится по следующему адресу:

    `[your solution folder]\packages\Microsoft.AspNet.SignalR.Utils.1.0.1\tools`
3. Введите следующую команду:

    `signalr ghp /path:[path to the .dll that contains your Hub class]`

    Путь к вашей *.dll* обычно *bin* папку в папке проекта.

    Эта команда создает файл с именем *server.js* в той же папке, что *signalr.exe*.
4. Поместите *server.js* файла в соответствующую папку в проекте, переименуйте его в зависимости от приложения и добавьте ссылку на него вместо ссылки «и концентраторы signalr».

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a>Как установить соединение

Перед установкой соединения необходимо создать объект подключения, создайте учетную запись-посредник и регистрируют обработчики событий для методов, которые могут быть вызваны с сервера. При настройке прокси-сервера и обработчики событий, установить соединение, вызвав `start` метод.

Если используется созданный прокси-сервер не нужно создать объект подключения в собственном коде, поскольку созданный код прокси делает это для вас.

<a id="nogenconnection"></a>

**Установить подключение (созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample8.js?highlight=5)]

**Для подключения (без созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample9.js?highlight=1,6)]

В примере кода используется значение по умолчанию «/ signalr» URL-адрес для подключения к службе SignalR. Сведения о том, как задать другой базовый URL-адрес см. в разделе [API концентраторов руководство - Server - /signalr URL-адрес для ASP.NET SignalR](../guide-to-the-api/hubs-api-guide-server.md#signalrurl).

> [!NOTE]
> Обычно регистрировать обработчики событий до вызова метода `start` метод, чтобы установить соединение. Если вы хотите зарегистрировать обработчики событий после установления соединения, это можно сделать, но необходимо зарегистрировать хотя бы один из вашего размещения обработчиков событий до вызова метода `start` метод. Одна из причин для этого — в приложении может быть много концентраторы, что не следует запускать `OnConnected` событий на каждый концентратора, если необходимо только для одного из них. Если соединение установлено, наличие метод клиента на прокси-сервера концентратора является сообщает, что SignalR для запуска `OnConnected` событий. Если вы не зарегистрируете все обработчики событий, перед вызовом метода `start` метод, можно вызывать методы концентратора, но концентратора `OnConnected` не будет вызван метод и методы клиента не будет вызываться с сервера.


<a id="connequivalence"></a>

### <a name="connectionhub-is-the-same-object-that-hubconnection-creates"></a>$. connection.hub совпадает с объекта, $.hubConnection() создает

Как видно из примеров, при использовании созданный прокси `$.connection.hub` ссылается на объект подключения. Это тот же объект, который можно получить, вызвав `$.hubConnection()` при неиспользуемые созданного прокси. Созданный код прокси создает подключение, выполнив следующую инструкцию:

![Создание подключения в файле созданный прокси-сервера](signalr-1x-hubs-api-guide-javascript-client/_static/image3.png)

Когда вы используете созданный прокси-сервера, можно делать все с `$.connection.hub` , можно сделать с помощью объекта подключения, когда вы не используете созданного прокси.

<a id="asyncstart"></a>

### <a name="asynchronous-execution-of-the-start-method"></a>Асинхронное выполнение метод start

`start` Метод выполняется асинхронно. Он возвращает [объекта отложенный jQuery](http://api.jquery.com/category/deferred-object/), что означает, что можно добавить функции обратного вызова, вызывая методы, такие как `pipe`, `done`, и `fail`. Если у вас есть код, который должен выполняться после установки подключения, например вызов метода сервера, необходимо включить соответствующий код в функции обратного вызова или вызывать из функции обратного вызова. `.done` Метод обратного вызова выполняется после установления соединения и после любой код, который имеется вашей `OnConnected` метод обработчика событий на сервере завершает выполнение.

Если поместить оператор «Подключение» из предыдущего примера в следующей строке кода после `start` вызова метода (не в `.done` обратного вызова), `console.log` строки будет выполняться до установления соединения, как показано в следующем Пример:

![Неправильный способ написания кода, который выполняется после установки подключения](signalr-1x-hubs-api-guide-javascript-client/_static/image5.png)

<a id="crossdomain"></a>

## <a name="how-to-establish-a-cross-domain-connection"></a>Как установить соединение между доменами

Как правило, если браузер загружает страницу из `http://contoso.com`, подключения SignalR находится в том же домене, в `http://contoso.com/signalr`. Если страницы из `http://contoso.com` подключается к `http://fabrikam.com/signalr`, то есть соединение между доменами. По соображениям безопасности соединения между доменами отключены по умолчанию. Для установления соединения между доменами, убедитесь, что соединения между доменами включены на сервере и укажите URL-адрес подключения при создании объекта подключения. SignalR подходящей технологии для подключения будет использоваться между доменами, такие как [JSONP](http://en.wikipedia.org/wiki/JSONP) или [CORS](http://en.wikipedia.org/wiki/Cross-origin_resource_sharing).

На сервере, включить подключения между доменами, выбрав соответствующий параметр, при вызове `MapHubs` метода.

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample10.cs?highlight=2)]

На стороне клиента укажите URL-адрес при создании объекта подключения (без созданный прокси-сервера) или прежде чем вызывать метод start (с созданный прокси-сервера).

**Клиентский код, который определяет связь между доменами (с помощью созданного прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample11.js?highlight=1)]

**Клиентский код, который определяет связь между доменами (без созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample12.js?highlight=1)]

При использовании `$.hubConnection` конструктор, не обязательно включать `signalr` в URL-адрес, поскольку она добавляется автоматически (если не указано `useDefaultUrl` как `false`).

Можно создать несколько подключений к другим конечным точкам.

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample13.js)]

> [!NOTE] 
> 
> - Не задано `jQuery.support.cors` значение true в коде.
> 
>     ![Не присвоено значение true jQuery.support.cors](signalr-1x-hubs-api-guide-javascript-client/_static/image7.png)
> 
>     SignalR обеспечивает использование JSONP или CORS. Параметр `jQuery.support.cors` в значение true отключает JSONP, так как вызывает SignalR предположить браузером CORS.
> - При подключении к URL-адрес localhost, Internet Explorer 10 не считают, что соединение между доменами, приложение будет использовать локально IE 10 даже если вы не включили подключений между доменами на сервере.
> - Сведения об использовании подключения между доменами с Internet Explorer 9 в разделе [этот поток StackOverflow](http://stackoverflow.com/questions/13573397/siganlr-ie9-cross-domain-request-dont-work).
> - Сведения об использовании подключения между доменами с Chrome см. в разделе [этот поток StackOverflow](http://stackoverflow.com/questions/15467373/signalr-1-0-1-cross-domain-request-cors-with-chrome).
> - В примере кода используется значение по умолчанию «/ signalr» URL-адрес для подключения к службе SignalR. Сведения о том, как задать другой базовый URL-адрес см. в разделе [API концентраторов руководство - Server - /signalr URL-адрес для ASP.NET SignalR](../guide-to-the-api/hubs-api-guide-server.md#signalrurl).


<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a>Настройка соединения

Перед установкой соединения можно указать параметры строки запроса или Указание метода транспортировки.

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a>Как задать параметры строки запроса

Если вы хотите отправлять данные на сервер при подключении клиента, можно добавить параметры строки запроса для объекта соединения. В следующих примерах параметра строки запроса в клиентском коде.

**Задайте значение строки запроса перед вызовом метода start (с созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample14.js?highlight=1)]

**Задайте значение строки запроса перед вызовом метода start (без созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample15.js?highlight=2)]

Приведенный ниже показано, как прочитать параметр строки запроса в серверном коде.

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample16.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a>Как задать метод транспорта

В составе процесса подключения клиента SignalR обычно запрашивает у сервера для определения наиболее транспорта, который поддерживается с сервера и клиента. Если вы уже знаете, какие транспорта, который вы хотите использовать, вы можете обойти этот процесс согласования путем указания метода транспортировки при вызове `start` метода.

**Клиентский код, который задает метод передачи (с созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample17.js?highlight=1)]

**Клиентский код, который задает метод передачи (без созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample18.js?highlight=2)]

В качестве альтернативы можно указать несколько методов перевозки в том порядке, в котором нужно SignalR оценить их:

**Клиентский код, который определяет резервный схемы пользовательского транспорта (с созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample19.js?highlight=1)]

**Клиентский код, который определяет резервный схемы пользовательского транспорта (без созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample20.js?highlight=2)]

Для указания метода транспортировки, можно использовать следующие значения:

- «webSockets»
- «foreverFrame»
- «serverSentEvents»
- «longPolling»

Следующие примеры показывают, как выяснить, какой метод транспорта используется соединение.

**Клиентский код, который отображает метод транспортировки, используемые соединением (с созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample21.js?highlight=2)]

**Клиентский код, который отображает метод транспортировки, используемый для подключения (без созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample22.js?highlight=3)]

Сведения о проверке метод транспорта в серверном коде см. в разделе [ASP.NET руководство по API концентраторов SignalR - Server - способы получения сведений о клиенте из контекстного свойства](../guide-to-the-api/hubs-api-guide-server.md#contextproperty). Дополнительные сведения о транспортов и в случае ошибки см. в разделе [Общие сведения о SignalR - транспорта и в случае ошибки](../getting-started/introduction-to-signalr.md#transports).

<a id="getproxy"></a>

## <a name="how-to-get-a-proxy-for-a-hub-class"></a>Как получить учетную запись-посредник для класса концентратора

Каждый объект соединения, создаваемого инкапсулирует сведения о подключении к службе SignalR, которая содержит один или несколько классов Hub. Для взаимодействия с классом концентратора используется прокси-объекта, созданные пользователем (Если вы не используете созданный прокси-сервера) или которых создается автоматически.

На клиентском компьютере имя прокси-сервера — это версия стиле Camel, от имени класса концентратора. SignalR автоматически делает это изменение, чтобы код JavaScript может соответствовать соглашениям JavaScript.

**Класс концентратора на сервере**

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample23.cs?highlight=1)]

**Получить ссылку на созданный прокси клиента для концентратора**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample24.js?highlight=1)]

**Создайте клиентский прокси для класса концентратора (без созданный прокси-сервера)**

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample25.cs?highlight=1)]

Если вы устанавливаете класса концентратора `HubName` атрибут, используйте точное имя без Смена регистра.

**Класс концентратора на сервере с атрибутом HubName**

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample26.cs?highlight=1)]

**Получить ссылку на созданный прокси клиента для концентратора**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample27.js?highlight=1)]

**Создайте клиентский прокси для класса концентратора (без созданный прокси-сервера)**

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample28.cs?highlight=1)]

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a>Как определить на стороне клиента, сервер может вызывать методы

Чтобы определить метод, который сервер может вызывать от концентратора, добавьте обработчик событий прокси-сервер концентратора с помощью `client` свойства созданного прокси или вызова `on` метод, если вы не используете созданного прокси. Параметры могут иметь сложные объекты.

Добавьте обработчик событий, перед вызовом метода `start` метод, чтобы установить соединение. (Если вы хотите добавить обработчики событий после вызова `start` метода, см. Примечание в [как для установления соединения](#establishconnection) ранее в этом документе и используйте синтаксис, показанный для определения метода без использования созданного прокси.)

Метод совпадение имен регистр не учитывается. Например `Clients.All.addContosoChatMessageToPage` на сервере будет выполняться `AddContosoChatMessageToPage`, `addContosoChatMessageToPage`, или `addcontosochatmessagetopage` на стороне клиента.

**Определить метод для клиента (с использованием созданного прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample29.js?highlight=2)]

**Альтернативный способ определить метод для клиента (с использованием созданного прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample30.js?highlight=1-2)]

**Определение метода на клиенте (без созданный прокси-сервера, или при добавлении после вызова метода start)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample31.js?highlight=3)]

**Код сервера, который вызывает метод клиента**

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample32.cs?highlight=5)]

Следующие примеры включают сложного объекта как параметра метода.

**Определение метода на клиенте, принимающего сложный объект (с созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample33.js?highlight=2-3)]

**Определение метода на клиенте, принимающего сложный объект (без созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample34.js?highlight=3-4)]

**Код сервера, который определяет сложный объект**

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample35.cs?highlight=1)]

**Кода сервера, который вызывает метод клиента с помощью сложного объекта**

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample36.cs?highlight=3)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a>Способ вызова методов сервера от клиента

Для вызова метода сервера от клиента, используйте `server` свойства созданного прокси-сервера или `invoke` метод прокси концентратора, если вы не используете созданного прокси. Возвращаемое значение или параметры могут быть сложными объектами.

Передайте в стиле Camel версии имя метода на концентраторе. SignalR автоматически делает это изменение, чтобы код JavaScript может соответствовать соглашениям JavaScript.

Как вызвать метод сервера, который не имеет возвращаемого значения и способ вызова метода сервера, который имеет значение, возвращаемое в следующих примерах.

**Метод сервера без атрибута HubMethodName**

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample37.cs?highlight=3)]

**Код сервера, который определяет сложный объект, переданный в параметре**

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample38.cs)]

**Клиентский код, вызывающий метод сервера (с созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample39.js?highlight=1)]

**Клиентский код, вызывающий метод сервера (без созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample40.js?highlight=1)]

Если внутреннего метода концентратора с `HubMethodName` атрибут, используйте его без Смена регистра.

**Метод сервера** с атрибутом HubMethodName

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample41.cs?highlight=3)]

**Клиентский код, вызывающий метод сервера (с созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample42.js?highlight=1)]

**Клиентский код, вызывающий метод сервера (без созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample43.js?highlight=1)]

Выше примерах показано, как вызвать метод сервера, который не имеет возвращаемого значения. Следующие примеры показывают, как вызвать метод сервера, который имеет возвращаемое значение.

**Серверный код для метода, который имеет возвращаемое значение**

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample44.cs?highlight=3)]

**Биржевая класс, используемый для** возвращаемое значение

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample45.cs?highlight=1)]

**Клиентский код, вызывающий метод сервера (с созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample46.js?highlight=2,4-5)]

**Клиентский код, вызывающий метод сервера (без созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample47.js?highlight=2,4-5)]

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a>Как обрабатывать события времени жизни соединений

SignalR обеспечивает следующее подключение события времени жизни, которые можно обрабатывать:

- `starting`: Возникает перед отправкой данных через соединение.
- `received`: Возникает при получении данных для соединения. Предоставляет полученных данных.
- `connectionSlow`: Возникает, если клиент обнаруживает подключение медленное или часто удаление.
- `reconnecting`: Возникает в случае транспорта начинает повторное подключение.
- `reconnected`: Возникает, когда была повторно присоединена транспорта.
- `stateChanged`: Возникает при изменении состояния подключения. Предоставляет состояния старое и новое состояние (подключение, подключен, Reconnecting или Disconnected).
- `disconnected`: Возникает, когда произойдет отключение соединения.

Например, если вы хотите отображать предупреждения при наличии проблем подключения, которые могут привести к значительным задержкам, обрабатывать `connectionSlow` событий.

**Обработать событие connectionSlow (с созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample48.js?highlight=1)]

**Обработать событие connectionSlow (без созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample49.js?highlight=2)]

Дополнительные сведения см. в разделе [понимание и обработка события времени жизни соединений в SignalR](index.md).

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a>Способ обработки ошибок

Клиент SignalR JavaScript предоставляет `error` , можно добавить обработчик для события. Можно также использовать метод fail Добавление обработчика для ошибки, возникающие в результате вызова метода сервера.

Если подробные сообщения об ошибках на сервере не включен явно, объект исключения, которое возвращает SignalR после возникновения ошибки содержит минимум информации об ошибке. Например, если вызов `newContosoChatMessage` завершается ошибкой, содержит сообщение об ошибке в объекте ошибки «`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`» отправки подробных сообщений об ошибках на клиентах в рабочей среде не рекомендуется по соображениям безопасности, но если вы хотите включить подробные сообщения об ошибках для устранения неполадок, используйте следующий код на сервере.

[!code-csharp[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample50.cs?highlight=2)]

Следующий пример демонстрирует добавление обработчика для события ошибки.

**Добавьте обработчик ошибок (с созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample51.js?highlight=1)]

**Добавьте обработчик ошибок (без созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample52.js?highlight=2)]

В следующем примере показано, как обрабатывать ошибку из вызова метода.

**Обработать ошибку из вызова метода (с созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample53.js?highlight=2)]

**Обработать ошибку из вызова метода (без созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample54.js?highlight=2)]

При сбое вызова метода `error` событие также создается, поэтому в коде `error` обработчик метода и в `.fail` бы выполнить метод обратного вызова.

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a>Как включить ведение журнала на стороне клиента

Чтобы включить ведение журнала клиентского соединения, установите `logging` свойство в объекте подключения перед вызовом метода `start` метод, чтобы установить соединение.

**Включить ведение журнала (с созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample55.js?highlight=1)]

**Включить ведение журнала (без созданный прокси-сервера)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-javascript-client/samples/sample56.js?highlight=2)]

Чтобы просмотреть журналы, откройте в браузере средств разработчика и перейдите на вкладку консоли. Учебник, в котором показан экран и пошаговые инструкции снимков, показывающих, как это сделать, см. [Server широковещательных пакетов с помощью ASP.NET Signalr - включить ведение журнала](index.md).
