---
title: Limites de mémoire et de concurrence – Azure SQL Data Warehouse | Microsoft Docs
description: Afficher les limites de mémoire et de concurrence allouées aux différents niveaux de performance et classes de ressources dans Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/04/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 802408f6ccd0a1cc0ed4f4d87d54a11760cd70fe
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55473440"
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Limites de mémoire et de concurrence pour Azure SQL Data Warehouse
Afficher les limites de mémoire et de concurrence allouées aux différents niveaux de performance et classes de ressources dans Azure SQL Data Warehouse. Pour plus d’informations et pour appliquer ces fonctionnalités à votre plan de gestion de la charge de travail, consultez [Classes de ressources pour la gestion des charges de travail](resource-classes-for-workload-management.md). 

Il existe deux générations disponibles avec SQL Data Warehouse : Gen1 et Gen2. Nous vous recommandons de tirer parti de Gen2 de SQL Data Warehouse afin d’obtenir les meilleures performances pour votre charge de travail d’entrepôt de données. Gen2 introduit un nouveau cache SSD NVMe qui conserve les données les plus fréquemment sollicitées proche de l’UC. Ainsi, les opérations d’E/S à distance liées à vos charges de travail les plus intensives et exigeantes sont supprimées. Outre les performances, Gen2 offre le meilleur niveau d’échelle en vous permettant de mettre à l’échelle jusqu’à 30 000 DWU et en fournissant un stockage en colonnes illimité. Nous prendrons toujours en charge la génération antérieure (Gen1) de SQL Data Warehouse et conserverons les mêmes fonctionnalités ; toutefois, nous vous encourageons à [effectuer une mise à niveau vers Gen2](upgrade-to-latest-generation.md) dès que possible. 

## <a name="data-warehouse-capacity-settings"></a>Paramètres de la capacité de l’entrepôt de données
Les tableaux suivants présentent la capacité maximale pour l’entrepôt de données à différents niveaux de performance. Pour modifier le niveau de performance, consultez [Calcul de mise à l’échelle – portail](quickstart-scale-compute-portal.md).

### <a name="gen2"></a>Deuxième génération

Gen2 fournit 2,5 fois plus de mémoire par requête que Gen1. Cette mémoire supplémentaire permet à Gen2 d’offrir des performances particulièrement rapides.  Les niveaux de performance de Gen2 vont de DW100c à DW30000c. 

| Niveau de performance | Nœuds de calcul | Distributions par nœud de calcul | Mémoire par entrepôt de données (Go) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c            | 1             | 60                             |   300                          |
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1 200                          |
| DW2500c           | 5.             | 12                             |  1 500                          |
| DW3000c           | 6.             | 10                             |  1 800                          |
| DW5000c           | 10            | 6.                              |  3000                          |
| DW6000c           | 12            | 5.                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

Le nombre maximal de DWU Gen2 est DW30000c, qui correspond à 60 nœuds de calcul et une distribution par nœud de calcul. Par exemple, un entrepôt de données de 600 To à DW30000c traite environ 10 To par nœud de calcul.

### <a name="gen1"></a>Première génération

Les niveaux de service pour Gen1 vont de DW100 à DW6000. 

| Niveau de performance | Nœuds de calcul | Distributions par nœud de calcul | Mémoire par entrepôt de données (Go) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100             | 1             | 60                             |  24                            |
| DW200             | 2             | 30                             |  48                            |
| DW300             | 3             | 20                             |  72                            |
| DW400             | 4             | 15                             |  96                            |
| DW500             | 5.             | 12                             | 120                            |
| DW600             | 6.             | 10                             | 144                            |
| DW1000            | 10            | 6.                              | 240                            |
| DW1200            | 12            | 5.                              | 288                            |
| DW1500            | 15            | 4                              | 360                            |
| DW2000            | 20            | 3                              | 480                            |
| DW3000            | 30            | 2                              | 720                            |
| DW6000            | 60            | 1                              | 1 440                           |

