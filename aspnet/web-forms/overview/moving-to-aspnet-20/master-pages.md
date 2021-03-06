---
uid: web-forms/overview/moving-to-aspnet-20/master-pages
title: Главные страницы | Документы Microsoft
author: microsoft
description: Одним из ключевых компонентов успешного веб-сайт является согласованного внешнего вида и поведения. В ASP.NET 1.x, разработчикам использовать пользовательские элементы управления для репликации общих elem. страницы...
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2005
ms.topic: article
ms.assetid: 9c0cce4d-efd9-4c14-b0e8-a1a140abb3f4
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/moving-to-aspnet-20/master-pages
msc.type: authoredcontent
ms.openlocfilehash: f45dd9704f665244d2a48ec000326f6e98984e4f
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
<a name="master-pages"></a>Главные страницы
====================
по [Microsoft](https://github.com/microsoft)

> Одним из ключевых компонентов успешного веб-сайт является согласованного внешнего вида и поведения. В ASP.NET 1.x, разработчики использовали пользовательские элементы управления для репликации Общие элементы страницы на веб-приложения. Хотя это определенно приемлемым решением, с помощью пользовательских элементов управления есть некоторые недостатки. Например изменения положения элемента управления пользователя требует изменения на несколько страниц на сайте. Пользовательские элементы управления также не подготавливаются к просмотру в режиме конструктора после вставкой на странице.


Одним из ключевых компонентов успешного веб-сайт является согласованного внешнего вида и поведения. В ASP.NET 1.x, разработчики использовали пользовательские элементы управления для репликации Общие элементы страницы на веб-приложения. Хотя это определенно приемлемым решением, с помощью пользовательских элементов управления есть некоторые недостатки. Например изменения положения элемента управления пользователя требует изменения на несколько страниц на сайте. Пользовательские элементы управления также не подготавливаются к просмотру в режиме конструктора после вставкой на странице.

ASP.NET 2.0 появился Master страниц способ сохранения согласованного внешнего вида и поведения, а как вы скоро увидите, основной страницы представляют существенным усовершенствованием по метода управления пользователя.

## <a name="why-master-pages"></a>Почему главные страницы?

Возможно, вас интересует почему требовались главных страниц в ASP.NET 2.0. В конце концов, разработчиков веб-сайт уже используете пользовательские элементы управления в ASP.NET 1.x для совместного использования содержимого области между страницами. Существует фактически несколько причин, почему пользовательские элементы управления являются менее чем оптимальное решение для создания макета страницы.

Пользовательские элементы управления не определил фактически макета страницы. Вместо этого они определяют структуру и функциональные возможности для части страницы. Различие между этими двумя важно, поскольку это повышает управляемость решения управления пользователя гораздо сложнее. Например если вы хотите изменить положение пользовательского элемента управления на странице, необходимо изменить Фактическая страница, на которой появляется пользовательский элемент управления. Если у вас есть несколько страниц, но в больших сайтах быстро становится кошмаром управления сайта, прекрасная thats!

Другим недостатком использования пользовательских элементов управления для определения макета страницы заключена в архитектуре самой ASP.NET. Если открытый член пользовательского элемента управления изменяется, он требует повторной компиляции всех страниц, использующих пользовательский элемент управления. В свою очередь ASP.NET будет получить доступ к страницам при первом повторного JIT. Как и раньше, получается-масштабируемой архитектуры и проблемы управления сайта для больших сайтов.

Обе эти проблемы (и многое другое) хорошо адресуются с помощью главных страниц в ASP.NET 2.0.

## <a name="how-master-pages-work"></a>Как функционируют главные страницы

Главная страница является аналогом шаблона для других страниц. Элементы страницы, которые должны совместно использоваться другие страницы (т. е. меню, границы, т. д.) добавляются на главную страницу. При добавлении новых страниц на сайте можно связать их с главной страницей. Страницы, которая связана с главной страницей называется **страницы содержимого**. По умолчанию страницы содержимого принимает внешний вид с главной страницы. Однако при создании главной страницы, можно определить частей страницы, страницы содержимого можно заменить его собственного содержимого. Эти части определяются с помощью нового элемента управления, появившиеся в ASP.NET 2.0. **ContentPlaceHolder** элемента управления.

Главная страница может содержать любое количество элементов управления ContentPlaceHolder (или вообще). На странице содержимого содержимое от элементов управления ContentPlaceHolder появляется внутри **содержимого** элементы управления, другой новый элемент управления в ASP.NET 2.0. По умолчанию страницы содержимого, элементы управления содержимым не указано, чтобы можно было предоставить собственное содержимое. Если вы хотите использовать содержимое с главной страницы внутри элементов управления содержимым, это можно сделать так, как вы узнаете позже в этом модуле. Элемент управления содержимым сопоставляется управления ContentPlaceHolder через атрибут ContentPlaceHolderID элемента управления содержимым. Код ниже maps элементу управления содержимым к элементу управления ContentPlaceHolder вызывается mainBody на главной странице.

[!code-aspx[Main](master-pages/samples/sample1.aspx)]

> [!NOTE]
> Часто вы услышите людей, которые описывают главные страницы, как базовый класс для всех других страниц. Thats фактически не true. Связь между главные страницы и страницы содержимого, не является одним из наследования.


**Рис. 1** показано главной страницы и связанного содержимого страницы, как они отображаются в Visual Studio 2005. Вы увидите управления ContentPlaceHolder в главной страницы и соответствующий элемент управления на странице содержимого содержимым. Обратите внимание, что содержимое главной страницы, выходящее за пределы ContentPlaceHolder видима, но выделена серым цветом, на странице содержимого. Только содержимое внутри ContentPlaceHolder может быть замещения страницы содержимого. Все остальное содержимое из главной страницы является неизменяемым.


![Главная страница и связанные с ней страницы содержимого](master-pages/_static/image1.jpg)

**Рис. 1**: главной страницы и связанные с ней страницы содержимого


## <a name="creating-a-master-page"></a>Создание главной страницы

Для создания новой главной страницы:

1. Откройте Visual Studio 2005 и создайте новый веб-сайт.
2. Щелкните новый файл, файл.
3. Выберите образец файла в диалоговом окне Добавление нового элемента, как показано в **рис. 2**.
4. Нажмите кнопку Добавить.


![Создание новой главной страницы](master-pages/_static/image2.jpg)

**На рисунке 2**: Создание новой главной страницы


Обратите внимание, что расширение файла для главной страницы <em>.master</em>. Это один из способов, главной страницы отличается от обычных страниц. Главным различием является, вместо @Page директив, Главная страница содержит @Master директивы. Переключитесь в режим конструктора для шаблона страницы вы только что создали и проверять код.

По умолчанию в новой главной страницы будут иметь один элемент управления ContentPlaceHolder. В большинстве случаев смысл сначала создать общие элементы страниц, а затем добавить элементы управления ContentPlaceHolder где требуемого пользовательского содержимого. В таких случаях разработчикам может потребоваться удалить элемент управления по умолчанию ContentPlaceHolder и добавлять новые как разработано на странице. Элементы управления ContentPlaceHolder не изменяется, несмотря на то, что они отображаются маркеры изменения размера. Размеры элемента управления ContentPlaceHolder автоматически на основе содержимого, содержащейся в ней с одним исключением; Если вы поместили элемент управления ContentPlaceHolder внутри блока, например ячейки таблицы, он будет масштабироваться в соответствии с размером элемента.

## <a name="lab-1-working-with-master-pages"></a>Практическое занятие 1 Работа с главными страницами

В этой лаборатории будет создание новой главной страницы и определить три элемента управления ContentPlaceHolder. Будет затем создайте новую страницу содержимого и замените содержимое по крайней мере один из элементов управления ContentPlaceHolder.

1. Создайте главную страницу и добавить элементы управления ContentPlaceHolder. 

    1. Создание новой главной страницы, как описано выше.
    2. Удалите элемент управления ContentPlaceHolder по умолчанию.
    3. Выберите элемент управления ContentPlaceHolder, щелкнув затененные верхней границы элемента управления и затем удалите его, нажав клавишу DEL на клавиатуре.
    4. Вставка новой таблицы с помощью *заголовок и сторона* шаблона, как показано на рис. 3. Измените ширину и высоту на 90%, чтобы вся таблица отображается в конструкторе.


![](master-pages/_static/image3.jpg)

**Рис. 3**


1. Поместите курсор в каждой ячейке таблицы и задать *valign* свойства *верхней*.
2. Из панели элементов Вставка элемента управления ContentPlaceHolder верхнюю ячейку таблицы (ячейку заголовка.)
3. При вставке этого элемента управления ContentPlaceHolder можно заметить, что высота строки может занять почти всю страницу как показано на рисунке 4. На этом этапе стоит подумать об этом.


![Пустое место находится в той же ячейке, как ContentPlaceHolder](master-pages/_static/image1.gif)

**Рис. 4**: пустое место находится в той же ячейке, как ContentPlaceHolder


1. Поместите управления ContentPlaceHolder в другие две ячейки. После вставки в других элементах управления ContentPlaceHolder размер ячеек таблицы должен быть должным образом. Страницы должен выглядеть как страница, отображаемая в **рис. 5**.


![Образец со всеми элементами управления ContentPlaceHolder. Обратите внимание, что высота ячейки для ячейки заголовка теперь его необходимо](master-pages/_static/image2.gif)

**Рис. 5**: главный со всеми элементами управления ContentPlaceHolder. Обратите внимание, что высота ячейки для ячейки заголовка теперь его необходимо


1. В каждой из трех элементов управления ContentPlaceHolder введите текст по своему усмотрению.
2. Сохранение exercise1.master главной страницы.
3. Создание нового веб-формы и связать его с exercise1.master главной страницы.
4. Выберите файл, новый, файлов в Visual Studio 2005.
5. Выберите **веб-формы** в диалоговом окне Добавление нового элемента.
6. Убедитесь, что установлен флажок использовать главную страницу, как показано на рис. 6.


![Добавление новой страницы содержимого](master-pages/_static/image3.gif)

**Рис. 6**: Добавление новой страницы содержимого


1. Нажмите кнопку Добавить.
2. Выберите exercise1.master в инструкции Select главной страницы диалоговое окно, как показано на рисунке 7.
3. Нажмите кнопку ОК, чтобы добавить новую страницу содержимого.

В Visual Studio появляется новая страница содержимого с одного элемента управления содержимым для каждого элемента управления ContentPlaceHolder на главной странице. По умолчанию элементы управления содержимым не указано, чтобы можно было добавить собственное содержимое. Если для них для использования содержимого с элементом управления ContentPlaceHolder на главной странице youd, просто щелкните смарт-тег символа (маленькой черной стрелки в правом верхнем углу элемента управления) и выберите *по умолчанию образцы содержимого* в смарт-теге, как показано в **рис. 8**. При этом элемент меню изменяется на *создать настраиваемый содержимое*. Удаляет содержимое из главной страницы, что позволяет определить настраиваемое содержимое для этого конкретного содержимого элемента управления, щелкнув его в этот момент.


![Задание элемента управления значение по умолчанию для содержимого страницы мастера](master-pages/_static/image4.gif)

**Рис. 7**: элемент управления содержимым установите значение по умолчанию для содержимого страницы мастера


## <a name="connecting-master-page-and-content-pages"></a>Подключение главной страницы и страницы содержимого

Связь между главной страницы и страницы содержимого можно настроить одним из четырех способов:

- <strong>MasterPageFile</strong> атрибут @Page директивы
- Установка **Page.MasterPageFile** свойства в коде.
- **&lt;Страниц&gt;** элемент в файле конфигурации приложения (web.config в корневой папке приложения)
- **&lt;Страниц&gt;** элемент в файле конфигурации (web.config во вложенной папке), вложенные папки

## <a name="masterpagefile-attribute"></a>Атрибут MasterPageFile

Атрибут MasterPageFile упрощает применение главной страницы для определенной страницы ASP.NET. Это метод, используемый для применения главной страницы при проверке **выбрать главную страницу** флажок, как описано в упражнении 1.

## <a name="setting-pagemasterpagefile-in-code"></a>Установка Page.MasterPageFile в коде

Задав свойство MasterPageFile в коде, можно применить определенной главной страницы к содержимому во время выполнения. Это полезно в случаях, когда необходимо применить конкретной главной страницы на основе ролей пользователей или некоторых других критериев. Свойство MasterPageFile задается в методе PreInit. Если он установлен после метода PreInit, будет создано исключение InvalidOperationException. Страницы, для которого задается это свойство также должно быть содержимого элемента управления как элемент верхнего уровня для страницы. В противном случае HttpException возникает, если установить свойство MasterPageFile.

## <a name="using-the-ltpagesgt-element"></a>С помощью &lt;страниц&gt; элемент

Главная страница для ваших страниц можно настроить, задав атрибут masterPageFile &lt;страниц&gt; элемента в файле web.config. При использовании этого метода, имейте в виду, что этот параметр можно переопределить файлов web.config, ниже в структуре приложения. Любой MasterPageFile набора атрибутов в @Page директива также может изменить это значение. С помощью &lt;страниц&gt; элемент позволяет легко создавать <em>master</em> главной страницы, который можно переопределить при необходимости в папках или файлах.

## <a name="properties-in-master-pages"></a>Свойства в главных страниц

Главная страница может предоставлять свойства, просто этих свойств в рамках главной страницы. Например следующий код определяет свойство с именем SomeProperty:

[!code-csharp[Main](master-pages/samples/sample2.cs)]

Для получения доступа к свойству SomeProperty страницы содержимого, необходимо использовать Master свойства следующим образом:

[!code-csharp[Main](master-pages/samples/sample3.cs)]

## <a name="nesting-master-pages"></a>Вложение главных страниц

Главные страницы — это идеальное решение для обеспечения общий внешний вид и поведение для больших веб-приложения. Тем не менее не часто имеет определенные части общий ресурс больших сайта общий интерфейс, пока другие части имеют другой интерфейс. Чтобы удовлетворить потребность, несколько главных страниц — это идеальное решение. Тем не менее, по-прежнему не удовлетворяет тот факт, что большого приложения могут иметь некоторые компоненты (например, меню, например), которые являются общими для всех страниц и других компонентов, которые являются общими только для определенных разделов сайта. Для такого типа ситуаций необходимость хорошо заполнения вложенные главные страницы. Как вы уже видели, обычный главной страницы состоит из главной страницы и страницы содержимого. В случае вложенном шаблоне страницы существует две главные страницы; основной родительской и дочерней главной. Дочерняя главная страница также страницу содержимого и его шаблон имеет родительской главной страницы.

Ниже приведен код для типичных главной страницы:

[!code-aspx[Main](master-pages/samples/sample4.aspx)]

В сценарии с вложением master это будет родительского шаблона. Эта страница будет используется другой главной страницы как главной страницей по, и этот код будет выглядеть следующим образом:

[!code-aspx[Main](master-pages/samples/sample5.aspx)]

Обратите внимание, что в этом случае дочерней главной также содержимого страницы для родительского шаблона. Все содержимое главного дочерних отображается внутри элемента управления содержимым, которое получает его содержимого из родительского элемента управления ContentPlaceHolder.

> [!NOTE]
> Поддержка конструктора недоступен для вложенных главных страниц. При разработке с помощью вложенных образцов, необходимо использовать представление источника.


В этом видео показано Пошаговое руководство по использованию вложенные главные страницы.


![](master-pages/_static/image1.png)


[Откройте весь экран](master-pages/_static/nested1.wmv)


![При выборе главной страницы](master-pages/_static/image4.jpg)

**Рис. 8**: Выбор главной страницы
