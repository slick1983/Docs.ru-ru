---
uid: web-forms/overview/deployment/advanced-enterprise-web-deployment/customizing-database-deployments-for-multiple-environments
title: Настройка развертывания базы данных для нескольких сред | Документы Microsoft
author: jrjlee
description: 'Описывается, как настроить свойства базы данных для конкретных целевых средах как часть процесса развертывания. Обратите внимание: Предполагается th...'
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: a172979a-1318-4318-a9c6-4f9560d26267
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/advanced-enterprise-web-deployment/customizing-database-deployments-for-multiple-environments
msc.type: authoredcontent
ms.openlocfilehash: 06f22bc9a3068ee5621df62ee5ed1bea06d7e9e6
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
<a name="customizing-database-deployments-for-multiple-environments"></a>Настройка развертывания базы данных для нескольких сред
====================
по [Джейсон Lee](https://github.com/jrjlee)

[Скачать PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> Описывается, как настроить свойства базы данных для конкретных целевых средах как часть процесса развертывания.
> 
> > [!NOTE]
> > Предполагается, что при развертывании проекта базы данных Visual Studio 2010 с помощью MSBuild.exe и VSDBCMD.exe. Дополнительные сведения о том, почему может выбрать этот подход см. в разделе [веб-развертывания на предприятии](../web-deployment-in-the-enterprise/web-deployment-in-the-enterprise.md) и [развертывание проектов базы данных](../web-deployment-in-the-enterprise/deploying-database-projects.md).
> 
> 
> При развертывании проекта базы данных в несколько назначений, часто необходимо настроить свойства развертывания базы данных для каждой целевой среды. Например в тестовой среде вы обычно повторно создать базу данных на каждом развертывании, тогда как в промежуточной или рабочей среде будет гораздо большей вероятностью вносить добавочные обновления для сохранения данных.
> 
> Параметры развертывания в проекте базы данных Visual Studio 2010 содержатся в файле конфигурации (.sqldeployment) развертывания. В этом разделе показано, как создать файлы конфигурации конкретной среды развертывания и указать тот, который вы хотите использовать в качестве параметра VSDBCMD.


Этот раздел входит в состав серию учебников, исходя из требования к развертыванию enterprise вымышленная компания Fabrikam, Inc. Этот учебник ряд использует образец решения&#x2014; [решения диспетчера контактов](../web-deployment-in-the-enterprise/the-contact-manager-solution.md)&#x2014;для представления веб-приложения с реалистичных уровень сложности, включая приложения ASP.NET MVC 3, Windows Communication Служба Foundation (WCF) и проект базы данных.

Метод развертывания, в основе этих учебников основан на разбиение проекта файл подход, описанный в [основные сведения о файле проекта](../web-deployment-in-the-enterprise/understanding-the-project-file.md), в которой процесс построения управляется двух файлов проекта&#x2014;один с построение инструкции, которые применяются для каждой целевой среде и, содержащий параметры построения и развертывания конкретной среды. Во время построения файла проекта среды объединяется в файл проекта зависит от среды, образуют полный набор инструкций построения.

## <a name="task-overview"></a>Общие сведения о задаче

В этом разделе предполагается, что:

- Используйте разбиение подход файл проекта для развертывания решения, описанные в [основные сведения о файле проекта](../web-deployment-in-the-enterprise/understanding-the-project-file.md).
- Вызовите VSDBCMD из файла проекта для развертывания проекта базы данных, как описано в [основные сведения о процессе построения](../web-deployment-in-the-enterprise/understanding-the-build-process.md).

Чтобы создать развертывание системы, поддерживающей, меняя параметры развертывания базы данных между целевой среды, необходимо:

- Создайте файл конфигурации (.sqldeployment) развертывания для каждой целевой среды.
- Создайте VSDBCMD команду, которая задает файл конфигурации развертывания в качестве параметра командной строки.
- Параметризовать VSDBCMD команды в файле проекта Microsoft Build Engine (MSBuild), чтобы параметры VSDBCMD подходят для целевой среды.

В этом разделе будет показано, как для выполнения каждой из этих процедур.

## <a name="creating-environment-specific-deployment-configuration-files"></a>Создание файлов конфигурации для конкретной среды развертывания

По умолчанию проект базы данных содержит одно развертывание файла конфигурации с именем *Database.sqldeployment*. При открытии этого файла в Visual Studio 2010, можно просматривать параметры развертывания, доступные вам:

- **Параметры сортировки для сравнения развертывания**. Это позволяет выбрать, следует ли использовать параметры сортировки базы данных проекта ( *источника* параметры сортировки), либо параметры сортировки базы данных на конечном сервере ( *целевой* параметров сортировки). В большинстве случаев имеет смысл использовать исходные параметры сортировки при развертывании в среде разработки или тестовой среде. При развертывании в тестовой или производственной среде, обычно имеет смысл оставлять с целевыми параметрами сортировки, без изменений, чтобы избежать проблем совместимости.
- **Развертывать свойства базы данных**. Это позволяет выбрать, следует ли применять свойства базы данных, как определено в *Database.sqlsettings* файла. При развертывании базы данных в первый раз, следует развертывать свойства базы данных. При обновлении существующей базы данных, свойства уже должны быть выполнены, и вам не потребуется их развертывания.
- **Всегда воссоздавать базу данных**. Это позволяет выбирать для повторного создания целевой базы данных каждый раз при развертывании или сделать добавочные изменения, чтобы перевести целевую базу данных в актуальном состоянии с схемой. Если воссоздать базу данных, будут потеряны все данные в существующую базу данных. Таким образом, вы должны обычно задать **false** для развертывания в промежуточной или рабочей среде.
- **Блокировать добавочное развертывание при возможности потери данных**. Это позволяет выбрать, должно ли останавливаться развертывание, если изменения в схему базы данных приведет к потере данных. Обычно это значение **true** для развертывания в производственной среде, чтобы предоставить возможность вмешательство и защитить все важные данные. Если вы задали **всегда воссоздавать базу данных** для **false**, этот параметр не будет действовать.
- **Выполнение развертывания в однопользовательском режиме**. Это не обычно является проблемой в средах разработки и тестирования. Тем не менее, рекомендуется устанавливать это **true** для развертывания в промежуточной или рабочей среде. Это не позволяет пользователям вносить изменения в базу данных во время развертывания.
- **Резервное копирование базы данных перед развертыванием**. Обычно это значение **true** при развертывании в рабочей среде для предотвращения потери данных. Можно также задать значение **true** при развертывании в промежуточной среде, если большой объем данных в промежуточной базе данных.
- **Создавать инструкции DROP для объектов, находящихся в целевой базе данных, но не в проекте базы данных**. В большинстве случаев это целочисленные и ключевой частью добавочные изменения в базе данных. Если вы задали **всегда воссоздавать базу данных** для **false**, этот параметр не будет действовать.
- **Не использовать инструкции ALTER ASSEMBLY для обновления типов CLR**. Этот параметр определяет, как SQL Server следует обновить до более новых версий сборки типами общеязыковой среды выполнения (CLR). Это свойство должно иметь значение **false** в большинстве сценариев.

В этой таблице показано типичное развертывание параметры для сред с другой целевой. Однако параметры может отличаться в зависимости от требований точное.

|  | Разработчик и тестирования | Промежуточные и интеграции | Производство |
| --- | --- | --- | --- |
| **Параметры сортировки для сравнения развертывания** | Исходный код | целевого объекта | целевого объекта |
| **Развертывать свойства базы данных** | Да | Только в первый раз | Только в первый раз |
| **Всегда воссоздавать базу данных** | Да | False | False |
| **Блокировать добавочное развертывание при возможности потери данных** | False | Может быть | Да |
| **Выполнить скрипт развертывания в однопользовательском режиме** | False | True | Да |
| **Создать резервную копию базы данных перед развертыванием** | False | Может быть | Да |
| **Создавать инструкции DROP для объектов, находящихся в целевой базе данных, но не в проекте базы данных** | False | True | Да |
| **Не использовать инструкции ALTER ASSEMBLY для обновления типов среды CLR** | False | False | False |
  

> [!NOTE]
> Дополнительные сведения о свойствах развертывания базы данных и особенности среды см. в разделе [Обзор из параметров проекта базы данных](https://msdn.microsoft.com/library/aa833291(v=VS.100).aspx), [как: Настройка свойств Дополнительные сведения о развертывании](https://msdn.microsoft.com/library/dd172125.aspx), [ Построение и развертывание базы данных в изолированной среде разработки](https://msdn.microsoft.com/library/dd193409.aspx), и [построение и развертывание баз данных в тестовой или производственной среде](https://msdn.microsoft.com/library/dd193413.aspx).


Для поддержки развертывания проекта базы данных в несколько назначений, следует создать файл конфигурации развертывания для каждой целевой среды.

**Чтобы создать файл конфигурации, относящиеся к среде**

1. В Visual Studio 2010 в **обозревателе решений** окно, щелкните правой кнопкой мыши проект базы данных и нажмите кнопку **свойства**.
2. На странице свойств проекта базы данных на **развернуть** вкладке **файл конфигурации развертывания** щелкните **New**.

    ![](customizing-database-deployments-for-multiple-environments/_static/image1.png)
3. В **новый файл конфигурации развертывания** диалогового окна поле, дайте файлу осмысленное имя (например, **TestEnvironment.sqldeployment**) и нажмите кнопку **Сохранить**.
4. На *[имя_файла] *** .sqldeployment** задайте свойства развертывания в соответствии с требованиями к целевой среде, а затем сохраните файл.

    ![](customizing-database-deployments-for-multiple-environments/_static/image2.png)
5. Обратите внимание, что новый файл добавляется в папку Properties в проекте базы данных.

    ![](customizing-database-deployments-for-multiple-environments/_static/image3.png)

## <a name="specifying-the-deployment-configuration-file-in-vsdbcmd"></a>Указание файла конфигурации развертывания в VSDBCMD

При использовании конфигурации решения (например, Debug и Release) в Visual Studio 2010, файл конфигурации развертывания можно связать с каждой конфигурации. При создании определенной конфигурации, процесс построения создает файл манифеста развертывания для конкретной конфигурации, указывающий на файл конфигурации развертывания для конкретной конфигурации. Однако одно из основных целей подход к развертыванию, описанные в этих учебниках — предоставить пользователям возможность управлять в процессе развертывания без использования Visual Studio 2010 и конфигурации решения. При таком подходе конфигурации решения одинаково независимо от целевой среды развертывания. Для настройки развертывания базы данных в среде определенное место назначения, можно использовать параметры командной строки VSDBCMD указать файл конфигурации развертывания.

Чтобы указать файл конфигурации развертывания в вашей VSDBCMD, используйте **p:/DeploymentConfigurationFile** переключения и укажите полный путь к файлу. Файл конфигурации развертывания, манифест развертывания определяет, будет переопределено. Например, эта команда VSDBCMD можно использовать для развертывания **ContactManager** базы данных в тестовой среде:


[!code-console[Main](customizing-database-deployments-for-multiple-environments/samples/sample1.cmd)]


> [!NOTE]
> Обратите внимание, что процесс построения может переименовать SQLDEPLOYMENT-файл, при копировании файла в выходной каталог.


При использовании переменные команд SQL в скриптах SQL до или после развертывания, аналогичный подход можно использовать для связи определенной среде SQLCMDVARS-файл с развертыванием. В этом случае используется **p:/SqlCommandVariablesFile** для идентификации SQLCMDVARS-файл.

## <a name="running-the-vsdbcmd-command-from-an-msbuild-project-file"></a>Выполнив команду VSDBCMD из файла проекта MSBuild

Можно выполнить команду VSDBCMD из файла проекта MSBuild с помощью **Exec** задачу в целевой объект MSBuild. В простейшей форме оно будет выглядеть следующим образом:


[!code-xml[Main](customizing-database-deployments-for-multiple-environments/samples/sample2.xml)]


- На практике чтобы облегчить чтение и повторно использовать, файлы проекта может потребоваться создать свойства для хранения различных параметров командной строки. Это упростит пользователям указать значения свойств в файле проекта для конкретной среды или переопределить значения по умолчанию из командной строки MSBuild. Если используется разделение проекта файл подход, описанный в [основные сведения о файле проекта](../web-deployment-in-the-enterprise/understanding-the-project-file.md), следует разделить свои инструкции сборки, а также свойства между двумя файлами соответствующим образом:
- Параметры среды, такие как имя файла конфигурации развертывания, строку подключения базы данных и имя целевой базы данных должен использоваться в файле проекта для конкретной среды.
- Целевой объект MSBuild, который запускает команду VSDBCMD вместе с любой универсальные свойства, как расположение исполняемого файла VSDBCMD, следует перейти в файле универсального проекта.

Также следует убедиться, построить проект базы данных, чтобы вызвать VSDBCMD так, DEPLOYMANIFEST-файл создан и готов к использованию. Полный пример такого подхода в разделе [основные сведения о процессе построения](../web-deployment-in-the-enterprise/understanding-the-build-process.md), который поможет выполнить файлы проекта в [диспетчера контактов образец решения](../web-deployment-in-the-enterprise/the-contact-manager-solution.md).

## <a name="conclusion"></a>Заключение

В этом разделе описано, как настроить свойства базы данных в другой целевой среды при развертывании проектов баз данных с помощью MSBuild и VSDBCMD. Этот подход полезен, если необходимо выполнить развертывание проектов базы данных как часть большего размера, решения корпоративного уровня. Эти решения часто развертываются в несколько назначений, таких как изолированных средах разработки и тестирования, промежуточного хранения или интеграции платформ и производства или динамической среды. Каждый из этих целевых средах обычно требует уникальный набор свойств развертывания базы данных.

## <a name="further-reading"></a>Дополнительные сведения

Дополнительные сведения о развертывании проектов базы данных, с помощью VSDBCMD.exe см. в разделе [развертывание проектов базы данных](../web-deployment-in-the-enterprise/deploying-database-projects.md). Дополнительные сведения об использовании пользовательские файлы проекта MSBuild для управления процессом развертывания см. в разделе [основные сведения о файле проекта](../web-deployment-in-the-enterprise/understanding-the-project-file.md) и [основные сведения о процессе построения](../web-deployment-in-the-enterprise/understanding-the-build-process.md).

Эти статьи в библиотеке MSDN содержат дополнительные общие рекомендации для развертывания базы данных:

- [Обзор параметров проекта базы данных](https://msdn.microsoft.com/library/aa833291(v=VS.100).aspx)
- [Как: Настройка свойств для сведений о развертывании](https://msdn.microsoft.com/library/dd172125.aspx)
- [Построение и развертывание баз данных в изолированной среде разработки](https://msdn.microsoft.com/library/dd193409.aspx)
- [Построение и развертывание баз данных в тестовой или производственной среде](https://msdn.microsoft.com/library/dd193413.aspx)

> [!div class="step-by-step"]
> [Назад](performing-a-what-if-deployment.md)
> [Вперед](deploying-database-role-memberships-to-test-environments.md)
