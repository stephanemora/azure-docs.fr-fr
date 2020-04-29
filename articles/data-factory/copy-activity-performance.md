---
title: Guide sur les performances et la scalabilité de l’activité de copie
description: Découvrez les facteurs clés ayant des répercussions sur les performances du déplacement de données dans Azure Data Factory lorsque vous utilisez l’activité de copie.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: aedb3df69821d1436b03b2eb1f12873b624d426e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414176"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Guide sur les performances et la scalabilité de l’activité de copie

> [!div class="op_single_selector" title1="Sélectionnez la version Azure Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-copy-activity-performance.md)
> * [Version actuelle](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Que vous souhaitiez effectuer une migration de données à grande échelle à partir d’un lac de données ou d’un entrepôt de données d’entreprise vers Azure ou ingérer des données à grande échelle à partir de différentes sources dans Azure à des fins d’analytique Big Data, il est essentiel d’obtenir des performances et une scalabilité optimales.  Azure Data Factory fournit un mécanisme performant, résilient et rentable pour l’ingestion de données à grande échelle, ce qui en fait une solution idéale pour les ingénieurs de données qui cherchent à créer des pipelines d’ingestion de données hautement performants et scalables.

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

- Quel niveau de performance et de scalabilité puis-je obtenir à l’aide de l’activité de copie ADF pour les scénarios de migration de données et d’ingestion de données ?

- Quelles sont les étapes à suivre pour régler les performances de l’activité de copie ADF ?
- Quels sont les leviers d’optimisation des performances ADF que je peux utiliser pour optimiser les performances d’une exécution d’activité de copie unique ?
- Quels sont les autres facteurs hors d’ADF à prendre en compte lors de l’optimisation des performances de copie ?

> [!NOTE]
> Si vous n’êtes pas familiarisé avec l’activité de copie, consultez la [vue d’ensemble de l’activité de copie](copy-activity-overview.md) avant de lire cet article.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Scalabilité et performances de l’activité de copie réalisables avec ADF

ADF offre une architecture serverless qui autorise le parallélisme à différents niveaux, ce qui permet aux développeurs de créer des pipelines pour utiliser pleinement la bande passante de votre réseau, ainsi que les IOPS et la bande passante de stockage pour maximiser le débit de déplacement de données pour votre environnement.  Cela signifie que vous pouvez estimer le débit atteignable en mesurant le débit minimal offert par le magasin de données source, le magasin de données de destination et la bande passante réseau entre la source et la destination.  Le tableau ci-dessous calcule la durée de la copie en fonction de la taille des données et de la limite de bande passante pour votre environnement. 

| Taille des données / <br/> bandwidth | 50 Mbits/s    | 100 Mbits/s  | 500 Mbits/s  | 1 Gbit/s   | 5 Gbit/s   | 10 Gbits/s  | 50 Gbit/s   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 Go**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 min  | 0,03 min | 0,01 min | 0,0 min   |
| **10 Go**                   | 27,3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 min  | 0,03 min  |
| **100 Go**                  | 4,6 heures    | 2,3 heures   | 0,5 heure   | 0,2 heures  | 0,05 heure | 0,02 heure | 0,0 heure   |
| **1 To**                    | 46,6 heures   | 23,3 heures  | 4,7 heures   | 2,3 heures  | 0,5 heure  | 0,2 heures  | 0,05 heure  |
| **10 To**                   | 19,4 jours  | 9,7 jours  | 1,9 jours  | 0,9 jour | 0,2 jour | 0,1 jour | 0,02 jour |
| **100 To**                  | 194,2 jours | 97,1 jours | 19,4 jours | 9,7 jours | 1,9 jours | 1 jour    | 0,2 jour  |
| **1 Po**                    | 64,7 mois    | 32,4 mois   | 6,5 mois    | 3,2 mois   | 0,6 mois   | 0,3 mois   | 0,06 mois   |
| **10 Po**                   | 647,3 mois   | 323,6 mois  | 64,7 mois   | 31.6 mois  | 6,5 mois   | 3,2 mois   | 0,6 mois    |

La copie ADF est scalable à différents niveaux :

![Mise à l’échelle de la copie ADF](media/copy-activity-performance/adf-copy-scalability.png)

- Le flux de contrôle ADF peut démarrer plusieurs activités de copie en parallèle, par exemple [Pour chaque boucle](control-flow-for-each-activity.md).
- Une seule activité de copie peut tirer parti des ressources de calcul évolutives : lorsque vous utilisez Azure Integration Runtime, vous pouvez spécifier [jusqu'à 256 DIUs](#data-integration-units) pour chaque activité de copie serverless ; lorsque vous utilisez des Integration Runtime auto-hébergés, vous pouvez effectuer un scale-up de la machine ou un scale-out vers plusieurs machines ([jusqu’à 4 nœuds](create-self-hosted-integration-runtime.md#high-availability-and-scalability)), et une seule activité de copie partitionnera son jeu de fichiers sur tous les nœuds.
- Une activité de copie unique lit et écrit dans le magasin de données à l’aide de plusieurs threads [en parallèle](#parallel-copy).

## <a name="performance-tuning-steps"></a>Procédure de réglage des performances

Effectuez cette procédure pour régler les performances de votre service Azure Data Factory avec l’activité de copie.

1. **Choisir un jeu de données de test et établir une base de référence.** Pendant la phase de développement, testez votre pipeline en utilisant l’activité de copie sur un échantillon de données représentatif. Le jeu de données que vous choisissez doit représenter vos modèles de données classiques (structure de dossiers, modèle de fichier, schéma de données, etc.) et être suffisamment grand pour évaluer les performances de copie ; par exemple, il faut 10 minutes ou plus pour que l’activité de copie se termine. Collectez les détails de l’exécution et les caractéristiques des performances suivant la [surveillance de l’activité de copie](copy-activity-monitoring.md).

2. **Comment optimiser les performances d’une seule activité de copie** :

   Pour commencer, nous vous recommandons d’optimiser les performances à l’aide d’une seule activité de copie.

   - **Si l’activité de copie est exécutée sur un runtime d’intégration Azure :** commencez avec les valeurs par défaut des paramètres [DIU (unités d’intégration de données)](#data-integration-units) et de [copie en parallèle](#parallel-copy). 

   - **Si l’activité de copie est exécutée sur un runtime d’intégration auto-hébergé :** nous vous recommandons d’utiliser une machine dédiée distincte du serveur hébergeant le magasin de données pour héberger le runtime d’intégration. Commencez avec les valeurs par défaut du paramètre de [copie parallèle](#parallel-copy) en utilisant un seul nœud pour l’IR auto-hébergé.  

   Effectuez une série de tests de performances et prenez note des performances atteintes ainsi que des valeurs réelles utilisées telles que les DIU et les copies parallèles. Reportez-vous à [Superviser l’activité de copie](copy-activity-monitoring.md) pour savoir comment collecter les résultats d’exécution et les paramètres de performances utilisés, et découvrez comment [résoudre les problèmes de performances de l’activité de copie](copy-activity-performance-troubleshooting.md) pour identifier et résoudre le goulot d’étranglement. 

   Renouvelez l’opération pour exécuter des tests de performances supplémentaires en suivant les instructions de résolution des problèmes et de réglage. Dès qu’une exécution d’activité de copie ne peut pas atteindre un meilleur débit, envisagez d’optimiser le débit agrégé en exécutant plusieurs copies simultanément, sur la base des indications fournies à l’étape 3.


3. **Comment optimiser le débit agrégé en exécutant plusieurs copies simultanément :**

   Maintenant que vous avez optimisé les performances d’une seule activité de copie, si vous n’avez pas encore atteint les limites supérieures de débit de votre environnement (réseau, magasin de données source et magasin de données de destination), vous pouvez exécuter plusieurs activités de copie en parallèle à l’aide de constructions de flux de contrôle ADF telles qu’une [boucle For Each](control-flow-for-each-activity.md). Reportez-vous aux modèles de solution [Copie de fichiers à partir de plusieurs conteneurs](solution-template-copy-files-multiple-containers.md), [Migrer des données d’Amazon S3 vers ADLS Gen2](solution-template-migration-s3-azure.md) ou [Copie en bloc avec une table de contrôles](solution-template-bulk-copy-with-control-table.md) en guise d’exemple général.

5. **Étendez la configuration à l’ensemble de votre jeu de données.** Lorsque vous êtes satisfait des résultats et des performances de l’exécution, vous pouvez étendre la définition et le pipeline pour couvrir l’ensemble de votre jeu de données.

## <a name="troubleshoot-copy-activity-performance"></a>Résoudre les problèmes de performances de l’activité de copie

Suivez la [procédure de réglage des performances](#performance-tuning-steps) pour planifier et effectuer un test de performances pour votre scénario. Et découvrez comment résoudre les problèmes de performances de chaque exécution d’activité de copie dans Azure Data Factory dans [Résoudre les problèmes de performances de l’activité de copie](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Fonctionnalités d’optimisation des performances de copie

Azure Data Factory fournit les fonctionnalités d’optimisation des performances suivantes :

- [Unités d’intégration de données](#data-integration-units)
- [Extensibilité du runtime d’intégration auto-hébergé](#self-hosted-integration-runtime-scalability)
- [Copie en parallèle](#parallel-copy)
- [Copie intermédiaire](#staged-copy)

### <a name="data-integration-units"></a>Unités d’intégration de données

Une unité d’intégration de données est une mesure qui représente la puissance (combinaison de l’allocation de ressources de processeur, de mémoire et de réseau) d’une seule unité dans Azure Data Factory. Une unité d’intégration de données s’applique seulement à [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime), et non pas au [runtime d’intégration auto-hébergé](concepts-integration-runtime.md#self-hosted-integration-runtime). [Plus d’informations](copy-activity-performance-features.md#data-integration-units)

### <a name="self-hosted-integration-runtime-scalability"></a>Extensibilité du runtime d’intégration auto-hébergé

Pour héberger une charge de travail simultanée croissante ou obtenir des performances supérieures, vous pouvez appliquer un scale-up ou un scale-out au runtime d’intégration auto-hébergé. [Plus d’informations](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)

### <a name="parallel-copy"></a>Copie en parallèle

Vous pouvez définir la copie parallèle pour indiquer le parallélisme que vous voulez que l’activité de copie utilise. Vous pouvez voir cette propriété comme le nombre maximum de threads dans l’activité de copie qui lisent dans votre source ou écrivent dans vos magasins de données récepteurs en parallèle. [Plus d’informations](copy-activity-performance-features.md#parallel-copy)

### <a name="staged-copy"></a>copie intermédiaire

Lorsque vous copiez des données entre une banque de données source et une banque de données réceptrice, vous pouvez choisir d’utiliser le stockage Blob comme banque intermédiaire. [Plus d’informations](copy-activity-performance-features.md#staged-copy)

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres articles relatifs à l’activité de copie :

- [Vue d’ensemble des activités de copie](copy-activity-overview.md)
- [Résoudre les problèmes de performances de l’activité de copie](copy-activity-performance-troubleshooting.md)
- [Fonctionnalités d’optimisation des performances de l’activité de copie](copy-activity-performance-features.md)
- [Utiliser Azure Data Factory pour migrer des données de Data Lake ou Data Warehouse vers Azure](data-migration-guidance-overview.md)
- [Migrer des données d’Amazon S3 vers le stockage Azure](data-migration-guidance-s3-azure-storage.md)
