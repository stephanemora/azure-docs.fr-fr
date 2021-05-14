---
title: Tutoriel - Accéder à votre cloud privé
description: Découvrez comment accéder à un cloud privé Azure VMware Solution
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: d2046acaf483022f977559fe74254ca58b8c42ea
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107945644"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>Tutoriel : Accéder à un cloud privé Azure VMware Solution

Azure VMware Solution ne vous permet pas de gérer votre cloud privé avec votre vCenter local. Vous devez vous connecter à l’instance Azure VMware Solution vCenter via une jumpbox. 

Dans ce tutoriel, vous allez créer une jumpbox dans le groupe de ressources que vous avez créé dans le [tutoriel précédent](tutorial-configure-networking.md), et vous connecter à l’Azure VMware Solution vCenter. Cette jumpbox est une machine virtuelle Windows sur le réseau virtuel que vous avez créé.  Elle donne accès au vCenter et au NSX Manager. 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une machine virtuelle Windows pour accéder à l’Azure VMware Solution vCenter
> * Vous connecter au vCenter à partir de cette machine virtuelle

## <a name="create-a-new-windows-virtual-machine"></a>Créer une machine virtuelle Windows

1. Dans le groupe de ressources, sélectionnez **+ Ajouter**, recherchez et sélectionnez **Microsoft Windows 10**, puis **Créer**.

   :::image type="content" source="media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Capture d’écran montrant comment ajouter une nouvelle machine virtuelle Windows 10 pour un Jumpbox." border="true":::

1. Entrez les valeurs nécessaires dans les champs, puis sélectionnez **Vérifier + créer**. 

   Pour plus d’informations sur les champs, consultez le tableau suivant.

   | Champ | Valeur |
   | --- | --- |
   | **Abonnement** | Cette valeur est prérenseignée avec l’abonnement appartenant au groupe de ressources. |
   | **Groupe de ressources** | La valeur est préremplie pour le groupe de ressources actuel que vous avez créé dans le tutoriel précédent.  |
   | **Nom de la machine virtuelle** | Entrez un nom unique pour la machine virtuelle. |
   | **Région** | Sélectionnez le lieu géographique de la machine virtuelle. |
   | **Options de disponibilité** | Laissez la valeur par défaut sélectionnée. |
   | **Image** | Sélectionnez l’image de machine virtuelle. |
   | **Taille** | Laissez la valeur de la taille par défaut. |
   | **Type d’authentification**  | Sélectionnez **Mot de passe**. |
   | **Nom d’utilisateur** | Entrez le nom d’utilisateur pour la connexion à la machine virtuelle. |
   | **Mot de passe** | Entrez le mot de passe pour la connexion à la machine virtuelle. |
   | **Confirmer le mot de passe** | Entrez le mot de passe pour la connexion à la machine virtuelle. |
   | **Ports d’entrée publics** | Sélectionnez **Aucun**. Si vous sélectionnez Aucun, vous pouvez utiliser l’[accès JIT](../security-center/security-center-just-in-time.md#jit-configure) pour contrôler l’accès à la machine virtuelle seulement quand vous voulez y accéder. Vous pouvez également utiliser un [bastion Azure](../bastion/tutorial-create-host-portal.md) si vous souhaitez accéder au serveur de rebond de façon sécurisée à partir d’Internet sans exposer de port réseau.  |


1. Une fois la validation réussie, sélectionnez **Créer** pour démarrer le processus de création de la machine virtuelle.

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


