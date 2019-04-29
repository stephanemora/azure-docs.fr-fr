---
title: Supervision visuelle de la fonctionnalité de mappage de flux de données d’Azure Data Factory
description: Comment superviser visuellement les flux de données d’Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 90aa6261aebb9d1f7da89c101854bad8061dd6ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61268993"
---
# <a name="monitor-data-flows"></a>Superviser les flux de données

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Après avoir généré et débogué votre flux de données, vous pouvez le planifier de façon à l’exécuter selon une planification dans le contexte d’un pipeline. Vous pouvez planifier le pipeline à partir d’Azure Data Factory à l’aide de déclencheurs. Vous pouvez également utiliser l’option Déclencher maintenant à partir du Générateur de pipeline d’Azure Data Factory pour déclencher une exécution unique afin de tester votre flux de données dans le contexte du pipeline.

Lors de l’exécution de votre pipeline, vous pourrez superviser celui-ci, ainsi que toutes les activités qu’il contient, dont celle du flux de données. Cliquez sur l’icône Superviser dans le panneau de gauche de l’interface utilisateur d’Azure Data Factory. Un écran similaire à celui ci-dessous s’affiche. Les icônes en surbrillance vous permettent d’explorer les activités en cours dans le pipeline, dont celle du flux de données.

<img src="media/data-flow/mon001.png" width="800">

Vous verrez également des statistiques à ce niveau, dont les temps d’exécution et l’état. L’ID d’exécution au niveau d’une activité diffère de celui au niveau du pipeline. L’ID d’exécution au niveau précédent a trait au pipeline. En cliquant sur les lunettes, vous pouvez obtenir des informations détaillées sur l’exécution de votre flux de données.

<img src="media/data-flow/mon002.png" width="800">

Dans la vue de surveillance des nœuds graphique, vous pouvez voir une version simplifiée en lecture seule de votre graphique de flux de données.

<img src="media/data-flow/mon003.png" width="800">

## <a name="view-data-flow-execution-plans"></a>Afficher les plans d’exécution de flux de données

Lorsque votre flux de données est exécuté dans Databricks, Azure Data Factory détermine les chemins de code optimaux en fonction de l’entièreté de votre flux de données. De plus, les chemins d’exécution peuvent se trouver sur différents nœuds de scale-out et partitions de données. Par conséquent, le graphique de supervision reflète la conception de votre flux, en tenant compte du chemin d’exécution de vos transformations. En cliquant sur des nœuds individuels, vous pouvez voir des « regroupements » représentant du code exécuté simultanément sur le cluster. Les minutages et les chiffres que vous voyez représentent ces groupes par opposition aux étapes individuelles de votre conception.

<img src="media/data-flow/mon004.png" width="800"> 

* Lorsque vous cliquez sur l’espace disponible dans la fenêtre de supervision, les statistiques dans le volet inférieur affichent le minutage et le nombre de lignes pour chaque récepteur, ainsi que les transformations ayant conduit aux données du récepteur pour le lignage de transformation.

* Lorsque vous sélectionnez des transformations individuelles, des commentaires supplémentaires apparaissent dans le panneau de droite présentant les statistiques de partition, les nombres de colonnes, l’asymétrie (degré d’uniformité de la distribution des données entre les partitions) ainsi le kurtosis (densité de pics).

* Lorsque vous cliquez sur le récepteur dans la vue du nœud, vous voyez le lignage de colonne. Avant d’arriver dans le récepteur, les données sont cumulées tout au long de votre flux de données selon trois méthodes différentes. Il s'agit de :

  * Calculée : Vous utilisez la colonne pour le traitement conditionnel ou à l’intérieur d’une expression dans votre flux de données, mais les données n’arrivent pas dans le récepteur.
  * Dérivée : La colonne est une nouvelle colonne que vous avez générée dans votre flux. Autrement dit, elle n’était pas présente dans la source.
  * Mappée : La colonne provient de la source et vous la mappez à un champ du récepteur.
  
## <a name="monitor-icons"></a>Icônes Superviser

Cette icône signifie que les données de la transformation étaient déjà en cache sur le cluster. Les minutages et le chemin d’exécution en ont donc tenu compte :

<img src="media/data-flow/mon005.png" width="800"> 

Vous verrez également des icônes de cercle vert dans la transformation. Elles représentent un décompte du nombre de récepteurs dans lesquels les données s’écoulent.
