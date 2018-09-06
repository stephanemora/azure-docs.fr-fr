---
title: Configurer Managed Service Identity sur un groupe de machines virtuelles identiques Azure à l’aide du portail Azure
description: Instructions détaillées pour configurer une identité de service managée sur un groupe de machines virtuelles identiques Azure à l’aide du portail Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: cbe2e3d9f60ced5c707ce5a701a5aac937ccc072
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887987"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-using-the-azure-portal"></a>Configurer une identité de service managée pour un groupe de machines virtuelles identiques à l’aide du portail Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

L’identité du service administré fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous apprenez à activer et à désactiver l’identité attribuée par le système et par l’utilisateur à un groupe de machines virtuelles identiques dans le portail Azure.

## <a name="prerequisites"></a>Prérequis

- Si vous ne connaissez pas Managed Service Identity, consultez la [section Vue d’ensemble](overview.md).
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour effectuer les opérations de gestion dans cet article, votre compte doit disposer de l’attribution de rôle suivant :
    - [Contributeur de machines virtuelles](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) pour activer et supprimer l’identité managée affectée par le système à partir d’un groupe de machines virtuelles identiques.

## <a name="system-assigned-identity"></a>Identité attribuée au système 

Dans cette section, vous apprenez à activer et à désactiver l’identité attribuée par le système sur un groupe de machines virtuelles identiques dans le portail Azure.

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>Activer une identité affectée par le système lors de la création d’un groupe de machines virtuelles identiques

À l’heure actuelle, le portail Azure ne prend pas en charge l’activation de l’identité attribuée par le système lors de la création d’un groupe de machines virtuelles identiques. Au lieu de cela, consultez l’article de démarrage rapide suivant sur la création de groupes de machines virtuelles identiques pour commencer par créer un groupe de machines virtuelles identiques, puis passez à la section suivante pour plus d’informations sur l’activation de l’identité attribuée par le système sur un groupe de machines virtuelles identiques :

- [Créer un groupe de machines virtuelles identiques dans le portail Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-identity-on-an-existing-virtual-machine-scale-set"></a>Activer une identité attribuée par le système sur un groupe de machines virtuelles identiques existant

Pour activer l’identité affectée par le système sur un groupe de machines virtuelles identiques ayant été initialement configuré sans :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient le groupe de machines virtuelles identiques.

2. Accédez au groupe de machines virtuelles identiques souhaité.

3. Sous **Attribuée par le système**, **État**, sélectionnez **Activé** puis cliquez sur **Enregistrer** :

   [![Capture d’écran de la page Configuration](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

### <a name="remove-system-assigned-identity-from-a-virtual-machine-scale-set"></a>Supprimer une identité attribuée par l’utilisateur d’un groupe de machines virtuelles identiques

Si vous disposez d’un groupe de machines virtuelles identiques qui n’a plus besoin d’une identité attribuée par le système :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient le groupe de machines virtuelles identiques. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur le groupe de machines virtuelles identiques.

2. Accédez au groupe de machines virtuelles identiques souhaité.

3. Sous **Attribuée par le système**, **État**, sélectionnez **Désactivé** puis cliquez sur **Enregistrer** :

   ![Capture d’écran de la page Configuration](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-identity"></a>Identité attribuée par l’utilisateur

Dans cette section, vous découvrez comment ajouter et supprimer une identité attribuée par l’utilisateur à partir d’un groupe de machines virtuelles identiques utilisant le portail Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Attribuer une identité affectée par l’utilisateur lors de la création d’un groupe de machines virtuelles identiques

À l’heure actuelle, le portail Azure ne prend pas en charge l’attribution d’une identité attribuée par l’utilisateur lors de la création d’un ensemble de machines virtuelles identiques. Au lieu de cela, consultez l’article de démarrage rapide suivant sur la création de groupes de machines virtuelles identiques pour commencer par créer un groupe de machines virtuelles identiques, puis passez à la section suivante pour plus d’informations sur l’attribution d’une identité par l’utilisateur à ce groupe  :

- [Créer un groupe de machines virtuelles identiques dans le portail Azure](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-identity-to-an-existing-virtual-machine-scale-set"></a>Attribuer une identité affectée par le système sur un groupe de machines virtuelles identiques existant

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient le groupe de machines virtuelles identiques.
2. Accédez au groupe de machines virtuelles identiques souhaité, cliquez sur **Identité**, **Attribuée par l’utilisateur**, puis sur **\+Ajouter**.

   ![Ajouter une identité attribuée par l’utilisateur à un groupe de machines virtuelles identiques](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Cliquez sur l’identité attribuée par l’utilisateur que vous souhaitez ajouter au groupe de machines virtuelles identiques, puis cliquez sur **Ajouter**.
   
   ![Ajouter une identité attribuée par l’utilisateur à un groupe de machines virtuelles identiques](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>Supprimer une identité affectée par l’utilisateur d’un groupe de machines virtuelles identiques

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient la machine virtuelle.
2. Accédez au groupe de machines virtuelles identiques souhaité, cliquez sur **Identité**, puis sur **Attribuée par l’utilisateur**. Cliquez ensuite sur le nom de l’identité attribuée par l’utilisateur que vous souhaitez supprimer, puis sur **Supprimer** (cliquez sur **Oui** dans le volet de confirmation).

   ![Supprimer l’identité attribuée par l’utilisateur d’un groupe de machines virtuelles identiques](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble](overview.md).

## <a name="next-steps"></a>Étapes suivantes

- Dans le portail Azure, accordez à l’identité de service managée d’un groupe de machines virtuelles identiques un [accès à une autre ressource Azure](howto-assign-access-portal.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.
