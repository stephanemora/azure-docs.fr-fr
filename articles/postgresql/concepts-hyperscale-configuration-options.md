---
title: Options de performance – Hyperscale (Citus) – Azure Database pour PostgreSQL
description: Options pour un groupe de serveurs Hyperscale (Citus), notamment le nœud de calcul, le stockage et les régions.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77462409"
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
    * Europe Nord
    * Sud du Royaume-Uni
    * Europe Ouest

Certaines de ces régions peuvent ne pas être initialement activées sur tous les abonnements Azure. Si vous souhaitez utiliser une région de la liste ci-dessus et ne la voyez pas dans votre abonnement, ou si vous souhaitez utiliser une région qui ne figure pas dans cette liste, ouvrez une [demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Tarifs
Pour obtenir les dernières informations sur la tarification, veuillez consulter le service [Page de tarification](https://azure.microsoft.com/pricing/details/postgresql/).
Pour voir le coût de la configuration souhaitée, le [Portail Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) affiche le coût mensuel dans l’onglet **Configurer** selon les options que vous avez sélectionnées. Si vous n’avez pas d’abonnement Azure, vous pouvez utiliser la calculatrice de prix Azure pour obtenir une estimation. Pour personnaliser les options, sur le site web [Calculatrice de prix d’Azure](https://azure.microsoft.com/pricing/calculator/), sélectionnez **Ajouter des éléments**, développez la catégorie **Bases de données**, puis choisissez **Azure Database pour PostgreSQL - Hyperscale (Citus)** .
 
## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [créer un groupe de serveurs Hyperscale (Citus) dans le portail](quickstart-create-hyperscale-portal.md).
