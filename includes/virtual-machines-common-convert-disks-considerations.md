---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9f7e2760ef8bf06a2e680dce90c323672ca9d491
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416062"
---
* La conversion nécessite un redémarrage de la machine virtuelle. Par conséquent, planifiez la migration de vos machines virtuelles au cours d’une fenêtre de maintenance préexistante. 

* Vous ne pouvez pas annuler la conversion. 

* Gardez à l’esprit que tous les utilisateurs ayant pour rôle [Contributeur de machines virtuelles](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) ne peuvent pas modifier la taille des machines virtuelles (comme ils le pouvaient avant la conversion). Cela s’explique par le fait que les machines virtuelles avec disques managés obligent l’utilisateur à détenir une autorisation Microsoft.Compute/disks/write sur les disques du système d’exploitation.

* Veillez à la tester. Migrez une machine virtuelle de test avant d’effectuer la migration en production.

* Lors de la conversion, vous libérez la machine virtuelle. Celle-ci reçoit une nouvelle adresse IP lorsqu’elle est démarrée après la conversion. Si nécessaire, vous pouvez [affecter une adresse IP statique](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) à la machine virtuelle.

* Vérifiez la version minimale nécessaire pour que l’agent de machine virtuelle Azure prenne en charge le processus de conversion. Pour savoir comment vérifier et mettre à jour la version de l’agent, consultez la page [Prise en charge de version minimale pour les agents de machine virtuelle dans Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).