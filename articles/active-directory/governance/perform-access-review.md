---
title: Réviser l'accès aux groupes et aux applications dans les révisions d'accès - Azure AD
description: Découvrez comment réviser l’accès des membres de groupes ou des applications dans les révisions d’accès Azure Active Directory.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4125e82dd5176f01de294011e22a1d66005094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128453"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Réviser l'accès aux groupes et aux applications dans les révisions d'accès Azure AD

Azure Active Directory (Azure AD) simplifie la manière dont les entreprises gèrent l’accès aux applications et aux groupes dans Azure AD, ainsi que d’autres services en ligne Microsoft, avec une fonctionnalité appelée révision d’accès Azure AD.

Cet article décrit comment un réviseur désigné effectue une révision d’accès pour les membres d’un groupe ou les utilisateurs ayant accès à une application.

## <a name="open-the-access-review"></a>Ouvrir la révision d’accès

La première étape pour effectuer une révision d’accès consiste à rechercher et ouvrir la révision d’accès.

1. Recherchez un e-mail de Microsoft vous invitant à réviser les accès. Voici un exemple d’e-mail vous invitant à réviser l’accès d’un groupe.

    ![Exemple d’e-mail de Microsoft pour la révision d’accès d’un groupe](./media/perform-access-review/access-review-email.png)

1. Cliquez sur le lien **Commencer une révision d'accès** pour ouvrir la révision d’accès.

Si vous n’avez pas reçu l’e-mail, vous trouverez les révisions d’accès en attente en procédant comme suit.

1. Connectez-vous au portail MyApps sur [https://myapps.microsoft.com](https://myapps.microsoft.com).

    ![Liste des applications pour lesquelles vous disposez d’autorisations dans le portail MyApps](./media/perform-access-review/myapps-access-panel.png)

1. En haut à droite de la page, cliquez sur le symbole d’utilisateur, qui affiche votre nom et organisation par défaut. Si plusieurs organisations sont listées, sélectionnez l’organisation qui a demandé une révision d’accès.

1. Cliquez sur la vignette **Révisions d’accès** pour voir la liste des révisions d’accès en attente.

    Si la vignette n’est pas visible, cela signifie qu’il n’y a pas de révisions d’accès à effectuer pour cette organisation et qu’aucune action n’est nécessaire pour l’instant.

    ![Liste de révisions d’accès en attente pour les applications et les groupes](./media/perform-access-review/access-reviews-list.png)

1. Cliquez sur le lien **Commencer la révision** associé à la révision d’accès que vous souhaitez effectuer.

## <a name="perform-the-access-review"></a>Effectuer la révision d’accès

Une fois que vous avez ouvert la révision d’accès, vous obtenez les noms des utilisateurs qui doivent être révisés.

Si la requête vise à réviser votre propre accès, la page aura un aspect différent. Pour plus d’informations, consultez [Réviser son accès à des groupes ou à des applications](review-your-access.md).

![Liste de révision d’accès ouverte répertoriant les utilisateurs à réviser](./media/perform-access-review/perform-access-review.png)

Il y a deux manières d’approuver ou de refuser l’accès  :

- Vous pouvez approuver ou refuser l’accès pour un ou plusieurs utilisateurs.
- Vous pouvez accepter les recommandations du système, ce qui est le moyen le plus simple et le plus rapide.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Approuver ou refuser l’accès pour un ou plusieurs utilisateurs

1. Passez en revue la liste des utilisateurs pour décider s’il faut approuver ou refuser leur accès permanent.

1. Pour approuver ou refuser l’accès pour un seul utilisateur, cliquez sur la ligne pour ouvrir une fenêtre et spécifier l’action à entreprendre. Pour approuver ou refuser l’accès pour plusieurs utilisateurs, ajoutez des coches en regard des utilisateurs, puis sur le bouton **Réviser X utilisateur(s)** pour ouvrir une fenêtre et spécifier l’action à entreprendre.

1. Cliquez sur **Approuver** ou **Refuser**. Si vous n’êtes pas sûr, vous pouvez cliquer sur **Je ne sais pas**. L’utilisateur gardera ainsi son accès, mais la sélection apparaîtra dans les journaux d’audit.

    ![Fenêtre d’action qui inclut les options Approuver, Refuser et Je ne sais pas](./media/perform-access-review/approve-deny.png)

1. Si nécessaire, indiquez un **motif**.

    L’administrateur de la révision d’accès peut demander que vous fournissiez un motif en cas d’acceptation d’un accès permanent ou d’une appartenance à un groupe.

1. Une fois que vous avez spécifié l’action à entreprendre, cliquez sur **Enregistrer**.

    Si vous souhaitez modifier votre réponse, sélectionnez la ligne et mettez à jour la réponse. Par exemple, vous pouvez approuver un utilisateur précédemment refusé ou refuser un utilisateur précédemment accepté. Vous pouvez modifier votre réponse à tout moment jusqu'à ce que la révision d’accès soit terminée.

    S’il existe plusieurs réviseurs, la dernière réponse envoyée est enregistrée. Prenons un exemple où un administrateur désigne deux réviseurs : Alice et Bob. Alice ouvre d’abord la révision d’accès et approuve l’accès. Avant la fin de la révision, Bob ouvre la révision d’accès et refuse l’accès. Le dernier refus est la réponse enregistrée.

    > [!NOTE]
    > Si un utilisateur se voit refuser l’accès, il n’est pas supprimé immédiatement. Il est supprimé une fois la révision terminée ou lorsqu’un administrateur interrompt la révision.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Approuver ou refuser l’accès selon les recommandations

Pour rendre les révisions d’accès plus faciles et plus rapides pour vous, nous fournissons également des recommandations que vous pouvez accepter en un seul clic. Les recommandations sont générées en fonction de l’activité de connexion de l’utilisateur.

1. Dans la barre bleue en bas de la page, cliquez sur **Accepter les recommandations**.

    ![Liste de révision d’accès ouverte montrant le bouton Accepter les recommandations](./media/perform-access-review/accept-recommendations.png)

    Vous obtenez un résumé des actions recommandées.

    ![Fenêtre qui affiche un résumé des actions recommandées](./media/perform-access-review/accept-recommendations-summary.png)

1. Cliquez sur **OK** pour accepter les recommandations.

## <a name="next-steps"></a>Étapes suivantes

- [Effectuer une révision d’accès de groupes ou révisions d’accès des applications dans Azure AD](complete-access-review.md)
