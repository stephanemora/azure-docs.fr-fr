---
title: Démarrage rapide Azure VMware Solutions (AVS) - Créer le service
description: Découvrez comment créer le service AVS, acheter des nœuds et réserver des nœuds.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e7eb414e51ca38f524ab83bfb51f80f771524287
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024430"
---
# <a name="quickstart---create-azure-vmware-solutions-avs-service"></a>Démarrage rapide - Créer le service Azure VMware Solutions (AVS)

Pour commencer, créez Azure VMware Solutions (AVS) dans le portail Azure.

## <a name="vmware-solutions-avs---service-overview"></a>VMware Solutions (AVS) - Vue d’ensemble du service

Le service AVS vous permet de consommer Azure VMware Solution by AVS. En créant ce service, vous pouvez provisionner et réserver des nœuds, et créer des clouds privés AVS. Vous ajoutez le service AVS dans chaque région Azure où le service AVS est disponible. Le service définit le réseau de périmètre d’Azure VMware Solution by AVS. Ce réseau de périmètre est destiné aux services comprenant une connectivité VPN, ExpressRoute et Internet vers vos clouds privés AVS.

Pour ajouter le service AVS, vous devez créer un sous-réseau de passerelle. Le sous-réseau de passerelle est utilisé lors de la création du réseau de périmètre et requiert un bloc CIDR /28. L’espace d’adressage du sous-réseau de passerelle doit être unique. Il ne peut pas chevaucher un de vos espaces d'adressage de réseau local ni l'espace d'adressage du réseau virtuel Azure.

## <a name="before-you-begin"></a>Avant de commencer

Allouez un bloc /28 CIDR pour le sous-réseau de passerelle. Un sous-réseau de passerelle est requis par le service AVS et est propre à la région dans laquelle il est créé. Le sous-réseau de passerelle est utilisé pour les services de réseau de périmètre Azure VMware Solution by AVS, et nécessite un bloc CIDR /28. L’espace d’adressage du sous-réseau de passerelle doit être unique. Il ne doit couvrir aucun réseau qui communique avec l’environnement AVS. Les réseaux qui communiquent avec AVS incluent les réseaux locaux et les réseaux virtuels Azure.

Passez en revue les [Conditions préalables à la mise en réseau](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Créer le service

1. Sélectionnez **Tous les services**.
2. Recherchez **Service AVS**.

    ![Recherchez AVS Service.](media/create-cloudsimple-service-search.png)

3. Sélectionnez **Services AVS**.
4. Cliquez sur **Ajouter** pour créer un service.

    ![Ajoutez un service AVS](media/create-cloudsimple-service-add.png)

5. Sélectionnez l’abonnement dans lequel vous souhaitez créer le service AVS.
6. Sélectionnez le groupe de ressources du service. Pour ajouter un nouveau groupe de ressources, cliquez sur **Créer**.
7. Entrez un nom pour identifier le service.
8. Entrez la notation CIDR de la passerelle du service. Spécifiez un sous-réseau /28 qui ne chevauche aucun de vos sous-réseaux locaux, sous-réseaux Azure ou sous-réseaux AVS planifiés. Une fois le service créé, vous ne pouvez plus modifier le CIDR.

    ![Création du service AVS](media/create-cloudsimple-service.png)

9. Cliquez sur **OK**.

Le service est créé et ajouté à la liste des services.

## <a name="provision-nodes"></a>Nœuds d'approvisionnement

Pour configurer la capacité avec paiement à l’utilisation d’un environnement de cloud privé AVS, commencez par provisionner des nœuds dans le portail Azure.

1. Sélectionnez **Tous les services**.
2. Recherchez **Nœuds AVS**.

    ![Recherchez Nœuds AVS](media/create-cloudsimple-node-search.png)

3. Sélectionnez **Nœuds AVS**.
4. Cliquez sur **Ajouter** pour créer les nœuds.

    ![Ajoutez des nœuds AVS](media/create-cloudsimple-node-add.png)

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

* [Créer un cloud privé AVS et configurer l’environnement](quickstart-create-private-cloud.md)
* En savoir plus sur le [service AVS](https://docs.azure.cloudsimple.com/cloudsimple-service)
