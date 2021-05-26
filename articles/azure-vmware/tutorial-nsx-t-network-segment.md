---
title: Tutoriel - Ajouter un segment réseau NSX-T dans Azure VMware Solution
description: Découvrez comment créer un segment réseau NSX-T utilisable par des machines virtuelles dans vCenter.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 70a0602b037aaebd4b9776c3a8dafafcb173fa23
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110064452"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>Tutoriel : Ajouter un segment réseau dans Azure VMware Solution 

Les machines virtuelles créées dans vCenter sont placées sur les segments réseau créés dans NSX-T, et sont visibles dans vCenter.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Naviguer dans NSX-T Manager pour ajouter des segments réseau
> * Ajouter un nouveau segment réseau
> * Observer le nouveau segment réseau dans vCenter

## <a name="prerequisites"></a>Prérequis

Un cloud privé Azure VMware Solution avec accès aux interfaces de gestion vCenter et NSX-T Manager. Pour plus d’informations, consultez le tutoriel [Configurer la mise en réseau](tutorial-configure-networking.md).

## <a name="add-a-network-segment"></a>Ajouter un segment réseau

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un segment réseau NSX-T utilisable par des machines virtuelles dans vCenter. 

Vous pouvez à présent : 

- [Créer et gérer le protocole DHCP pour Azure VMware Solution](configure-dhcp-azure-vmware-solution.md)
- [Créer une bibliothèque de contenu pour déployer des machines virtuelles dans Azure VMware Solution](deploy-vm-content-library.md) 
- [Appairer des environnements locaux avec un cloud privé](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
