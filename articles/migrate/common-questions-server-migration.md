---
title: Questions courantes sur la migration de serveur Azure Migrate
description: Obtenez des réponses aux questions courantes sur la migration de serveur Azure Migrate
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: bae3447f0fada18de5473e1ef1a1c1d431535f63
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77065996"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migration de serveur Azure Migrate : Questions courantes

Cet article répond à des questions courantes sur la migration de serveur Azure Migrate . Si, après avoir lu cet article, vous avez d’autres questions, posez-les sur le [forum Azure Migrate](https://aka.ms/AzureMigrateForum). Si vous avez d’autres questions, consultez ces articles :

- [Questions générales](resources-faq.md) sur Azure Migrate.
- [Questions](common-questions-appliance.md) sur l’appliance Azure Migrate.
- [Questions](common-questions-discovery-assessment.md) sur la découverte, l’évaluation et la visualisation des dépendances.


## <a name="how-does-agentless-vmware-replication-work"></a>Comment fonctionne la réplication VMware sans agent ?

La méthode de réplication sans agent pour VMware utilise des instantanés VMware et le suivi de bloc modifié VMware. Un cycle de réplication initial est planifié quand l’utilisateur démarre la réplication. Dans le cycle de réplication initial, un instantané de la machine virtuelle est pris et cet instantané est utilisé pour répliquer les VMDK des machines virtuelles (disques). Une fois le cycle de réplication initial terminé, des cycles de réplication delta sont planifiés périodiquement. Dans le cycle de réplication delta, un instantané est pris et les blocs de données qui ont changé depuis le cycle de réplication précédent sont répliqués. Le suivi des blocs modifiés VMware est utilisé pour déterminer les blocs qui ont changé depuis le dernier cycle.
La fréquence des cycles de réplication périodiques est gérée automatiquement par le service en fonction du nombre des autres machines virtuelles/disques également en cours de réplication sur le même magasin de données, la condition idéale étant d’arriver à 1 cycle par heure et par machine virtuelle.

Quand vous effectuez une migration, un cycle de réplication à la demande est planifié pour la machine virtuelle afin de capturer les données restantes. Vous pouvez choisir d’arrêter la machine virtuelle dans le cadre de la migration pour garantir l’absence de perte de données et la cohérence des applications.

## <a name="why-is-the-resynchronization-option-not-exposed-in-agentless-stack"></a>Pourquoi l’option de resynchronisation n’est-elle pas exposée dans la pile sans agent ?

Dans la pile sans agent, dans chaque cycle delta, nous transférons la différence entre l’instantané actuel et l’instantané précédent que nous avions pris. Étant donné qu’il s’agit toujours d’une différence entre instantanés, il est possible de réduire les données (autrement dit, si un secteur particulier est écrit « n » fois entre les instantanés, nous devons uniquement transférer la dernière écriture, car nous nous intéressons uniquement à la dernière synchronisation). Cela diffère de la pile basée sur les agents dans laquelle nous effectuons le suivi de chaque écriture et les appliquons. Ainsi, chaque cycle delta est une resynchronisation. Aucune option de resynchronisation n’est donc exposée. 

Si les disques ne sont pas synchronisés en raison d’une défaillance, la situation est résolue dans le cycle suivant. 

## <a name="what-is-the-impact-of-churn-rate-if-i-use-agentless-replication"></a>Quel est l’impact du taux de variation si j’utilise la réplication sans agent ?

La pile étant tributaire de la réduction des données, plus que du taux de variation, c’est le modèle de variation qui importe dans cette pile. Un modèle dans lequel un fichier est écrit à plusieurs reprises n’a pas un impact important. Toutefois, un modèle dans lequel un secteur sur deux est écrit entraîne une variation élevée dans le cycle suivant. Comme nous réduisons au minimum la quantité de données que nous transférons, nous autorisons la réduction des données autant que possible avant de planifier le cycle suivant.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>À quelle fréquence un cycle de réplication est-il planifié ?

La formule pour planifier le prochain cycle de réplication est la suivante : (Durée de cycle précédente/2) ou 1 heure, la valeur la plus élevée étant choisie. Par exemple, si une machine virtuelle a pris quatre heures pour un cycle delta, nous planifions son prochain cycle dans 2 heures et pas dans l’heure suivante. Il en va différemment quand le cycle intervient juste après le runtime d’intégration ; dans ce cas, nous planifions le premier cycle delta immédiatement.

## <a name="what-is-the-impact-on-performance-of-vcenter-server-or-esxi-host-while-using-agentless-replication"></a>Quel est l’impact sur les performances de vCenter Server ou de l’hôte ESXi lors de l’utilisation de la réplication sans agent ?

Étant donné que la réplication sans agent utilise des instantanés, il y a une consommation d’IOPS sur le stockage et les clients ont besoin d’une marge d’IOPS sur le stockage. Nous vous déconseillons d’utiliser cette pile sur un environnement soumis à des contraintes de stockage ou d’IOPS.

## <a name="does-agentless-migration-stack-support-migration-of-uefi-vms-to-azure-gen-2-vms"></a>La pile de migration sans agent prend-elle en charge la migration de machines virtuelles UEFI vers des machines virtuelles Azure Gen 2 ?

Non, vous devez utiliser Azure Site Recovery pour migrer ces machines virtuelles vers des machines virtuelles Azure Gen 2. 

## <a name="can-i-pin-my-vms-to-azure-availability-zones-when-i-migrate"></a>Puis-je épingler mes machines virtuelles à Zones de disponibilité Azure lors de la migration ?

Non, la prise en charge de Zones de disponibilité Azure n’est pas assurée dans ce cas.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Quel protocole de transport est utilisé par Azure Migrate lors de la réplication ?

Azure Migrate utilise le protocole NBD (Network Block Device) avec chiffrement SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Quelle est la version minimale de vCenter Server requise pour la migration ?

Vous devez disposer d’au moins vCenter Server 5.5 et de l’hôte VMware vSphere ESXi version 5.5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Les clients peuvent-ils migrer leurs machines virtuelles vers des disques non managés ?

Non. Azure Migrate prend en charge la migration uniquement vers des disques managés (disques durs standard, disques SSD Premium).

## <a name="how-many-vms-can-replicate-simultaneously-using-agentless-vmware-stack"></a>Combien de machines virtuelles peuvent faire l’objet d’une réplication simultanément à l’aide de la pile VMware sans agent ?

Les clients peuvent migrer 100 machines virtuelles par serveur VMware vCenter simultanément. Cette opération peut être faite par lots de 10 machines virtuelles.




