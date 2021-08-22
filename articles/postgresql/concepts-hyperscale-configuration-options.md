---
title: Options de configuration – Hyperscale (Citus) – Azure Database pour PostgreSQL
description: Options pour un groupe de serveurs Hyperscale (Citus), notamment le nœud de calcul, le stockage et les régions.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.custom: references_regions
ms.date: 08/03/2021
ms.openlocfilehash: f46f5a01765d03c521c7bbc21b51aef9f3a8f93a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532494"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database pour PostgreSQL - Options de configuration d’Hyperscale (Citus)

## <a name="compute-and-storage"></a>Calcul et stockage
 
Vous pouvez sélectionner les paramètres de calcul et de stockage indépendamment pour les nœuds Worker et le nœud coordinateur dans un groupe de serveurs Hyperscale (Citus).  Les ressources de calcul sont fournies en tant que vCores, représentant le processeur logique du matériel sous-jacent. La taille de stockage pour l’approvisionnement fait référence à la capacité disponible pour les nœuds coordinateur et Worker dans votre groupe de serveurs Hyperscale (Citus). Le stockage inclut les fichiers de base de données, les fichiers temporaires, les journaux d’activité de transaction et les journaux d’activité du serveur PostgreSQL.

### <a name="standard-tier"></a>Niveau standard
 
| Ressource              | Nœud Worker           | Nœud coordinateur      |
|-----------------------|-----------------------|-----------------------|
| Calcule, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Mémoire par vCore, Gio | 8                     | 4                     |
| Taille de stockage, Tio     | 0,5, 1, 2             | 0,5, 1, 2             |
| Type de stockage          | Usage général (SSD) | Usage général (SSD) |
| E/S par seconde                  | Jusqu’à 3 IOPS/Gio      | Jusqu’à 3 IOPS/Gio      |

La quantité totale de mémoire RAM dans un seul nœud Hyperscale (Citus) est basée sur le nombre de vCores sélectionné.

| vCores | Un nœud Worker, Gio de RAM | Nœud coordinateur, Gio de RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

La quantité totale de stockage que vous approvisionnez définit également la capacité d’E/S disponible sur chaque nœud Worker et coordinateur.

| Taille de stockage, Tio | Nombre maximal d’E/S par seconde |
|-------------------|--------------|
| 0.5               | 1 536        |
| 1                 | 3 072        |
| 2                 | 6 148        |

Pour l’ensemble du cluster Hyperscale (Citus), les IOPS agrégées présentent les valeurs suivantes :

| Nœuds de travail | 0,5 Tio, IOPS totales | 1 Tio, IOPS totales | 2 Tio, IOPS totales |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3 072               | 6 144             | 12 296            |
| 3            | 4 608               | 9 216             | 18 444            |
| 4            | 6 144               | 12 288            | 24 592            |
| 5            | 7 680               | 15 360            | 30 740            |
| 6            | 9 216               | 18 432            | 36 888            |
| 7            | 10 752              | 21 504            | 43 036            |
| 8            | 12 288              | 24 576            | 49 184            |
| 9            | 13 824              | 27 648            | 55 332            |
| 10           | 15 360              | 30 720            | 61 480            |
| 11           | 16 896              | 33 792            | 67 628            |
| 12           | 18 432              | 36 864            | 73 776            |
| 13           | 19 968              | 39 936            | 79 924            |
| 14           | 21 504              | 43 008            | 86 072            |
| 15           | 23,040              | 46 080            | 92 220            |
| 16           | 24 576              | 49 152            | 98 368            |
| 17           | 26 112              | 52 224            | 104 516           |
| 18           | 27 648              | 55 296            | 110 664           |
| 19           | 29 184              | 58 368            | 116 812           |
| 20           | 30 720              | 61 440            | 122 960           |

### <a name="basic-tier"></a>Niveau de base

Le [niveau de base](concepts-hyperscale-tiers.md) Hyperscale (Citus) est un groupe de serveurs avec un seul nœud.  Étant donné qu’il n’existe pas de distinction entre les nœuds coordinateur et Worker, il est moins compliqué de choisir des ressources de calcul et de stockage.

| Ressource              | Options disponibles     |
|-----------------------|-----------------------|
| Calcule, vCores       | 2, 4, 8               |
| Mémoire par vCore, Gio | 4                     |
| Taille de stockage, Gio     | 128, 256, 512         |
| Type de stockage          | Usage général (SSD) |
| E/S par seconde                  | Jusqu’à 3 IOPS/Gio      |

La quantité totale de mémoire RAM dans un seul nœud Hyperscale (Citus) est basée sur le nombre de vCores sélectionné.

| vCores | Gio de RAM |
|--------|---------|
| 2      | 8       |
| 4      | 16      |
| 8      | 32      |

La quantité totale de stockage que vous approvisionnez définit également la capacité d’E/S disponible sur le nœud du niveau de base.

| Taille de stockage, Gio | Nombre maximal d’E/S par seconde |
|-------------------|--------------|
| 128               | 384          |
| 256               | 768          |
| 512               | 1 536        |

## <a name="regions"></a>Régions
Les groupes de serveurs Hyperscale (Citus) sont disponibles dans les régions Azure suivantes :

* États-Unis:
    * Brésil Sud
    * Centre du Canada
    * USA Centre
    * USA Est
    * USA Est 2
    * Centre-Nord des États-Unis
    * USA Ouest 2
* Asie-Pacifique :
    * Australie Est
    * Japon Est
    * Centre de la Corée
    * Asie Sud-Est
* Europe :
    * France Centre
    * Allemagne Centre-Ouest
    * Europe Nord
    * Suisse Nord
    * Sud du Royaume-Uni
    * Europe Ouest

Certaines de ces régions peuvent ne pas être initialement activées sur tous les abonnements Azure. Si vous souhaitez utiliser une région de la liste ci-dessus et ne la voyez pas dans votre abonnement, ou si vous souhaitez utiliser une région qui ne figure pas dans cette liste, ouvrez une [demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Tarifs
Pour obtenir les dernières informations sur la tarification, veuillez consulter le service [Page de tarification](https://azure.microsoft.com/pricing/details/postgresql/).
Pour voir le coût de la configuration souhaitée, le [Portail Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) affiche le coût mensuel dans l’onglet **Configurer** selon les options que vous avez sélectionnées. Si vous n’avez pas d’abonnement Azure, vous pouvez utiliser la calculatrice de prix Azure pour obtenir une estimation. Pour personnaliser les options, sur le site web [Calculatrice de prix d’Azure](https://azure.microsoft.com/pricing/calculator/), sélectionnez **Ajouter des éléments**, développez la catégorie **Bases de données**, puis choisissez **Azure Database pour PostgreSQL - Hyperscale (Citus)** .
 
## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [créer un groupe de serveurs Hyperscale (Citus) dans le portail](quickstart-create-hyperscale-portal.md).
