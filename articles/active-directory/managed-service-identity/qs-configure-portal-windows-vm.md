---
title: Guide pratique pour configurer Managed Service Identity sur une machine virtuelle Azure en utilisant le portail Azure
description: Instructions détaillées pour configurer une identité de service managée sur une machine virtuelle Azure en utilisant le portail Azure.
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
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 552fce2ffd8b6bd786010da82e702ee98c3f8647
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888554"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>Configurer une identité de service managée sur une machine virtuelle en utilisant le portail Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

L’identité du service administré fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous apprendrez à activer et désactiver l’identité attribuée par le système et à l’utilisateur pour une machine virtuelle Azure, à l’aide du portail Microsoft Azure. 

## <a name="prerequisites"></a>Prérequis

- Si vous ne connaissez pas Managed Service Identity, consultez la [section Vue d’ensemble](overview.md).
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour effectuer les opérations de gestion dans cet article, votre compte doit disposer de l’attribution de rôle suivant :
    - [Contributeur de machines virtuelles](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor), pour activer et supprimer l’identité affectée par le système à partir d’une machine virtuelle Azure.

## <a name="system-assigned-identity"></a>Identité attribuée au système

Dans cette section, vous découvrirez comment activer et désactiver l’identité attribuée par le système pour une machine virtuelle à l’aide du portail Microsoft Azure.

### <a name="enable-system-assigned-identity-during-creation-of-a-vm"></a>Activer l’identité attribuée par le système lors de la création d’une machine virtuelle

À l’heure actuelle, le portail Microsoft Azure ne prend pas en charge l’activation de l’identité attribuée par le système lors de la création d’une machine virtuelle. Pour créer une machine virtuelle, reportez-vous plutôt à l’un des articles de démarrage rapide suivants, puis passez à la section suivante pour obtenir des informations détaillées sur l’activation de l’identité attribuée par le système sur la machine virtuelle :

- [Créer une machine virtuelle Windows avec le portail Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Créer une machine virtuelle Linux avec le portail Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

### <a name="enable-system-assigned-identity-on-an-existing-vm"></a>Activer l’identité attribuée par le système sur une machine virtuelle existante

Pour activer l’identité système affectée sur une machine virtuelle qui a été initialement configurée sans :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient la machine virtuelle.

2. Accédez à la machine virtuelle souhaitée et sélectionnez **Identité**.

3. Sous **Attribuée par le système**, **État**, sélectionnez **Activé** puis cliquez sur **Enregistrer** :

   ![Capture d’écran de la page Configuration](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-identity-from-a-vm"></a>Supprimer l’identité attribuée par le système d’une machine virtuelle

Si vous disposez d’une machine virtuelle qui ne nécessite l’identité affectée du système :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient la machine virtuelle. 

2. Accédez à la machine virtuelle souhaitée et sélectionnez **Identité**.

3. Sous **Attribué par le système**, **État**, sélectionnez **Désactivé** puis cliquez sur **Enregistrer** :

   ![Capture d’écran de la page Configuration](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-identity"></a>Identité attribuée par l’utilisateur

 Dans cette section, vous découvrirez comment ajouter et supprimer l’identité attribuée par l’utilisateur sur une machine virtuelle à l’aide du portail Microsoft Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Attribuer une identité attribuée par l’utilisateur lors de la création d’une machine virtuelle

À l’heure actuelle, le portail Microsoft Azure ne prend pas en charge l’activation de l’identité attribuée par l’utilisateur lors de la création d’une machine virtuelle. Pour créer une machine virtuelle, reportez-vous plutôt à l’un des articles de démarrage rapide suivants, puis passez à la section suivante pour obtenir des informations détaillées sur l’activation de l’identité attribuée par l’utilisateur à la machine virtuelle :

- [Créer une machine virtuelle Windows avec le portail Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Créer une machine virtuelle Linux avec le portail Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-identity-to-an-existing-vm"></a>Attribuer une identité attribuée par l’utilisateur à une machine virtuelle existante

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient la machine virtuelle.
2. Accédez à la machine virtuelle souhaitée, cliquez sur **Identité**, **Attribuée par l’utilisateur**, puis sur **\+Ajouter**.

   ![Ajouter l’identité attribuée par l'utilisateur à une machine virtuelle](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Cliquez sur l’identité attribuée par l’utilisateur que vous souhaitez ajouter à la machine virtuelle, puis cliquez sur **Ajouter**.

    ![Ajouter l’identité attribuée par l'utilisateur à une machine virtuelle](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-vm"></a>Supprimer l’identité attribuée par l’utilisateur d’une machine virtuelle

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient la machine virtuelle.
2. Accédez à la machine virtuelle souhaitée, cliquez sur **Identité**, puis sur **Attribuée par l’utilisateur**. Cliquez ensuite sur le nom de l’identité attribuée par l’utilisateur que vous souhaitez supprimer, puis sur **Supprimer** (cliquez sur **Oui** dans le volet de confirmation).

   ![Supprimer l’identité attribuée par l’utilisateur d’une machine virtuelle](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble](overview.md).

## <a name="next-steps"></a>Étapes suivantes

- Utilisez le portail Azure pour accorder à l’identité de service managée d’une machine virtuelle un [accès à une autre ressource Azure](howto-assign-access-portal.md).

