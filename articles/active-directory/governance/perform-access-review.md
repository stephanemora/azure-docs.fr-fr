---
title: Réviser l’accès à des groupes ou des applications dans les révisions d’accès - Azure Active Directory | Microsoft Docs
description: Découvrez comment réviser les accès des membres du groupe ou l’accès aux applications dans les révisions d’accès Azure Active Directory.
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
ms.date: 04/01/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d42fe972b79ecd9bcee65d0664c5d13da02d2238
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470608"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>Réviser l’accès à des groupes ou les révisions d’accès des applications dans Azure AD

Azure Active Directory (Azure AD) simplifie la façon dont les entreprises gèrent l’accès aux groupes et applications dans Azure AD et passe en revue des autres Services en ligne Microsoft avec une fonctionnalité appelée accès Azure AD.

Cet article décrit comment un réviseur désigné effectue une révision d’accès pour les membres d’un groupe ou les utilisateurs ayant accès à une application.

## <a name="open-the-access-review"></a>Ouvrez la révision d’accès

La première étape pour effectuer une révision d’accès consiste à rechercher et ouvrir la révision d’accès.

1. Recherchez un message électronique à partir de Microsoft qui vous invitant à réviser les accès. Voici un exemple d’e-mail pour passer en revue l’accès pour un groupe.

    ![E-mail de révision d’accès](./media/perform-access-review/access-review-email.png)

1. Cliquez sur le **démarrer révision** lien pour ouvrir la révision d’accès.

Si vous n’avez pas reçu l’e-mail, vous pouvez trouver que votre accès en attente passe en revue en suivant ces étapes.

1. Connectez-vous au portail MyApps à [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![Portail MyApps](./media/perform-access-review/myapps-access-panel.png)

1. En haut à droite de la page, cliquez sur le symbole d’utilisateur, qui affiche votre nom et organisation par défaut. Si plusieurs organisations sont listées, sélectionnez l’organisation qui a demandé une révision d’accès.

1. Cliquez sur le **révisions d’accès** vignette pour afficher une liste des révisions d’accès en attente.

    Si la vignette n’est pas visible, cela signifie qu’il n’y a pas de révisions d’accès à effectuer pour cette organisation et qu’aucune action n’est nécessaire pour l’instant.

    ![Liste des révisions d’accès](./media/perform-access-review/access-reviews-list.png)

1. Cliquez sur le **commencer la révision** lien pour la révision d’accès que vous souhaitez effectuer.

## <a name="perform-the-access-review"></a>Effectuer la révision d’accès

Une fois que vous avez ouvert la révision d’accès, vous voyez les noms des utilisateurs qui doivent être révisés.

Si la demande est de réviser son propre accès, la page aura un aspect différente. Pour plus d’informations, consultez [vous-même réviser l’accès à des groupes ou des applications](review-your-access.md).

![Effectuer la révision d’accès](./media/perform-access-review/perform-access-review.png)

Il existe deux méthodes que vous pouvez approuver ou refuser l’accès :

- Vous pouvez approuver ou refuser chaque demande individuellement, ou
- Vous pouvez accepter les recommandations système, qui est le moyen le plus simple et plus rapide.

### <a name="approve-or-deny-access-for-each-request"></a>Approuver ou refuser l’accès pour chaque demande

1. Passez en revue la liste des utilisateurs à décider s’il faut approuver ou refuser leur accès continu.

1. Pour approuver ou refuser chaque demande, cliquez sur la ligne pour ouvrir une fenêtre pour spécifier l’action à entreprendre.

1. Cliquez sur **approuver** ou **refuser**. Si vous n’êtes pas sûr, vous pouvez cliquer sur **ne sais pas**. Cela entraînera l’utilisateur conservant son accès, mais la sélection apparaîtront dans les journaux d’audit.

    ![Effectuer la révision d’accès](./media/perform-access-review/approve-deny.png)

1. Si nécessaire, entrez un motif dans la **raison** boîte.

    L’administrateur de la révision d’accès peut nécessiter que vous fournissez une raison pour l’approbation d’un accès permanent ou l’appartenance au groupe.

1. Une fois que vous avez spécifié l’action à entreprendre, cliquez sur **enregistrer**.

    Si vous souhaitez modifier votre réponse, sélectionnez la ligne et mettre à jour de la réponse. Par exemple, vous pouvez approuver un utilisateur précédemment refusé ou refuser un utilisateur accepté précédemment. Vous pouvez modifier votre réponse à tout moment jusqu'à ce que la révision d’accès s’est terminée.

    S’il existe plusieurs réviseurs, la dernière réponse envoyée est enregistrée. Prenons un exemple où un administrateur désigne deux relecteurs : Alice et Bob. Alice ouvre d’abord la révision d’accès et approuve les accès. Avant la fin de la révision, Bob ouvre la révision d’accès et refuse l’accès. La dernière refuser la réponse est ce qui est enregistré.

    > [!NOTE]
    > Si l’accès est refusé à un utilisateur, elles ne sont pas supprimées immédiatement. Ils sont supprimés lors de la révision est terminée ou lorsqu’un administrateur interrompt la révision.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Approuver ou refuser l’accès selon les recommandations

Pour rendre les révisions d’accès plus facile et plus rapide pour vous, nous fournissons également des recommandations que vous pouvez accepter en un seul clic. Les recommandations sont générées en fonction de l’activité de connexion de l’utilisateur.

1. Dans la barre bleue en bas de la page, cliquez sur **accepter les recommandations**.

    ![Accepter les recommandations](./media/perform-access-review/accept-recommendations.png)

    Vous consultez un résumé des actions recommandées.

    ![Accepter les recommandations de résumé](./media/perform-access-review/accept-recommendations-summary.png)

1. Cliquez sur **Ok** d’accepter les recommandations.

## <a name="next-steps"></a>Étapes suivantes

- [Effectuer une révision d’accès des groupes ou des applications](complete-access-review.md)
