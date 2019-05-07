---
title: Fichier Include
description: Fichier Include
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: 364d4a11772e6bb72e2e258503f3cce49dc61453
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141841"
---
## <a name="create-one-or-more-access-reviews"></a>Créer un ou plusieurs révisions d’accès

1. Cliquez sur **New** pour créer une nouvelle révision d’accès.

1. Nommez la révision d’accès. Si vous le souhaitez, vous pouvez fournir une description de cette révision. Les réviseurs ont accès au nom et à la description de la révision.

    ![Créer une révision d’accès - Nom et description de la révision](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Définissez la **Date de début**. Par défaut, une révision d’accès se produit une seule fois, démarre le jour même de sa création et s’arrête un mois plus tard. Vous pouvez modifier les dates de début et de fin pour que la révision d’accès démarre ultérieurement et dure le nombre de jours souhaité.

    ![Créer une révision d’accès - Dates de début et de fin](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Pour que la révision d’accès périodique, modifiez le **fréquence** définir à partir de **une fois** à **hebdomadaire**, **mensuel**,  **Tous les trimestres**, **une fois par an**, ou **semestrielle**. Utilisez le **durée** curseur ou zone de texte pour définir le nombre de jours chaque révision de la série périodique sera ouvre pour l’entrée des réviseurs. Par exemple, la durée maximale d’une révision mensuelle est de 27 jours, ce qui permet d’éviter le chevauchement des révisions.

1. Utilisez le paramètre **Fin** pour spécifier comment mettre fin à la série de révisions d’accès récurrentes. Les séries accès récurrentes peuvent se terminer de trois façons : elles peuvent s’exécuter de façon continue pour démarrer des révisions indéfiniment, s’exécuter jusqu’à une date spécifique ou s’exécuter jusqu’à ce qu’un nombre défini d’occurrences se soient produites. Vous, un autre administrateur de l’utilisateur ou un autre administrateur général arrêter la série après la création en modifiant la date dans **paramètres**, afin qu’elle se termine à cette date.

1. Dans le **utilisateurs** , sélectionnez un ou plusieurs rôles que vous souhaitez examiner l’appartenance de.

    ![Créer une révision d’accès - utilisateurs](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > En sélectionnant plusieurs rôles créera plusieurs révisions d’accès. Par exemple, en sélectionnant des cinq rôles créera cinq révisions d’accès distincts.

    Si vous créez une révision d’accès des rôles Azure AD, ce qui suit montre un exemple de la liste d’appartenance de révision.

    ![Créer une révision d’accès - appartenance au rôle de révision](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Si vous créez une révision d’accès des rôles de ressources Azure, ce qui suit montre un exemple de la liste d’appartenance de révision.

    ![Créer une révision d’accès - appartenance au rôle de révision](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. Dans le **réviseurs** , sélectionnez une ou plusieurs personnes pour passer en revue tous les utilisateurs. Sinon, vous pouvez choisir de faire en sorte que les membres vérifient leur propre accès.

    ![Créer une révision d’accès - Réviseurs](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Les utilisateurs sélectionnés** -Utilisez cette option lorsque vous ne savez pas qui doit accéder. Avec cette option, vous pouvez affecter la révision à un propriétaire de ressource ou un responsable de groupe.
    - **Membres (auto)** -Utilisez cette option pour demander aux utilisateurs de réviser leurs propres affectations de rôle.

### <a name="upon-completion-settings"></a>Paramètres de saisie semi-automatique

1. Pour spécifier ce qui se passe après la fin d’une révision, développez la section **Paramètres de saisie semi-automatique**.

    ![Paramètres de saisie semi-automatique](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Si vous voulez supprimer automatiquement l’accès pour les utilisateurs qui ont été refusés, définissez **Appliquer automatiquement les résultats à ressource** sur **Activer**. Si vous voulez appliquer manuellement les résultats quand la révision est terminée, cliquez sur **Désactiver**.

1. Utilisez la liste **Le réviseur ne doit pas répondre** pour spécifier ce qui se passe pour les utilisateurs qui ne sont pas vérifiés par le réviseur au cours de la révision. Ce paramètre n’affecte pas les utilisateurs qui ont été vérifiés manuellement par les réviseurs. Si la décision finale du réviseur est Refuser, l’accès de l’utilisateur est supprimé.

    - **Aucune modification** : laisser l’accès de l’utilisateur inchangé
    - **Supprimer l’accès** : supprimer l’accès de l’utilisateur
    - **Approuver l’accès** : approuver l’accès de l’utilisateur
    - **Accepter les recommandations** : accepter la recommandation du système sur le refus ou l’approbation de la prolongation de l’accès de l’utilisateur

### <a name="advanced-settings"></a>Paramètres avancés

1. Pour spécifier d’autres paramètres, développez la section **Paramètres avancés**.

    ![Paramètres avancés](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Définissez **Afficher les recommandations** sur **Activer** pour montrer aux réviseurs les recommandations du système basées sur les informations des accès de l’utilisateur.

1. Définissez **Exiger la raison lors de l’approbation** sur **Activer** afin d’exiger que le réviseur indique un motif pour l’approbation.

1. Définissez **Notifications par e-mail** sur **Activer** pour qu’Azure AD envoie des notifications par e-mail aux réviseurs quand une révision d’accès commence et aux administrateurs quand une révision se termine.

1. Définissez **Rappels** sur **Activer** pour qu’Azure AD envoie des rappels concernant les révisions d’accès en cours aux réviseurs qui n’ont pas terminé leur révision.
