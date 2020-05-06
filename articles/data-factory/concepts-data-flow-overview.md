---
title: Mappage des flux de données
description: Vue d’ensemble du mappage de flux de données dans Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/28/2020
ms.openlocfilehash: 9f280aafabd59878ee24a9c3fe809dd027a97284
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187849"
---
# <a name="what-are-mapping-data-flows"></a>Que sont les flux de données de mappage ?

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Les mappages de flux de données sont des transformations de données conçues de manière graphique dans Azure Data Factory. Les flux de données permettent aux ingénieurs de données de développer une logique de transformation graphique des données sans rédiger de code. Les flux de données qui en résultent sont exécutés en tant qu'activités dans les pipelines Azure Data Factory qui utilisent des clusters Apache Spark faisant l'objet d'un scale-out. Les activités de flux de données peuvent être engagées à l'aide des fonctionnalités existantes de planification, de contrôle, de flux et d'analyse de Data Factory.

Le flux de données de mappage fournit une expérience entièrement visuelle sans aucun codage. Vos flux de données sont exécutés sur votre cluster d'exécution pour un traitement des données faisant l'objet d'un scale-out. Azure Data Factory gère intégralement la traduction du code, l’optimisation du chemin et l'exécution de vos travaux de flux de données.

## <a name="getting-started"></a>Prise en main

Pour créer un flux de données, sélectionnez le signe plus (+) sous **Ressources Factory**, puis sélectionnez **Flux de données**. 

![Nouveau flux de données](media/data-flow/newdataflow2.png "nouveau flux de données")

Vous accédez au canevas du flux de données dans lequel vous pouvez créer votre logique de transformation. Sélectionnez **Ajouter une source** pour commencer à configurer votre transformation de la source. Pour plus d’informations, consultez [Transformation de la source](data-flow-source.md).

## <a name="data-flow-canvas"></a>Canevas de flux de données

Le canevas de flux de données est divisé en trois parties : la barre supérieure, le graphe et le panneau de configuration. 

![Canevas](media/data-flow/canvas1.png "Canevas")

### <a name="graph"></a>Graph

Le graphe affiche le flux de transformation. Il montre la traçabilité des données sources à mesure qu’elles sont transmises à un ou plusieurs récepteurs. Pour ajouter une nouvelle source, sélectionnez **Ajouter une source**. Pour ajouter une nouvelle transformation, sélectionnez le signe plus (+) situé dans la partie inférieure droite d’une transformation existante.

![Canevas](media/data-flow/canvas2.png "Canevas")

### <a name="azure-integration-runtime-data-flow-properties"></a>Propriétés du flux de données du runtime d’intégration Azure

![Bouton Déboguer](media/data-flow/debugbutton.png "Bouton Déboguer")

Lorsque vous commencez à travailler avec les flux de données dans ADF, vous pouvez activer le commutateur « Déboguer » associé aux flux de données en haut de l'interface utilisateur du navigateur. Cela permet de créer un cluster Spark à utiliser pour le débogage interactif, les aperçus de données et les exécutions de débogage de pipeline. Vous pouvez définir la taille du cluster en cours d’utilisation en choisissant un [runtime d’intégration Azure](concepts-integration-runtime.md) personnalisé. La session de débogage reste active pendant un maximum de 60 minutes après la dernière exécution de l'aperçu des données ou du pipeline de débogage.

Lorsque vous configurez vos pipelines avec des activités de flux de données, ADF utilise le runtime Azure IR associé à l'[activité](control-flow-execute-data-flow-activity.md) dans la propriété « Exécuter sur ».

Le runtime Azure IR par défaut est un petit cluster de nœuds Worker à 4 cœurs qui vous permet d'afficher un aperçu des données et d'exécuter rapidement des pipelines de débogage à moindres frais. Définissez une configuration Azure IR plus vaste si vous effectuez des opérations sur des jeux de données volumineux.

Vous pouvez ordonner à ADF de conserver un pool de ressources de cluster (machines virtuelles) en définissant une durée de vie dans les propriétés du flux de données Azure IR. Cette action entraîne une exécution plus rapide des travaux lors des activités ultérieures.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Runtime d’intégration Azure et stratégies de flux de données

##### <a name="execute-data-flows-in-parallel"></a>Exécuter des flux de données en parallèle

