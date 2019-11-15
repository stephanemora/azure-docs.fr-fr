---
title: Réduire une solution Azure VMware par cloud privé CloudSimple
description: Décrit comment réduire un cloud privé CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1ae2f87a3719853f4a91cb8ba801be6d578597d3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825682"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Réduire un cloud privé CloudSimple

CloudSimple fournit la flexibilité nécessaire pour réduire de manière dynamique un cloud privé.  Un cloud privé se compose d’un ou de plusieurs clusters vSphere. Chaque cluster peut contenir entre 3 et 16 nœuds. Lors de la réduction d’un cloud privé, vous supprimez un nœud du cluster existant ou supprimez un cluster entier. 

## <a name="before-you-begin"></a>Avant de commencer

Les conditions suivantes doivent être remplies pour réduire un cloud privé.  Le cluster de gestion (premier cluster) créé lors de la création d’un cloud privé ne peut pas être supprimé.

* Un cluster vSphere doit contenir trois nœuds.  Un cluster ne contenant que trois nœuds ne peut pas être réduit.
* Le stockage total consommé ne doit pas dépasser la capacité totale après réduction du cluster. 

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Réduire un cloud privé

1. [Accédez au portail CloudSimple](access-cloudsimple-portal.md).

2. Ouvrez la page **Ressources**.

3. Cliquez sur le cloud privé que vous souhaitez réduire.

4. Sur la page de résumé, cliquez sur **Réduire**.

    ![Réduire un cloud privé](media/shrink-private-cloud.png)

5. Sélectionnez le cluster que vous souhaitez réduire ou supprimer. 

    ![Réduire un cloud privé - sélectionner un cluster](media/shrink-private-cloud-select-cluster.png)

6. Sélectionnez **Remove one node** (Supprimer un nœud) ou **Delete the whole cluster** (Supprimer tout le cluster). 

7. Vérifier la capacité du cluster

8. Cliquez sur **Soumettre** pour réduire le cloud privé.

La réduction du cloud privé démarre.  Vous pouvez suivre la progression dans des tâches.  Le processus de réduction peut prendre quelques heures en fonction des données, qui doivent être resynchronisées sur le vSAN.

> [!NOTE]
> Si vous réduisez un cloud privé en supprimant le dernier ou le seul cluster dans le centre de données, le centre de données n’est pas supprimé.  


## <a name="next-steps"></a>Étapes suivantes

* [Utiliser des machines virtuelles VMware sur Azure](quickstart-create-vmware-virtual-machine.md)
* Découvrir plus en détail les [clouds privés](cloudsimple-private-cloud.md)
