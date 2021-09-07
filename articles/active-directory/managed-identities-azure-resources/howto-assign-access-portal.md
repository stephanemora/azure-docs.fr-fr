---
title: Attribuer à une identité managée l’accès à une ressource à l’aide du Portail Azure – Azure AD
description: Instructions détaillées pour attribuer à un identité managée sur une ressource un accès à une autre ressource, à l’aide du portail Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.custom: subject-rbac-steps
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/24/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeff666af9d680baa037415cedb19077de9c061f
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112966353"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Attribuer à une identité managée un accès à une ressource à l’aide du portail Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Après avoir configuré une ressource Azure avec une identité managée, vous pouvez accorder à cette dernière un accès à une autre ressource, tout comme n’importe quel principal de sécurité. Cet article montre comment accorder à l’identité managée d’une machine virtuelle ou d’un groupe de machines virtuelles identiques Azure un accès à un compte de stockage Azure, à l’aide du portail Azure.

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter la [différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#managed-identity-types)** .
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Utiliser Azure RBAC pour affecter à une identité managée l’accès à une autre ressource

Après avoir activé l’identité managée sur une ressource Azure, comme une [machine virtuelle Azure](qs-configure-portal-windows-vm.md) ou un [groupe de machines virtuelles identiques Azure](qs-configure-portal-windows-vmss.md) :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure sur lequel vous avez configuré l’identité managée.

2. Accédez à la ressource souhaitée sur laquelle vous voulez modifier le contrôle d’accès. Dans cet exemple, nous offrons à une machine virtuelle Azure l’accès à un compte de stockage auquel nous accédons.

1. Sélectionnez **Contrôle d’accès (IAM)** .

1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir la page Ajouter une attribution de rôle.

1. Sélectionnez le rôle et l’identité gérée. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).

    ![Page Ajouter une attribution de rôle dans le portail Azure.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)
     
## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de l’identité managée pour les ressources Azure](overview.md)
- Pour activer l’identité managée sur une machine virtuelle Azure, voir [Configurer des identités managées pour ressources Azure sur une machine virtuelle en utilisant le portail Azure](qs-configure-portal-windows-vm.md).
- Pour activer l’identité managée sur un groupe de machines virtuelles identiques Azure, voir [Configurer des identités managées pour ressources Azure sur un groupe de machines virtuelles identiques en utilisant le portail Azure](qs-configure-portal-windows-vmss.md).


