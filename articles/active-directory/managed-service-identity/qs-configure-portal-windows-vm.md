---
title: Configurer l’identité du service administré sur une machine virtuelle Azure à l’aide du portail Azure
description: Instructions détaillées sur la configuration de l’identité du service administré (MSI) sur une machine virtuelle Azure, à l’aide du portail Azure.
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
ms.openlocfilehash: 27ecb00bddb41ae45e790a54702c058ff3f1d24b
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035939"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Configurer l’identité du service administré (MSI) d’une machine virtuelle à l’aide du portail Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

L’identité du service administré fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez apprendre à activer et désactiver l’identité affectée au système d’une machine virtuelle Azure, à l’aide du portail Azure. L’affectation et la suppression des identités affectées par l’utilisateur à partir de machines virtuelles Azure ne sont actuellement pas prises en charge via le portail Azure.

> [!NOTE]
> Actuellement, les opérations d’identité affectées par l’utilisateur ne sont pas prises en charge via le portail Azure. Revenez ultérieurement pour des mises à jour. 

## <a name="prerequisites"></a>Prérequis

- Si vous ne connaissez pas MSI, consultez la [section Vue d’ensemble](overview.md).
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Identité de service gérée lors de la création d’une machine virtuelle Azure

Actuellement, la création de machines virtuelles via le portail Azure ne prend pas en charge les opérations Managed Service Identity. Consultez plutôt l’un des articles de démarrage rapide de création de machine virtuelle suivants pour créer une machine virtuelle :

- [Créer une machine virtuelle Windows avec le portail Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Créer une machine virtuelle Linux avec le portail Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Passez ensuite à la section suivante pour plus d’informations sur l’activation de l’identité du service administré sur la machine virtuelle.

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>Activer l’identité de service administré sur une machine virtuelle Azure existante

Pour activer l’identité système affectée sur une machine virtuelle qui a été initialement configurée sans :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient la machine virtuelle. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur la machine virtuelle, comme « Contributeur de machines virtuelles ».

2. Accédez à la machine virtuelle souhaitée et sélectionnez la page « Configuration ».

3. Activez l’identité affectée au système sur la machine virtuelle en sélectionnant « Oui » sous « Identité du service administré », puis cliquez sur **Enregistrer**. Cette opération peut durer 60 secondes ou plus :

    > [!NOTE]
    > Actuellement, l’ajout d’une identité à une machine virtuelle n’est pas pris en charge via le portail Azure.

   ![Capture d’écran de la page Configuration](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Supprimer l’identité de service administré d’une machine virtuelle Azure

Si vous disposez d’une machine virtuelle qui ne nécessite l’identité affectée du système :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient la machine virtuelle. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur la machine virtuelle, comme « Contributeur de machines virtuelles ».

2. Accédez à la machine virtuelle souhaitée et sélectionnez la page « Configuration ».

3. Désactivez l’identité affectée au système sur la machine virtuelle en sélectionnant « Non » sous « Identité du service administré », puis cliquez sur Enregistrer. Cette opération peut durer 60 secondes ou plus :

    > [!NOTE]
    > Actuellement, l’ajout d’une identité à une machine virtuelle n’est pas pris en charge via le portail Azure.

   ![Capture d’écran de la page Configuration](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble](overview.md).

## <a name="next-steps"></a>Étapes suivantes

- À l’aide du portail Azure, accordez à l’identité du service administré d’une machine virtuelle Azure [un accès à une autre ressource Azure](howto-assign-access-portal.md).

