---
title: Déterminer la taille de sous-réseau requise
titleSuffix: Azure SQL Managed Instance
description: Cette rubrique explique comment calculer la taille du sous-réseau dans lequel les instances Azure SQL Managed Instance seront déployées.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma, bonova, srbozovi, wiassaf
ms.date: 06/14/2021
ms.openlocfilehash: e3c789ec59e66189753c8515235b2375aa20e5f1
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113687390"
---
# <a name="determine-required-subnet-size-and-range-for-azure-sql-managed-instance"></a>Déterminer la taille et la plage du sous-réseau pour Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance doit être déployé au sein d’un [réseau virtuel](../../virtual-network/virtual-networks-overview.md) Azure. Le nombre d’instances managées pouvant être déployées dans le sous-réseau d’un réseau virtuel dépend de la taille du sous-réseau (plage de sous-réseau).

Quand vous créez une Instance gérée, Azure alloue un certain nombre de machines virtuelles en fonction du niveau que vous avez sélectionné pendant l’approvisionnement. Étant donné que ces machines virtuelles sont associées à votre sous-réseau, elles nécessitent des adresses IP. Pour garantir la haute disponibilité pendant les opérations normales et la maintenance des services, Azure peut allouer davantage de machines virtuelles. Le nombre d’adresses IP requises dans un sous-réseau dépasse alors le nombre d’instances gérées dans ce sous-réseau.

Par défaut, une option Managed Instance a besoin d’un minimum de 32 adresses IP dans un sous-réseau. Ainsi, vous pouvez utiliser un masque de sous-réseau minimal de /27 lors de la définition de vos plages d’adresses IP de sous-réseau. Nous vous recommandons de planifier minutieusement la taille des sous-réseaux pour vos déploiements d’instances managées. Lors de la planification, tenez compte des entrées suivantes :

- Nombre d’instances managées, y compris les paramètres d’instance suivants :
  - Niveau de service
  - Génération du matériel
  - Nombre de vCores
  - [Fenêtre de maintenance](../database/maintenance-window.md)
- Plans de mise à l’échelle ou de modification du niveau de service

> [!IMPORTANT]
> Un sous-réseau d’une taille de 16 adresses IP (masque de sous-réseau /28) permet le déploiement d’une seule instance gérée. Il ne doit être utilisé qu’à des fins d’évaluation ou pour des scénarios de développement/test où des opérations de mise à l’échelle ne sont pas effectuées. 

## <a name="determine-subnet-size"></a>Déterminer la taille du sous-réseau

Dimensionnez votre sous-réseau en fonction de vos besoins futurs de déploiement et de mise à l’échelle de l’instance. Les paramètres suivants peuvent vous aider à effectuer un calcul :

- Azure utilise cinq adresses IP dans le sous-réseau pour ses besoins propres.
- Chaque cluster virtuel alloue un nombre supplémentaire d’adresses. 
- Chaque instance gérée utilise un nombre d’adresses dépendant du niveau tarifaire et de la génération de matériel.
- Chaque demande de mise à l’échelle alloue temporairement un nombre supplémentaire d’adresses.

> [!IMPORTANT]
> Il n’est pas possible de modifier la plage d’adresses de sous-réseau s’il existe une ressource dans le sous-réseau. Il n’est pas non plus possible de déplacer des instances gérées d’un sous-réseau vers un autre. Envisagez d’utiliser des sous-réseaux de plus grande taille pour éviter des problèmes futurs.

GP = usage général ; BC = critique pour l’entreprise ; VC = cluster virtuel

| **Génération du matériel** | **Niveau tarifaire** | **Utilisation d’Azure** | **Utilisation de VC** | **Utilisation de l’instance** | **Total** |
| --- | --- | --- | --- | --- | --- |
| Gen4 | GP | 5 | 1 | 5 | 11 |
| Gen4 | BC | 5 | 1 | 5 | 11 |
| Gen5 | GP | 5 | 6 | 3 | 14 |
| Gen5 | BC | 5 | 6 | 5 | 16 |

Dans le tableau précédent :

- La colonne **Total** affiche le nombre total d’adresses utilisées par une seule instance déployée sur le sous-réseau. 
- Lorsque vous ajoutez des instances au sous-réseau, le nombre d’adresses utilisées par les instances augmente. Le nombre total d’adresses augmente alors également. Par exemple, l’ajout d’une autre instance gérée Gen4 GP aurait pour effet de porter à 10 la valeur **Utilisation des instances**, et à 16 celle du **Total** des adresses utilisées. 
- Les adresses représentées dans la colonne **Azure usage** (Utilisation d’Azure) sont partagées entre plusieurs clusters virtuels.  
- Les adresses représentées dans la colonne **VC usage** (Utilisation de cluster virtuel) sont partagées entre plusieurs clusters virtuels.

Tenez également compte de la [fonctionnalité de fenêtre de maintenance](../database/maintenance-window.md) lorsque vous déterminez la taille de sous-réseau, en particulier lorsque plusieurs instances seront déployées dans le même sous-réseau. Si vous spécifiez une fenêtre de maintenance pour une instance gérée pendant ou après sa création, celle-ci doit être placée dans un cluster virtuel dont la fenêtre de maintenance correspond. S’il n’existe pas de cluster virtuel de ce type dans le sous-réseau, vous devez d’abord en créer un pour l’instance.

