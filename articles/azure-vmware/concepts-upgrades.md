---
title: Concepts – Mises à jour et mises à niveau des clouds privés
description: Découvrez les processus et fonctionnalités clés de mise à niveau dans Azure VMware Solution.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 9810de40944f70a4efb7ec81d17868ffdf256c7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586146"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Mises à jour et mises à niveau des clouds privés Azure VMware Solution

Un avantage des clouds privés Azure VMware Solution est que la plateforme est maintenue pour vous. La maintenance comprend des mises à jour automatisées vers un bundle de logiciels validée par VMware, ce qui aide à garantir que vous utilisez la dernière version des logiciels de cloud privé d’Azure VMware Solution.

Plus précisément, un cloud privé Azure VMware Solution inclut :

- Des nœuds de serveurs nus dédiés provisionnés à l’aide de l’hyperviseur VMware ESXi 
- Un serveur vCenter pour la gestion d’ESXi et de vSAN 
- Une mise en réseau SDN (Software Defined Networking) VMware NSX-T pour les machines virtuelles de charge de travail vSphere  
- Un magasin de données VMware vSAN pour les machines virtuelles de charge de travail vSphere  
- VMware HCX pour la mobilité des charges de travail  

Un cloud privé Azure VMware Solution comprend aussi des ressources dans le réseau sous-jacent Azure, qui sont nécessaires pour la connectivité et pour exploiter le cloud privé. Azure VMware Solution surveille en permanence l’intégrité des composants sous-jacents et VMware. Quand Azure VMware Solution détecte une défaillance, elle entreprend des actions pour réparer les composants défaillants. 

## <a name="what-components-get-updated"></a>Quels composants sont-ils mis à jour ?   

Azure VMware Solution met à jour les composants VMware suivants : 

- vCenter Server et ESXi s’exécutant sur les nœuds de serveurs nus 
- vSAN 
- NSX-T 

Azure VMware Solution met également à jour les logiciels sous-jacents, tels que les pilotes, les logiciels sur les commutateurs réseau et les microprogrammes sur les nœuds nus. 

## <a name="types-of-updates"></a>Types de mise à jour

Azure VMware Solution applique les types suivants de mises à jour aux composants VMware :

- Correctifs : correctifs de sécurité et correctifs de bogues publiés par VMware. 
- Mises à jour : mises à jour de la version mineure d’un ou de plusieurs composants VMware. 
- Mises à niveau : mises à jour de la version majeure d’un ou de plusieurs composants VMware.

Vous serez averti avant et après l’application des correctifs sur vos clouds privés. Nous travaillerons également avec vous pour planifier une fenêtre de maintenance avant d’appliquer des mises à jour ou des mises à niveau à votre cloud privé. 

## <a name="vmware-appliance-backup"></a>Sauvegarde d’une appliance VMware 

Azure VMware Solution effectue également une sauvegarde de la configuration des composants VMware suivants :

- Serveur vCenter 
- Gestionnaire NSX-T 

En cas d’échec, Azure VMware Solution peut restaurer ces composants à partir de la sauvegarde de la configuration. 

## <a name="vmware-software-versions"></a>Versions des logiciels VMware
[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous vous êtes penchés sur les processus et fonctionnalités de la mise à niveau de clés dans la solution Azure VMware (AVS), concentrez-vous sur :

- [Comment créer un cloud privé](tutorial-create-private-cloud.md).
- [Comment activer la ressource Azure VMware Solution](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
