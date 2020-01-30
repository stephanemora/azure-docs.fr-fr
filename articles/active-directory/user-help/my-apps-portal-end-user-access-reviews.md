---
title: Gérer l’accès à votre organisation aux applications et groupes - Azure AD
description: Découvrez comment exécuter une révision d’accès pour gérer l’accès à la sécurité des applications et groupes de votre organisation à partir du portail Mes applications.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e739024686bdac497b9b7dd450c5ed46e3cf9a63
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705018"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Effectuer une révision d’accès à partir du portail Mes applications

Vous pouvez utiliser votre compte professionnel ou scolaire avec le portail web **Mes applications** pour voir et démarrer la plupart des applications cloud de votre organisation, mettre à jour votre profil et vos informations de compte, consulter les informations de vos **groupes** et effectuer des **révisions d’accès** pour vos applications et vos groupes. Si vous n’avez pas accès au portail **Mes applications**, contactez le support technique pour obtenir l’autorisation.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ce contenu est destiné aux utilisateurs. Si vous êtes administrateur, vous trouverez des informations sur la configuration et la gestion de vos applications cloud dans la [documentation sur la gestion des applications](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Gérer les révisions d’accès

Si votre administrateur vous a autorisé à effectuer vos propres révisions d’accès, vous pouvez gérer l’accès à vos groupes ou applications à partir de la vignette **Révisions d’accès** sur la page du portail **Mes applications**.

>[!Note]
>Si vous ne voyez pas la vignette **Révisions d’accès**, cela signifie que vous n’êtes pas autorisé à effectuer des révisions d’accès ou que vous n’avez pas de révisions en attente de votre approbation. Si vous pensez que vous devez avoir accès à la vignette, contactez votre support technique pour obtenir une assistance.

### <a name="to-perform-your-access-reviews"></a>Pour effectuer vos révisions d’accès

1. Connectez-vous à votre compte professionnel ou scolaire.

2. Ouvrez votre navigateur web et accédez à https://myapps.microsoft.com, ou utilisez le lien fourni par votre organisation. Par exemple, vous pouvez être dirigé vers une page personnalisée de votre organisation, comme https://myapps.microsoft.com/contoso.com.

    La page **Applications** s'affiche, avec toutes les applications cloud de votre organisation que vous pouvez utiliser.

    ![Page Applications du portail Mes applications](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Sélectionnez la vignette **Révisions d’accès** pour afficher la liste des révisions d’accès en attente de votre approbation.

    ![Page Révisions d’accès avec des révisions d’accès en attente pour l’organisation](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Sélectionnez **Commencer la révision** pour démarrer votre révision d’accès.

5. Révisez votre accès et déterminez s’il est toujours nécessaire.

    ![Page Révision d’accès présentant les détails de la révision](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Si vous êtes un administrateur autorisé à réviser l’accès de votre organisation à des groupes et applications, vous verrez une page différente. Pour plus d’informations sur la révision de groupes ou d’applications pour votre organisation, voir [Réviser l’accès à des groupes ou applications dans les Révisions d’accès Azure AD](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Sélectionnez **Oui** conserver votre accès ou **Non** pour supprimer votre accès.

    Si vous sélectionnez **Oui**, vous devrez peut-être spécifier une justification dans la zone **Raison**.

    ![Page Révision d’accès affichant la zone Raison avec exemple de texte](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Sélectionnez **Envoyer**.

    Votre révision d’accès est terminée et vous revenez au portail **Mes applications**.

    >[!Note]
    >Vous pouvez modifier votre accès à tout moment jusqu’à la fin de votre période de révision d’accès. Si vous supprimez votre accès à une application ou à un groupe, cet accès n’est pas supprimé immédiatement. La suppression se produit à la fin de la période de révision d’accès ou quand un administrateur ferme la révision.

## <a name="next-steps"></a>Étapes suivantes

- [Accéder aux applications et les utiliser sur le portail Mes applications](my-apps-portal-end-user-access.md).

- [Modifier vos informations de profil](my-apps-portal-end-user-update-profile.md).

- [Consulter et mettre à jour vos informations relatives aux groupes](my-apps-portal-end-user-groups.md).
