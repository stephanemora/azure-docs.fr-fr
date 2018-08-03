---
title: Tableau de bord des applications LUIS | Microsoft Docs
description: Découvrez le tableau de bord d’application, un outil visuel de création de rapports, qui permet de surveiller vos applications en un coup d’œil.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/01/2018
ms.author: diberry
ms.openlocfilehash: 518227d9f4165a08fafefa357de255d97c710f61
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224498"
---
# <a name="application-dashboard"></a>Tableau de bord d’application
Le tableau de bord d’application permet de surveiller votre application d’un seul coup d’œil. Le **tableau de bord** s’affiche lorsque vous ouvrez une application en cliquant sur son nom sur la page **Mes applications**, puis choisissez **Tableau de bord** dans le panneau supérieur. 

> [!CAUTION]
> Pour obtenir les métriques les plus récentes de LUIS, vous devez :
> * Utiliser une [clé de point de terminaison](luis-how-to-azure-subscription.md) LUIS créée dans Azure.
> * Utiliser une clé de point de terminaison pour toutes les requêtes de point de terminaison incluant l’[API](https://aka.ms/luis-endpoint-apis) LUIS et le bot.
> * Utiliser une clé de point de terminaison différente pour chaque application LUIS. N’utilisez pas une même clé de point de terminaison pour toutes les applications. La clé de point de terminaison est suivie au niveau de la clé, pas au niveau de l’application.  

La page **Tableau de bord** donne une vue d’ensemble de l’application LUIS, y compris l’état du modèle actuel et l’utilisation du [point de terminaison](luis-glossary.md#endpoint) au fil du temps. <!--The following image shows the **Dashboard** page.-->

<!-- TBD: Get a working screen shot
![The Dashboard](./media/luis-how-to-use-dashboard/dashboard.png)
-->

<!-- TBD: IS THIS STILL TRUE?
At the top of the **Dashboard** page, a contextual notification bar constantly displays notifications to update you on the required or recommended actions appropriate for the current state of your app. It also provides useful tips and alerts as needed. A detailed description of the data reported on the **Dashboard** page follows.
-->
  
## <a name="app-status"></a>État de l’application
Le tableau de bord affiche l’apprentissage de l’application et l’état de publication, notamment la date et l’heure du dernier apprentissage et de la dernière publication de l’application.  

![Tableau de bord - État de l’application](./media/luis-how-to-use-dashboard/app-state.png)

## <a name="model-data-statistics"></a>Statistiques des données du modèle
Le tableau de bord affiche le nombre total d’intentions, d’entités et d’énoncés étiquetés existant dans l’application. 

![Statistiques des données de l’application](./media/luis-how-to-use-dashboard/app-model-count.png)

## <a name="endpoint-hits"></a>Accès de point de terminaison
Le tableau de bord affiche le nombre total d’accès de point de terminaison que reçoit l’application LUIS et vous permet d’afficher les accès pendant une période que vous spécifiez. Le nombre total d’accès affiché est la somme des accès point de terminaison qui utilisent une [clé de point de terminaison](./luis-concept-keys.md#endpoint-key) et des accès de point de terminaison qui utilisent une [clé de création](./luis-concept-keys.md#authoring-key).

<!-- TBD: this image is old but I don't have a new one based on usage -->
![Accès de point de terminaison](./media/luis-how-to-use-dashboard/dashboard-endpointhits.png)

> [!NOTE] 
> Le nombre d’accès de point de terminaison le plus récent se trouve dans le portail Azure, dans la vue d’ensemble du service LUIS. 
 
### <a name="total-endpoint-hits"></a>Nombre total d’accès de point de terminaison
Nombre total d’accès de point de terminaison reçus par votre application depuis sa création jusqu’à la date actuelle.

### <a name="endpoint-hits-per-period"></a>Accès de point de terminaison par période
Nombre d’accès reçus pendant une période passée, affichés par jour. Les points entre les dates de début et de fin représentent les jours relevant de cette période. Placez le pointeur de la souris sur chaque point afin d’afficher le nombre d’accès pour chaque jour de la période. 

Pour sélectionner une période à afficher sur le graphique :
 
1. Cliquez sur **Paramètres supplémentaires** ![bouton Paramètres supplémentaires](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) pour accéder à la liste des périodes. Vous pouvez sélectionner des périodes allant d’une semaine à un an. 

    ![Accès de point de terminaison par période](./media/luis-how-to-use-dashboard/timerange.png)

2. Sélectionnez une période dans la liste, puis cliquez sur la flèche vers la gauche ![Flèche vers la gauche](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) pour afficher le graphique.

### <a name="key-usage"></a>Utilisation de la clé
Nombre d’accès utilisés à partir de la clé de point de terminaison de l’application. Pour plus d’informations sur les clés de point de terminaison, voir [Clés dans LUIS](luis-concept-keys.md). 
  
## <a name="intent-breakdown"></a>Répartition des intentions
La **répartition des intentions** repose sur les énoncés étiquetés ou les accès de point de terminaison. Ce graphique de synthèse indique l’importance relative de chaque intention dans l’application. Lorsque vous placez le pointeur de la souris sur un secteur, vous voyez le nom de l’intention et son pourcentage par rapport au nombre total d’énoncés étiquetés ou d’accès de point de terminaison. 

![Répartition des intentions](./media/luis-how-to-use-dashboard/intent-breakdown.png)

Pour déterminer si la répartition est basée sur les énoncés étiquetés ou les accès de point de terminaison :

1. Cliquez sur **Paramètres supplémentaires** ![bouton Paramètres supplémentaires](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) pour accéder à la liste comme dans l’image suivante :

    ![Liste de répartition des intentions](./media/luis-how-to-use-dashboard/intent-breakdown-based-on.png)
2. Sélectionnez une valeur dans la liste, puis cliquez sur la flèche vers la gauche ![Flèche vers la gauche](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) pour afficher le graphique.

## <a name="entity-breakdown"></a>Répartition des entités
Le tableau de bord affiche la répartition des entités selon les énoncés étiquetés ou les accès de point de terminaison. Ce graphique de synthèse indique l’importance relative de chaque entité dans l’application. Lorsque vous placez le pointeur de la souris sur un secteur, vous voyez le nom de l’entité et son pourcentage par rapport aux énoncés étiquetés ou accès de point de terminaison. 

![Répartition des entités](./media/luis-how-to-use-dashboard/entity-breakdown.png)

Pour déterminer si la répartition est basée sur les énoncés étiquetés ou les accès de point de terminaison :

1. Cliquez sur **Paramètres supplémentaires** ![bouton Paramètres supplémentaires](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) pour accéder à la liste comme dans l’image suivante :

    ![Liste de répartition des entités](./media/luis-how-to-use-dashboard/entity-breakdown-based-on.png)
2. Sélectionnez une valeur dans la liste, puis cliquez sur la flèche vers la gauche ![Flèche vers la gauche](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) pour afficher le graphique en conséquence.
