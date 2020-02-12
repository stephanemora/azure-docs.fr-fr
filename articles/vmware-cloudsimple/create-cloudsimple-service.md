---
title: Azure VMware Solutions (AVS) - Créer un service AVS
description: Décrit comment créer le service AVS dans le portail Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 468ef8751438812422d7eee83d98acc9e3aedb82
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024821"
---
# <a name="create-the-azure-vmware-solutions-avs-service"></a>Créer le service Azure VMware Solutions (AVS)

Pour commencer à utiliser Azure VMware Solutions (AVS), créez le service Azure VMware Solutions (AVS) dans le portail Azure.

## <a name="before-you-begin"></a>Avant de commencer

Allouez un bloc /28 CIDR pour le sous-réseau de passerelle. Un sous-réseau de passerelle est requis par le service AVS et est propre à la région dans laquelle il est créé. Le sous-réseau de passerelle est utilisé pour les services de réseau Edge et nécessite un bloc CIDR /28. L’espace d’adressage du sous-réseau de passerelle doit être unique. Il ne doit couvrir aucun réseau qui communique avec l’environnement AVS. Les réseaux qui communiquent avec AVS incluent les réseaux locaux et les réseaux virtuels Azure.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Créer le service

1. Sélectionnez **Tous les services**.
2. Recherchez **Services AVS**.
    ![Recherchez AVS Service](media/create-cloudsimple-service-search.png)
3. Sélectionnez **Services AVS**.
4. Cliquez sur **Ajouter** pour créer un service.
    ![Ajoutez un service AVS](media/create-cloudsimple-service-add.png)
5. Sélectionnez l’abonnement dans lequel vous souhaitez créer le service AVS.
6. Sélectionnez le groupe de ressources du service. Pour ajouter un nouveau groupe de ressources, cliquez sur **Créer**.
7. Entrez un nom pour identifier le service.
8. Entrez la notation CIDR de la passerelle du service. Spécifiez un sous-réseau /28 qui ne couvre aucun de vos sous-réseaux locaux, sous-réseaux Azure ou sous-réseaux AVS planifiés. Une fois le service créé, vous ne pouvez plus modifier le CIDR.

    ![Création du service AVS](media/create-cloudsimple-service.png)
9. Cliquez sur **OK**.

Le service est créé et ajouté à la liste des services.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [approvisionner des nœuds](create-nodes.md)
* Découvrez comment [créer un cloud privé AVS](create-private-cloud.md)
* Découvrez comment [configurer un environnement de cloud privé AVS](quickstart-create-private-cloud.md)
