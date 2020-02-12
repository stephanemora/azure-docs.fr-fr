---
title: Supprimer des nœuds pour Azure VMware Solutions (AVS) - Azure
description: Découvrez comment supprimer des nœuds de votre déploiement VMWare avec AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30d128d6bd2f2e1e2705a7b742f02d11fd947a03
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024736"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-avs"></a>Supprimer des nœuds dans Azure VMware Solution by AVS

Les nœuds AVS sont mesurés dès leur création. Pour que ces mesures cessent, les nœuds doivent être supprimés. Vous devez supprimer les nœuds qui ne sont pas utilisés dans le portail Azure.

## <a name="before-you-begin"></a>Avant de commencer

Un nœud ne peut être supprimé que dans les conditions suivantes :

* Un cloud privé AVS créé avec les nœuds est supprimé. Pour supprimer un cloud privé AVS, consultez [Supprimer un cloud privé Azure VMware Solution by AVS](delete-private-cloud.md).
* Le nœud a été supprimé du cloud privé AVS dans le cadre de la réduction de celui-ci. Pour réduire la taille d’un cloud privé AVS, consultez [Réduire le cloud privé Azure VMware Solution by AVS](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-avs-node"></a>Supprimer le nœud AVS

1. Sélectionnez **Tous les services**.

2. Recherchez **Nœuds AVS**.

   ![Rechercher dans les nœuds AVS](media/create-cloudsimple-node-search.png)

3. Sélectionnez **Nœuds AVS**.

4. Sélectionnez les nœuds qui n’appartiennent pas à un cloud privé AVS à supprimer. La colonne **NOM DE CLOUD PRIVÉ AVS** affiche le nom du cloud privé AVS auquel appartient un nœud. Si un nœud n’est pas utilisé par un cloud privé AVS, la valeur sera vide. 

    ![Sélectionner les nœuds AVS](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Seuls les nœuds qui ne font pas partie du cloud privé AVS peuvent être supprimés.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez le [cloud privé AVS](cloudsimple-private-cloud.md)
