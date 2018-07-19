---
title: Configurer l’identité du service administré sur un groupe de machines virtuelles identiques à l’aide du portail Azure
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 8779600f2c85a8bb309f7b2a8874608170de8877
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035239"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>Configurer Managed Service Identity (MSI) pour un groupe de machines virtuelles identiques à l’aide du portail Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

L’identité du service administré fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous apprenez à activer et désactiver l’identité affectée par le système à un groupe de machines virtuelles identiques dans le portail Azure. L’attribution et la suppression des identités affectées par l’utilisateur pour un groupe de machines virtuelles identiques ne sont actuellement pas prises en charge via le portail Azure.

> [!NOTE]
> Actuellement, les opérations d’identité affectées par l’utilisateur ne sont pas prises en charge via le portail Azure. Revenez ultérieurement pour des mises à jour.

## <a name="prerequisites"></a>Prérequis


- Si vous ne connaissez pas MSI, consultez la [section Vue d’ensemble](overview.md).
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.

## <a name="managed-service-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Identité de service administrée lors de la création d’un groupe de machines virtuelles identiques Azure

Actuellement, la création de machines virtuelles via le portail Azure ne prend pas en charge les opérations Managed Service Identity. À la place, veuillez vous référer au démarrage rapide suivant sur la création d’un groupe de machines virtuelles identiques Azure pour en créer un d’abord :

- [Créer un groupe de machines virtuelles identiques dans le portail Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

Passez ensuite à la section suivante pour plus d’informations sur l’activation de l’identité du service administré sur le groupe de machines virtuelles identiques.

## <a name="enable-managed-service-identity-on-an-existing-azure-vmms"></a>Activer l’identité de service administré sur VMSS Azure existante

Pour activer l’identité système affectée sur une machine virtuelle qui a été initialement configurée sans :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient le groupe de machines virtuelles identiques.

2. Accédez au groupe de machines virtuelles identiques souhaité.

3. Activez l’identité affectée au système sur la machine virtuelle en sélectionnant « Oui » sous « Identité du service administré », puis cliquez sur **Enregistrer**. Cette opération peut durer 60 secondes ou plus :

   [![Capture d’écran de la page Configuration](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

## <a name="remove-managed-service-identity-from-an-azure-virtual-machine-scale-set"></a>Supprimer l’identité de service administrée d’un groupe de machines virtuelles identiques Azure

Si vous disposez d’un groupe de machines virtuelles identiques qui ne nécessite plus d’identité du service administré :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure qui contient le groupe de machines virtuelles identiques. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur le groupe de machines virtuelles identiques.

2. Accédez au groupe de machines virtuelles identiques souhaité.

3. Désactivez l’identité affectée au système sur la machine virtuelle en sélectionnant « Non » sous « Identité du service administré », puis cliquez sur Enregistrer. Cette opération peut durer 60 secondes ou plus :

   ![Capture d’écran de la page Configuration](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble](overview.md).

## <a name="next-steps"></a>Étapes suivantes

- À l’aide du portail Azure, accordez à l’identité du service administré d’un groupe de machines virtuelles identiques un [accès à une autre ressource Azure](howto-assign-access-portal.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.
