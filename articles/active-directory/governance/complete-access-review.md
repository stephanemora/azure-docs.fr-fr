---
title: Effectuer une révision d’accès des groupes et applications - Azure AD
description: Découvrez comment effectuer une révision d’accès pour des membres de groupes ou pour l’accès aux applications dans les révisions d’accès Azure Active Directory.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 02/08/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65ccc68585e127741a0acffde54273ae496aea47
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124817972"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Effectuer une révision d’accès des groupes et applications dans les révisions d’accès Azure AD

En tant qu’administrateur, vous [créez une révision d’accès de groupes ou d’applications](create-access-review.md) tandis que les réviseurs [effectuent la révision d’accès](perform-access-review.md). Cet article explique comment afficher les résultats de la révision d’accès et les appliquer.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Conditions préalables requises

- Azure AD Premium P2
- Administrateur général, administrateur d’utilisateurs, administrateur de la sécurité ou lecteur Sécurité

Pour plus d’informations, consultez [Exigences des licences](access-reviews-overview.md#license-requirements).

## <a name="view-an-access-review"></a>Afficher une révision d’accès

Vous pouvez suivre la progression des révisions effectuées par les réviseurs.

1. Connectez-vous au portail Azure et ouvrez la page [Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Dans le menu de gauche, cliquez sur **Révisions d’accès**.

1. Dans la liste, cliquez sur une révision d’accès.

    Pour afficher les futures instances d’une révision d’accès, accédez à la révision d’accès et sélectionnez Révisions planifiées.

    La page **Vue d’ensemble** affiche la progression de l’instance actuelle. Aucun droit d’accès n’est modifié dans le répertoire avant que la révision ne soit terminée.

     ![Révision du groupe All Company](./media/complete-access-review/all-company-group.png)

    Les entrées sous Current ne sont visibles que pendant la durée de chaque instance de révision. 

    La page Results fournit des informations supplémentaires sur chaque utilisateur révisé dans l’instance, dont la possibilité d’arrêter, de réinitialiser et de télécharger les résultats.

    ![Réviser l’accès invité dans les groupes Microsoft 365](./media/complete-access-review/all-company-group-results.png)


    Si vous affichez une révision d’accès qui révise l’accès invité dans les groupes Microsoft 365, le panneau Vue d’ensemble répertorie chaque groupe dans la révision. 
   
    ![Réviser l’accès invité dans les groupes Microsoft 365](./media/complete-access-review/review-guest-access-across-365-groups.png)

    Cliquez sur un groupe pour connaître l’état d’avancement de la révision de ce groupe, ainsi que pour arrêter, réinitialiser, appliquer et supprimer la révision.

   ![Réviser l’accès invité dans les groupes Microsoft 365 en détail](./media/complete-access-review/progress-group-review.png)

1. Si vous souhaitez arrêter une révision d’accès avant qu’elle ait atteint la date de fin planifiée, cliquez sur le bouton **Arrêter**.

    Lorsque vous arrêtez une révision, les réviseurs ne peuvent plus donner de réponses. Vous ne pouvez pas redémarrer une révision ayant été arrêtée.

1. Si vous n’êtes plus intéressé par la révision d’accès, vous pouvez la supprimer en cliquant sur le bouton **Supprimer**.

## <a name="apply-the-changes"></a>Appliquer les modifications

Si l’option **Appliquer automatiquement les résultats à la ressource** a été activée sur la base de vos sélections dans **Paramètres de saisie semi-automatique**, l’application automatique est exécutée après la date de fin de la révision ou lorsque vous arrêtez celle-ci manuellement.

Si l’option **Appliquer automatiquement les résultats à la ressource** n’a pas été activée pour la révision, accédez **Historique des révisions** sous **Série** une fois la révision terminée ou arrêtée, puis cliquez sur l’instance de la révision que vous souhaitez appliquer.

![Appliquer des modifications à une révision d’accès](./media/complete-access-review/apply-changes.png)

Cliquez sur **Appliquer** pour appliquer les modifications. Si l’accès d’un utilisateur a été refusé dans la révision, lorsque vous cliquez sur **Appliquer**, Azure AD supprime l’appartenance de cet utilisateur ou son attribution à l’application.

![Bouton Appliquer les modifications de révision d’accès](./media/complete-access-review/apply-changes-button.png)


La révision passe alors de l’état **Terminé** à divers états intermédiaires, comme **Application en cours**, pour arriver enfin à l’état **Résultat appliqué**. Les utilisateurs dont l’accès est refusé doivent normalement perdre leur appartenance au groupe ou leur affectation d’applications au bout de quelques minutes.

L’application manuelle ou automatique des résultats est sans sur un groupe provenant d’un répertoire local ou un groupe dynamique. Si vous souhaitez modifier un groupe qui provient d’un répertoire local, téléchargez les résultats et appliquez ces modifications à la représentation du groupe dans ce répertoire.

## <a name="retrieve-the-results"></a>Récupérer les résultats

Pour voir les résultats d’une révision d’accès ponctuelle, cliquez sur la page **Résultats**. Pour voir uniquement l’accès d’un utilisateur, dans la zone de recherche, tapez le nom d’affichage ou le nom d’utilisateur principal d’un utilisateur dont l’accès a été refusé.

![Récupérer les résultats d’une révision d’accès](./media/complete-access-review/retrieve-results.png) 

Pour visualiser la progression d’une révision d’accès active récurrente, cliquez sur la page **Résultats**.

Pour visualiser les résultats d’une instance terminée d’une révision d’accès récurrente, cliquez sur **Historique des révisions**, puis choisissez l’instance concernée dans la liste des instances de révision d’accès terminées, en fonction des dates de début et de fin de l’instance. Vous pouvez obtenir les résultats de cette instance à partir de la page **Résultats**.

Pour récupérer tous les résultats d’une révision d’accès, cliquez sur le bouton **Télécharger**. Le fichier CSV généré est consultable dans Excel ou dans d’autres programmes qui permettent d’ouvrir des fichiers CSV encodés UTF-8.

## <a name="remove-users-from-an-access-review"></a>Supprimer des utilisateurs d’une révision d’accès

 Par défaut, un utilisateur supprimé conserve cet état dans Azure AD pendant 30 jours, au cours desquels il peut être restauré par un administrateur s’il y a lieu.  Au bout de 30 jours, cet utilisateur est définitivement supprimé.  En outre, à l’aide du portail Azure Active Directory, un Administrateur général peut explicitement [supprimer définitivement un utilisateur récemment supprimé](../fundamentals/active-directory-users-restore.md) avant que cette période arrive à son terme.  Une fois qu’un utilisateur a été définitivement supprimé, les données le concernant sont par la suite supprimées des révisions d’accès actives.  Les informations d’audit relatives aux utilisateurs supprimés sont conservées dans le journal d’audit.

## <a name="next-steps"></a>Étapes suivantes

- [Manage user access with Azure AD access reviews](manage-user-access-with-access-reviews.md) (Gérer l’accès des utilisateurs avec les révisions d’accès Azure AD)
- [Gérer l’accès invité avec les révisions d’accès Azure AD](manage-guest-access-with-access-reviews.md)
- [Créer une révision d’accès de groupes ou d’applications](create-access-review.md)
- [Create an access review of users in an Azure AD administrative role](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md) (Créer une révision d’accès pour les utilisateurs dans un rôle administratif d’Azure AD)