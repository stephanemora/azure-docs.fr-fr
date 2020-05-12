---
title: Questions courantes sur la migration de serveur Azure Migrate
description: Obtenez des réponses aux questions courantes sur l’utilisation d’Azure Migrate Server Migration pour migrer des machines.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 0cfe23b4e544040fc3ab69796988ca34b1bdcdbf
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744326"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migration de serveur Azure Migrate : Questions courantes

Cet article répond à des questions courantes sur la migration de serveur Azure Migrate d’Azure Migrate. Si vous avez d’autres questions, consultez les ressources suivantes :

- [Questions générales](resources-faq.md) sur Azure Migrate.
- Questions sur l’[appliance Azure Migrate](common-questions-appliance.md)
- Questions sur la [découverte, l’évaluation et la visualisation des dépendances](common-questions-discovery-assessment.md)
- Obtenez des réponses à vos questions sur le [forum Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Quelles sont les zones géographiques prises en charge pour la migration avec Azure Migrate ?

Passez en revue les zones géographiques prises en charge pour les clouds [publics](migrate-support-matrix.md#supported-geographies-public-cloud) et du [secteur public](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="how-does-agentless-vmware-replication-work"></a>Comment fonctionne la réplication VMware sans agent ?

La méthode de réplication sans agent pour VMware utilise des instantanés VMware et la fonctionnalité Changed Block Tracking (CBT) VMware.

Le processus est le suivant :

1. Au démarrage de la réplication, un cycle de réplication initial est planifié. Lors du cycle initial, un instantané de la machine virtuelle est pris. L’instantané est utilisé pour répliquer les VMDK des machines virtuelles (disques). 
2. Une fois le cycle de réplication initial terminé, des cycles de réplication delta sont planifiés périodiquement.
    - Lors de la réplication delta, un instantané est pris et les blocs de données qui ont changé depuis le cycle de réplication précédent sont répliqués.
    - La fonctionnalité CBT VMware est utilisée pour déterminer les blocs qui ont changé depuis le dernier cycle.
    - La fréquence des cycles de réplication périodiques est gérée automatiquement par Azure Migrate, et elle dépend du nombre des autres machines virtuelles et disques également en cours de réplication à partir du même magasin de données. Idéalement, la réplication aboutit à un cycle par heure pour chaque machine virtuelle.

Quand vous effectuez une migration, un cycle de réplication à la demande est planifié pour la machine afin de capturer les données restantes. Pour garantir l’absence de perte de données et la cohérence des applications, vous pouvez choisir d’arrêter la machine durant la migration.

## <a name="why-isnt-resynchronization-exposed"></a>Pourquoi la resynchronisation n’est-elle pas exposée ?

Lors d’une migration sans agent, dans chaque cycle delta, la différence entre l’instantané actuel et l’instantané précédemment pris est écrite. Il s’agit toujours de la différence entre instantanés, avec repli des données. Si un secteur spécifique est écrit *N* fois entre les instantanés, seule la dernière écriture doit être transférée, car nous nous intéressons uniquement à la dernière synchronisation. Le processus est différent de la réplication basée sur agent, durant laquelle nous suivons et appliquons chaque écriture. Dans ce processus, chaque cycle delta est une resynchronisation. Ainsi, aucune option de resynchronisation n’est exposée. Si les disques ne sont pas synchronisés en raison d’une défaillance, la situation est résolue au cours du cycle suivant. 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Dans quelle mesure le taux de variation affecte-t-il la réplication sans agent ?

La réplication sans agent opérant un repli des données, le *modèle de variation* est plus important que le *taux de variation*. Quand un fichier est écrit à plusieurs reprises, le taux n’a pas un impact important. Toutefois, un modèle dans lequel un secteur sur deux est écrit entraîne une variation élevée lors du cycle suivant. Comme nous réduisons au minimum la quantité de données que nous transférons, nous autorisons la réduction des données autant que possible avant de planifier le cycle suivant.  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>À quelle fréquence un cycle de réplication est-il planifié ?

La formule pour planifier le prochain cycle de réplication est (durée du cycle précédente / 2) ou une heure (la valeur la plus élevée s’applique).

Par exemple, si une machine virtuelle prend quatre heures pour un cycle delta, le cycle suivant est planifié en deux heures, et non dans l’heure suivante. Le processus diffère immédiatement après la réplication initiale, quand le premier cycle delta est planifié immédiatement.

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Dans quelle mesure la réplication sans agent affecte les serveurs VMware ?

La réplication sans agent a un impact sur les performances du serveur VMware vCenter et des hôtes VMware ESXi. La réplication sans agent utilisant des instantanés, elle consomme des IOPS en matière de stockage. Une bande passante de stockage IOPS est donc requise. Nous vous déconseillons d’utiliser la réplication sans agent si vous avez des contraintes de stockage ou d’IOPS dans votre environnement.

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>Puis-je effectuer une migration sans agent de machines virtuelles UEFI vers Azure Gen 2 ?

Non. Utilisez Azure Site Recovery pour migrer ces machines virtuelles vers des machines virtuelles Azure Gen 2. 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>Puis-je épingler des machines virtuelles à Zones de disponibilité Azure lors de la migration ?

Non. Les Zones de disponibilité Azure ne sont pas prises en charge pour la migration Azure Migrate.

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Quel protocole de transport Azure Migrate utilise-t-il pendant la réplication ?

Azure Migrate utilise le protocole NBD (Network Block Device) avec chiffrement TLS.

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>Quelle est la version minimale de vCenter Server requise pour la migration ?

Vous devez disposer au minimum de vCenter Server 5.5 et de l’hôte VMware vSphere ESXi version 5.5.

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>Les clients peuvent-ils migrer leurs machines virtuelles vers des disques non managés ?

Non. Azure Migrate prend en charge la migration uniquement vers des disques managés (disques HDD Standard, disques SSD Premium).

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>Combien de machines virtuelles puis-je répliquer en même temps avec la migration sans agent ?

Actuellement, vous pouvez migrer simultanément 100 machines virtuelles par instance de vCenter Server. Procédez à une migration par lots de 10 machines virtuelles.

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Comment faire pour limiter la réplication lors de l’utilisation de l’appliance Azure Migrate pour une réplication VMware sans agent ?  

Vous pouvez limiter l’utilisation de NetQosPolicy. Par exemple :

L’AppNamePrefix à utiliser dans NetQosPolicy est « GatewayWindowsService.exe ». Vous pouvez créer une stratégie sur l’appliance Azure Migrate pour limiter le trafic de réplication de l’appliance en créant une stratégie telle que celle-ci :
 
New-NetQosPolicy -Name "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1MB

## <a name="can-i-migrate-vms-that-are-already-being-replicated-to-azure"></a>Puis-je migrer des machines virtuelles qui sont déjà en cours de réplication vers Azure ? 

Si des machines virtuelles sont déjà en cours de réplication vers Azure par d’autres moyens, vous ne pouvez pas migrer ces dernières en tant que machines virtuelles à l’aide de la migration de serveur Azure Migrate. En guise de solution de contournement, vous pouvez traiter les machines virtuelles comme des serveurs physiques et les migrer conformément à la [migration des serveurs physiques prise en charge](migrate-support-matrix-physical-migration.md).

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>Quand dois-je migrer des machines en tant que serveurs physiques ?

La migration de machines en les traitant comme des serveurs physiques est utile dans plusieurs scénarios :

- Quand vous migrez des serveurs physiques locaux.
- Si vous migrez des machines virtuelles virtualisées par des plateformes telles que Xen ou KVM.
- Pour migrer des machines virtuelles Hyper-V ou VMware. Une telle migration s’avère utile si, pour une raison quelconque, vous ne pouvez pas utiliser le processus de migration standard pour migrer les machines [Hyper-V](tutorial-migrate-hyper-v.md) ou [VMware](server-migrate-overview.md). Par exemple, si vous n’exécutez pas VMware vCenter et que vous utilisez uniquement des hôtes ESXi.
- Pour migrer des machines virtuelles en cours d’exécution dans des clouds privés vers Azure.
- Si vous souhaitez migrer des machines virtuelles exécutées dans des clouds publics tels qu’Amazon Web Services (AWS) ou Google Cloud Platform (GCP) vers Azure.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliance"></a>J’ai déployé deux appliances (ou plus) pour découvrir des machines virtuelles dans vCenter Server. Cependant, lorsque j’essaie de migrer les machines virtuelles, je ne vois que celles qui correspondent à l’une des appliances.

Bien que cela puisse représenter un bon cas d’usage, nous ne le prenons pas en charge actuellement. Le déploiement de deux appliances (ou plus) pour découvrir le même ensemble de machines virtuelles provoque un problème de service : la propriété des machines virtuelles ne cesse de basculer entre les appliances. C’est la raison pour laquelle les machines virtuelles apparaissent et disparaissent. Dans ce cas, pour résoudre le problème, vous devez supprimer une appliance et procéder à une actualisation matérielle.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Ai-je besoin de VMware vCenter pour migrer des machines virtuelles VMware ?
Pour [migrer des machines virtuelles VMware](server-migrate-overview.md) à l’aide d’une migration sans agent ou basée sur un agent VMware, les hôtes ESXi sur lesquels résident les machines virtuelles doivent être managés par vCenter Server. Si vous n’avez pas vCenter Server, vous pouvez migrer des machines virtuelles VMware en les migrant en tant que serveurs physiques. [Plus d’informations](migrate-support-matrix-physical-migration.md)
 
## <a name="next-steps"></a>Étapes suivantes

Consultez la [vue d’ensemble d’Azure Migrate](migrate-services-overview.md).
