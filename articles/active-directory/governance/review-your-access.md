---
title: Réviser votre accès aux groupes et aux applications dans les révisions d’accès - Azure AD
description: Découvrez comment réviser votre propre accès à des groupes ou applications dans les révisions d’accès Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d75d7ffe28fa126ee5e359cba19e66e3c0f36e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422401"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Réviser votre accès à des groupes ou applications dans Azure AD

Azure Active Directory (Azure AD) simplifie la manière dont les entreprises gèrent l’accès aux applications ou groupes dans Azure AD et d’autres services en ligne Microsoft, avec une fonction appelée révision d’accès Azure AD.

Cet article décrit comment réviser votre propre accès à un groupe ou une application.

## <a name="open-the-access-review"></a>Ouvrir la révision d’accès

La première étape pour effectuer une révision d’accès consiste à rechercher et ouvrir la révision d’accès.

1. Recherchez un e-mail de Microsoft vous invitant à réviser les accès. Voici un exemple d’e-mail vous invitant à réviser votre accès à un groupe.

    ![Exemple d’e-mail de Microsoft pour la révision d’accès à un groupe](./media/review-your-access/access-review-email.png)

1. Cliquez sur le lien **Réviser l’accès** pour ouvrir la révision d’accès.

Si vous n’avez pas reçu l’e-mail, vous trouverez les révisions d’accès en attente en procédant comme suit.

1. Connectez-vous au portail MyApps sur [https://myapps.microsoft.com](https://myapps.microsoft.com).

    ![Liste des applications pour lesquelles vous disposez d’autorisations dans le portail MyApps](./media/review-your-access/myapps-access-panel.png)

1. En haut à droite de la page, cliquez sur le symbole d’utilisateur, qui affiche votre nom et organisation par défaut. Si plusieurs organisations sont listées, sélectionnez l’organisation qui a demandé une révision d’accès.

1. Sur le côté droit de la page, cliquez sur la vignette **Révisions d’accès** pour voir la liste des révisions d’accès en attente.

    Si la vignette n’est pas visible, cela signifie qu’il n’y a pas de révisions d’accès à effectuer pour cette organisation et qu’aucune action n’est nécessaire pour l’instant.

    ![Liste de révisions d’accès en attente pour vos applications et groupes](./media/review-your-access/access-reviews-list.png)

1. Cliquez sur le lien **Commencer la révision** associé à la révision d’accès que vous souhaitez effectuer.

## <a name="perform-the-access-review"></a>Effectuer la révision d’accès

Une fois que vous avez ouvert la révision d’accès, vous pouvez voir votre accès.

1. Révisez votre accès et déterminez si vous en avez toujours besoin.

    Si la requête vise à réviser l’accès d’autres utilisateurs, la page aura un aspect différent. Pour plus d’informations, consultez [Réviser l’accès à des groupes ou à des applications](perform-access-review.md).

    ![Ouvrir la révision d’accès en vous demandant si vous avez toujours besoin de l’accès à un groupe](./media/review-your-access/perform-access-review.png)

1. Cliquez sur **Oui** pour conserver votre accès ou sur **Non** pour supprimer votre accès.

1. Si vous cliquez sur **Oui**, vous devrez peut-être spécifier une justification dans la zone **Raison**.

    ![Révision d’accès déterminant si vous avez toujours besoin de l’accès à un groupe terminée](./media/review-your-access/perform-access-review-submit.png)

1. Cliquez sur **Envoyer**.

    Votre sélection est envoyée et vous revenez au portail MyApps.

    Si vous souhaitez modifier votre réponse, rouvrez la page de révisions d’accès et mettez à jour votre réponse. Vous pouvez modifier votre réponse à tout moment jusqu'à ce que la révision d’accès soit terminée.

    > [!NOTE]
    > Si vous avez indiqué que vous n’avez plus besoin de l’accès, votre accès n’est pas supprimé immédiatement. Vous êtes supprimé une fois la révision terminée ou lorsqu’un administrateur interrompt la révision.

## <a name="next-steps"></a>Étapes suivantes

- [Effectuer une révision d’accès de groupes ou révisions d’accès des applications dans Azure AD](complete-access-review.md)
