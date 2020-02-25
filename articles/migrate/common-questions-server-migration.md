---
title: Questions courantes sur la migration de serveur Azure Migrate
description: Obtenez des réponses aux questions courantes sur la migration de machine avec Migration de serveur Azure Migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0c967027457b925b45ea19d994cfadfdbd0b8ab3
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425831"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migration de serveur Azure Migrate : Questions courantes

Cet article répond à des questions courantes sur l’outil Azure Migrate : Migration de serveur. Si vous avez d’autres questions après avoir lu cet article, consultez les articles suivants :

- [Questions générales](resources-faq.md) sur Azure Migrate.
- [Questions](common-questions-appliance.md) sur l’appliance Azure Migrate.
- [Questions](common-questions-discovery-assessment.md) sur la découverte, l’évaluation et la visualisation des dépendances.
- Postez des questions sur le [forum Azure Migrate](https://aka.ms/AzureMigrateForum).


## <a name="how-does-agentless-vmware-replication-work"></a>Comment fonctionne la réplication VMware sans agent ?

La méthode de réplication sans agent pour VMware utilise des instantanés VMware et la fonctionnalité Changed Block Tracking (CBT) VMware.

1. Au démarrage de la réplication, un cycle de réplication initial est planifié. Lors du cycle initial, un instantané de la machine virtuelle est pris. Cet instantané est utilisé pour répliquer les VMDK des machines virtuelles (disques). 
2. Une fois le cycle de réplication initial terminé, des cycles de réplication delta sont planifiés périodiquement.
    - Lors de la réplication delta, un instantané est pris et les blocs de données qui ont changé depuis le cycle de réplication précédent sont répliqués.
    - La fonctionnalité CBT VMware est utilisée pour déterminer les blocs qui ont changé depuis le dernier cycle.
    - La fréquence des cycles de réplication périodiques est gérée automatiquement par Azure Migrate en fonction du nombre des autres machines virtuelles/disques également en cours de réplication depuis le même magasin de données. Idéalement, la réplication aboutit à un cycle par heure pour chaque machine virtuelle.

Lorsque vous effectuez une migration, un cycle de réplication à la demande est planifié pour la machine afin de capturer les données restantes. Vous pouvez choisir d’arrêter la machine durant la migration pour garantir l’absence de perte de données et la cohérence des applications.

## <a name="why-isnt-resynchronization-exposed"></a>Pourquoi la resynchronisation n’est-elle pas exposée ?

Lors d'une migration sans agent, dans chaque cycle delta, la différence entre l’instantané actuel et l’instantané précédemment pris est écrite. Il s'agit toujours de la différence entre instantanés, avec repli des données. Ainsi, si un secteur particulier est écrit n fois entre les instantanés, seule la dernière écriture doit être transférée, car nous nous intéressons uniquement à la dernière synchronisation. Cela diffère de la réplication basée sur agent, où nous suivons et appliquons chaque écriture. Ainsi, chaque cycle delta est une resynchronisation. Aucune option de resynchronisation n’est donc exposée. Si les disques ne sont pas synchronisés en raison d’une défaillance, la situation est résolue au cours du cycle suivant. 

## <a name="how-does-churn-rate-impact-agentless-replication"></a>Dans quelle mesure le taux de variation impacte-t-il la réplication sans agent ?

La réplication sans agent opérant un repli des données, le modèle de variation est plus important que le taux de variation. Lorsqu'un fichier est écrit à plusieurs reprises, le taux n’a pas un impact important. Toutefois, un modèle dans lequel un secteur sur deux est écrit entraîne une variation élevée lors du cycle suivant. Comme nous réduisons au minimum la quantité de données que nous transférons, nous autorisons la réduction des données autant que possible avant de planifier le cycle suivant.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>À quelle fréquence un cycle de réplication est-il planifié ?

Formule pour planifier le prochain cycle de réplication suivant : (Durée de cycle précédente/2) ou 1 heure, la valeur la plus élevée étant choisie.

Par exemple, si une machine virtuelle prend quatre heures pour un cycle delta, le cycle suivant est planifié en deux heures, et non dans l’heure suivante. Cela diffère immédiatement après la réplication initiale, où le premier cycle delta est planifié immédiatement.

## <a name="how-does-agentless-replication-impact-vmware-servers"></a>Dans quelle mesure la réplication sans agent impacte-t-elle les serveurs VMware ?

En termes de performances, elle a un impact sur les hôtes vCenter Server/ESXi. La réplication sans agent utilisant des instantanés, elle consomme des IOPS en matière de stockage, et une bande passante de stockage IOPS est requise. Nous vous déconseillons d’utiliser la réplication sans agent en présence de contraintes de stockage/IOPS dans votre environnement.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Puis-je effectuer une migration sans agent de machines virtuelles UEFI vers Azure Gen 2 ?

Non. Utilisez Azure Site Recovery pour migrer ces machines virtuelles vers des machines virtuelles Azure Gen 2. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Puis-je épingler des machines virtuelles à Zones de disponibilité Azure lors de la migration ?

Non, les Zones de disponibilité Azure ne sont pas prises en charge.

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>Quel protocole de transport est utilisé par Azure Migrate lors de la réplication ?

Azure Migrate utilise le protocole NBD (Network Block Device) avec chiffrement SSL.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Quelle est la version minimale de vCenter Server requise pour la migration ?

Vous devez disposer d’au moins vCenter Server 5.5 et de l’hôte VMware vSphere ESXi version 5.5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Les clients peuvent-ils migrer leurs machines virtuelles vers des disques non managés ?

Non. Azure Migrate prend en charge la migration uniquement vers des disques managés (disques durs standard, disques SSD Premium).

## <a name="how-many-vms-can-i-replicate-together-with-agentless-migration"></a>Combien de machines virtuelles puis-je répliquer simultanément avec la migration sans agent ?

Actuellement, vous pouvez migrer simultanément 100 machines virtuelles par vCenter Server. Procédez à une migration par lots de 10 machines virtuelles.
 



