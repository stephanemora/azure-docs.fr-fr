---
title: Gérer les utilisateurs exclus des stratégies d’accès conditionnel - Azure AD
description: Découvrez comment utiliser les révisions d’accès Azure Active Directory (Azure AD) pour gérer les utilisateurs exclus des stratégies d’accès conditionnel
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1d7bce940f2b614c239e8b5e5719d96da10a6c0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422714"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Utiliser les révisions d’accès Azure AD pour gérer les utilisateurs exclus des stratégies d’accès conditionnel

Dans l’idéal, tous les utilisateurs doivent respecter les stratégies d’accès pour sécuriser l’accès aux ressources de votre organisation. Toutefois, certains scénarios métiers nécessitent que vous fassiez des exceptions. Cet article décrit quelques exemples de situations pouvant exiger des exclusions et vous indique comment, en votre qualité d’administrateur informatique, vous pouvez gérer cette tâche, éviter d’avoir à superviser les exceptions de stratégie et fournir aux auditeurs la preuve du passage en revue régulier de ces exceptions à l’aide des révisions d’accès Azure Active Directory (Azure AD).

> [!NOTE]
> L’utilisation des révisions d’accès Azure AD requiert une licence payante ou d’essai Azure AD Premium P2 ou Enterprise Mobility + Security E5 valide. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Pourquoi exclure certains utilisateurs des stratégies ?

En tant qu’administrateur informatique, il est possible que vous utilisiez [l’accès conditionnel Azure AD](../conditional-access/overview.md) pour obliger les utilisateurs à s’authentifier à l’aide de l’authentification multifacteur (MFA) ou à se connecter à partir d’un réseau ou d’un appareil approuvés. Toutefois, lors de la planification du déploiement, vous découvrez que certaines de ces exigences ne peuvent pas être satisfaites par l’ensemble des utilisateurs. Par exemple, certains utilisateurs travaillent dans un bureau distant n’appartenant pas à votre réseau interne, ou un responsable utilise un ancien téléphone qui n’est pas pris en charge. L’entreprise a besoin que ces utilisateurs soient autorisés à se connecter et à effectuer leur travail ; ces derniers sont donc exclus des stratégies d’accès conditionnel.

Il est également possible que vous utilisiez des [emplacements nommés](../conditional-access/location-condition.md) dans l’accès conditionnel afin de configurer un ensemble de pays et de régions dont les locataires doivent rester inaccessibles aux utilisateurs.

![Emplacements nommés dans l’accès conditionnel](./media/conditional-access-exclusion/named-locations.png)

Toutefois, dans certains cas, les utilisateurs peuvent avoir un motif légitime de se connecter à partir de ces pays/régions bloqués. Par exemple, ces utilisateurs peuvent être en déplacement pour des raisons professionnelles ou personnelles. Dans ce cas, la stratégie d’accès conditionnel destinée à bloquer ces pays/régions peut comporter un groupe de sécurité cloud dédié pour les utilisateurs qui sont exclus de la stratégie. Les utilisateurs qui nécessitent un accès dans le cadre de leurs déplacements peuvent alors s’ajouter à ce groupe à l’aide de la fonctionnalité de [gestion de groupes en libre-service Azure AD](../users-groups-roles/groups-self-service-management.md).

