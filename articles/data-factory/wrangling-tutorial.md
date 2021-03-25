---
title: Prise en main du flux de wrangling data dans Azure Data Factory
description: Didacticiel sur la façon de préparer des données dans Azure Data Factory à l’aide d’un flux de données de wrangling
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 3d43a532f57feab361c6a3de79269991f46fc55d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684020"
---
# <a name="prepare-data-with-data-wrangling"></a>Préparer des données avec data wrangling

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

La data wrangling dans la fabrique de données vous permet de créer des compositions (« mash-up ») Power Query interactives en mode natif dans ADF, puis de les exécuter à grande échelle à l’intérieur d’un pipeline ADF.

> [!NOTE]
> L’activité Power Query dans ADF est actuellement disponible en préversion publique.

## <a name="create-a-power-query-activity"></a>Créer une activité Power Query

Il existe deux façons de créer une activité Power Query dans Azure Data Factory. Vous pouvez cliquer sur l’icône plus et sélectionner **Flux de données** dans le volet de ressources de la fabrique.

> [!NOTE]
> Auparavant, la fonctionnalité de data wrangling intervenait dans le flux de travail du flux de données. Désormais, vous allez créer votre composition (« mash-up ») de data wrangling hybride à partir de ```New > Power query```.

![Capture d’écran montrant Power Query dans le volet de ressources de la fabrique.](media/data-flow/power-query-wrangling.png)

L’autre méthode se trouve dans le volet d’activités du canevas du pipeline. Ouvrez l’accordéon **Power Query**, puis faites glisser l’activité **Power Query** sur le canevas.

![Capture d’écran mettant en évidence l’option de data wrangling.](media/data-flow/power-query-activity.png)

## <a name="author-a-power-query-data-wrangling-activity"></a>Créer une activité de data wrangling Power Query

Ajoutez un **jeu de données source** pour votre composition (« mash-up ») Power Query. Vous pouvez choisir un jeu de données existant ou en créer un. Vous pouvez également sélectionner un jeu de données récepteur. Vous pouvez choisir un ou plusieurs jeux de données sources, mais un seul récepteur est autorisé à ce stade. Choisir un jeu de données récepteur est facultatif, mais au moins un jeu de données source est requis.

![Wrangling](media/wrangling-data-flow/tutorial4.png)

Cliquez sur **Créer** pour ouvrir l’éditeur mashup Power Query Online.

![Capture d’écran montrant le bouton Créer qui ouvre l’éditeur mashup Power Query Online.](media/wrangling-data-flow/tutorial5.png)

Créez votre wrangling Power Query à l’aide d’une préparation des données sans code. Pour obtenir la liste des fonctions disponibles, consultez les [fonctions de transformation](wrangling-functions.md). ADF convertit le script M en script de flux de données afin que vous puissiez exécuter votre Power Query à grande échelle à l’aide de l’environnement Spark de flux de données Azure Data Factory.

![Capture d’écran montrant le processus de création de votre data wrangling Power Query.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>Exécution et surveillance d’une activité de data wrangling Power Query

Pour déboguer un pipeline d’activité Power Query, cliquez sur **Déboguer** dans le canevas du pipeline. Une fois que vous avez publié votre pipeline, la commande **Déclencher maintenant** effectue une exécution à la demande du dernier pipeline publié. Des pipelines Power Query peuvent être planifiés avec tous les déclencheurs Azure Data Factory existants.

![Capture d’écran montrant comment ajouter une activité de data wrangling Power Query.](media/wrangling-data-flow/tutorial3.png)

Accédez à l’onglet **Analyse** pour visualiser la sortie de l’exécution d’une activité Power Query déclenchée.

![Capture d’écran montrant la sortie de l’exécution d’une activité de data wrangling Power Query déclenchée.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [créer un flux de données de mappage](tutorial-data-flow.md).
