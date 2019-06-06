---
title: Effectuer une révision d’accès de groupes ou d’applications - Azure Active Directory | Microsoft Docs
description: Découvrez comment effectuer une révision d’accès des membres du groupe ou l’accès aux applications dans les révisions d’accès Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/22/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec3909ffbb624284f999360140b7454098643062
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473371"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Effectuer une révision d’accès de groupes ou révisions d’accès des applications dans Azure AD

En tant qu’administrateur, vous [créer une révision d’accès des groupes ou des applications](create-access-review.md) et réviseurs [effectuer la révision d’accès](perform-access-review.md). Cet article décrit comment afficher les résultats de la révision d’accès et d’appliquer les résultats.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Conditions préalables

- Azure AD Premium P2
- Administrateur général, administrateur de l’utilisateur, administrateur de sécurité ou lecteur sécurité

Pour plus d’informations, consultez [les utilisateurs doivent disposer de licences ?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="view-an-access-review"></a>Afficher une révision d’accès

Vous pouvez suivre la progression à mesure que les réviseurs effectuent des révisions.

1. Connectez-vous pour le portail Azure et ouvrez le [page de gouvernance des identités](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Dans le menu de gauche, cliquez sur **révisions d’accès**.

1. Dans la liste, cliquez sur une révision d’accès.

    Pour afficher une série d’accès révisions, accédez à la révision d’accès, et vous trouverez les occurrences à venir dans les révisions planifiée.

    Sur le **vue d’ensemble** page, vous pouvez voir la progression. Aucun droit d’accès n’est modifiées dans le répertoire jusqu'à ce que la révision est terminée.

    ![Progression des révisions d’accès](./media/complete-access-review/overview-progress.png)

1. Si vous souhaitez arrêter une révision d’accès avant qu’il a atteint la date de fin planifiée, cliquez sur le **arrêter** bouton.

    Arrêter quand une révision, les réviseurs ne seront plus en mesure de donner des réponses. Vous ne pouvez pas redémarrer une révision ayant été arrêtée.

1. Si vous n’êtes plus intéressé par la révision d’accès, vous pouvez le supprimer en cliquant sur le **supprimer** bouton.

## <a name="apply-the-changes"></a>Appliquer les modifications

Si **appliquer automatiquement les résultats à ressource** était activé et en fonction des sélections effectuées dans **paramètres de saisie semi-automatique**, automatique-appliquer va être exécutée après la date de fin de la révision ou lorsque vous arrêtez manuellement la révision.

Si **appliquer automatiquement les résultats à ressource** n’a pas été activé pour la révision, cliquez sur **appliquer** pour appliquer manuellement les modifications. Si accès un utilisateur l’a été refusé lors de la révision, lorsque vous cliquez sur **appliquer**, Azure AD supprime leur attribution de l’appartenance ou d’application.

![Appliquer les modifications de révision d’accès](./media/complete-access-review/apply-changes.png)

L’état de la révision ne pourra **terminé** par le biais des états intermédiaires comme **application** et enfin à l’état **résultat appliqué**. Les utilisateurs dont l’accès est refusé doivent normalement perdre leur appartenance au groupe ou leur affectation d’applications au bout de quelques minutes.

Lorsque l’application automatique est configurée, l’option **Appliquer** n’aura pas d’effet sur un groupe provenant d’un répertoire local ou un groupe dynamique. Si vous souhaitez modifier un groupe qui provient d’un répertoire local, téléchargez les résultats et appliquez ces modifications à la représentation du groupe dans ce répertoire.

## <a name="retrieve-the-results"></a>Récupérer les résultats

Pour afficher les résultats d’une révision d’accès à usage unique, cliquez sur le **résultats** page. Pour afficher uniquement l’accès d’un utilisateur, dans la zone de recherche, tapez le nom complet ou le nom d’utilisateur principal d’un utilisateur dont l’accès a été révisé.

![Récupérer les résultats d’une révision d’accès](./media/complete-access-review/retrieve-results.png)

Pour afficher la progression d’une révision d’accès active qui est récurrent, cliquez sur le **résultats** page.

Pour afficher les résultats d’une instance finalisée d’une révision d’accès qui est récurrent, cliquez sur **passez en revue l’historique**, puis sélectionnez l’instance spécifique de la liste d’instances de révision d’accès terminée, en fonction de démarrage de l’instance et la date de fin. Les résultats de cette instance peuvent être obtenus à partir de la **résultats** page.

Pour récupérer tous les résultats d’une révision d’accès, cliquez sur le **télécharger** bouton. Le fichier CSV généré est consultable dans Excel ou dans d’autres programmes qui permettent d’ouvrir des fichiers CSV encodés UTF-8.

## <a name="remove-users-from-an-access-review"></a>Supprimer des utilisateurs à partir d’une révision d’accès

 Par défaut, un utilisateur supprimé conserve cet état dans Azure AD pendant 30 jours, au cours desquels il peut être restauré par un administrateur s’il y a lieu.  Au bout de 30 jours, cet utilisateur est définitivement supprimé.  En outre, à l’aide du portail Azure Active Directory, un Administrateur général peut explicitement [supprimer définitivement un utilisateur récemment supprimé](../fundamentals/active-directory-users-restore.md) avant que cette période arrive à son terme.  Une fois qu’un utilisateur a été définitivement supprimé, les données le concernant sont par la suite supprimées des révisions d’accès actives.  Les informations d’audit relatives aux utilisateurs supprimés sont conservées dans le journal d’audit.

## <a name="next-steps"></a>Étapes suivantes

- [Manage user access with Azure AD access reviews](manage-user-access-with-access-reviews.md) (Gérer l’accès des utilisateurs avec les révisions d’accès Azure AD)
- [Manage guest access with Azure AD access reviews](manage-guest-access-with-access-reviews.md) (Gérer l’accès des invités avec les révisions d’accès Azure AD)
- [Créer une révision d’accès des groupes ou des applications](create-access-review.md)
- [Create an access review of users in an Azure AD administrative role](../privileged-identity-management/pim-how-to-start-security-review.md) (Créer une révision d’accès pour les utilisateurs dans un rôle administratif d’Azure AD)
