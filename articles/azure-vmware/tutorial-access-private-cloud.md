---
title: Tutoriel - Accéder à votre cloud privé
description: Découvrez comment accéder à un cloud privé Azure VMware Solution
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: f2af1cffda08bf4b9c62e63f32d36cc9bbd7024a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494391"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>Tutoriel : Accéder à un cloud privé Azure VMware Solution

Azure VMware Solution ne vous permet pas de gérer votre cloud privé avec votre vCenter local. Vous devez vous connecter à l’instance Azure VMware Solution vCenter via une jumpbox. 

Dans ce tutoriel, vous allez créer une jumpbox dans le groupe de ressources que vous avez créé dans le [tutoriel précédent](tutorial-configure-networking.md), et vous connecter à l’Azure VMware Solution vCenter. Cette jumpbox est une machine virtuelle Windows sur le réseau virtuel que vous avez créé.  Elle donne accès au vCenter et au NSX Manager. 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une machine virtuelle Windows pour accéder à l’Azure VMware Solution vCenter
> * Vous connecter au vCenter à partir de cette machine virtuelle

## <a name="create-a-new-windows-virtual-machine"></a>Créer une machine virtuelle Windows

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Se connecter au vCenter local de votre cloud privé

1. À partir de la jumpbox, connectez-vous au client vSphere avec VMware vCenter SSO à l’aide d’un nom d’utilisateur d’administrateur cloud, et vérifiez que l’interface utilisateur s’affiche correctement.

1. Dans le portail Azure, sélectionnez votre cloud privé, puis **Gérer** > **Identité**. 

   Les URL et les informations d’identification de l’utilisateur pour le cloud privé vCenter et NSX-T Manager s’affichent.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Affichez les URL et les informations d’identification vCenter et NSX Manager du cloud privé." border="true" lightbox="media/tutorial-access-private-cloud/ss4-display-identity.png":::

1. Accédez à la machine virtuelle que vous avez créée à l’étape précédente et connectez-vous à cette machine. 

   Si vous avez besoin d’aide pour la connexion à la machine virtuelle, consultez [connexion à une machine virtuelle](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) pour plus d’informations.

1. Dans la machine virtuelle Windows, ouvrez un navigateur et accédez aux URL vCenter et NSX-T Manager dans deux onglets. 

1. Sous l’onglet vCenter, entrez les informations d’identification de l’utilisateur `cloudadmin@vmcp.local` de l’étape précédente.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Connectez-vous au vCenter du cloud privé." border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="Portail vCenter." border="true":::

1. Sous le deuxième onglet du navigateur, connectez-vous à NSX-T Manager.

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Sous le deuxième onglet du navigateur, connectez-vous à NSX-T Manager." border="true":::



## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle Windows à utiliser pour se connecter à vCenter
> * Vous connecter à vCenter à partir de votre machine virtuelle

Passez au tutoriel suivant pour découvrir comment créer un réseau virtuel afin de configurer la gestion locale pour vos clusters de cloud privé.

> [!div class="nextstepaction"]
> [Créer un réseau virtuel](tutorial-configure-networking.md)


