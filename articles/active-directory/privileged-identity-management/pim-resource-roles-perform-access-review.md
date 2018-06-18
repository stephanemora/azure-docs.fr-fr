---
title: Effectuer une révision d’accès dans Privileged Identity Management pour les ressources Azure | Microsoft Docs
description: Ce document explique comment effectuer une révision d’accès dans PIM pour les ressources Azure, en fonction de leur rôle.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 10da40cf83dd39251033403a6a6913eb2e3b8b1d
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260849"
---
# <a name="perform-an-access-review-in-pim-according-to-resource-role"></a>Effectuer une vérification d’accès dans PIM, en fonction du rôle des ressources
Privileged Identity Management (PIM) pour les ressources Azure simplifie la gestion des accès privilégiés aux ressources dans Azure. 

Si vous avez un rôle d’administrateur, l’administrateur des rôles privilégiés de votre organisation est susceptible de vous demander régulièrement de confirmer que vous avez toujours besoin de ce rôle dans le cadre de votre travail. Vous pouvez recevoir un e-mail contenant un lien ou accéder directement au [portail Azure](https://portal.azure.com). Suivez les étapes décrites dans cet article pour effectuer un auto-examen de vos rôles attribués.

Si vous êtes un administrateur de rôle privilégié intéressé par les révisions d’accès, consultez [Comment démarrer une révision de sécurité](pim-resource-roles-start-access-review.md)pour plus de détails.

## <a name="add-the-privileged-identity-management-application"></a>Ajout de l’application Privileged Identity Management
Vous pouvez utiliser l’application Azure Active Directory (Azure AD) PIM dans le [Portail Azure](https://portal.azure.com/) pour effectuer votre révision. Si vous ne disposez pas de l’application sur votre portail, suivez ces étapes pour commencer.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Sélectionnez votre nom d’utilisateur dans le coin supérieur droit du Portail Azure, puis sélectionnez le répertoire que vous allez utiliser.
3. Sélectionnez **Tous les services** et utilisez la zone **Filtre** pour rechercher *Azure AD Privileged Identity Management*.
4. Cochez **Épingler au tableau de bord**, puis sélectionnez **Créer**. L’application PIM s’ouvre.

## <a name="approve-or-deny-access"></a>Approuver ou refuser l'accès
Lorsque vous acceptez ou refusez l’accès, vous indiquez simplement au réviseur si vous utilisez toujours ce rôle ou non. Choisissez **Approuver** si vous souhaitez conserver le rôle, ou **Refuser** si vous n’avez plus besoin de l’accès. La modification de l’état prendra effet une fois que le réviseur aura appliqué les résultats.

Procédez comme suit pour rechercher et terminer la révision de l’accès :
1. Accédez à l’application Azure AD PIM.
2. Sélectionnez le panneau **Réviser l’accès**.

   ![Capture d’écran de l’application PIM, avec sélection du panneau Réviser l’accès](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Sélectionnez la révision à terminer. 
4. Choisissez **Approuver** ou **Refuser**. Vous devrez peut-être motiver votre choix dans la zone **Indiquez une raison**.

   ![Capture d’écran de la page Détails de la révision](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
