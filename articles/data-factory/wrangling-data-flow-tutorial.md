---
title: Prise en main du flux de wrangling data dans Azure Data Factory
description: Didacticiel sur la façon de préparer des données dans Azure Data Factory à l’aide d’un flux de données de wrangling
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: f9b5380fa219d768651703eeb9fe445fcd215332
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921777"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Préparer des données avec un flux de données de wrangling

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

> [!NOTE]
> Le flux de wrangling data est actuellement disponible en préversion publique.

## <a name="create-a-wrangling-data-flow"></a>Créer un flux de données de wrangling

Il existe deux façons de créer un flux de données de wrangling dans Azure Data Factory. Vous pouvez cliquer sur l’icône plus et sélectionner **Flux de données** dans le volet de ressources de la fabrique.

![Wrangling](media/wrangling-data-flow/tutorial7.png)

L’autre méthode se trouve dans le volet d’activités du canevas du pipeline. Ouvrez la section **Déplacer et transformer**, puis faites glisser l’activité **Flux de données** sur le canevas.

Pour les deux méthodes, dans le volet latéral qui s’ouvre, sélectionnez **Create new data flow** (Créer un flux de données), puis choisissez **Wrangling data flow** (Flux de données de wrangling). Cliquez sur OK.

![Wrangling](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Créer un flux de données de wrangling

Ajoutez un **jeu de données source** à votre flux de données de wrangling. Vous pouvez choisir un jeu de données existant ou en créer un. Vous pouvez également sélectionner un jeu de données récepteur. Vous pouvez choisir un ou plusieurs jeux de données sources, mais un seul récepteur est autorisé à ce stade. Choisir un jeu de données récepteur est facultatif, mais au moins un jeu de données source est requis.

> [!NOTE]
> Seul le texte délimité ADLS Gen 2 est pris en charge pour la version préliminaire limitée. 

![Wrangling](media/wrangling-data-flow/tutorial4.png)

Cliquez sur **Créer** pour ouvrir l’éditeur mashup Power Query Online.

![Wrangling](media/wrangling-data-flow/tutorial5.png)

Créez votre flux de données de wrangling à l’aide de la préparation des données sans code. Pour obtenir la liste des fonctions disponibles, consultez les [fonctions de transformation](wrangling-data-flow-functions.md)/

![Wrangling](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Exécution et supervision d’un flux de données de wrangling

Pour déboguer un pipeline d’un flux de données de wrangling, cliquez sur **Déboguer** dans le canevas du pipeline. Une fois que vous avez publié votre flux de données, **Déclencher maintenant** procède à une exécution à la demande du dernier pipeline publié. Les flux de données de wrangling peuvent être programmés avec tous les déclencheurs Azure Data Factory existants.

![Wrangling](media/wrangling-data-flow/tutorial3.png)

Accédez à l’onglet **Analyse** pour visualiser la sortie de l’exécution d’une activité de flux de données de wrangling déclenchée.

![Wrangling](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [créer un flux de données de mappage](tutorial-data-flow.md).