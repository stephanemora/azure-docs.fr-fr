---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5687fb99c27b8b2141e0a2a817327cfbb124951a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109134"
---
## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Créer une tâche web déclenchée manuellement

1. Accédez au [portail Azure](https://portal.azure.com).
1. Accédez au **App Service** de votre <abbr title="Votre ressource d’application peut être une application web, une application API ou une application mobile.">ressource d’application</abbr>.
1. Sélectionnez **WebJobs**.

    ![Sélection de WebJobs](../media/web-sites-create-web-jobs/select-webjobs.png)

2. Dans la page **WebJobs**, sélectionnez **Ajouter**.

   ![Page WebJob](../media/web-sites-create-web-jobs/wjblade.png)

3. Utilisez les paramètres **Ajouter une tâche web**, comme spécifié dans le tableau.

    ![Capture d’écran montrant les paramètres qui doivent être définis pour créer un tâche web déclenchée manuellement.](../media/web-sites-create-web-jobs/addwjtriggered.png)
    
    | Paramètre      | Exemple de valeur   | 
    | ------------ | ----------------- | 
   | <abbr title="Un nom unique au sein d’une application App Service. Doit commencer par une lettre ou un chiffre et ne peut pas contenir de caractères spéciaux, à part les tirets et les traits de soulignement (`-` et `_`).">Nom</abbr> | myTriggeredWebJob | 
    | <abbr title="Un fichier *.zip* qui contient votre exécutable un fichier script ainsi que les fichiers de prise en charge requis pour exécuter le programme ou le script.">Chargement de fichiers</abbr> | ConsoleApp.zip |
    | <abbr title="Les types incluent : continu, déclenché.">Type</abbr> | Déclenchée | 
    | <abbr title="Les types incluent : planifié ou manuel">Déclencheurs</a> | Manuel | |

4. Cliquez sur **OK**. 

   La nouvelle tâche web apparaît dans la page **WebJobs**.

   ![Liste des tâches web](../media/web-sites-create-web-jobs/listallwebjobs.png)

7. **Pour exécuter une tâche web manuelle**, cliquez avec le bouton droit sur son nom dans la liste, puis cliquez sur **Exécuter**.
   
    ![Exécuter une tâche Web](../media/web-sites-create-web-jobs/runondemand.png)

