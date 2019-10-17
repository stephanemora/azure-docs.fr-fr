---
title: Mappage de flux de données dans Azure Data Factory | Microsoft Docs
description: Vue d’ensemble du mappage de flux de données dans Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/7/2019
ms.openlocfilehash: 8d0ad794caee8a06c8d403a981037d6560fb3f43
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030131"
---
# <a name="what-are-mapping-data-flows"></a>Qu’est-ce que le mappage de flux de données?

Les mappages de flux de données sont des transformations de données conçues de manière graphique dans Azure Data Factory. Les flux de données permettent aux ingénieurs de données de développer une logique de transformation graphique des données sans rédiger de code. Les flux de données qui en résultent sont exécutés en tant qu’activités dans les pipelines Azure Data Factory à l’aide de clusters Spark faisant l’objet d’un scale-out. Les activités de flux de données peuvent être mises en œuvre à l’aide de fonctionnalités de planification, de contrôle, de flux et de supervision Data Factory existantes.

Le mappage de flux de données offre une expérience entièrement visuelle sans qu’aucun codage ne soit nécessaire. Vos flux de données s’exécuteront sur votre propre cluster d’exécution pour un traitement des données faisant l’objet d’un scale-out. Azure Data Factory gère intégralement la traduction du code, l'optimisation du chemin et l'exécution de vos travaux de flux de données.

## <a name="getting-started"></a>Prise en main

Pour créer un flux de données, cliquez sur le signe plus (+) sous Ressources de fabrique. 

![nouveau flux de données](media/data-flow/newdataflow2.png "nouveau flux de données")

Vous accédez au canevas du flux de données dans lequel vous pouvez créer votre logique de transformation. Cliquez sur la zone « Ajouter une source » pour commencer à configurer votre transformation de la source. Pour plus d’informations, consultez [Transformation de la source](data-flow-source.md).

## <a name="data-flow-canvas"></a>Canevas de flux de données

Le canevas de flux de données est divisé en trois parties : la barre supérieure, le graphe et le panneau de configuration. 

![Canvas](media/data-flow/canvas1.png "Canvas")

### <a name="graph"></a>Graph

Le graphe affiche le flux de transformation. Il montre la traçabilité des données sources à mesure qu’elles sont transmises à un ou plusieurs récepteurs. Pour ajouter une nouvelle source, cliquez sur la zone « Ajouter une source ». Pour ajouter une nouvelle transformation, cliquez sur le signe plus (+) situé en bas à droite d’une transformation existante.

![Canvas](media/data-flow/canvas2.png "Canvas")

### <a name="configuration-panel"></a>Panneau de configuration

Le panneau de configuration affiche les paramètres spécifiques à la transformation actuellement sélectionnée ou, si aucune transformation n’est sélectionnée, le flux de données. Dans la configuration globale du flux de données, vous pouvez modifier le nom et la description sous l’onglet **Général** ou ajouter des paramètres à l’aide de l’onglet **Paramètres**. Pour plus d’informations, consultez [Paramètres du mappage de flux de données](parameters-data-flow.md).

Chaque transformation comprend au moins quatre onglets de configuration :

#### <a name="transformation-settings"></a>Paramètres de transformation

Le premier onglet du volet de configuration de chaque transformation contient les paramètres spécifiques à cette transformation. Pour plus d’informations, reportez-vous à la page de documentation de cette transformation.

![Onglet Paramètres de la source](media/data-flow/source1.png "Onglet Paramètres de la source")

#### <a name="optimize"></a>Optimisation

L’onglet _Optimiser_ contient des paramètres pour configurer des schémas de partitionnement.

![Optimiser](media/data-flow/optimize1.png "Optimiser")

Le paramètre par défaut est d’utiliser le partitionnement actuel, ce qui indique à Azure Data Factory d’utiliser le schéma de partitionnement natif pour les flux de données s’exécutant sur Spark. Dans la plupart des scénarios, ce paramètre est l’approche recommandée.

Il existe des instances où vous pouvez souhaiter ajuster le partitionnement. Par exemple, si vous voulez générer vos transformations dans un seul fichier dans le lac, choisissez « Partition unique » dans une transformation de récepteur.

L’optimisation des performances est un autre cas où vous pouvez souhaiter contrôler les schémas de partitionnement. L’ajustement du partitionnement permet de contrôler la répartition de vos données entre les nœuds de calcul et les optimisations de la localisation des données qui peuvent avoir des effets tant positifs que négatifs sur les performances globales de vos flux de données. Pour plus d’informations, consultez le [Guide des performances des flux de données](concepts-data-flow-performance.md).