## <a name="concurrency-maximums"></a>Valeurs maximales de concurrence
Pour s’assurer que chaque requête dispose de suffisamment de ressources pour s’exécuter efficacement, SQL Data Warehouse suit l’utilisation des ressources en assignant des emplacements de concurrence à chaque requête. Le système place les requêtes en file d’attente jusqu’à ce que suffisamment d’[emplacements de concurrence](resource-classes-for-workload-management.md#concurrency-slots) soient disponibles. Les emplacements de concurrence déterminent également la hiérarchisation des priorités du processeur. Pour plus d’informations, voir [Analyser votre charge de travail](analyze-your-workload.md).

### <a name="gen2"></a>Deuxième génération
 
**Classes de ressources statiques**

Le tableau suivant indique le nombre maximal de requêtes concurrentes et d’emplacements de concurrence pour chaque [classe de ressources statique](resource-classes-for-workload-management.md).  

| Niveau de service | Nombre maximal de requêtes concurrentes | Emplacements de concurrence disponibles |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1 200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Classes de ressources dynamiques**

> [!NOTE]
> La classe de ressources smallrc sur Gen2 ajoute dynamiquement de la mémoire quand le niveau de service augmente et ne prend en charge que 32 requêtes concurrentes au maximum avec DW1000c et 4 avec DW100c.  Une fois l’instance mise à l’échelle au-delà de DW1500c, la mémoire et les emplacements de concurrence utilisés par smallrc augmentent quand le niveau de service augmente. 
>
>

Le tableau suivant indique le nombre maximal de requêtes concurrentes et d’emplacements de concurrence pour chaque [classe de ressources dynamique](resource-classes-for-workload-management.md). À la différence de Gen1, les classes de ressources dynamiques sur Gen2 sont véritablement dynamiques.  Gen2 utilise une allocation de pourcentage de mémoire 3-10-22-70 pour les classes de ressources small-medium-large-xlarge à tous les niveaux de service.

| Niveau de service | Nombre maximal de requêtes concurrentes | Emplacements de concurrence disponibles | Emplacements utilisés par smallrc | Emplacements utilisés par mediumrc | Emplacements utilisés par largerc | Emplacements utilisés par xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5.                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6.                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6.                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1 200                        | 36                    | 120                    | 264                   | 840                    |



#### <a name="gen1"></a>Première génération

Classes de ressources statiques

Le tableau suivant indique le nombre maximal de requêtes concurrentes et d’emplacements de concurrence pour chaque [classe de ressources statique](resource-classes-for-workload-management.md) sur **Gen1**.

| Niveau de service | Nombre maximal de requêtes concurrentes | Nombre maximal d’emplacements de concurrence |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:-------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100         | 4                          |   4                       | 1         | 2          | 4          | 4          |  4         |  4         |  4         |   4        |
| DW200         | 8                          |   8                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW300         | 12                         |  12                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400         | 16                         |  16                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500         | 20                         |  20                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW600         | 24                         |  24                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000        | 32                         |  40                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1200        | 32                         |  48                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500        | 32                         |  60                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000        | 48                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 64                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 128                        | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Classes de ressources dynamiques
> [!NOTE]
> La classe de ressources smallrc sur Gen1 alloue une quantité fixe de mémoire par requête, à l’image de la classe de ressources statique staticrc10.  La classe smallrc étant statique, elle peut prendre en charge jusqu’à 128 requêtes concurrentes. 
>
>

Le tableau suivant indique le nombre maximal de requêtes concurrentes et d’emplacements de concurrence pour chaque [classe de ressources dynamique](resource-classes-for-workload-management.md) sur **Gen1**.

| Niveau de service | Nombre maximal de requêtes concurrentes | Emplacements de concurrence disponibles | smallrc | mediumrc | largerc | xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:-------:|:--------:|:-------:|:--------:|
| DW100         |  4                         |   4                         | 1       |  1       |  2      |   4      |
| DW200         |  8                         |   8                         | 1       |  2       |  4      |   8      |
| DW300         | 12                         |  12                         | 1       |  2       |  4      |   8      |
| DW400         | 16                         |  16                         | 1       |  4       |  8      |  16      |
| DW500         | 20                         |  20                         | 1       |  4       |  8      |  16      |
| DW600         | 24                         |  24                         | 1       |  4       |  8      |  16      |
| DW1000        | 32                         |  40                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  48                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  60                         | 1       |  8       | 16      |  32      |
| DW2000        | 48                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 64                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 128                        | 240                         | 1       | 32       | 64      | 128      |


Lorsque l’un de ces seuils est atteint, les nouvelles requêtes sont mises en file d’attente et exécutées sur la base du modèle premier entré, premier sorti.  À mesure que les requêtes se terminent et que le nombre de requêtes et d’emplacements chute sous les limites, SQL Data Warehouse libère des requêtes en file d’attente. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon de tirer parti des classes de ressources pour optimiser davantage votre charge de travail, voir les articles suivants :
* [Classes de ressources pour la gestion des charges de travail](resource-classes-for-workload-management.md)
* [Analyse de votre charge de travail](analyze-your-workload.md)

