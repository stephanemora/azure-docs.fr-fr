---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 33dc766643355a5f5ebb6138e000595fd1bfe6fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101743177"
---
## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Création d’une tâche web continue

1. Accédez au [portail Azure](https://portal.azure.com).
1. Accédez au **App Service** de votre <abbr title="Votre ressource d’application peut être une application web, une application API ou une application mobile.">ressource d’application</abbr>.
1. Sélectionnez **WebJobs**.

   ![Sélection de WebJobs](../media/web-sites-create-web-jobs/select-webjobs.png)

1. Dans la page **WebJobs**, sélectionnez **Ajouter**.

    ![Page WebJob](../media/web-sites-create-web-jobs/wjblade.png)

1. Utilisez les paramètres **Ajouter une tâche web**, comme spécifié dans le tableau.

   ![Capture d’écran montrant les paramètres Ajouter une tâche web que vous devez configurer.](../media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Paramètre      | Exemple de valeur   | 
   | ------------ | ----------------- | 
   | <abbr title="Un nom unique au sein d’une application App Service. Doit commencer par une lettre ou un chiffre et ne peut pas contenir de caractères spéciaux, à part les tirets et les traits de soulignement (`-` et `_`).">Nom</abbr> | myContinuousWebJob | 
   | <abbr title=" Un fichier *.zip* qui contient votre exécutable un fichier script ainsi que les fichiers de prise en charge requis pour exécuter le programme ou le script.">Chargement de fichiers</abbr> | ConsoleApp.zip |
   | <abbr title="Les types incluent : continu, déclenché.">Type</abbr> | Continue | 
   | <abbr title="Disponible uniquement pour les tâches web continues. Détermine si le programme ou le script s’exécute sur toutes les instances ou une seule instance. L’option permettant une exécution sur plusieurs instances ne s’applique pas aux niveaux tarifaires Gratuit ou Partagé.">Scale</abbr> | Multi-instances | 

1. Cliquez sur **OK**.

    La nouvelle tâche web apparaît dans la page **WebJobs**.

    ![Liste des tâches web](../media/web-sites-create-web-jobs/listallwebjobs.png)

1. **Pour arrêter ou redémarrer** une tâche web continue, cliquez avec le bouton droit sur la tâche web dans la liste, puis sélectionnez **Arrêter** ou **Démarrer**.

   ![Arrêt d’une tâche web continue](../media/web-sites-create-web-jobs/continuousstop.png)
