---
title: Développer et déployer des tâches web à l’aide de Visual Studio - Azure
description: Découvrez comment développer et déployer des tâches web Azure dans Azure App Service à l’aide de Visual Studio.
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 02/18/2019
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: ede7e2fe3a2ab4c0dfd4efaea5ec789924968194
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750155"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Développer et déployer des tâches web à l’aide de Visual Studio - Azure App Service

Cet article explique comment utiliser Visual Studio pour déployer un projet d’Application Console à une application web dans [App Service](overview.md) comme un [une tâche Web Azure](https://go.microsoft.com/fwlink/?LinkId=390226). Pour plus d’informations sur le déploiement de tâches web à l’aide du [portail Azure](https://portal.azure.com), consultez la section [Exécuter des tâches en arrière-plan avec les tâches web](webjobs-create.md).

Vous pouvez publier plusieurs WebJobs pour une application web unique. Assurez-vous que chaque tâche Web dans une application web a un nom unique.

Version 3.x de le [Azure WebJobs SDK](webjobs-sdk-how-to.md) permet de développer des tâches Web qui s’exécutent en tant qu’applications .NET Core, tandis que la version 2.x prend en charge le .NET Framework. La façon de déployer un projet de tâches est différents projets .NET Core par rapport à celles de .NET Framework.

## <a name="webjobs-as-net-core-console-apps"></a>Tâches Web en tant qu’applications de console .NET Core

Lorsque vous utilisez la version 3.x des tâches Web, vous pouvez créer et publier des tâches Web en tant qu’applications de console .NET Core. Pour obtenir des instructions pas à pas créer et publier une application de console .NET Core sur Azure en tant qu’une tâche Web, consultez [bien démarrer avec le SDK Azure WebJobs pour le traitement d’arrière-plan pilotée par événements](webjobs-sdk-get-started.md).

> [!NOTE]
> .NET core WebJobs ne peut pas être lié avec des projets web. Si vous avez besoin déployer votre tâche Web avec une application web, vous devez [créer votre tâche Web comme une application de console .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Déployer sur Azure App Service

Publication d’une tâche Web de .NET Core sur App Service à partir de Visual Studio utilise les mêmes outils que la publication d’une application ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Types de tâches web

Par défaut, une tâche Web publiés à partir d’un projet de console s’exécute uniquement lorsque le déclenchement de .NET Core ou à la demande. Vous pouvez également mettre à jour le projet à [exécuter selon une planification](#scheduled-execution) ou exécuter en continu.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Exécution planifiée

Lorsque vous publiez une application de console .NET Core sur Azure, un nouveau *settings.job* fichier est ajouté au projet. Utilisez ce fichier pour définir une planification de l’exécution de votre tâche Web. Pour plus d’informations, consultez [planification d’une tâche Web déclenchée](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Exécution en continu

Vous pouvez utiliser Visual Studio pour modifier la tâche Web s’exécute en permanence lorsque Always On est activée dans Azure.

1. Si vous n’avez pas déjà fait, [publier le projet sur Azure](#deploy-to-azure-app-service).

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

1. Dans le **publier** , choisir **paramètres**. 

1. Dans le **paramètres de profil** boîte de dialogue, choisissez **continu** pour **Type de tâche Web**, puis choisissez **enregistrer**.

    ![Boîte de dialogue Paramètres de publication pour une tâche Web](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Sélectionnez **publier** à republier la tâche Web avec les paramètres mis à jour.

## <a name="webjobs-as-net-framework-console-apps"></a>Tâches Web en tant qu’applications de console .NET Framework  

Lorsque Visual Studio déploie un projet prenant en charge les tâches Web de Application Console .NET Framework, il effectue deux tâches :

* Copie des fichiers exécutables dans le dossier approprié dans l’application web (*App_Data/tâches/continuous* pour les tâches Web continues et *App_Data/tâches/triggered* pour les tâches Web planifiées ou à la demande).
* Il configure des [tâches Azure Scheduler](https://docs.microsoft.com/azure/scheduler/) pour WebJobs dont l’exécution est prévue à des heures précises. (inutile pour les tâches web continues).

Un projet compatible avec les tâches web se voit ajouter les éléments suivants :

* Le package NuGet [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* Un fichier [webjob-publish-settings.json](#publishsettings) qui contient des paramètres de déploiement et de planification. 

![Diagram showing what is added to a Console App to enable deployment as a WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

Vous pouvez ajouter ces éléments à un projet d'application console existant ou utiliser un modèle pour créer un projet d'application console compatible avec les tâches web. 

Vous pouvez déployer un projet sous forme de tâche web ou le lier à un projet web afin qu'il soit déployé automatiquement lorsque vous déployez le projet web. Pour lier les projets, Visual Studio inclut le nom du projet compatible avec les tâches web dans un fichier [webjobs-list.json](#webjobslist) dans le projet web.

![Diagram showing WebJob project linking to web project](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Conditions préalables

Si vous utilisez Visual Studio 2015, installez le [Kit de développement logiciel (SDK) Azure .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Si vous utilisez Visual Studio 2017, installez la [charge de travail de développement Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads).

### <a id="convert"></a> Activer le déploiement de tâches web pour un projet d’application de console

Deux options s'offrent à vous :

* [Activation d'un déploiement automatique avec un projet web](#convertlink).

  Configurez un projet d'application console existant afin qu'il soit déployé automatiquement sous forme de tâche web lorsque vous déployez un projet web. Utilisez cette option lorsque vous voulez exécuter votre tâche web dans la même application web que celle dans laquelle vous exécutez l’application web liée.

* [Activation d'un déploiement sans projet web](#convertnolink).

  Configurez un projet d'application console existant pour déployer une tâche web seule, sans lien avec un projet web. Utilisez cette option lorsque vous voulez exécuter une tâche web dans une application web seule, sans application web s’exécutant dans cette dernière. Vous pouvez utiliser cette méthode afin de faire évoluer vos ressources de tâches web indépendamment de vos ressources d'application web.

#### <a id="convertlink"></a> Activer un déploiement automatique de tâches web avec un projet web

1. Cliquez avec le bouton droit sur le projet web dans l’**Explorateur de solutions**, puis cliquez sur **Ajouter** > **Projet existant en tant que tâche web Azure**.
   
    ![Projet existant sous forme de tâche web Azure](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    La boîte de dialogue [Ajouter une tâche web Azure](#configure) s'affiche.
2. Dans la liste déroulante **Nom du projet** , sélectionnez le projet d'application console à ajouter sous forme de tâche web.
   
    ![Selecting project in Add Azure WebJob dialog](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Remplissez la boîte de dialogue [Ajouter une tâche web Azure](#configure) , puis cliquez sur **OK**. 

#### <a id="convertnolink"></a> Activer un déploiement de tâches web sans projet web
1. Cliquez avec le bouton droit sur le projet d’application de console dans l’**Explorateur de solutions**, puis cliquez sur **Publier en tant que tâche web Azure**. 
   
    ![Publier sous forme de tâche web Azure](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    La boîte de dialogue [Ajouter une tâche web Azure](#configure) s'affiche avec le projet sélectionné dans la zone **Nom du projet** .
2. Remplissez la boîte de dialogue [Ajouter une tâche web Azure](#configure) , puis cliquez sur **OK**.
   
   L'Assistant **Publier le site Web** s'ouvre.  Si vous ne voulez pas publier immédiatement, fermez l’Assistant. Les paramètres que vous avez saisis sont enregistrés au cas où vous souhaiteriez [déployer le projet](#deploy).

### <a id="create"></a>Créer un projet compatible avec les tâches web
Pour créer un projet compatible avec les tâches web, vous pouvez utiliser le modèle de projet d'application console et activer le déploiement des tâches web comme expliqué dans [la section précédente](#convert). Vous pouvez également utiliser le modèle de nouveau projet de tâche web :

* [Utiliser le modèle de nouveau projet WebJobs pour une tâche web indépendante](#createnolink)
  
    Créez un projet et configurez-le pour qu'il se déploie seul sous forme de tâche web, sans lien avec un projet web. Utilisez cette option lorsque vous voulez exécuter une tâche web dans une application web seule, sans application web s’exécutant dans cette dernière. Vous pouvez utiliser cette méthode afin de faire évoluer vos ressources de tâches web indépendamment de vos ressources d'application web.
* [Utiliser le modèle de nouveau projet WebJobs pour une tâche web liée à un projet web](#createlink)
  
    Créez un projet configuré pour être déployé automatiquement sous forme de tâche web lorsqu'un projet web dans la même solution est déployé. Utilisez cette option lorsque vous voulez exécuter votre tâche web dans la même application web que celle dans laquelle vous exécutez l’application web liée.

> [!NOTE]
> Le modèle de nouveau projet WebJobs installe automatiquement les packages NuGet et inclut le code dans *Program.cs* pour le [Kit de développement logiciel (SDK) WebJobs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Si vous ne souhaitez pas utiliser le Kit de développement logiciel (SDK) WebJobs, supprimez ou modifiez l’instruction `host.RunAndBlock` dans *Program.cs*.
> 
> 

#### <a id="createnolink"></a> Utiliser le modèle de nouveau projet WebJobs pour une tâche web indépendante
1. Cliquez sur **Fichier** > **Nouveau projet**, puis dans la boîte de dialogue **Nouveau projet**, cliquez sur **Cloud** > **Azure WebJob (.NET Framework)**.
   
    ![New Project dialog showing WebJob template](./media/webjobs-dotnet-deploy-vs/np.png)
2. Suivez les instructions affichées précédemment pour [faire du projet de l'application console un projet de tâche web indépendant](#convertnolink).

#### <a id="createlink"></a> Utiliser le modèle de nouveau projet WebJobs pour une tâche web liée à un projet web
1. Cliquez avec le bouton droit sur le projet web dans l’**Explorateur de solutions**, puis cliquez sur **Ajouter** > **Nouveau projet de tâche web Azure**.
   
    ![New Azure WebJob Project menu entry](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    La boîte de dialogue [Ajouter une tâche web Azure](#configure) s'affiche.
2. Remplissez la boîte de dialogue [Ajouter une tâche web Azure](#configure) , puis cliquez sur **OK**.

### <a id="configure"></a>Boîte de dialogue Ajouter une tâche web Azure
La boîte de dialogue **Ajouter un WebJob Azure** vous permet d’entrer le nom du WebJob et d’exécuter le paramètre de mode de votre WebJob. 

![Add Azure WebJob dialog](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Les champs de cette boîte de dialogue correspondent à ceux de la boîte de dialogue **Ajouter une tâche web** du portail Azure. Pour plus d’informations, consultez [Exécuter des tâches en arrière-plan avec les tâches web](webjobs-create.md).

> [!NOTE]
> * Pour plus d’informations sur le déploiement en ligne de commande, consultez la page [Activation de la ligne de commande ou de la livraison continue de tâches web Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Si vous déployez une tâche web et que vous décidez ensuite d’en modifier le type et de la redéployer, vous devez supprimer le fichier *webjobs-publish-settings.json*. Ainsi Visual Studio affiche à nouveau les options de publication qui vous permettent de modifier le type de la tâche web.
> * Si vous déployez une tâche web et que vous modifiez ultérieurement le mode d'exécution continue en non continue ou vice-versa, Visual Studio crée une tâche web dans Azure lorsque vous procédez à un nouveau déploiement. Si vous modifiez d'autres paramètres de planification, mais que vous conservez le même mode d'exécution ou passez à une tâche planifiée ou à la demande, Visual Studio met à jour la tâche existante plutôt que d'en créer une.
> 
> 

### <a id="publishsettings"></a>webjob-publish-settings.json
Lorsque vous configurez une application console pour un déploiement de tâches web, Visual Studio installe le package NuGet [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) et stocke les informations de planification dans un fichier *webjob-publish-settings.json* du dossier *Propriétés* du projet WebJobs. Voici un exemple de ce fichier :

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Vous pouvez modifier ce fichier directement. Visual Studio est doté d'IntelliSense. Le schéma de fichier est stocké à l’adresse [https://schemastore.org](https://schemastore.org/schemas/json/webjob-publish-settings.json) où vous pouvez le consulter.  

### <a id="webjobslist"></a>webjobs-list.json
Lorsque vous liez un projet compatible avec des tâches web à un projet web, Visual Studio stocke le nom du projet de tâches web sous le nom de fichier *webjobs-list.json* dans le dossier *Propriétés* du projet web. La liste peut contenir plusieurs projets WebJobs, comme illustré dans l’exemple suivant :

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

Vous pouvez modifier ce fichier directement. Visual Studio est doté d'IntelliSense. Le schéma de fichier est stocké à l’adresse [https://schemastore.org](https://schemastore.org/schemas/json/webjobs-list.json) où vous pouvez le consulter.

### <a id="deploy"></a>Déployer un projet WebJobs
Lorsqu'il est lié à un projet web, un projet de tâches web est déployé automatiquement avec ce dernier. Pour plus d’informations sur le déploiement du projet web, consultez **Procédures** > **Déployer une application** dans le volet de navigation gauche.

Pour déployer un projet WebJobs seul, cliquez avec le bouton droit sur le projet dans l’**Explorateur de solutions**, puis sur **Publier en tant que tâche web Azure**. 

![Publier sous forme de tâche web Azure](./media/webjobs-dotnet-deploy-vs/paw.png)

Pour une tâche web indépendante, l'Assistant **Publier le site Web** utilisé pour les projets web s'affiche, mais avec moins de paramètres modifiables.

## <a name="scheduling-a-triggered-webjob"></a>Planification d’une tâche Web déclenchée

WebJobs utilise un *settings.job* fichier afin de déterminer quand une tâche Web est exécutée. Utilisez ce fichier pour définir une planification de l’exécution de votre tâche Web. L’exemple suivant exécute toutes les heures entre 9 h et 17 h :

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Ce fichier doit se trouver à la racine du dossier tâches Web, le long de côté un script de votre tâche Web, tel que `wwwroot\app_data\jobs\triggered\{job name}` ou `wwwroot\app_data\jobs\continuous\{job name}`. Quand vous déployez une tâche web à partir de Visual Studio, marquez les propriétés du fichier `settings.job` comme **Copier si plus récent**. 

Lorsque vous [créer une tâche Web à partir du portail Azure](webjobs-create.md), le fichier settings.job est créé pour vous.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>Expressions CRON

WebJobs utilise les mêmes expressions CRON pour la planification en tant que le déclencheur de minuteur dans Azure Functions. Pour en savoir plus sur le support CRON, consultez le [article de référence de déclencheur de minuteur](../azure-functions/functions-bindings-timer.md#cron-expressions).

### <a name="settingjob-reference"></a>référence de Setting.job

Les paramètres suivants sont pris en charge par les tâches Web :

| **Paramètre** | **Type**  | **Description** |
| ----------- | --------- | --------------- |
| `is_in_place` | Tous | Autorise la tâche à exécuter en place sans tout d’abord copiées dans un dossier temporaire. Pour plus d’informations, consultez [répertoire de travail de WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Continue | Exécutez uniquement les tâches Web sur une seule instance lorsqu’il est étendu. Pour plus d’informations, consultez [définir une tâche en continu en tant que singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Déclenchée | Exécuter la tâche Web selon une planification en fonction de CRON. Pour plus d’informations, consultez le [article de référence de déclencheur de minuteur](../azure-functions/functions-bindings-timer.md#cron-expressions). |
| `stopping_wait_time`| Tous | Permet de contrôler le comportement d’arrêt. Pour plus d’informations, consultez [arrêt progressif](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur le kit SDK WebJobs](webjobs-sdk-how-to.md)
