---
title: Créer une révision d’accès de groupes ou d’applications - Azure Active Directory | Microsoft Docs
description: Découvrez comment créer une révision d’accès des membres du groupe ou l’accès aux applications dans les révisions d’accès Azure Active Directory.
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
ms.date: 05/21/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ef72f1649c3f3e0af7fba53b2e8dbcee49d4b59
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734560"
---
# <a name="create-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Créer une révision d’accès de groupes ou révisions d’accès des applications dans Azure AD

L’accès aux groupes et aux applications pour les employés et les invités change au fil du temps. Afin de réduire les risques associés aux affectations d’accès obsolètes, les administrateurs peuvent utiliser Azure Active Directory (Azure AD) pour créer des révision des accès pour les membres de groupes ou pour l’accès aux applications. Si vous devez régulièrement passer en revue les accès, vous pouvez aussi créer des révisions d’accès périodiques. Pour plus d’informations sur ces scénarios, consultez [Gérer l’accès des utilisateurs](manage-user-access-with-access-reviews.md) et [Gérer l’accès des invités](manage-guest-access-with-access-reviews.md).

Cet article décrit comment créer un ou plusieurs révisions d’accès pour les membres du groupe ou l’accès aux applications.

## <a name="prerequisites"></a>Conditions préalables

- Azure AD Premium P2
- Administrateur général ou Administrateur d’utilisateurs

