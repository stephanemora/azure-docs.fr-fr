---
title: Prise en main du flux de wrangling data dans Azure Data Factory
description: Didacticiel sur la façon de préparer des données dans Azure Data Factory à l’aide d’un flux de données de wrangling
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: 43ff1591730f240e1ae3ebcfc4066fdc34835372
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355561"
---
# <a name="prepare-data-with-data-wrangling"></a>Préparer des données avec data wrangling

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

La data wrangling dans la fabrique de données vous permet de créer des compositions (« mash-up ») Power Query interactives en mode natif dans ADF, puis de les exécuter à grande échelle à l’intérieur d’un pipeline ADF.

## <a name="create-a-power-query-activity"></a>Créer une activité Power Query

Il existe deux façons de créer une activité Power Query dans Azure Data Factory. Vous pouvez cliquer sur l’icône plus et sélectionner **Power Query** dans le volet de ressources de la fabrique.

:::image type="content" source="media/data-flow/power-query-wrangling.png" alt-text="Capture d’écran montrant Power Query dans le volet de ressources de la fabrique.":::

L’autre méthode se trouve dans le volet d’activités du canevas du pipeline. Ouvrez l’accordéon **Power Query**, puis faites glisser l’activité **Power Query** sur le canevas.

:::image type="content" source="media/data-flow/power-query-activity.png" alt-text="Capture d’écran mettant en évidence l’option de data wrangling.":::

## <a name="author-a-power-query-data-wrangling-activity"></a>Créer une activité de data wrangling Power Query

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWJd3T]
> 
Ajoutez un **jeu de données source** pour votre composition (« mash-up ») Power Query. Vous pouvez choisir un jeu de données existant ou en créer un. Après avoir enregistré votre mashup, vous pouvez ajouter l’activité de data wrangling de Power Query à votre pipeline, puis sélectionner un jeu de données récepteur pour indiquer à ADF où placer vos données. Vous pouvez choisir un ou plusieurs jeux de données sources, mais un seul récepteur est autorisé à ce stade. Choisir un jeu de données récepteur est facultatif, mais au moins un jeu de données source est requis.

:::image type="content" source="media/wrangling-data-flow/tutorial4.png" alt-text="Wrangling":::

Cliquez sur **Créer** pour ouvrir l’éditeur mashup Power Query Online.

Vous allez commencer par choisir une source de jeu de données pour l’éditeur de mashup.

:::image type="content" source="media/wrangling-data-flow/power-query-new-source.png" alt-text="Source de Power Query.":::

Une fois que vous avez terminé la génération de votre Power Query, vous pouvez l’enregistrer et ajouter le mashup en tant qu’activité à votre pipeline. C’est à ce moment-là que vous allez définir les propriétés du jeu de données de récepteur.

:::image type="content" source="media/wrangling-data-flow/power-query-new-sink.png" alt-text="Récepteur de Power Query.":::

Créez votre wrangling Power Query à l’aide d’une préparation des données sans code. Pour obtenir la liste des fonctions disponibles, consultez les [fonctions de transformation](wrangling-functions.md). ADF convertit le script M en script de flux de données afin que vous puissiez exécuter votre Power Query à grande échelle à l’aide de l’environnement Spark de flux de données Azure Data Factory.

:::image type="content" source="media/wrangling-data-flow/tutorial6.png" alt-text="Capture d’écran montrant le processus de création de votre data wrangling Power Query.":::

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>Exécution et surveillance d’une activité de data wrangling Power Query

Pour déboguer un pipeline d’activité Power Query, cliquez sur **Déboguer** dans le canevas du pipeline. Une fois que vous avez publié votre pipeline, la commande **Déclencher maintenant** effectue une exécution à la demande du dernier pipeline publié. Des pipelines Power Query peuvent être planifiés avec tous les déclencheurs Azure Data Factory existants.

:::image type="content" source="media/data-flow/power-query-activity.png" alt-text="Capture d’écran montrant comment ajouter une activité de data wrangling Power Query.":::

Accédez à l’onglet **Analyse** pour visualiser la sortie de l’exécution d’une activité Power Query déclenchée.

:::image type="content" source="media/wrangling-data-flow/tutorial2.png" alt-text="Capture d’écran montrant la sortie de l’exécution d’une activité de data wrangling Power Query déclenchée.":::

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [créer un flux de données de mappage](tutorial-data-flow.md).
