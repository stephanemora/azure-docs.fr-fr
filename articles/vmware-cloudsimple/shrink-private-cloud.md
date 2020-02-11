---
title: Réduire un cloud privé Azure VMware Solutions (AVS)
description: Explique comment réduire un cloud privé AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0ea764081cd0b4d5c6d44cd7364d1e9a89a3cec3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014264"
---
# <a name="shrink-an-avs-private-cloud"></a>Réduire un cloud privé AVS

AVS offre la flexibilité nécessaire pour réduire de manière dynamique un cloud privé AVS. Un cloud privé AVS se compose d'un ou de plusieurs clusters vSphere. Chaque cluster peut contenir entre 3 et 16 nœuds. Lors de la réduction d'un cloud privé AVS, vous supprimez un nœud du cluster existant ou supprimez un cluster tout entier. 

## <a name="before-you-begin"></a>Avant de commencer

Les conditions suivantes doivent être remplies avant de réduire un cloud privé AVS. Le cluster de gestion (premier cluster) est créé lors de la création du cloud privé AVS. Il ne peut pas être supprimé.

* Un cluster vSphere doit contenir trois nœuds. Un cluster ne contenant que trois nœuds ne peut pas être réduit.
* L'espace de stockage total utilisé ne doit pas dépasser la capacité totale après réduction du cluster.
* Vérifiez si des règles DRS (Distributed Resource Scheduler) empêchent vMotion d’une machine virtuelle. Si des règles sont présentes, désactivez ou supprimez-les. Les règles DRS incluent la machine virtuelle pour héberger des règles d’affinité.


## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="shrinking-an-avs-private-cloud"></a>Réduire un cloud privé AVS

1. [Accédez au portail AVS](access-cloudsimple-portal.md).

2. Ouvrez la page **Ressources**.

3. Cliquez sur le cloud privé AVS que vous souhaitez réduire

4. Sur la page de résumé, cliquez sur **Réduire**.

    ![Réduire le cloud privé AVS](media/shrink-private-cloud.png)

5. Sélectionnez le cluster que vous souhaitez réduire ou supprimer. 

    ![Réduire le cloud privé AVS - sélectionner un cluster](media/shrink-private-cloud-select-cluster.png)

6. Sélectionnez **Remove one node** (Supprimer un nœud) ou **Delete the whole cluster** (Supprimer tout le cluster). 

7. Vérifier la capacité du cluster

8. Cliquez sur **Soumettre** pour réduire le cloud privé AVS.

La réduction du cloud privé AVS démarre. Vous pouvez suivre la progression dans des tâches. Le processus de réduction peut prendre quelques heures en fonction des données, qui doivent être resynchronisées sur le vSAN.

> [!NOTE]
> 1. Si vous réduisez un cloud privé en supprimant le dernier ou le seul cluster dans le centre de données, le centre de données n’est pas supprimé.
> 2. Si une violation de règle DRS se produit, le nœud n’est pas supprimé du cluster et la description de la tâche indique que la suppression d’un nœud viole les règles DRS sur le cluster.    


## <a name="next-steps"></a>Étapes suivantes

* [Utiliser des machines virtuelles VMware sur Azure](quickstart-create-vmware-virtual-machine.md)
* En savoir plus sur les [clouds privés AVS](cloudsimple-private-cloud.md)