Pour plus d’informations, consultez [les utilisateurs doivent disposer de licences ?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="create-one-or-more-access-reviews"></a>Créer un ou plusieurs révisions d’accès

1. Connectez-vous pour le portail Azure et ouvrez le [page de gouvernance des identités](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Dans le menu de gauche, cliquez sur **révisions d’accès**.

1. Cliquez sur **Nouvelle révision d’accès** pour créer une révision d’accès.

    ![Révision d’accès - Contrôles](./media/create-access-review/access-reviews.png)

1. Nommez la révision d’accès. Si vous le souhaitez, vous pouvez fournir une description de cette révision. Les réviseurs ont accès au nom et à la description de la révision.

    ![Créer une révision d’accès - Nom et description de la révision](./media/create-access-review/name-description.png)

1. Définissez la **Date de début**. Par défaut, une révision d’accès se produit une seule fois, démarre le jour même de sa création et s’arrête un mois plus tard. Vous pouvez modifier les dates de début et de fin pour que la révision d’accès démarre ultérieurement et dure le nombre de jours souhaité.

    ![Créer une révision d’accès - Dates de début et de fin](./media/create-access-review/start-end-dates.png)

1. Pour que la révision d’accès périodique, modifiez le **fréquence** définir à partir de **une fois** à **hebdomadaire**, **mensuel**,  **Tous les trimestres** ou **une fois par an**. Utilisez le **durée** curseur ou zone de texte pour définir le nombre de jours chaque révision de la série périodique sera ouvre pour l’entrée des réviseurs. Par exemple, la durée maximale d’une révision mensuelle est de 27 jours, ce qui permet d’éviter le chevauchement des révisions.

1. Utilisez le paramètre **Fin** pour spécifier comment mettre fin à la série de révisions d’accès récurrentes. Les séries accès récurrentes peuvent se terminer de trois façons : elles peuvent s’exécuter de façon continue pour démarrer des révisions indéfiniment, s’exécuter jusqu’à une date spécifique ou s’exécuter jusqu’à ce qu’un nombre défini d’occurrences se soient produites. Vous, un autre administrateur de l’utilisateur ou un autre administrateur général arrêter la série après la création en modifiant la date dans **paramètres**, afin qu’elle se termine à cette date.

1. Dans le **utilisateurs** section, spécifiez les utilisateurs qui la révision d’accès s’applique à. Les révisions d’accès peuvent porter sur les membres d’un groupe ou sur les utilisateurs qui ont été assignés à une application. Vous pouvez affiner davantage la révision d’accès pour passer en revue seulement les utilisateurs invités qui sont des membres (ou affectés à l’application) au lieu d’examiner tous les utilisateurs qui sont des membres ou qui ont accès à l’application.

    ![Créer une révision d’accès - Utilisateurs](./media/create-access-review/users.png)

1. Dans le **groupe** , sélectionnez un ou plusieurs groupes que vous souhaitez revoir l’appartenance de.

    > [!NOTE]
    > Sélection de plusieurs groupes créera plusieurs révisions d’accès. Par exemple, en sélectionnant les cinq groupes créera cinq révisions d’accès distincts.
    
    ![Créer une révision d’accès - sélectionner un groupe](./media/create-access-review/select-group.png)

1. Dans le **Applications** section (si vous avez sélectionné **affectés à une application** à l’étape 8), sélectionnez les applications que vous souhaitez revoir l’accès à.

    > [!NOTE]
    > Sélection de plusieurs applications créera plusieurs révisions d’accès. Par exemple, en sélectionnant cinq applications créera cinq révisions d’accès distincts.
    
    ![Créer une révision d’accès - sélectionner l’application](./media/create-access-review/select-application.png)

1. Dans la section **Réviseurs**, sélectionnez une ou plusieurs personnes pour passer en revue tous les utilisateurs concernés. Sinon, vous pouvez choisir de faire en sorte que les membres vérifient leur propre accès. Si la ressource est un groupe, vous pouvez demander aux propriétaires de groupe d’effectuer la révision. Vous pouvez également demander à ce que les réviseurs fournissent un motif lorsqu’ils approuvent l’accès.

    ![Créer une révision d’accès - Réviseurs](./media/create-access-review/reviewers.png)

1. Dans la section **Programmes**, sélectionnez le programme que vous voulez utiliser. L’option **Programme par défaut** est toujours présente.

    ![Créer une révision d’accès - Programmes](./media/create-access-review/programs.png)

    Vous pouvez simplifier le suivi et la collecte des révisions d’accès à des fins différentes en les organisant dans des programmes. Chaque révision d’accès peut être liée à un programme. Ainsi, lorsque vous préparez des rapports pour un auditeur, vous pouvez vous concentrer sur les révisions d’accès qui sont associées à une certaine initiative. Programmes et les résultats de révision d’accès sont visibles aux utilisateurs de l’administrateur général, administrateur de l’utilisateur, administrateur de sécurité ou rôle de lecteur de sécurité.

    Pour afficher la liste des programmes, accédez à l’accès révisions page et sélectionnez **programmes**. Si vous êtes un administrateur général ou un rôle d’utilisateur administrateur, vous pouvez créer des programmes supplémentaires. Par exemple, vous pouvez choisir de disposer d’un programme pour chaque initiative de conformité ou objectif de l’entreprise. Si vous n’avez plus besoin d’un programme et qu’il n’est lié à aucun contrôle, vous pouvez le supprimer.

### <a name="upon-completion-settings"></a>Paramètres de saisie semi-automatique

1. Pour spécifier ce qui se passe après la fin d’une révision, développez la section **Paramètres de saisie semi-automatique**.

    ![Paramètres de saisie semi-automatique](./media/create-access-review/upon-completion-settings.png)

1. Si vous voulez supprimer automatiquement l’accès pour les utilisateurs qui ont été refusés, définissez **Appliquer automatiquement les résultats à ressource** sur **Activer**. Si vous voulez appliquer manuellement les résultats quand la révision est terminée, cliquez sur **Désactiver**.

1. Utilisez la liste **Le réviseur ne doit pas répondre** pour spécifier ce qui se passe pour les utilisateurs qui ne sont pas vérifiés par le réviseur au cours de la révision. Ce paramètre n’affecte pas les utilisateurs qui ont été vérifiés manuellement par les réviseurs. Si la décision finale du réviseur est Refuser, l’accès de l’utilisateur est supprimé.

    - **Aucune modification** : laisser l’accès de l’utilisateur inchangé
    - **Supprimer l’accès** : supprimer l’accès de l’utilisateur
    - **Approuver l’accès** : approuver l’accès de l’utilisateur
    - **Accepter les recommandations** : accepter la recommandation du système sur le refus ou l’approbation de la prolongation de l’accès de l’utilisateur

### <a name="advanced-settings"></a>Paramètres avancés

1. Pour spécifier d’autres paramètres, développez la section **Paramètres avancés**.

    ![Paramètres avancés](./media/create-access-review/advanced-settings.png)

1. Définissez **Afficher les recommandations** sur **Activer** pour montrer aux réviseurs les recommandations du système basées sur les informations des accès de l’utilisateur.

1. Définissez **Exiger la raison lors de l’approbation** sur **Activer** afin d’exiger que le réviseur indique un motif pour l’approbation.

1. Définissez **Notifications par e-mail** sur **Activer** pour qu’Azure AD envoie des notifications par e-mail aux réviseurs quand une révision d’accès commence et aux administrateurs quand une révision se termine.

1. Définissez **Rappels** sur **Activer** pour qu’Azure AD envoie des rappels concernant les révisions d’accès en cours aux réviseurs qui n’ont pas terminé leur révision.

    Par défaut, Azure AD envoie automatiquement un rappel à mi-parcours de l’échéance fixée pour les réviseurs n’ayant pas encore répondu.

## <a name="start-the-access-review"></a>Démarrer la révision d’accès

Une fois que vous avez spécifié les paramètres pour une révision d’accès, cliquez sur **Démarrer**. La révision d’accès s’affiche dans votre liste avec un indicateur de son état.

![Liste des révisions d’accès](./media/create-access-review/access-reviews-list.png)

Par défaut, Azure AD envoie un e-mail aux réviseurs peu de temps après le démarrage de la révision. Si vous ne souhaitez pas qu’Azure AD envoie cet e-mail, veillez à informer les réviseurs qu’une révision d’accès leur a été assignée. Vous pouvez leur montrer les instructions pour savoir comment [réviser l’accès à des groupes ou des applications](perform-access-review.md). Si la révision s’adresse aux invités de revoir leur propre accès, donnez-leur des instructions pour savoir comment [vous-même réviser l’accès à des groupes ou des applications](review-your-access.md).

Si vous avez affecté des invités en tant que réviseurs et ils n’ont pas accepté l’invitation, ils ne recevra un e-mail de révisions d’accès, car ils doivent tout d’abord accepter l’invitation avant l’examen.

## <a name="create-reviews-via-apis"></a>Créer des révisions via des API

Vous pouvez également créer des révisions d’accès avec des API. Ce que vous faites pour gérer les révisions d’accès des groupes et des utilisateurs d’applications dans le portail Azure peut également être effectué avec les API Microsoft Graph. Pour plus d’informations, consultez le [référence de l’API des révisions d’accès d’Azure AD](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Pour obtenir un exemple de code, consultez [passe en revue de l’exemple de récupération de l’accès Azure AD via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Étapes suivantes

- [Réviser l’accès à des groupes ou des applications](perform-access-review.md)
- [Vous-même réviser l’accès à des groupes ou des applications](review-your-access.md)
- [Effectuer une révision d’accès des groupes ou des applications](complete-access-review.md)
