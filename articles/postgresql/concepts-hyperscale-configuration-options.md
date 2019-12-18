---
title: Options de performance – Hyperscale (Citus) – Azure Database pour PostgreSQL
description: Options pour un groupe de serveurs Hyperscale (Citus), notamment le nœud de calcul, le stockage et les régions.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 2ee3e661d6c01aa2e4f37ac9a70e00be5da5f794
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975633"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Azure Database pour PostgreSQL - Options de performance d’Hyperscale (Citus)

## <a name="compute-and-storage"></a>Calcul et stockage
 
Vous pouvez sélectionner les paramètres de calcul et de stockage indépendamment pour les nœuds Worker et le nœud coordinateur dans un groupe de serveurs Hyperscale (Citus).  Les ressources de calcul sont fournies en tant que vCores, représentant le processeur logique du matériel sous-jacent. La taille de stockage pour l’approvisionnement fait référence à la capacité disponible pour les nœuds coordinateur et Worker dans votre groupe de serveurs Hyperscale (Citus). Le stockage inclut les fichiers de base de données, les fichiers temporaires, les journaux d’activité de transaction et les journaux d’activité du serveur PostgreSQL. La quantité totale de stockage que vous approvisionnez définit également la capacité d’E/S disponible sur chaque nœud Worker et coordinateur.
 
|                       | Nœud Worker           | Nœud coordinateur      |
|-----------------------|-----------------------|-----------------------|
| Calcule, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Mémoire par vCore, Gio | 8                     | 4                     |
| Taille de stockage, Tio     | 0,5, 1, 2             | 0,5, 1, 2             |
| Type de stockage          | Usage général (SSD) | Usage général (SSD) |
| E/S par seconde                  | Jusqu’à 3 IOPS/Gio      | Jusqu’à 3 IOPS/Gio      |


## <a name="regions"></a>Régions
Les groupes de serveurs Hyperscale (Citus) sont disponibles dans les régions Azure suivantes :

* Amérique :
    * Canada Centre *
    * USA Est
    * USA Est 2
    * USA Centre Nord *
    * USA Ouest 2
* Asie-Pacifique :
    * Australie Est *
    * Asie Sud-Est
* Europe :
    * Europe Nord
    * Royaume-Uni Sud
    * Europe Ouest

Les régions avec un astérisque (\*) ne prennent pas encore en charge la [haute disponibilité](concepts-hyperscale-high-availability.md).

## <a name="pricing"></a>Tarifs
Pour obtenir les dernières informations sur la tarification, veuillez consulter le service [Page de tarification](https://azure.microsoft.com/pricing/details/postgresql/).
Pour voir le coût de la configuration souhaitée, le [Portail Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) affiche le coût mensuel dans l’onglet **Configurer** selon les options que vous avez sélectionnées. Si vous n’avez pas d’abonnement Azure, vous pouvez utiliser la calculatrice de prix Azure pour obtenir une estimation. Pour personnaliser les options, sur le site web [Calculatrice de prix d’Azure](https://azure.microsoft.com/pricing/calculator/), sélectionnez **Ajouter des éléments**, développez la catégorie **Bases de données**, puis choisissez **Azure Database pour PostgreSQL - Hyperscale (Citus)** .
 
## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [créer un groupe de serveurs Hyperscale (Citus) dans le portail](quickstart-create-hyperscale-portal.md).
