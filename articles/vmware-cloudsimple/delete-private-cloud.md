---
title: Supprimer un cloud privé Azure VMware Solutions (AVS)
description: Explique comment supprimer un cloud privé AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77cbfb19c3861bac517142f7491e6b1a5fb4ca27
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024753"
---
# <a name="delete-an-avs-private-cloud"></a>Supprimer un cloud privé AVS

AVS offre la flexibilité nécessaire pour supprimer un cloud privé AVS. Un cloud privé AVS se compose d’un ou de plusieurs clusters vSphere. Chaque cluster peut contenir entre 3 et 16 nœuds. Lorsque vous supprimez un cloud privé AVS, tous ses clusters sont supprimés.

## <a name="before-you-begin"></a>Avant de commencer

Lorsque vous supprimez un cloud privé AVS, vous supprimez l’intégralité de celui-ci. Tous les composants du cloud privé AVS seront supprimés. Si vous souhaitez conserver certaines données, sauvegardez ces données dans un stockage local ou dans le stockage Azure.

Un cloud privé AVS comprend notamment les composants suivants :

* Nœuds AVS
* Machines virtuelles
* Réseaux VLAN/Sous-réseaux
* Toutes les données utilisateur stockées dans le cloud privé AVS
* Tous les éléments joints aux règles de pare-feu d’un réseau VLAN ou d’un sous-réseau

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-an-avs-private-cloud"></a>Supprimer un cloud privé AVS

1. [Accédez au portail AVS](access-cloudsimple-portal.md).

2. Ouvrez la page **Ressources**.

3. Cliquez sur le cloud privé AVS que vous souhaitez supprimer

4. Dans la page récapitulative, cliquez sur **Supprimer**.

    ![Supprimer un cloud privé AVS](media/delete-private-cloud.png)

5. Dans la page de confirmation, entrez le nom du cloud privé AVS, puis cliquez sur **Supprimer**. 

    ![Supprimer un cloud privé AVS - Confirmation](media/delete-private-cloud-confirm.png)

Le cloud privé AVS est marqué pour la suppression. Le processus de suppression démarre au bout de trois heures et aboutit à la suppression du cloud privé AVS.

> [!CAUTION]
> Les nœuds doivent être supprimés après la suppression du cloud privé AVS. Le contrôle des nœuds continuera tant que ceux-ci ne seront pas supprimés de votre abonnement.

## <a name="next-steps"></a>Étapes suivantes

* [Supprimer des nœuds](delete-nodes.md)
