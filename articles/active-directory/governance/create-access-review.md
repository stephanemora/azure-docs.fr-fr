---
title: Créer une révision d’accès pour les membres d’un groupe ou des utilisateurs ayant accès à une application avec Azure AD| Microsoft Docs
description: Découvrez comment créer une révision d’accès pour les membres d’un groupe ou des utilisateurs ayant accès à une application.
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
ms.date: 11/15/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 1c71333a49643647bae3730e4f14f26939388c4f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153741"
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Créer une révision d’accès des membres du groupe ou un accès aux applications avec Azure AD

L’accès aux groupes et aux applications pour les employés et les invités change au fil du temps. Afin de réduire les risques associés aux affectations d’accès obsolètes, les administrateurs peuvent utiliser Azure Active Directory (Azure AD) pour créer des révision des accès pour les membres de groupes ou pour l’accès aux applications. Si vous devez régulièrement passer en revue les accès, vous pouvez aussi créer des révisions d’accès périodiques. Pour plus d’informations sur ces scénarios, consultez [Gérer l’accès des utilisateurs](manage-user-access-with-access-reviews.md) et [Gérer l’accès des invités](manage-guest-access-with-access-reviews.md).

Cet article explique comment créer une nouvelle révision d’accès pour les membres de groupes ou pour l’accès aux applications.

## <a name="prerequisites"></a>Prérequis

- [Révisions d’accès activées](access-reviews-overview.md)
- Administrateur général ou Administrateur de compte

## <a name="create-an-access-review"></a>Créer une révision d’accès

