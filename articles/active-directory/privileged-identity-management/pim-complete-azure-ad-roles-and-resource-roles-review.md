---
title: Terminer une révision d’accès des rôles de ressources Azure et des rôles Azure AD dans PIM – Azure AD | Microsoft Docs
description: Découvrez comment terminer une révision d’accès des rôles de ressources Azure et des rôles Azure AD dans Privileged Identity Management dans Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 9/3/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 614f45e4d83ff815bae0020b354b1b10d2812452
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452633"
---
# <a name="complete-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>Terminer une révision d’accès des rôles de ressources Azure et des rôles Azure AD dans PIM

Les administrateurs de rôle privilégié peuvent examiner l’accès privilégié dès le [démarrage d’une révision d’accès](pim-create-azure-ad-roles-and-resource-roles-review.md). Privileged Identity Management (PIM) dans Azure Active Directory (Azure AD) enverra automatiquement un e-mail invitant les utilisateurs à réviser leur accès. Si un utilisateur ne reçoit pas d’e-mail, vous pouvez lui envoyer les instructions relatives à l’[exécution d’une révision d’accès](pim-perform-azure-ad-roles-and-resource-roles-review.md).

Une fois la révision créée, suivez les étapes de cet article pour terminer la révision et voir les résultats.

## <a name="complete-access-reviews"></a>Terminer des révisions d’accès

1. Connectez-vous au [portail Azure](https://portal.azure.com/). Pour les **ressources Azure**, accédez à **Privileged Identity Management** et sélectionnez **Ressources Azure** sous **Gérer** dans le tableau de bord. Pour **Rôles Azure AD**, sélectionnez **Rôles Azure AD** dans le même tableau de bord.

2. Pour les **ressources Azure**, sélectionnez votre ressource sous **Ressources Azure**, puis sélectionnez **Révisions d’accès** dans le tableau de bord. Pour **Rôles Azure AD**, passez directement aux **révisions d’accès** sur le tableau de bord.

3. Sélectionnez la révision d’accès que vous souhaitez gérer. Vous trouverez ci-dessous un exemple de capture d’écran de la vue d’ensemble **Révisions d’accès** pour les **ressources Azure** et les **rôles Azure AD**.

    :::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-azure-ad-roles-home-list.png" alt-text="Capture d’écran de la liste des révisions d’accès indiquant le rôle, le propriétaire, la date de début, la date de fin et l’état." lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-azure-ad-roles-home-list.png":::

Sur la page de détails, les options suivantes sont disponibles pour la gestion de la révision des **ressources Azure** et des **rôles Azure AD** :

![Capture d’écran des options de gestion d’une révision dans les ressources Azure : Arrêter, Rétablir, Appliquer, Supprimer.](media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-menu.png)

### <a name="stop-an-access-review"></a>Arrêter une révision d’accès

Toutes les révisions d’accès ont une date de fin, mais vous pouvez utiliser le bouton **Arrêter** pour les terminer plus tôt. Le bouton **Arrêter** est sélectionnable uniquement lorsque l’instance de révision est active. Vous ne pouvez pas redémarrer une révision arrêtée.

### <a name="reset-an-access-review"></a>Rétablir une révision d’accès

Lorsque l’instance de révision est active et qu’au moins une décision a été prise par les réviseurs, vous pouvez rétablir la révision d’accès en sélectionnant le bouton **Rétablir** pour supprimer toutes les décisions qui ont été prises à son sujet. Une fois la révision d’accès réinitialisée, tous les utilisateurs sont à nouveau marqués comme non révisés.

### <a name="apply-an-access-review"></a>Appliquer une révision d’accès

Lorsqu’une révision d’accès est terminée, soit parce que vous avez atteint la date de fin, soit parce que vous l’avez arrêtée manuellement, le bouton **Appliquer** supprime l’accès des utilisateurs auxquels l’accès au rôle a été refusé. Si l’accès d’un utilisateur a été refusé lors de la révision, cette étape supprimera son attribution de rôle. Si le paramètre **Application automatique** est configuré lors de la création de la révision, ce bouton sera toujours désactivé, car la révision sera appliquée automatiquement et non manuellement.

### <a name="delete-an-access-review"></a>Supprimer une révision d’accès

Si vous n'êtes plus intéressé par la révision, supprimez-la. Pour supprimer la révision d’accès du service Privileged Identity Management, sélectionnez le bouton **Supprimer**.

> [!IMPORTANT]
> Vous ne serez pas obligé de confirmer cette modification destructrice, donc vérifiez que vous souhaitez supprimer cette révision.

## <a name="results"></a>Résultats

Sur la page **Résultats**, vous pouvez afficher et télécharger la liste des résultats de votre révision.

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-results.png" alt-text="Capture d’écran de la page Résultats répertoriant les utilisateurs, la décision, la raison, les réviseurs, les personnes ayant appliqué le résultat pour les rôles Azure AD." lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-results.png":::

> [!Note]
> Les **rôles Azure AD** ont un concept de groupes assignables à un rôle, où un groupe peut être affecté au rôle. Dans ce cas, le groupe apparaîtra dans la révision au lieu de développer les membres du groupe, et un réviseur approuvera ou refusera le groupe entier.

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-results.png" alt-text="Capture d’écran de la page Résultats répertoriant les utilisateurs, la décision, la raison, les réviseurs, les personnes ayant appliqué le résultat pour les rôles de ressources Azure." lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-results.png":::

> [!Note]
>Si un groupe est affecté à des **rôles de ressources Azure**, le réviseur du rôle de ressources Azure verra la liste développée des utilisateurs d’un groupe imbriqué. Si un réviseur refuse un membre d’un groupe imbriqué, le résultat de ce refus ne sera pas appliqué correctement, car l’utilisateur n’est pas supprimé du groupe imbriqué.

## <a name="reviewers"></a>Réviseurs

Sur la page **Réviseurs**, vous pouvez visualiser et ajouter des réviseurs à votre révision d’accès existante. Vous pouvez également rappeler aux réviseurs de terminer leurs révisions ici.

> [!Note]
> Si le type de réviseur sélectionné est un utilisateur ou un groupe, vous pouvez à tout moment ajouter d’autres utilisateurs ou d’autres groupes comme réviseurs principaux. Vous pouvez également supprimer des réviseurs principaux à tout moment. Si le type de réviseur est Manager, vous pouvez ajouter des utilisateurs ou des groupes en tant que réviseurs de secours pour terminer les révisions des utilisateurs qui n’ont pas de manager. Les réviseurs de secours ne peuvent pas être supprimés.

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-reviewers.png" alt-text="Capture d’écran de la page Réviseurs listant le nom et le nom d’utilisateur principal pour les rôles de ressources Azure." lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-reviewers.png":::

## <a name="next-steps"></a>Étapes suivantes

- [Créer une révision d’accès des rôles de ressources Azure et des rôles Azure AD dans PIM](pim-create-azure-ad-roles-and-resource-roles-review.md)
- [Effectuer une révision d’accès des rôles de ressources Azure et des rôles Azure AD dans PIM](pim-perform-azure-ad-roles-and-resource-roles-review.md)
