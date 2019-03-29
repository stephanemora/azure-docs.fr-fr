---
title: Vous-même réviser l’accès à des groupes ou des applications dans les révisions d’accès - Azure Active Directory | Microsoft Docs
description: Découvrez comment réviser son propre accès à des groupes ou des applications dans les révisions d’accès Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fe2013ff84dd0451fed7d108539606520cb9403
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576307"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Vous-même réviser l’accès à des groupes ou les révisions d’accès des applications dans Azure AD

Azure Active Directory (Azure AD) simplifie la façon dont les entreprises gèrent l’accès à des groupes ou des applications dans Azure AD et passe en revue des autres Services en ligne Microsoft avec une fonctionnalité appelée accès Azure AD.

Cet article décrit comment réviser son propre accès à un groupe ou une application.

## <a name="open-the-access-review"></a>Ouvrez la révision d’accès

La première étape pour effectuer une révision d’accès consiste à rechercher et ouvrir la révision d’accès.

1. Recherchez un message électronique à partir de Microsoft qui vous invitant à réviser les accès. Voici un exemple d’e-mail pour passer en revue votre accès à un groupe.

    ![E-mail de révision d’accès](./media/review-your-access/access-review-email.png)

1. Cliquez sur le **réviser les accès** lien pour ouvrir la révision d’accès.

Si vous n’avez pas reçu l’e-mail, vous pouvez trouver que votre accès en attente passe en revue en suivant ces étapes.

1. Connectez-vous au portail MyApps à [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![Portail MyApps](./media/review-your-access/myapps-access-panel.png)

1. En haut à droite de la page, cliquez sur le symbole d’utilisateur, qui affiche votre nom et organisation par défaut. Si plusieurs organisations sont listées, sélectionnez l’organisation qui a demandé une révision d’accès.

1. Sur le côté droit de la page, cliquez sur le **révisions d’accès** vignette pour afficher une liste des révisions d’accès en attente.

    Si la vignette n’est pas visible, cela signifie qu’il n’y a pas de révisions d’accès à effectuer pour cette organisation et qu’aucune action n’est nécessaire pour l’instant.

    ![Liste des révisions d’accès](./media/review-your-access/access-reviews-list.png)

1. Cliquez sur le **commencer la révision** lien pour la révision d’accès que vous souhaitez effectuer.

## <a name="perform-the-access-review"></a>Effectuer la révision d’accès

Une fois que vous avez ouvert la révision d’accès, vous pouvez voir votre accès.

1. Révision d’accès et décidez si vous devez toujours avoir accès.

    Si la demande est de réviser l’accès d’autres utilisateurs, la page aura un aspect différente. Pour plus d’informations, consultez [réviser l’accès à des groupes ou des applications](perform-access-review.md).

    ![Effectuer la révision d’accès](./media/review-your-access/perform-access-review.png)

1. Cliquez sur **Oui** pour conserver votre accès ou cliquez sur **non** pour supprimer votre accès.

1. Si vous cliquez sur **Oui**, vous devrez peut-être spécifier une justification dans le **raison** boîte.

    ![Effectuer la révision d’accès](./media/review-your-access/perform-access-review-submit.png)

1. Cliquez sur **Envoyer**.

    Votre sélection est envoyée et vous retournés au portail MyApps.

    Si vous souhaitez modifier votre réponse, ouvrez à nouveau la page de révisions d’accès et mettre à jour votre réponse. Vous pouvez modifier votre réponse à tout moment jusqu'à ce que la révision d’accès s’est terminée.

    > [!NOTE]
    > Si vous avez indiqué que vous n’avez plus accès, vous ne sont pas supprimées immédiatement. Vous êtes supprimé lors de la révision est terminée ou lorsqu’un administrateur interrompt la révision.

## <a name="next-steps"></a>Étapes suivantes

- [Effectuer une révision d’accès des groupes ou des applications](complete-access-review.md)
