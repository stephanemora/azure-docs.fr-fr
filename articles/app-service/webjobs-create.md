---
title: Exécuter des tâches en arrière-plan avec WebJobs
description: Découvrez comment utiliser WebJobs pour exécuter des tâches en arrière-plan dans Azure App Service. Choisissez un des différents formats de script et exécutez-les avec des expressions CRON.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 6/25/2021
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./webjobs-create-ieux
ms.openlocfilehash: 3ecb31f6d008fda51d03c3e0d2d44b881397b466
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2021
ms.locfileid: "122641198"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Exécuter des tâches en arrière-plan avec WebJobs dans Azure App Service

Déployez WebJobs à l’aide du [portail Azure](https://portal.azure.com) pour charger un fichier exécutable ou un script. Vous pouvez exécuter des tâches en arrière-plan dans Azure App Service.

Si, au lieu d’Azure App Service, vous utilisez Visual Studio 2019 pour développer et déployer WebJobs, consultez [Déployer WebJobs avec Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Vue d’ensemble
WebJobs est une fonctionnalité [Azure App Service](index.yml) qui vous permet d’exécuter un programme ou un script dans une même instance, en tant qu’application web, application API ou application mobile. L’utilisation des tâches web n’entraîne aucun coût supplémentaire.

Vous pouvez utiliser le Kit de développement logiciel (SDK) Azure WebJobs avec WebJobs pour simplifier de nombreuses tâches de programmation. WebJobs n’est pas encore pris en charge pour App Service sur Linux. Pour plus d’informations, consultez [Présentation du Kit de développement logiciel (SDK) WebJobs Azure](https://github.com/Azure/azure-webjobs-sdk/wiki).

Azure Functions fournit une autre façon d’exécuter des programmes et des scripts. Pour obtenir une comparaison entre WebJobs et Functions, consultez [Choisir entre Flow, Logic Apps, Functions et WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Types de tâches web

Le tableau suivant décrit les différences entre une tâche Web *continue* et une tâche web *déclenchée*.


|Continue  |Déclenchée  |
|---------|---------|
| Démarre immédiatement lorsque la tâche web est créée. Pour empêcher la tâche de se terminer, le programme ou le script est généralement exécuté à l’intérieur d’une boucle sans fin. Si la tâche se termine, vous pouvez la redémarrer. Généralement utilisé avec le Kit de développement logiciel (SDK) WebJobs. | Démarre uniquement en cas de déclenchement manuel ou selon une planification. |
| S’exécute sur toutes les instances sur lesquelles l’application web s’exécute. Vous pouvez limiter la tâche web à une seule instance. |S’exécute sur une seule instance sélectionnée par Azure pour l’équilibrage de charge.|
| Prend en charge le débogage à distance. | Ne prend pas en charge le débogage à distance.|
| Le code est déployé sous `\site\wwwroot\app_data\Jobs\Continuous`. | Le code est déployé sous `\site\wwwroot\app_data\Jobs\Triggered`. |

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Types de fichier pris en charge pour les scripts ou les programmes

Les types de fichiers suivants sont pris en charge :

* .cmd, .bat, .exe (en utilisant une commande Windows)
* .ps1 (en utilisant PowerShell)
* .sh (en utilisant un interpréteur de commandes)
* .php (en utilisant PHP)
* .py (en utilisant Python)
* .js (en utilisant Node.js)
* .jar (en utilisant Java)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Création d’une tâche web continue

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

> [!IMPORTANT]
> Lorsque le contrôle de code source est configuré pour votre application, Webjobs doit être déployé dans le cadre de l’intégration du contrôle de code source. Une fois que le contrôle de code source est configuré pour votre application, une tâche WebJob ne peut pas être ajoutée à partir du portail Azure.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez la page **App Service** de votre application Web App Service, application API ou application mobile.

1. Dans le volet de gauche de la page **App Service** de votre application, recherchez et sélectionnez **WebJobs**.

   ![Sélection de WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

1. Sur la page **WebJobs**, sélectionnez **Ajouter**.

    ![Page WebJob](./media/web-sites-create-web-jobs/wjblade.png)

1. Renseignez les paramètres **Ajouter une tâche web** comme spécifié dans le tableau.

   ![Capture d’écran montrant les paramètres Ajouter une tâche web que vous devez configurer.](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Paramètre      | Exemple de valeur   | Description  |
   | ------------ | ----------------- | ------------ |
   | **Nom** | myContinuousWebJob | Un nom unique au sein d’une application App Service. Doit commencer par une lettre ou un chiffre et ne peut pas contenir de caractères spéciaux, à part les tirets et les traits de soulignement (« - » et « _ »). |
   | **Chargement de fichiers** | ConsoleApp.zip | Un fichier *.zip* qui contient votre exécutable un fichier script ainsi que les fichiers de prise en charge requis pour exécuter le programme ou le script. Les types de fichiers exécutables ou scripts pris en charge sont répertoriés dans la section [Types de fichiers pris en charge](#acceptablefiles). |
   | **Type** | Continue | Les [types de tâches web](#webjob-types) sont décrites précédemment dans cet article. |
   | **Mettre à l'échelle** | Multi-instances | Disponible uniquement pour les tâches web continues. Détermine si le programme ou le script s’exécute sur toutes les instances ou une seule instance. L’option permettant une exécution sur plusieurs instances ne s’applique pas aux [niveaux tarifaires](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Gratuit ou Partagé. | 

1. Sélectionnez **OK**. 

   La nouvelle tâche web apparaît dans la page **WebJobs**. Si vous voyez un message indiquant que la tâche web a été ajoutée, mais que vous ne la voyez pas, sélectionnez **Actualiser**. 

   ![Liste des tâches web](./media/web-sites-create-web-jobs/list-continuous-webjob.png)

1. Pour arrêter ou redémarrer une tâche WebJob continue, cliquez avec le bouton droit sur la tâche WebJob dans la liste, puis sélectionnez **Arrêter** ou **Démarrer**.

    ![Arrêt d’une tâche web continue](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Créer une tâche web déclenchée manuellement

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez **App Services**. 

1. Sélectionnez votre application web, application API ou application mobile dans la liste. 

1. Dans le volet de gauche de la page **App Service** de votre application, sélectionnez **WebJobs**.

   ![Sélection de WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Sur la page **WebJobs**, sélectionnez **Ajouter**.

    ![Page WebJob](./media/web-sites-create-web-jobs/wjblade.png)

1. Renseignez les paramètres **Ajouter une tâche web** comme spécifié dans le tableau. 

   ![Capture d’écran montrant les paramètres qui doivent être définis pour créer un tâche web déclenchée manuellement.](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Paramètre      | Exemple de valeur   | Description  |
   | ------------ | ----------------- | ------------ |
   | **Nom** | myTriggeredWebJob | Un nom unique au sein d’une application App Service. Doit commencer par une lettre ou un chiffre et ne peut pas contenir de caractères spéciaux, à part les tirets et les traits de soulignement (« - » et « _ »).|
   | **Chargement de fichiers** | ConsoleApp.zip | Un fichier *.zip* qui contient votre exécutable un fichier script ainsi que les fichiers de prise en charge requis pour exécuter le programme ou le script. Les types de fichiers exécutables ou scripts pris en charge sont répertoriés dans la section [Types de fichiers pris en charge](#acceptablefiles). |
   | **Type** | Déclenchée | Les [types de WebJobs](#webjob-types) sont décrits précédemment dans cet article. |
   | **Déclencheurs** | Manuel | |

4. Sélectionnez **OK**.

   La nouvelle tâche web apparaît dans la page **WebJobs**. Si vous voyez un message indiquant que la tâche web a été ajoutée, mais que vous ne la voyez pas, sélectionnez **Actualiser**.  

   ![Liste des tâches WebJobs déclenchées](./media/web-sites-create-web-jobs/list-triggered-webjob.png)

7. Pour exécuter la tâche WebJob, cliquez avec le bouton droit sur son nom dans la liste et sélectionnez **Exécuter**.
   
    ![Exécuter une tâche Web](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Créer une tâche web planifiée

Une tâche Webjob planifiée est également déclenchée. Vous pouvez planifier le déclencheur pour qu’il se produise automatiquement selon la planification que vous spécifiez.
 
<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez **App Services**. 

1. Sélectionnez votre application web, application API ou application mobile dans la liste. 

1. Dans le volet de gauche de la page **App Service** de votre application, sélectionnez **WebJobs**.

   ![Sélection de WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

1. Sur la page **WebJobs**, sélectionnez **Ajouter**.

   ![Page WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Renseignez les paramètres **Ajouter une tâche web** comme spécifié dans le tableau.

   ![Ajouter une page WebJob](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Paramètre      | Exemple de valeur   | Description  |
   | ------------ | ----------------- | ------------ |
   | **Nom** | myScheduledWebJob | Un nom unique au sein d’une application App Service. Doit commencer par une lettre ou un chiffre et ne peut pas contenir de caractères spéciaux, à part les tirets et les traits de soulignement (« - » et « _ »). |
   | **Chargement de fichiers** | ConsoleApp.zip | Un fichier *.zip* qui contient votre exécutable un fichier script ainsi que les fichiers de prise en charge requis pour exécuter le programme ou le script. Les types de fichiers exécutables ou scripts pris en charge sont répertoriés dans la section [Types de fichiers pris en charge](#acceptablefiles). |
   | **Type** | Déclenchée | Les [types de tâches web](#webjob-types) sont décrites précédemment dans cet article. |
   | **Déclencheurs** | Planifiée | Pour que la planification fonctionne correctement, activez la fonctionnalité Toujours actif. La fonctionnalité Toujours actif est disponible uniquement dans les niveaux tarifaires De base, Standard et Premium.|
   | **Expression CRON** | 0 0/20 * * * * | Les [expressions CRON](#ncrontab-expressions) sont décrites dans la section suivante. |

4. Sélectionnez **OK**.

   La nouvelle tâche web apparaît dans la page **WebJobs**. Si vous voyez un message indiquant que la tâche web a été ajoutée, mais que vous ne la voyez pas, sélectionnez **Actualiser**.  

   ![Liste des tâches WebJobs planifiées](./media/web-sites-create-web-jobs/list-scheduled-webjob.png)

## <a name="ncrontab-expressions"></a>Expressions NCRONTAB

Vous pouvez entrer une [expression NCRONTAB](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) dans le portail ou inclure un fichier `settings.job` à la racine du fichier *.zip* de votre tâche web, comme dans l’exemple suivant :

```json
{
    "schedule": "0 */15 * * * *"
}
```

Pour en savoir plus, consultez [Planification d’un WebJob déclenché](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="manage-webjobs"></a>Gérer les tâches WebJobs

Vous pouvez gérer l’état d’exécution des tâches WebJobs individuelles exécutées sur votre site dans le [portail Azure](https://portal.azure.com). Il suffit d’aller dans **Paramètres** > **WebJobs**, de choisir la tâche WebJob, et vous pouvez démarrer et arrêter la tâche WebJob. Vous pouvez également afficher et modifier le mot de passe du webhook qui exécute tâche WebJob.  

Vous pouvez également [ajouter un paramètre d’application](configure-common.md#configure-app-settings) nommé `WEBJOB_STOPPED` avec une valeur de `1` pour arrêter toutes les tâches WebJobs en cours d’exécution sur votre site. Cela peut être pratique pour empêcher les tâches WebJobs conflictuelles de s’exécuter à la fois dans les emplacements de préproduction et dans ceux de production. Vous pouvez également utiliser une valeur de `1` pour le paramètre `WEBJOBS_DISABLE_SCHEDULE` pour désactiver les tâches WebJobs déclenchées sur le site ou dans un emplacement de préproduction. Pour les emplacements, n’oubliez pas d’activer l’option **Paramètre des emplacements de déploiement** afin que le paramètre lui-même ne soit pas échangé.    

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a> Affichage de l’historique des tâches

1. Sélectionnez la tâche WebJob puis, pour voir l’historique, sélectionnez **Journaux**.
   
   ![Bouton Journaux d’activité](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. À la page **Détails de la tâche web**, sélectionnez une heure pour afficher les détails d’une exécution.
   
   ![Détails de la tâche web](./media/web-sites-create-web-jobs/webjobdetails.png)

3. À la page **WebJob Run Details**, sélectionnez **Activer/désactiver la sortie** pour afficher le texte du contenu du journal.
   
    ![Détails d'exécution de la tâche WebJob](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Pour afficher le texte de sortie dans une nouvelle fenêtre de navigateur, sélectionnez **télécharger** . Pour télécharger le texte, cliquez avec le bouton droit sur **télécharger** et utilisez les options de votre navigateur pour enregistrer le contenu du fichier.
   
5. Sélectionnez le lien de navigation **WebJobs** en haut de la page pour accéder à une liste de tâches web.

    ![Barre de navigation de la tâche web](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Liste des tâches Web dans le tableau de bord d’historique](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a>Étapes suivantes

Le SDK Azure WebJobs peut être utilisé avec WebJobs pour simplifier de nombreuses tâches de programmation. Pour plus d’informations, consultez [Présentation du Kit de développement logiciel (SDK) WebJobs Azure](https://github.com/Azure/azure-webjobs-sdk/wiki).
