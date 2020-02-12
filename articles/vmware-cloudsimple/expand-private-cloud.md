---
title: Étendre un cloud privé Azure VMware Solutions (AVS)
description: Décrit comment étendre un cloud privé AVS existant pour accroître la capacité d’un cluster existant ou en ajouter un nouveau
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3286b7537056a6c2f282533aa629ebbe47612690
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025297"
---
# <a name="expand-an-avs-private-cloud"></a>Étendre un cloud privé AVS

AVS fournit la flexibilité nécessaire pour étendre de manière dynamique un cloud privé AVS. Vous pouvez commencer avec une configuration plus petite, puis la faire grandir quand vous avez besoin d’une capacité supérieure. Vous pouvez également créer un cloud privé AVS en fonction de vos besoins actuels et l'étendre ensuite lorsque la consommation augmente.

Un cloud privé AVS se compose d’un ou de plusieurs clusters vSphere. Chaque cluster peut contenir entre 3 et 16 nœuds. Lorsque vous étendez un cloud privé AVS, vous ajoutez des nœuds au cluster existant ou créez un nouveau cluster. Pour développer un cluster existant, les nœuds supplémentaires doivent être du même type (référence SKU) que les nœuds existants. Pour la création d’un nouveau cluster, les nœuds peuvent être d’un type différent. Pour plus d’informations sur les limites du cloud privé AVS, consultez la section Limites de l’article [Vue d’ensemble des clouds privés AVS](cloudsimple-private-cloud.md).

Un cloud privé AVS est créé avec un **centre de données** par défaut sur vCenter. Chaque centre de données constitue une entité de gestion de niveau supérieur. Pour un nouveau cluster, AVS permet de choisir parmi l’ajout au centre de données existant ou la création d’un nouveau centre de données.

Dans le cadre de la nouvelle configuration de cluster, AVS configure l’infrastructure VMware. Les paramètres incluent les paramètres de stockage pour les groupes de disques vSAN, la haute disponibilité VMware et Distributed Resource Scheduler (DRS).

Un cloud privé AVS peut être étendu plusieurs fois. L’expansion peut être effectuée uniquement dans les limites de nœud globales. Chaque fois que vous étendez un cloud privé AVS, vous ajoutez au cluster existant ou en créez un nouveau.

## <a name="before-you-begin"></a>Avant de commencer

Les nœuds doivent être configurés avant de pouvoir étendre votre cloud privé AVS. Pour plus d’informations sur l'approvisionnement des nœuds, consultez l’article [Approvisionner des nœuds pour VMware Solution by AVS - Azure](create-nodes.md). Pour créer un nouveau cluster, vous devez disposer d’au moins trois nœuds disponibles avec la même référence SKU.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-an-avs-private-cloud"></a>Étendre un cloud privé AVS

1. [Accédez au portail AVS](access-cloudsimple-portal.md).

2. Ouvrez la page **Ressources** et sélectionnez le cloud privé AVS que vous souhaitez étendre.

3. Dans la section Résumé , cliquez sur **Développer**.

    ![Étendre un cloud privé AVS](media/resources-expand-private-cloud.png)

4. Choisissez si vous souhaitez développer votre cluster existant ou créer un nouveau cluster vSphere. Lorsque vous apportez des modifications, les informations de résumé sur la page sont mises à jour.

    * Pour développer votre cluster existant, cliquez sur **Développez le cluster existant**. Sélectionnez le cluster que vous souhaitez développer et entrez le nombre de nœuds à ajouter. Chaque cluster peut avoir un maximum de 16 nœuds.
    * Pour ajouter un nouveau cluster, cliquez sur **Créer un cluster**. Entrez un nom pour le cluster. Sélectionnez un centre de données existant, ou entrez un nom pour créer un nouveau centre de données. Choisissez le type de nœud. Vous pouvez choisir un type de nœud différent lors de la création d’un nouveau cluster vSphere, mais pas lors du développement d’un cluster vSphere existant. Sélectionnez le nombre de nœuds. Chaque nouveau cluster doit avoir au moins trois nœuds.

    ![Étendre un cloud privé AVS - Ajouter des nœuds](media/resources-expand-private-cloud-add-nodes.png)

5. Cliquez sur **Envoyer** pour étendre le cloud privé AVS.

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser des machines virtuelles VMware sur Azure](quickstart-create-vmware-virtual-machine.md)
* En savoir plus sur les [clouds privés AVS](cloudsimple-private-cloud.md)