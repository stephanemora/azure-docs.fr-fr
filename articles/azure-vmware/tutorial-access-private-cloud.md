---
title: Tutoriel - Découvrir comment accéder à votre cloud privé
description: Découvrir comment accéder à un cloud privé de solution Azure VMware
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 332b1b01f05ca97b03264201384ee38ac266470f
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873600"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-avs-private-cloud"></a>Tutoriel : Découvrir comment accéder à un cloud privé de solution Azure VMware

Dans sa préversion, AVS (Azure VMware Solution, Solution Azure VMware) ne vous permet pas de gérer votre cloud privé avec votre vCenter local. Vous devez procéder à une configuration supplémentaire et disposer d’une connexion à une instance vCenter locale via une jumpbox. 

Dans ce tutoriel, vous créez une machine virtuelle Windows pour une jumpbox dans le groupe de ressources que vous avez créé dans le tutoriel précédent [Tutoriel : Configurer le réseau pour votre cloud privé VMware dans Azure](tutorial-configure-networking.md) et connectez-vous à vCenter. Il s’agit d’une machine virtuelle sur le même réseau virtuel que celui que vous avez créé et qui permet d’accéder à vCenter et à NSX Manager. 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une machine virtuelle Windows à utiliser pour se connecter à vCenter
> * Vous connecter à vCenter à partir de votre machine virtuelle

## <a name="create-a-new-windows-virtual-machine"></a>Créer une machine virtuelle Windows

Dans le groupe de ressources, sélectionnez **+ Ajouter**, recherchez et sélectionnez **Microsoft Windows 10**, puis cliquez sur **Créer**.

:::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Ajouter une nouvelle machine virtuelle Windows 10 pour une jumpbox" border="true":::

Entrez les valeurs nécessaires dans les champs, puis sélectionnez **Vérifier + créer**. Pour plus d’informations sur les champs, consultez le tableau suivant.

| Champ | Valeur |
| --- | --- |
| **Abonnement** | Cette valeur est déjà renseignée avec l’abonnement auquel appartient le groupe de ressources. |
| **Groupe de ressources** | Cette valeur est déjà renseignée pour le groupe de ressources actuel. Il doit s’agir du groupe de ressources que vous avez créé dans un tutoriel précédent. |
| **Nom de la machine virtuelle** | Entrez un nom unique pour la machine virtuelle. |
| **Région** | Sélectionnez le lieu géographique de la machine virtuelle. |
| **Options de disponibilité** | Laissez la valeur par défaut sélectionnée. |
| **Image** | Sélectionnez l’image de machine virtuelle. |
| **Taille** | Laissez la valeur de la taille par défaut. |
| **Type d’authentification**  | Sélectionnez **Mot de passe**. |
| **Nom d’utilisateur** | Entrez le nom d’utilisateur pour la connexion à la machine virtuelle. |
| **Mot de passe** | Entrez le mot de passe pour la connexion à la machine virtuelle. |
| **Confirmer le mot de passe** | Entrez le mot de passe pour la connexion à la machine virtuelle. |
| **Ports d’entrée publics** | Sélectionnez **Aucun**. Si vous sélectionnez Aucun, vous pouvez utiliser l’[accès JIT](../security-center/security-center-just-in-time.md#configure-jit-access-from-an-azure-vms-page-) pour contrôler l’accès à la machine virtuelle seulement quand vous voulez y accéder.  |

Après avoir entré toutes les informations appropriées, cliquez sur **Vérifier + créer**. Une fois la validation réussie, sélectionnez **Créer** pour démarrer le processus de création de la machine virtuelle.

:::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Créer une machine virtuelle Windows 10 pour une jumpbox" border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Se connecter au vCenter local de votre cloud privé

Depuis la jumpbox, connectez-vous au client vSphere avec l’authentification unique VMware vCenter. Connectez-vous au client vSphere avec un nom d’utilisateur d’administrateur de cloud, acceptez le risque de sécurité et continuez quand un avertissement s’affiche à propos d’un risque de sécurité potentiel, puis connectez-vous à VMware vCenter avec des informations d’identification pour l’authentification unique et vérifiez que l’interface utilisateur s’affiche correctement.

Dans le portail Azure, sélectionnez votre cloud privé puis, dans la vue **Vue d’ensemble**, sélectionnez **Identité > Par défaut**. Les URL et les informations d’identification de connexion pour le cloud privé vCenter et le gestionnaire NSX-T sont affichées.

:::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Afficher les URL et les informations d’identification vCenter et NSX Manager du cloud privé" border="true":::

Accédez à la machine virtuelle que vous avez créée à l’étape précédente et connectez-vous à cette machine. Pour obtenir des instructions détaillées sur la façon de se connecter à la machine virtuelle, consultez [Se connecter à une machine virtuelle](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)

Dans la machine virtuelle Windows, ouvrez un navigateur et accédez aux URL vCenter et NSX-T Manager dans deux onglets. Sous l’onglet vCenter, entrez les informations d’identification de l’utilisateur `cloudadmin@vmcp.local` de l’étape précédente.

:::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Se connecter au vCenter du cloud privé" border="true":::

:::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="Portail vCenter" border="true":::

Sous le deuxième onglet du navigateur, connectez-vous à NSX-T Manager.

:::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Page d’accueil du NSX Manager du cloud privé local" border="true":::

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle Windows à utiliser pour se connecter à vCenter
> * Vous connecter à vCenter à partir de votre machine virtuelle

Passez au tutoriel suivant pour découvrir comment mettre à l’échelle votre cloud privé AVS.

> [!div class="nextstepaction"]
> [Mettre à l’échelle un cloud privé AVS](tutorial-scale-private-cloud.md)
