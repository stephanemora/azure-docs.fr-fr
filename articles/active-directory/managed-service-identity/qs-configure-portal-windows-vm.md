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
ms.openlocfilehash: 6ba090065b18a44cc1f01a62eefb5dcf52bcf356
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213263"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>Configurer une identité de service managée sur une machine virtuelle en utilisant le portail Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Managed Service Identity fournit des services Azure avec une identité managée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez apprendre à activer et désactiver l’identité affectée au système d’une machine virtuelle Azure, à l’aide du portail Azure. L’affectation et la suppression des identités affectées par l’utilisateur à partir de machines virtuelles Azure ne sont actuellement pas prises en charge via le portail Azure.

> [!NOTE]
> Actuellement, les opérations d’identité affectées par l’utilisateur ne sont pas prises en charge via le portail Azure. Revenez ultérieurement pour des mises à jour. 

## <a name="prerequisites"></a>Prérequis


- Si vous ne connaissez pas Managed Service Identity, consultez la [section Vue d’ensemble](overview.md).
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour effectuer les opérations de gestion dans cet article, votre compte doit disposer de l’attribution de rôle suivant :
    - [Contributeur de machines virtuelles](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor), pour activer et supprimer l’identité affectée par le système à partir d’une machine virtuelle Azure.

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Managed Service Identity lors de la création d’une machine virtuelle Azure

Actuellement, la création de machines virtuelles via le portail Azure ne prend pas en charge les opérations Managed Service Identity. Consultez plutôt l’un des articles de démarrage rapide de création de machine virtuelle suivants pour créer une machine virtuelle :

- [Créer une machine virtuelle Windows avec le portail Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Créer une machine virtuelle Linux avec le portail Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Passez ensuite à la section suivante pour plus d’informations sur l’activation de l’identité du service administré sur la machine virtuelle.

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>Activer l’identité de service administré sur une machine virtuelle Azure existante

Pour activer l’identité système affectée sur une machine virtuelle qui a été initialement configurée sans :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient la machine virtuelle.

2. Accédez à la machine virtuelle souhaitée et sélectionnez la page « Configuration ».

3. Activez l’identité affectée au système sur la machine virtuelle en sélectionnant « Oui » sous « Identité du service administré », puis cliquez sur **Enregistrer**. Cette opération peut durer 60 secondes ou plus :

   > [!NOTE]
   > Actuellement, l’ajout d’une identité à une machine virtuelle n’est pas pris en charge via le portail Azure.

   ![Capture d’écran de la page Configuration](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Supprimer l’identité de service administré d’une machine virtuelle Azure

Si vous disposez d’une machine virtuelle qui ne nécessite l’identité affectée du système :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient la machine virtuelle. 

2. Accédez à la machine virtuelle souhaitée et sélectionnez la page « Configuration ».

3. Désactivez l’identité affectée au système sur la machine virtuelle en sélectionnant « Non » sous « Identité du service administré », puis cliquez sur Enregistrer. Cette opération peut durer 60 secondes ou plus :

    > [!NOTE]
    > Actuellement, l’ajout d’une identité à une machine virtuelle n’est pas pris en charge via le portail Azure.

   ![Capture d’écran de la page Configuration](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble](overview.md).

## <a name="next-steps"></a>Étapes suivantes

- Utilisez le portail Azure pour accorder à l’identité de service managée d’une machine virtuelle un [accès à une autre ressource Azure](howto-assign-access-portal.md).