Voici un troisième exemple : vous disposez d’une stratégie d’accès conditionnel qui [bloque l’authentification héritée pour la grande majorité de vos utilisateurs](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Microsoft vous recommande vivement de bloquer l’utilisation des protocoles hérités dans votre locataire afin de renforcer vos mesures de sécurité. Toutefois, si certains de vos utilisateurs ont absolument besoin d’utiliser des méthodes d’authentification héritées pour accéder à vos ressources par le biais de clients Office 2010 ou IMAP/SMTP/POP, vous pouvez exclure ces utilisateurs de la stratégie qui bloque les méthodes d’authentification héritées.

## <a name="why-are-exclusions-challenging"></a>Pourquoi les exclusions sont-elles complexes ?

Dans Azure AD, vous pouvez restreindre une stratégie d’accès conditionnel à un ensemble d’utilisateurs. Vous pouvez également exclure certains de ces utilisateurs en sélectionnant des rôles Azure AD, des utilisateurs spécifiques ou des utilisateurs invités. Gardez bien à l’esprit que lorsque ces exclusions sont configurées, l’intention de stratégie n’est pas applicable aux utilisateurs correspondants. Si ces exclusions ont été configurées sous la forme d’une liste d’utilisateurs spécifiques ou par le biais d’un groupe de sécurité local hérité, cela limite la visibilité de cette liste d’exclusions (les utilisateurs peuvent ignorer son existence), ainsi que le contrôle de l’administrateur informatique sur cette liste (les utilisateurs peuvent rejoindre le groupe de sécurité afin de contourner la stratégie). En outre, les utilisateurs qualifiés pour l’exclusion à un moment donné peuvent ne plus en avoir besoin ou ne plus y être éligibles par la suite.

Au début d’une exclusion, la liste des utilisateurs qui contournent la stratégie est brève. Cette liste s’allonge au fil du temps à mesure que le nombre d’utilisateurs exclus s’accroît. Il est donc nécessaire d’examiner la liste à un moment donné et de vérifier que chacun de ces utilisateurs doit toujours être exclu. La gestion de cette liste peut se révéler relativement simple d’un point de vue technique, mais vous devez déterminer qui prend les décisions métiers à ce sujet et vous assurer qu’elles sont toutes auditables.

En revanche, si vous configurez l’exclusion de la stratégie d’accès conditionnel par le biais d’un groupe Azure AD, vous pouvez utiliser les révisions d’accès en guise de contrôle de compensation afin d’améliorer la visibilité et de réduire le nombre d’utilisateurs qui disposent d’une exception.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Création d’un groupe d’exclusions dans une stratégie d’accès conditionnel

Suivez la procédure ci-après pour créer un groupe Azure AD et une stratégie d’accès conditionnel qui ne s’applique pas à ce groupe.

### <a name="create-an-exclusion-group"></a>Créer un groupe d’exclusions

1. Connectez-vous au portail Azure.

1. Dans le volet de navigation gauche, cliquez sur **Azure Active Directory**, puis sur **Groupes**.

1. Dans le menu supérieur, cliquez sur **Nouveau groupe** pour ouvrir le volet de groupe.

1. Dans la liste **Type de groupe**, sélectionnez **Sécurité**. Spécifiez un nom et une description.

1. Veillez à définir le type **Appartenance** sur **Attribuée**.

1. Sélectionnez les utilisateurs qui doivent faire partie de ce groupe d’exclusions, puis cliquez sur **Créer**.

    ![Volet de nouveau groupe d’Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Créer une stratégie d’accès conditionnel excluant le groupe

Vous pouvez à présent créer une stratégie d’accès conditionnel qui utilise ce groupe d’exclusions.

1. Dans le volet de navigation gauche, cliquez sur **Azure Active Directory**, puis cliquez sur **Accès conditionnel** pour ouvrir le panneau **Stratégies**.

1. Cliquez sur **Nouvelle stratégie** pour ouvrir le volet **Nouveau**.

1. Spécifiez un nom.

1. Sous Affectations, cliquez sur **Utilisateurs et groupes**.

1. Dans l’onglet **Inclure**, sélectionnez **Tous les utilisateurs**.

1. Dans l’onglet **Exclure**, cochez la case **Utilisateurs et groupes**, puis cliquez sur **Sélectionner les utilisateurs exclus**.

1. Sélectionnez le groupe d’exclusions que vous avez créé.

    > [!NOTE]
    > Une bonne pratique consiste à exclure au moins un compte d’administrateur de la stratégie lors du test pour vous assurer que votre accès à votre locataire n’est pas verrouillé.

1. Continuez à configurer la stratégie d’accès conditionnel en fonction des exigences de votre organisation.

    ![Sélectionner le volet des utilisateurs exclus dans l’accès conditionnel](./media/conditional-access-exclusion/select-excluded-users.png)

Examinons deux exemples dans lesquels vous pouvez utiliser les révisions d’accès pour gérer les exclusions dans les stratégies d’accès conditionnel.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Exemple 1 : Révision d’accès relative aux utilisateurs accédant au locataire à partir de pays/régions bloqués

Supposons que vous disposiez d’une stratégie d’accès conditionnel qui bloque l’accès à partir de certains pays/régions. Vous avez créé un groupe qui est exclu de la stratégie. Voici une révision d’accès recommandée qui passe en revue les membres du groupe.

> [!NOTE]
> La création des révisions d’accès requiert un rôle Administrateur général ou un rôle Administrateur des utilisateurs.

1. La révision se reproduit chaque semaine.

2. La révision ne prend jamais fin, de sorte que ce groupe d’exclusions reste continuellement à jour.

3. La révision porte sur tous les membres de ce groupe.

4. Chaque utilisateur doit lui-même attester qu’il a toujours besoin d’un accès à partir des pays/régions bloqués, et qu’il doit donc toujours être membre du groupe.

5. Si l’utilisateur ne répond pas à la requête de révision, il est automatiquement supprimé du groupe et ne peut donc plus accéder au locataire pendant qu’il est en déplacement dans ces pays/régions.

6. Activez les notifications par e-mail afin que les utilisateurs soient avertis du démarrage et de l’achèvement de la révision d’accès.

    ![Volet Créer une révision d’accès pour l’exemple 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Exemple 2 : Révision d’accès relative aux utilisateurs accédant au locataire avec l’authentification héritée

Supposons que vous disposiez d’une stratégie d’accès conditionnel qui bloque l’accès des utilisateurs ayant recours à l’authentification héritée et à des versions de client antérieures. Vous avez créé un groupe qui est exclu de la stratégie. Voici une révision d’accès recommandée qui passe en revue les membres du groupe.

1. Cette révision doit être récurrente.

2. La révision doit porter sur tous les membres du groupe.

3. La révision peut être configurée pour répertorier les propriétaires de division en tant que réviseurs sélectionnés.

4. Appliquez automatiquement les résultats et supprimez les utilisateurs qui ne sont pas autorisés à poursuivre l’utilisation des méthodes d’authentification héritées.

5. Il peut être avantageux d’activer les suggestions de manière à faciliter la prise de décisions des réviseurs de groupes volumineux.

6. Activez les notifications par e-mail afin que les utilisateurs soient avertis du démarrage et de l’achèvement de la révision d’accès.

    ![Volet Créer une révision d’accès pour l’exemple 2](./media/conditional-access-exclusion/create-access-review-2.png)

**Conseil** : Si vous disposez de nombreux groupes d’exclusions et que vous devez donc créer plusieurs révisions d’accès, nous proposons désormais dans le point de terminaison bêta Microsoft Graph une API qui vous permet de créer et de gérer ces révisions par programmation. Pour commencer, consultez les [informations de référence sur les API des révisions d’accès Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root), ainsi que l’article [Example of retrieving Azure AD access reviews via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096) (Exemple de récupération de révisions d’accès Azure AD par le biais de Microsoft Graph).

## <a name="access-review-results-and-audit-logs"></a>Résultats des révisions d’accès et journaux d’audit

Une fois que vous avez mis en place tous les éléments requis (groupe, stratégie d’accès conditionnel et révisions d’accès), vous devez surveiller et suivre les résultats de ces révisions.

1. Dans le Portail Azure, ouvrez le panneau **Révisions d’accès**.

1. Ouvrez le contrôle et le programme que vous avez créés pour gérer le groupe d’exclusions.

1. Cliquez sur **Résultats** pour découvrir les utilisateurs qui ont été autorisés à rester dans la liste et ceux qui ont été supprimés.

    ![Les résultats des révisions d’accès affichent les utilisateurs approuvés](./media/conditional-access-exclusion/access-reviews-results.png)

1. Ensuite, cliquez sur **Journaux d’audit** pour visualiser les actions qui ont été exécutées durant cette révision.

    ![Journaux d’audit des révisions d’accès répertoriant les actions](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

En tant qu’administrateur informatique, vous savez que la gestion des groupes d’exclusions de vos stratégies se révèle parfois inévitable. Toutefois, les révisions d’accès Azure AD contribuent à faciliter la mise à jour de ces groupes, leur passage en revue à intervalles réguliers par le propriétaire de la division ou par les utilisateurs eux-mêmes, ainsi que l’audit de ces modifications.

## <a name="next-steps"></a>Étapes suivantes

- [Créer une révision d’accès de groupes ou d’applications](create-access-review.md)
- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](../conditional-access/overview.md)