Une opération de mise à jour nécessite généralement un [redimensionnement du cluster virtuel](management-operations-overview.md). Quand une nouvelle demande de création ou de mise à jour apparaît, le service SQL Managed Instance communique avec la plateforme de calcul avec une demande de nouveaux nœuds qui doivent être ajoutés. En fonction de la réponse de calcul, le système de déploiement développe le cluster virtuel existant ou en crée un nouveau. Même si, dans la plupart des cas, l’opération est effectuée au sein du même cluster virtuel, une nouvelle opération peut être créée côté calcul. 


## <a name="update-scenarios"></a>Scénarios de mise à jour

Pendant l’opération de mise à l’échelle, les instances nécessitent temporairement une capacité d’adresse IP supplémentaire qui dépend du niveau tarifaire et de la génération de matériel :

| **Génération du matériel** | **Niveau tarifaire** | **Scénario** | **Adresses supplémentaires**  |
| --- | --- | --- | --- |
| Gen4<sup>1</sup> | GP ou BC | Mise à l’échelle de vCores | 5 |
| Gen4<sup>1</sup> | GP ou BC | Mise à l’échelle du stockage | 5 |
| Gen4 | GP ou BC | Passage de GP à BC ou BC à GP | 5 |
| Gen4 | GP | Passage à Gen5 | 9 |
| Gen4 | BC | Passage à Gen5 | 11 |
| Gen5 | GP | Mise à l’échelle de vCores | 3 |
| Gen5 | GP | Mise à l’échelle du stockage | 0 |
| Gen5 | GP | Passage à BC | 5 |
| Gen5 | BC | Mise à l’échelle de vCores | 5 |
| Gen5 | BC | Mise à l’échelle du stockage | 5 |
| Gen5 | BC | Passage à GP | 3 |

<sup>1</sup> Le matériel Gen4 est en cours de retrait et n’est plus disponible pour de nouveaux déploiements. La mise à jour de la génération de matériel de Gen4 vers Gen5 tirera parti des fonctionnalités spécifiques de Gen5.
  
## <a name="calculate-the-number-of-ip-addresses"></a>Calculer le nombre d’adresses IP

Nous vous recommandons d’utiliser la formule suivante pour calculer le nombre total d’adresses IP. Cette formule prend en compte la création potentielle d’un nouveau cluster virtuel lors d’une demande de création ou d’une mise à jour d’instance ultérieure. Elle prend également en compte les exigences de fenêtre de maintenance pour les clusters virtuels.

**Formule : 5 + (a * 12) + (b * 16) + (c * 16)**

- a = nombre d’instances à usage général (GP)
- b = nombre d’instances critiques pour l’entreprise (BC)
- c = nombre de configurations de fenêtre de maintenance différentes

Explication :
- 5 = nombre d’adresses IP réservées par Azure
- 12 adresses par instance GP = 6 pour le cluster virtuel, 3 pour l’instance gérée, 3 supplémentaires pour l’opération de mise à l’échelle
- 16 adresses par instance BC = 6 pour le cluster virtuel, 5 pour l’instance gérée, 5 supplémentaires pour l’opération de mise à l’échelle
- 16 adresses en tant que sauvegarde = scénario dans lequel un nouveau cluster virtuel est créé

Exemple : 
- Vous envisagez de disposer de trois instances gérées à usage général et de deux instances gérées critiques pour l’entreprise déployées dans le même sous-réseau. La même fenêtre de maintenance est configurée pour toutes les instances. Cela signifie que vous avez besoin de 5 + (3 * 12) + (2 * 16) + (1 * 16) = 89 adresses IP. 

  Étant donné que les plages d’adresses IP sont définies dans des puissances de 2, votre sous-réseau requiert une plage d’adresses IP minimale de 128 (2^7) pour ce déploiement. Vous devez réserver le sous-réseau avec un masque de sous-réseau de /25.

> [!NOTE]
> Bien qu’il soit possible de déployer des instances gérées sur un sous-réseau avec un nombre d’adresses IP inférieur à la sortie de la formule de sous-réseau, songez toujours à utiliser à la place des sous-réseaux de plus grande taille. L’utilisation d’un sous-réseau de plus grande taille peut aider à éviter des problèmes futurs résultant de l’incapacité à créer des instances supplémentaires dans le sous-réseau ou de mettre à l’échelle des instances existantes. 

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation d’Azure SQL Managed Instance](sql-managed-instance-paas-overview.md).
- Découvrez plus en détail l’[architecture de connectivité pour SQL Managed Instance](connectivity-architecture-overview.md).
- Découvrez comment [créer un réseau virtuel dans lequel vous allez déployer des instances gérées SQL](virtual-network-subnet-create-arm-template.md).
- Pour les problèmes liés au DNS, consultez [Configurer un DNS personnalisé](custom-dns-configure.md).
