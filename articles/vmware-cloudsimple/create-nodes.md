---
title: Provisionner des nœuds pour Azure VMware Solutions (AVS) - Azure
description: Découvrez comment ajouter des nœuds à votre déploiement VMWare avec AVS
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94ca681153f1e3ccd9ff628b41a9d0e547be5802
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024804"
---
# <a name="provision-nodes-for-azure-vmware-solutions-avs"></a>Provisionner des nœuds pour Azure VMware Solutions (AVS)

Approvisionnez des nœuds sur le portail Azure. Vous pouvez ensuite configurer la capacité avec un paiement à l’utilisation pour votre environnement de cloud privé AVS.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-avs-private-cloud"></a>Ajouter un nœud à votre cloud privé AVS

1. Sélectionnez **Tous les services**.
2. Recherchez **Nœuds AVS**.

   ![Rechercher dans les nœuds AVS](media/create-cloudsimple-node-search.png)

3. Sélectionnez **Nœuds AVS**.
4. Cliquez sur **Ajouter** pour créer les nœuds.

    ![Ajouter des nœuds AVS](media/create-cloudsimple-node-add.png)

5. Sélectionnez l’abonnement dans lequel vous souhaitez provisionner des nœuds AVS.
6. Sélectionnez le groupe de ressources pour vos nœuds. Pour ajouter un nouveau groupe de ressources, cliquez sur **Créer**.
7. Entrez le préfixe pour identifier les nœuds.
8. Sélectionnez l’emplacement des ressources de nœud.
9. Sélectionnez l’emplacement dédié où héberger les ressources de nœud.
10. Sélectionnez le [type de nœud](cloudsimple-node.md).
11. Sélectionnez le nombre de nœuds à approvisionner.
12. Sélectionnez **Vérifier + créer**.
13. Passez en revue les paramètres. Pour modifier les paramètres, cliquez sur **Précédent**.
14. Sélectionnez **Create** (Créer).

## <a name="next-steps"></a>Étapes suivantes

* [Créer un cloud privé AVS](create-private-cloud.md)