Pour changer le partitionnement de n’importe quelle transformation, cliquez sur l’onglet Optimiser, puis sélectionnez la case d’option « Définir le partitionnement ». Vous obtenez alors une série d’options concernant le partitionnement. La meilleure méthode de partitionnement varie en fonction des volumes de données, des clés candidates, des valeurs null et de la cardinalité. Il est conseillé de commencer avec le partitionnement par défaut, puis d’essayer différentes options de partitionnement. Vous pouvez effectuer des tests à l’aide d’exécutions de débogage de pipeline, puis voir la durée d’exécution et l’utilisation des partitions dans chaque regroupement de transformations à partir de la vue Supervision. Pour plus d’informations, consultez [Supervision des flux de données](concepts-data-flow-monitoring.md).

Vous trouverez ci-dessous les options de partitionnement disponibles.

##### <a name="round-robin"></a>Tourniquet 

Un tourniquet (round robin) est une partition simple qui distribue automatiquement les données de façon uniforme sur les partitions. Utilisez un tourniquet (round robin) quand vous n’avez pas de bonnes clés candidates pour implémenter une stratégie de partitionnement solide et intelligente. Vous pouvez définir le nombre de partitions physiques.

##### <a name="hash"></a>Hachage

Azure Data Factory produit un hachage de colonnes pour produire des partitions uniformes de sorte que des lignes contenant des valeurs similaires tombent dans la même partition. Lorsque vous utilisez l’option Hachage, effectuez un test pour détecter une éventuelle inclinaison de partition. Vous pouvez définir le nombre de partitions physiques.

##### <a name="dynamic-range"></a>Plage dynamique

Une plage dynamique utilise des plages dynamiques Spark basées sur les colonnes ou expressions que vous fournissez. Vous pouvez définir le nombre de partitions physiques. 

##### <a name="fixed-range"></a>Plage fixe

Créez une expression qui fournit une plage fixe pour les valeurs figurant dans vos colonnes de données partitionnées. Avant d’utiliser cette option pour éviter une inclinaison de partition, vous devez avoir une bonne compréhension de vos données. Les valeurs que vous entrez pour l’expression seront utilisées dans une fonction de partition. Vous pouvez définir le nombre de partitions physiques.

##### <a name="key"></a>Clé

Si vous avez une bonne compréhension de la cardinalité de vos données, une clé de partitionnement peut être une bonne stratégie de partition. Une clé de partitionnement crée des partitions pour chaque valeur unique dans votre colonne. Vous ne pouvez pas définir le nombre de partitions car celui-ci dépend des valeurs uniques dans les données.

#### <a name="inspect"></a>Inspecter

Le volet _Inspecter_ permet de visualiser les métadonnées du flux de données que vous êtes en train de transformer. Vous pouvez afficher le nombre de colonnes, les colonnes modifiées, les colonnes ajoutées, les types de données, l’ordre des colonnes et les références de colonne. « Inspecter » est un affichage en lecture seule de vos métadonnées. Il n’est pas nécessaire que le mode de débogage soit activé pour voir les métadonnées dans le volet Inspecter.

![Inspecter](media/data-flow/inspect1.png "Inspecter")

À mesure que vous modifiez la forme de vos données par le biais de transformations, les changements de métadonnées sont visibles dans le volet Inspecter. Si votre transformation de la source ne comporte pas de schéma défini, les métadonnées ne sont pas visibles dans le volet Inspecter. L’absence de métadonnées est fréquent dans les scénarios de dérive de schéma.

#### <a name="data-preview"></a>Aperçu des données

Si le mode de débogage est activé, l’onglet _Aperçu des données_  vous donne une capture instantanée interactive des données à chaque transformation. Pour en savoir plus, consultez [Aperçu des données en mode de débogage](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Barre supérieure

La barre supérieure contient des actions qui affectent l’ensemble du flux de données comme l’enregistrement et la validation. Vous pouvez basculer entre les modes graphe et configuration à l’aide des boutons **Afficher le graphique** et **Masquer le graphique**.

![Masquer le graphique](media/data-flow/hideg.png "Masquer le graphique")

Si vous masquez votre graphe, vous pouvez naviguer latéralement dans vos nœuds de transformation à l’aide des boutons **précédent** et **suivant**.

![Naviguer](media/data-flow/showhide.png "Naviguer")

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment créer une [transformation de la source](data-flow-source.md)
* Découvrir comment créer vos flux de données en [mode de débogage](concepts-data-flow-debug-mode.md)