Si vous exécutez des flux de données dans un pipeline en parallèle, ADF crée des clusters Spark distincts pour chaque exécution d'activité en fonction des paramètres de votre runtime Azure IR joints à chaque activité. Pour concevoir des exécutions parallèles dans des pipelines ADF, ajoutez vos activités de flux de données sans contraintes de précédence dans l’interface utilisateur.

Parmi ces trois options, celle-ci est probablement celle qui s'exécute dans les plus brefs délais. Toutefois, chaque flux de données parallèle s'exécute en même temps sur des clusters distincts, de sorte que l'ordre des événements n'est pas déterministe.

Si vous exécutez vos activités de flux de données en parallèle à l'intérieur de vos pipelines, il est recommandé de ne pas utiliser la TTL. Cette action s'explique par le fait que des exécutions parallèles de votre flux de données utilisant simultanément le même runtime Azure IR se traduisent par plusieurs instances de pools à chaud pour votre fabrique de données.

##### <a name="overload-single-data-flow"></a>Surcharger le flux de données unique

Si vous placez toute votre logique dans un même flux de données, ADF exécute ce même contexte d'exécution de travail sur une seule instance du cluster Spark.

Cette option peut être plus complexe en termes de suivi et de résolution des problèmes, car vos règles d'entreprise et votre logique métier peuvent se confondre. Les possibilités de réutilisation de cette option sont également réduites.

##### <a name="execute-data-flows-sequentially"></a>Exécuter des flux de données séquentiellement

Si vous exécutez vos activités de flux de données en séquence dans le pipeline et que vous avez défini une durée de vie dans la configuration Azure IR, ADF réutilise les ressources de calcul (machines virtuelles), ce qui accélère les exécutions ultérieures. Vous recevrez toujours un nouveau contexte Spark pour chaque exécution.

Parmi ces trois options, celle-ci est probablement celle dont l'exécution complète est la plus longue. Cependant, elle ne sépare pas clairement les opérations logiques à chaque étape du flux de données.

### <a name="configuration-panel"></a>Panneau de configuration

Le panneau de configuration affiche les paramètres spécifiques à la transformation actuellement sélectionnée. Si aucune transformation n’est sélectionnée, le flux de données est affiché. Dans la configuration globale du flux de données, vous pouvez modifier le nom et la description sous l’onglet **Général** ou ajouter des paramètres à l’aide de l’onglet **Paramètres**. Pour plus d’informations, consultez [Paramètres du mappage de flux de données](parameters-data-flow.md).

Chaque transformation contient au moins quatre onglets de configuration.

#### <a name="transformation-settings"></a>Paramètres de transformation

Le premier onglet du volet de configuration de chaque transformation contient les paramètres spécifiques à cette transformation. Pour plus d’informations, reportez-vous à la page de documentation de cette transformation.

![Onglet Paramètres de la source](media/data-flow/source1.png "Onglet Paramètres de la source")

#### <a name="optimize"></a>Optimiser

L’onglet **Optimiser** contient des paramètres pour configurer des schémas de partitionnement.

![Optimize](media/data-flow/optimize1.png "Optimiser")

Le paramètre par défaut, **Utiliser le partitionnement actuel**, indique à Azure Data Factory d’utiliser le schéma de partitionnement natif pour les flux de données s’exécutant sur Spark. Dans la plupart des scénarios, nous vous recommandons d’utiliser ce paramètre.

Il existe des instances pour lesquelles vous souhaitez ajuster le partitionnement. Par exemple, si vous voulez générer vos transformations dans un seul fichier dans le lac, sélectionnez **Partition unique** dans une transformation de récepteur.

L’optimisation des performances est un autre cas où vous pouvez souhaiter contrôler les schémas de partitionnement. L’ajustement du partitionnement permet de contrôler la répartition de vos données entre les nœuds de calcul et les optimisations de la localisation des données qui peuvent avoir des effets tant positifs que négatifs sur les performances globales de vos flux de données. Pour plus d’informations, consultez le [Guide des performances des flux de données](concepts-data-flow-performance.md).

Pour changer le partitionnement de n’importe quelle transformation, sélectionnez l’onglet **Optimiser**, puis la case d’option **Définir le partitionnement**. Une série d'options de partitionnement vous est présentée. La méthode de partitionnement qui convient varie en fonction des volumes de données, des clés candidates, des valeurs null et de la cardinalité. 

