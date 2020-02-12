---
title: Azure VMware Solutions (AVS) - Accéder au client vSphere
description: Explique comment accéder au serveur vCenter depuis votre cloud privé AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad018ea89b194d42ab1867a0569725c4c3680f7d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022662"
---
# <a name="access-your-avs-private-cloud-vcenter-portal"></a>Accéder au portail vCenter de votre cloud privé AVS

Vous pouvez ouvrir le portail vCenter de votre cloud privé AVS à partir du portail Azure ou du portail AVS. Le portail vCenter vous permet de gérer l’infrastructure VMware sur votre cloud privé AVS.

## <a name="before-you-begin"></a>Avant de commencer

Pour accéder au portail vCenter, une connexion réseau doit être établie et la résolution des noms DNS doit être activée. Vous pouvez établir une connexion réseau à votre cloud privé AVS à l’aide de l’une des options ci-dessous.

* [Connecter un réseau local à AVS à l’aide d’ExpressRoute](on-premises-connection.md)
* [Configurer une connexion VPN à votre cloud privé AVS](set-up-vpn.md)

Pour configurer la résolution des noms DNS des composants de l’infrastructure VMware de votre cloud privé AVS, consultez [Configurer DNS pour la résolution de noms pour l’accès à un vCenter de cloud privé AVS à partir de stations de travail locales](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Accéder à vCenter à partir du portail Azure

Vous pouvez ouvrir le portail vCenter de votre cloud privé AVS à partir du portail Azure.

1. Sélectionnez **Tous les services**.

2. Recherchez **Services AVS**.

3. Sélectionnez le service AVS du cloud privé AVS auquel vous voulez vous connecter.

4. Dans la page **Vue d’ensemble**, cliquez sur **Afficher les clouds privés AVS VMware**

    ![Vue d’ensemble du service AVS](media/cloudsimple-service-overview.png)

5. Sélectionnez le cloud privé AVS dans la liste, puis cliquez sur **Lancer le client vSphere**.

    ![Lancer le client vSphere](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-avs-portal"></a>Accéder à vCenter à partir du portail AVS

Vous pouvez ouvrir le portail vCenter de votre cloud privé AVS à partir du portail AVS.

1. Accédez à votre [portail AVS](access-cloudsimple-portal.md).

2. Dans **Ressources**, sélectionnez le cloud privé AVS auquel vous souhaitez accéder, puis cliquez sur **Lancer le client vSphere**.

    ![Lancer le client vSphere - Ressources](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Vous pouvez également ouvrir le portail vCenter à partir de l’écran récapitulatif de votre cloud privé AVS.

    ![Lancer le client vSphere - Récapitulatif](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Étapes suivantes

* [Créer et gérer des réseaux locaux virtuels ou des sous-réseaux pour vos clouds privés AVS](create-vlan-subnet.md)
* [Modèle d’autorisation de cloud privé AVS de VMware vCenter](learn-private-cloud-permissions.md)