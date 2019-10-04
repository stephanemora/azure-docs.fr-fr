---
title: Supprimer des nœuds dans une solution VMware CloudSimple - Azure
description: Découvrez comment supprimer des nœuds de votre déploiement VMWare CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972830"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Supprimer des nœuds dans une solution Azure VMware de CloudSimple

Les nœuds CloudSimple sont mesurés dès leur création.  Pour que ces mesures cessent, les nœuds doivent être supprimés.  Vous devez supprimer les nœuds qui ne sont pas utilisés dans le portail Azure.

## <a name="before-you-begin"></a>Avant de commencer

Un nœud ne peut être supprimé que dans les conditions suivantes :

* Un cloud privé créé avec les nœuds a été supprimé.  Pour supprimer un cloud privé, consultez [Supprimer une solution Azure VMware par cloud privé CloudSimple](delete-private-cloud.md).
* Le nœud a été supprimé du cloud privé dans le cadre de la réduction de celui-ci.  Pour réduire la taille d’un cloud privé, consultez [Réduire la taille du cloud privé d’une solution Azure VMware CloudSimple](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-cloudsimple-node"></a>Supprimer un nœud CloudSimple

1. Sélectionnez **Tous les services**.

2. Recherchez **Nœuds CloudSimple**.

   ![Rechercher des nœuds CloudSimple](media/create-cloudsimple-node-search.png)

3. Sélectionnez **Nœuds CloudSimple**.

4. Sélectionnez les nœuds qui n’appartiennent pas à un cloud privé et supprimez-les.  La colonne **Nom du cloud privé** affiche le nom du cloud privé auquel appartient un nœud.  Si un nœud n’est pas utilisé par un cloud privé, la valeur sera vide. 

    ![Sélectionner des nœuds CloudSimple](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Seuls les nœuds qui ne font pas partie du cloud privé peuvent être supprimés.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez-en plus sur le [Cloud privé](cloudsimple-private-cloud.md)