1. Connectez-vous au portail Azure et ouvrez la [page Révisions d’accès](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Cliquez sur **Contrôles**.

1. Cliquez sur **Nouvelle révision d’accès** pour créer une révision d’accès.

    ![Révision d’accès - Contrôles](./media/create-access-review/controls.png)

1. Nommez la révision d’accès. Si vous le souhaitez, vous pouvez fournir une description de cette révision. Les réviseurs ont accès au nom et à la description de la révision.

    ![Créer une révision d’accès - Nom et description de la révision](./media/create-access-review/name-description.png)

1. Définissez la **Date de début**. Par défaut, une révision d’accès se produit une seule fois, démarre le jour même de sa création et s’arrête un mois plus tard. Vous pouvez modifier les dates de début et de fin pour que la révision d’accès démarre ultérieurement et dure le nombre de jours souhaité.

    ![Créer une révision d’accès - Dates de début et de fin](./media/create-access-review/start-end-dates.png)

1. Pour rendre une révision d’accès récurrente, changez le paramètre de **Fréquence** de **Une fois** en **Hebdomadaire**, **Mensuel**, **Trimestriel** ou **Annuel**, puis utilisez le curseur ou la zone de texte **Durée** pour définir le nombre de jours pendant lesquels chaque révision de la série récurrente doit être ouverte pour permettre aux réviseurs d’y entrer des informations. Par exemple, la durée maximale d’une révision mensuelle est de 27 jours, ce qui permet d’éviter le chevauchement des révisions.

1. Utilisez le paramètre **Fin** pour spécifier comment mettre fin à la série de révisions d’accès récurrentes. Les séries accès récurrentes peuvent se terminer de trois façons : elles peuvent s’exécuter de façon continue pour démarrer des révisions indéfiniment, s’exécuter jusqu’à une date spécifique ou s’exécuter jusqu’à ce qu’un nombre défini d’occurrences se soient produites. Un autre administrateur de comptes d’utilisateur, un autre administrateur général ou vous-même pouvez arrêter la série après sa création en modifiant la date définie dans les **Paramètres**, de façon à ce qu’elle s’arrête à cette date.

1. Dans la section **Utilisateurs**, spécifiez les utilisateurs auxquels s’applique cette révision d’accès. Les révisions d’accès peuvent porter sur les membres d’un groupe ou sur les utilisateurs qui ont été assignés à une application. Vous pouvez affiner davantage la révision d’accès pour passer en revue seulement les utilisateurs invités qui sont des membres (ou affectés à l’application) au lieu d’examiner tous les utilisateurs qui sont des membres ou qui ont accès à l’application.

    ![Créer une révision d’accès - Utilisateurs](./media/create-access-review/users.png)

1. Dans la section **Réviseurs**, sélectionnez une ou plusieurs personnes pour passer en revue tous les utilisateurs concernés. Sinon, vous pouvez choisir de faire en sorte que les membres vérifient leur propre accès. Si la ressource est un groupe, vous pouvez demander aux propriétaires de groupe d’effectuer la révision. Vous pouvez également demander à ce que les réviseurs fournissent un motif lorsqu’ils approuvent l’accès.

    ![Créer une révision d’accès - Réviseurs](./media/create-access-review/reviewers.png)

1. Dans la section **Programmes**, sélectionnez le programme que vous voulez utiliser. Vous pouvez simplifier le suivi et la collecte des révisions d’accès à des fins différentes en les organisant dans des programmes. Le **programme par défaut »** est toujours présent, mais vous pouvez aussi créer un autre programme. Par exemple, vous pouvez choisir de disposer d’un programme pour chaque initiative de conformité ou objectif de l’entreprise.

    ![Créer une révision d’accès - Programmes](./media/create-access-review/programs.png)

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

## <a name="start-the-access-review"></a>Démarrer la révision d’accès

Une fois que vous avez spécifié les paramètres pour une révision d’accès, cliquez sur **Démarrer**.

Par défaut, Azure AD envoie un e-mail aux réviseurs peu de temps après le démarrage de la révision. Si vous ne souhaitez pas qu’Azure AD envoie cet e-mail, veillez à informer les réviseurs qu’une révision d’accès leur a été assignée. Vous pouvez leur afficher des instructions relatives à la [révision d’accès](perform-access-review.md). Si votre révision s’adresse à des invités qui doivent réviser leur propre accès, donnez-leur des instructions sur la [façon de réviser son propre accès](perform-access-review.md).

Si certains réviseurs sont invités, ces derniers sont uniquement notifiés par e-mail s’ils ont déjà accepté leur invitation.

## <a name="manage-the-access-review"></a>Gestion de la révision d’accès

Vous pouvez suivre la progression des révisions des réviseurs dans le tableau de bord Azure AD, dans la section des **Révisions d’accès**. Aucun droit d’accès n’est modifié dans le répertoire avant que [la révision ne soit terminée](complete-access-review.md).

S’il s’agit d’une révision unique, une fois la période de révision d’accès terminée, ou si l’administrateur interrompt la révision d’accès, suivez les étapes décrivant la [réalisation d’une révision d’accès](complete-access-review.md) pour voir et appliquer les résultats.  

Pour gérer une série de révisions d’accès, accédez à la révision d’accès dans **Contrôles**. Vous y voyez les occurrences à venir dans les révisions planifiées, pour lesquelles vous pouvez modifier la date de fin, ou ajouter/supprimer des réviseurs en conséquence. 

En fonction de vos sélections dans les paramètres de saisie semi-automatique, l’application automatique est exécutée après la date de fin de la révision ou lorsque vous arrêtez manuellement la révision. La révision passe alors de l’état Terminé à divers états intermédiaires, comme Application en cours, pour arriver enfin à l’état Appliqué. Les utilisateurs dont l’accès est refusé doivent normalement perdre leur appartenance au groupe ou leur affectation d’applications au bout de quelques minutes.

## <a name="create-reviews-via-apis"></a>Créer des révisions via des API

Vous pouvez également créer des révisions d’accès avec des API. Ce que vous faites pour gérer les révisions d’accès des groupes et des utilisateurs d’applications dans le portail Azure peut également être effectué avec les API Microsoft Graph. Pour plus d’informations, consultez [Informations de référence sur les API des révisions d’accès d’Azure AD](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/accessreviews_root). Pour obtenir un exemple de code, consultez [Exemple de récupération de révisions d’accès Azure AD via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Étapes suivantes

- [Démarrer une révision d’accès avec les révisions d’accès Azure AD](perform-access-review.md)
- [Effectuer une révision d’accès des membres d’un groupe ou des accès des utilisateurs à une application dans Azure AD](complete-access-review.md)
