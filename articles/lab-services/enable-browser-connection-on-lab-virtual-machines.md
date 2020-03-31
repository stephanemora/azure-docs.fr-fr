---
title: Activer la connexion du navigateur sur les machines virtuelles Azure DevTest Labs | Microsoft Docs
description: DevTest Labs s’intègre désormais à Azure Bastion. En tant que propriétaire du labo, vous pouvez activer l’accès à toutes les machines virtuelles du labo via un navigateur.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 2ddc56c60c547bd4ce48d620a83fb79246762bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "69641981"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Activer la connexion du navigateur sur les machines virtuelles du labo 

DevTest Labs s’intègre à [Azure Bastion](https://docs.microsoft.com/azure/bastion/), ce qui vous permet de vous connecter à vos machines virtuelles via un navigateur. Vous devez tout d’abord activer la connexion du navigateur sur les machines virtuelles du labo.

En tant que propriétaire d’un labo, vous pouvez activer l’accès à toutes les machines virtuelles du labo via un navigateur. Vous n’avez pas besoin d’un client, d’un agent ou d’un logiciel supplémentaire. Azure Bastion fournit une connectivité RDP/SSH sécurisée et fluide à vos machines virtuelles directement dans le portail Azure via SSL. Lorsque vous vous connectez via Azure Bastion, vos machines virtuelles n’ont pas besoin d’une adresse IP publique. Pour plus d’informations, consultez la page [Présentation d’Azure Bastion](../bastion/bastion-overview.md).

> [!NOTE]
> L’activation de la connexion du navigateur sur les machines virtuelles du labo est en préversion.

Cet article vous indique comment activer la connexion du navigateur sur les machines virtuelles du labo.

## <a name="prerequisites"></a>Prérequis 
Vous pouvez déployer un hôte Bastion dans le réseau virtuel de votre labo existant **(OU)** connecter votre labo à un réseau virtuel configuré sur Bastion. 

Pour découvrir comment déployer un hôte Bastion dans un réseau virtuel, consultez la page [Créer un hôte bastion Azure (préversion)](../bastion/bastion-create-host-portal.md). Lorsque vous créez l’hôte Bastion, sélectionnez le réseau virtuel du labo. 

Pour découvrir comment connecter votre labo à un réseau virtuel configuré sur Bastion, consultez la page [Configurer un réseau virtuel dans Azure DevTest Labs](devtest-lab-configure-vnet.md). Sélectionnez le réseau virtuel sur lequel l’hôte Bastion est déployé et le **AzureBastionSubnet** qui s’y trouve. Voici les étapes détaillées : 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu de navigation de gauche, sélectionnez **Tous les services**. 
1. Sélectionnez **DevTest Labs** dans la liste. 
1. Dans la liste de labos, sélectionnez *votre labo*. 

    > [!NOTE]
    > Azure Bastion est actuellement proposé en préversion. Elle se limite aux régions suivantes : Usa West, USA Est, Europe Ouest, USA Centre Sud, Australie Est et Japon Est. Créez donc un labo dans l’une de ces régions s’il n’est pas dans l’une d’entre elles. 
1. Sélectionnez **Configuration et stratégies** dans la section **Paramètres** du menu à gauche. 
1. Sélectionner **Réseaux virtuels**.
1. Sélectionnez **Ajouter** depuis la barre d’outils. 
1. Sélectionnez le **réseau virtuel** sur lequel l’hôte Bastion est déployé. 
1. Sélectionnez le sous-réseau : **AzureBastionSubnet**. 

    ![Subnet](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. Sélectionnez l’option **Utiliser dans la création de machine virtuelle**. 
1. Sélectionnez **Enregistrer** dans la barre d’outils. 
1. Si vous avez un ancien réseau virtuel pour le labo, supprimez-le en sélectionnant * *…* et **Supprimer**. 

## <a name="enable-browser-connection"></a>Activer la connexion du navigateur 

Une fois que vous avez configuré un réseau virtuel Bastion dans le labo, vous pouvez, en tant que propriétaire du labo, activer la connexion du navigateur sur les machines virtuelles du labo.

Pour activer la connexion du navigateur sur les machines virtuelles du labo, procédez comme suit :

1. Accédez à *votre labo* dans le portail Azure.
1. Sélectionnez **Configuration et stratégies**.
1. Dans **Paramètres**,sélectionnez **Connexion du navigateur (préversion)** .

![Activer la connexion du navigateur](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Étapes suivantes
Consultez l’article suivant pour découvrir comment connecter à vos machines virtuelles à l’aide d’un navigateur : [Se connecter à votre machine virtuelle à l’aide d’un navigateur](connect-virtual-machine-through-browser.md)