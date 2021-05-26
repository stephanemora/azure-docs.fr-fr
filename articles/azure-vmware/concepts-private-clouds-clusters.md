---
title: Concepts – Clusters et clouds privés
description: Découvrez les principales fonctionnalités des centres de données à définition logicielle Azure VMware Solution et des clusters vSphere.
ms.topic: conceptual
ms.date: 05/13/2021
ms.openlocfilehash: 7b32752e368f9aac003a45f96f7c0477e9221481
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110089611"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Concepts de cloud privé et de cluster Azure VMware Solution

Azure VMware Solution fournit des clouds privés basés sur VMware dans Azure. Les déploiements de logiciels et composants matériels de cloud privé sont entièrement intégrés et automatisés dans Azure. Vous déployez et gérez le cloud privé par le biais du portail Azure, de l’interface CLI ou de PowerShell.  

Un cloud privé comprend des clusters avec :

- Des nœuds de serveurs nus dédiés provisionnés à l’aide de l’hyperviseur VMware ESXi 
- Un serveur VMware vCenter pour la gestion d’ESXi et de vSAN 
- Une mise en réseau SDN (Software Defined Networking) VMware NSX-T pour les machines virtuelles de charge de travail vSphere  
- Un magasin de données VMware vSAN pour les machines virtuelles de charge de travail vSphere  
- VMware HCX pour la mobilité des charges de travail  
- Ressources dans la sous-couche Azure (nécessaires pour la connectivité et l’opération du cloud privé)

Comme avec d’autres ressources, les clouds privés sont installés et gérés à partir d’un abonnement Azure. Le nombre de clouds privés au sein d’un abonnement est évolutif. Au départ, il existe une limite d’un cloud privé par abonnement.  Il existe une relation logique entre les abonnements Azure, les clouds privés Azure VMware Solution, les clusters vSAN et les hôtes. 

Le diagramme montre un abonnement Azure avec deux clouds privés représentant un environnement de développement et de production. Chacun de ces clouds privés comprend deux clusters. 

:::image type="content" source="media/hosts-clusters-private-clouds-final.png" alt-text="Image montrant deux clouds privés dans un abonnement client.":::

## <a name="hosts"></a>Hôtes

[!INCLUDE [disk-capabilities-of-the-host](includes/disk-capabilities-of-the-host.md)]

## <a name="clusters"></a>Clusters

[!INCLUDE [hosts-minimum-initial-deployment-statement](includes/hosts-minimum-initial-deployment-statement.md)]

[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="vmware-software-versions"></a>Versions des logiciels VMware

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]

## <a name="host-maintenance-and-lifecycle-management"></a>Maintenance de l’hôte et gestion du cycle de vie

Un avantage des clouds privés Azure VMware Solution est que la plateforme est maintenue pour vous.  Microsoft est responsable de la gestion du cycle de vie des logiciels VMware (ESXi vCenter et vSAN). Microsoft est également responsable de la gestion du cycle de vie des appliances NSX-T et du démarrage de la configuration du réseau, par exemple la création de la passerelle de niveau 0 et l’activation du routage Nord-Sud. Vous êtes responsable de la configuration du SDN NSX-T : segments réseau, règles de pare-feu distribuées, passerelles de niveau 1 et équilibreurs de charge. 

[!INCLUDE [vmware-software-update-frequency](includes/vmware-software-update-frequency.md)]

## <a name="host-monitoring-and-remediation"></a>Surveillance et correction de l’hôte

Azure VMware Solution surveille en permanence l’intégrité des composants sous-jacents et VMware. Quand Azure VMware Solution détecte une défaillance, elle entreprend des actions pour réparer les composants défaillants. Quand Azure VMware Solution détecte une dégradation ou une défaillance sur un nœud Azure VMware Solution, il déclenche un processus de correction de l’hôte. 

La correction de l’hôte implique le remplacement du nœud défaillant par un nouveau nœud sain dans le cluster. Ensuite, lorsque cela est possible, l’hôte défectueux est placé en mode de maintenance VMware vSphere. VMware vMotion déplace les machines virtuelles de l’hôte défaillant vers d’autres serveurs disponibles dans le cluster, permettant ainsi une migration dynamique des charges de travail sans temps d’arrêt. Si l’hôte défaillant ne peut pas être placé en mode de maintenance, l’hôte est supprimé du cluster.

Azure VMware Solution supervise les conditions suivantes sur l’hôte :  

- État des processeurs 
- État de la mémoire 
- État des connexions et de l’alimentation 
- État du ventilateur matériel 
- Perte de connectivité réseau 
- État de la carte système matérielle 
- Des erreurs se sont produites sur le ou les disques d’un hôte vSAN 
- Tension du matériel 
- État de la température du matériel 
- État de l’alimentation du matériel 
- État du stockage 
- Échec de connexion 

> [!NOTE]
> Les administrateurs de locataires Azure VMware Solution ne doivent ni modifier ni supprimer les alarmes VMware vCenter définies ci-dessus, car celles-ci sont gérées par le plan de contrôle Azure VMware Solution sur vCenter. Ces alarmes sont utilisées par l’analyse de la solution VMware Azure pour déclencher le processus de correction de l’hôte Azure VMware Solution.

## <a name="backup-and-restoration"></a>Sauvegarde et restauration

Les configurations de vCenter et de NSX-T sont effectuées sur une planification de sauvegarde horaire.  Les sauvegardes sont conservées pendant trois jours. Si vous devez effectuer une restauration à partir d’une sauvegarde, ouvrez une [demande de support](https://rc.portal.azure.com/#create/Microsoft.Support) dans le portail Azure pour demander une restauration.

Azure VMware Solution surveille en permanence l’intégrité des composants sous-jacents et VMware. Quand Azure VMware Solution détecte une défaillance, elle entreprend des actions pour réparer les composants défaillants.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez abordé les concepts de cloud privé d’Azure VMware Solution, vous pouvez en apprendre davantage sur les sujets suivants : 

- [Concepts de mise en réseau et d’interconnexion d’Azure VMware Solution](concepts-networking.md)
- [Concepts de stockage pour Azure VMware Solution](concepts-storage.md)
- [Comment activer la ressource Azure VMware Solution](deploy-azure-vmware-solution.md#step-1-register-the-microsoftavs-resource-provider)

<!-- LINKS - internal -->
[concepts-networking]: ./concepts-networking.md

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