Il est conseillé de commencer avec le partitionnement par défaut, puis d’essayer différentes options de partitionnement. Vous pouvez effectuer des tests à l’aide d’exécutions de débogage de pipeline, puis voir la durée d’exécution et l’utilisation des partitions dans chaque regroupement de transformations à partir de la vue de supervision. Pour plus d’informations, consultez [Supervision des flux de données](concepts-data-flow-monitoring.md).

Les options de partitionnement suivantes sont disponibles.

##### <a name="round-robin"></a>Tourniquet 

Un tourniquet (round robin) est une partition simple qui distribue automatiquement les données de façon uniforme sur les partitions. Utilisez un tourniquet (round robin) lorsque vous ne disposez pas des clés candidates adéquates pour implémenter une stratégie de partitionnement solide et intelligente. Vous pouvez définir le nombre de partitions physiques.

##### <a name="hash"></a>Hachage

Azure Data Factory produit un hachage de colonnes pour obtenir des partitions uniformes, de sorte que des lignes contenant des valeurs similaires tombent dans la même partition. Lorsque vous utilisez l’option Hachage, effectuez un test pour détecter une éventuelle inclinaison de partition. Vous pouvez définir le nombre de partitions physiques.

##### <a name="dynamic-range"></a>Plage dynamique

La plage dynamique utilise des plages dynamiques Spark basées sur les colonnes ou expressions que vous fournissez. Vous pouvez définir le nombre de partitions physiques. 

##### <a name="fixed-range"></a>Plage fixe

Créez une expression qui fournit une plage fixe pour les valeurs figurant dans vos colonnes de données partitionnées. Pour éviter une inclinaison de partition, vous devez avoir une bonne compréhension de vos données avant d’utiliser cette option. Les valeurs que vous entrez pour l'expression sont utilisées dans le cadre d'une fonction de partition. Vous pouvez définir le nombre de partitions physiques.

##### <a name="key"></a>Clé

Si vous avez une bonne compréhension de la cardinalité de vos données, une clé de partitionnement peut être une bonne stratégie. La clé de partitionnement crée des partitions pour chaque valeur unique de votre colonne. Vous ne pouvez pas définir le nombre de partitions car celui-ci dépend des valeurs uniques figurant dans les données.

#### <a name="inspect"></a>Inspecter

Le volet **Inspecter** permet de visualiser les métadonnées du flux de données que vous êtes en train de transformer. Vous pouvez voir le nombre de colonnes, les colonnes modifiées, les colonnes ajoutées, les types de données, l'ordre des colonnes et les références des colonnes. **Inspecter** est un affichage en lecture seule de vos métadonnées. Il n’est pas nécessaire que le mode de débogage soit activé pour voir les métadonnées dans le volet **Inspecter**.

![Inspecter](media/data-flow/inspect1.png "Inspecter")

À mesure que vous modifiez la forme de vos données par le biais de transformations, les changements de métadonnées sont visibles dans le volet **Inspecter**. Si votre transformation de la source ne comporte pas de schéma défini, les métadonnées ne sont pas visibles dans le volet **Inspecter**. L’absence de métadonnées est fréquent dans les scénarios de dérive de schéma.

#### <a name="data-preview"></a>Aperçu des données

Si le mode de débogage est activé, l’onglet **Aperçu des données**  vous donne une capture instantanée interactive des données à chaque transformation. Pour en savoir plus, consultez [Aperçu des données en mode de débogage](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Barre supérieure

La barre supérieure contient des actions qui affectent l’ensemble du flux de données comme l’enregistrement et la validation. Vous pouvez basculer entre les modes graphe et configuration à l’aide des boutons **Afficher le graphique** et **Masquer le graphique**.

![Masquer le graphique](media/data-flow/hideg.png "Masquer le graphique")

Si vous masquez votre graphe, vous pouvez parcourir latéralement dans vos nœuds de transformation à l’aide des boutons **Précédent** et **Suivant**.

![Boutons Précédent et Suivant](media/data-flow/showhide.png "boutons précédent et suivant")

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment créer une [transformation de la source](data-flow-source.md).
* Découvrez comment créer vos flux de données en [mode de débogage](concepts-data-flow-debug-mode.md).
