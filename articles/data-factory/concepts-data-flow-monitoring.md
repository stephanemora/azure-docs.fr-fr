---
title: Supervision visuelle du flux de données de mappage
description: Comment superviser visuellement les flux de données d’Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: 93d92286fa9eecbc64229059274cc8f9ed99e21e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928277"
---
# <a name="monitor-data-flows"></a>Superviser les flux de données



Après avoir généré et débogué votre flux de données, vous pouvez le planifier de façon à l’exécuter selon une planification dans le contexte d’un pipeline. Vous pouvez planifier le pipeline à partir d’Azure Data Factory à l’aide de déclencheurs. Vous pouvez également utiliser l’option Déclencher maintenant à partir du Générateur de pipeline d’Azure Data Factory pour déclencher une exécution unique afin de tester votre flux de données dans le contexte du pipeline.

Lors de l’exécution de votre pipeline, vous pourrez superviser celui-ci, ainsi que toutes les activités qu’il contient, dont celle du flux de données. Cliquez sur l’icône Superviser dans le panneau de gauche de l’interface utilisateur d’Azure Data Factory. Un écran similaire à celui ci-dessous s’affiche. Les icônes en surbrillance vous permettent d’explorer les activités en cours dans le pipeline, dont celle du flux de données.

![Supervision du flux de données](media/data-flow/mon001.png "Supervision du flux de données")

À ce niveau, vous verrez également des statistiques, notamment l’état et le temps d’exécution. L’ID d’exécution au niveau d’une activité diffère de celui au niveau du pipeline. L’ID d’exécution au niveau précédent a trait au pipeline. En cliquant sur les lunettes, vous pouvez obtenir des informations détaillées sur l’exécution de votre flux de données.

![Supervision du flux de données](media/data-flow/mon002.png "Supervision du flux de données")

Dans la vue de surveillance des nœuds graphique, vous pouvez voir une version simplifiée en lecture seule de votre graphique de flux de données.

![Supervision du flux de données](media/data-flow/mon003.png "Supervision du flux de données")

## <a name="view-data-flow-execution-plans"></a>Afficher les plans d’exécution de flux de données

Quand votre flux de données est exécuté dans Spark, Azure Data Factory détermine les chemins de code optimaux en fonction de l’intégralité de votre flux de données. De plus, les chemins d’exécution peuvent se trouver sur différents nœuds de scale-out et partitions de données. Par conséquent, le graphique de supervision reflète la conception de votre flux, en tenant compte du chemin d’exécution de vos transformations. En cliquant sur des nœuds individuels, vous pouvez voir des « regroupements » représentant du code exécuté simultanément sur le cluster. Les minutages et les chiffres que vous voyez représentent ces groupes par opposition aux étapes individuelles de votre conception.

![Supervision du flux de données](media/data-flow/mon004.png "Supervision du flux de données")

* Lorsque vous cliquez sur l’espace disponible dans la fenêtre de supervision, les statistiques dans le volet inférieur affichent le minutage et le nombre de lignes pour chaque récepteur, ainsi que les transformations ayant conduit aux données du récepteur pour le lignage de transformation.

* Quand vous sélectionnez des transformations individuelles, des commentaires supplémentaires apparaissent dans le panneau de droite, présentant les statistiques de partition, les nombres de colonnes, l’asymétrie (degré d’uniformité de la distribution des données entre les partitions) et le kurtosis (l’intensité des pics de données).

* Lorsque vous cliquez sur le récepteur dans la vue du nœud, vous voyez le lignage de colonne. Avant d’arriver dans le récepteur, les données sont cumulées tout au long de votre flux de données selon trois méthodes différentes. Il s'agit de :

  * Calculée : Vous utilisez la colonne pour le traitement conditionnel ou à l’intérieur d’une expression dans votre flux de données, mais les données n’arrivent pas dans le récepteur.
  * Dérivée : La colonne est une nouvelle colonne que vous avez générée dans votre flux. Autrement dit, elle n’était pas présente dans la source.
  * Mappée : La colonne provient de la source et vous la mappez à un champ du récepteur.
  * État du flux de données : État actuel de l’exécution
  * Heure de démarrage du cluster : Durée d’acquisition de l’environnement de calcul JIT Spark pour l’exécution de votre flux de données
  * Nombre de transformations : Nombre d’étapes de transformation exécutées dans votre flux
  
![Supervision du flux de données](media/data-flow/monitornew.png "Supervision du flux de données - Nouveau")  
  
## <a name="monitor-icons"></a>Icônes Superviser

Cette icône signifie que les données de la transformation étaient déjà en cache sur le cluster. Les minutages et le chemin d’exécution en ont donc tenu compte :

![Supervision du flux de données](media/data-flow/mon004.png "Supervision du flux de données")

Vous verrez également des icônes de cercle vert dans la transformation. Elles représentent un décompte du nombre de récepteurs dans lesquels les données s’écoulent.
