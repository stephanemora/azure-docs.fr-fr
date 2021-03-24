---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ef0aa8ba1983ca30fd44c27fe570b6b5f51733a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101743179"
---
## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Créer une tâche web planifiée


1. Accédez au [portail Azure](https://portal.azure.com).
1. Accédez au **App Service** de votre <abbr title="Votre ressource d’application peut être une application web, une application API ou une application mobile.">ressource d’application</abbr>.
1. Sélectionnez **WebJobs**.

   ![Sélection de WebJobs](../media/web-sites-create-web-jobs/select-webjobs.png)

1. Dans la page **WebJobs**, sélectionnez **Ajouter**.

    ![Page WebJob](../media/web-sites-create-web-jobs/wjblade.png)

1. Utilisez les paramètres **Ajouter une tâche web**, comme spécifié dans le tableau.

    ![Ajouter une page WebJob](../media/web-sites-create-web-jobs/addwjscheduled.png)
    
    | Paramètre      | Exemple de valeur   |
    | ------------ | ----------------- | 
    | <abbr title="Un nom unique au sein d’une application App Service. Doit commencer par une lettre ou un chiffre et ne peut pas contenir de caractères spéciaux, à part les tirets et les traits de soulignement (`-` et `_`).">Nom</a> | myScheduledWebJob |  |
    | <abbr title="Un fichier *.zip* qui contient votre exécutable un fichier script ainsi que les fichiers de prise en charge requis pour exécuter le programme ou le script.">Chargement de fichiers</abbr> | ConsoleApp.zip |
    | <abbr title="Les types incluent : continu, déclenché.">Type</abbr> | Déclenchée |
    | <abbr title="Pour que la planification fonctionne correctement, activez la fonctionnalité Toujours actif. La fonctionnalité Toujours actif est disponible uniquement dans les niveaux tarifaires De base, Standard et Premium.">Déclencheurs</a> | Planifié |
    | Expression CRON</a> | 0 0/20 * * * * | 
    
    <br>
    
    <details>
     <summary>En savoir plus sur les expressions CRON</summary>
     <a name="#ncrontab-expressions"></a>
    
     Vous pouvez entrer une [expression NCRONTAB](../../azure-functions/functions-bindings-timer.md#ncrontab-expressions) dans le portail ou inclure un fichier `settings.job` à la racine du fichier *.zip* de votre tâche web, comme dans l’exemple suivant :
     
     ```json
     {
         "schedule": "0 */15 * * * *"
     }
     ```
     
     Pour en savoir plus, consultez [Planification d’un WebJob déclenché](../webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).
     
     [!INCLUDE [webjobs-cron-timezone-note](../../../includes/webjobs-cron-timezone-note.md)]
     </details>
     <br>

1. Cliquez sur **OK**.

    La nouvelle tâche web apparaît dans la page **WebJobs**.
    
    ![Liste des tâches web](../media/web-sites-create-web-jobs/listallwebjobs.png)
