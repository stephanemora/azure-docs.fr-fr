---
title: Azure Database pour PostgreSQL – très grande échelle (Citus) (version préliminaire) options de performances
description: Options pour un groupe de serveurs de très grande échelle (Citus), y compris des régions, le stockage et calcul de nœud.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e8c1c2e51ca14ae9b17f0d7efb20552cdd55139b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077289"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-preview-performance-options"></a>Azure Database pour PostgreSQL – très grande échelle (Citus) (version préliminaire) options de performances

## <a name="compute-and-storage"></a>Calcul et stockage
 
Vous pouvez sélectionner les paramètres de calcul et de stockage indépendamment pour les nœuds worker et le nœud coordinateur dans un groupe de serveurs de très grande échelle (Citus).  Les ressources de calcul sont fournies en tant que vCores, représentant le processeur logique du matériel sous-jacent. La taille de stockage pour la configuration fait référence à la capacité disponible pour les nœuds de coordinateur et de travail dans votre groupe de serveurs de très grande échelle (Citus). Le stockage inclut les fichiers de base de données, les fichiers temporaires, les journaux des transactions et les journaux du serveur Postgres. La quantité totale de stockage que vous approvisionnez également définit la capacité d’e/s disponible sur chaque nœud worker et coordinateur.
 
|                       | Nœud Worker           | Nœud coordinateur      |
|-----------------------|-----------------------|-----------------------|
| Compute, vCores       | 4, 8, 16, 32          | 4, 8, 16, 32          |
| Mémoire par vCore, Gio | 8                     | 4                     |
| Taille de stockage, TIO     | 0.5, 1, 2             | 0.5, 1, 2             |
| Type de stockage          | Usage général (SSD) | Usage général (SSD) |
| E/S par seconde                  | Jusqu'à 3 e/s/Gio      | Jusqu'à 3 e/s/Gio      |


## <a name="regions"></a>Régions
Les groupes de serveurs de très grande échelle (Citus) sont disponibles dans les régions Azure suivantes :
* USA Est 2
* Asie Sud-Est
* Europe Ouest
* USA Ouest 2

## <a name="pricing"></a>Tarifs
Pour obtenir les dernières informations sur la tarification, veuillez consulter le service [Page de tarification](https://azure.microsoft.com/pricing/details/postgresql/).
Pour voir le coût de la configuration souhaitée, le [Azure portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) affiche le coût mensuel sur le **configurer** onglet basé sur les options que vous sélectionnez. Si vous n’avez pas d’abonnement Azure, vous pouvez utiliser la calculatrice de prix Azure pour obtenir une estimation. Sur le [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) site Web, sélectionnez **ajouter des éléments**, développez le **bases de données** catégorie, puis choisissez **Azure Database pour PostgreSQL : Très grande échelle (Citus)** pour personnaliser les options.
 
## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [créer un groupe de serveurs de très grande échelle (Citus) dans le portail](quickstart-create-hyperscale-portal.md).
