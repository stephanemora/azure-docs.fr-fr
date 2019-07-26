---
title: 'Tutoriel : Créer votre premier package d’accès dans la gestion des droits d’utilisation Azure AD (préversion) - Azure Active Directory'
description: Ce tutoriel pas à pas vous explique comment créer votre premier package d’accès dans la gestion des droits d’utilisation Azure Active Directory (préversion).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1688651466ba6748e1254c9d33bb24435602868b
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489163"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>Didacticiel : Créer votre premier package d’accès dans la gestion des droits d’utilisation Azure AD (préversion)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La gestion de l’accès à toutes les ressources dont les employés ont besoin, comme les groupes, les applications et les sites, est une fonction importante pour les organisations. Vous devez accorder aux employés le niveau d’accès dont ils ont besoin pour être productifs et leur retirer cet accès dès qu’il n’est plus nécessaire.

Dans ce tutoriel, vous travaillez pour Woodgrove Bank en tant qu’administrateur informatique. Vous êtes chargé de créer un package de ressources pour un projet web en veillant à ce que les utilisateurs internes puissent demander l’accès à ces ressources en libre-service. Les demandes doivent être approuvées, et l’accès de l’utilisateur est valide 30 jours. Dans ce tutoriel, les ressources du projet web représentent simplement une appartenance à un groupe unique, mais vous pouvez avoir un ensemble de groupes, d’applications ou de sites SharePoint Online.

![Présentation du scénario](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un package d’accès avec un groupe comme ressource
> * Désigner un approbateur
> * Montrer comment un utilisateur interne peut demander le package d’accès
> * Approuver la demande d’accès

Si vous ne disposez pas d’une licence Azure AD Premium P2 ou Enterprise Mobility + Security E5, créez un [essai gratuit Enterprise Mobility + Security E5](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1).

## <a name="prerequisites"></a>Prérequis

Pour utiliser la gestion des droits d’utilisation Azure AD (préversion), vous devez disposer d’une des licences suivantes :

- Azure AD Premium P2
- Licence Enterprise Mobility + Security (EMS) E5

## <a name="step-1-set-up-users-and-group"></a>Étape 1 : Configurer des utilisateurs et un groupe

Un répertoire de ressources comprend une ou plusieurs ressources à partager. Dans cette étape, vous créez un groupe nommé **Engineering Group** dans l’annuaire Woodgrove Bank qui constitue la ressource cible pour la gestion des droits d’utilisation. Vous configurez également un demandeur interne.

**Rôle prérequis :** Administrateur général ou Administrateur d’utilisateurs

![Créer des utilisateurs et des groupes](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’Administrateur général ou Administrateur d’utilisateurs.  

1. Dans le volet de navigation à gauche, cliquez sur **Azure Active Directory**.

1. Créez ou configurez les deux utilisateurs suivants. Vous pouvez utiliser ces noms ou des noms différents. **Admin1** peut être l’utilisateur avec lequel vous êtes actuellement connecté.

    | Nom | Rôle d’annuaire | Description |
    | --- | --- | --- |
    | **Admin1** | Administrateur général<br/>-ou-<br/>Administrateur limité (Administrateur d’utilisateurs) | Administrateur et approbateur |
    | **Requestor1** | Utilisateur | Demandeur interne |

    Pour ce tutoriel, l’administrateur et l’approbateur ne font qu’un, mais il est courant de désigner une ou plusieurs personnes comme approbateurs.

1. Créez un groupe de sécurité Azure AD nommé **Engineering Group** avec le type d’appartenance **Affecté**.

    Ce groupe sera la ressource cible pour la gestion des droits d’utilisation. Le groupe ne doit contenir aucun membre pour commencer.

## <a name="step-2-create-an-access-package"></a>Étape 2 : Créer un package d’accès

Un *package d’accès* est un bundle de toutes les ressources dont un utilisateur a besoin pour travailler sur un projet ou effectuer son travail. Les packages d’accès sont définis dans des conteneurs appelés *catalogues*. Dans cette étape, vous allez créer un **package d’accès de projet web** dans le catalogue **Général**.

**Rôle prérequis :** Administrateur général ou Administrateur d’utilisateurs

![Créer un package d’accès](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Dans le portail Azure, dans le volet de navigation gauche, cliquez sur **Azure Active Directory**.

1. Dans le menu gauche, cliquez sur **Identity Governance**.

1. Dans le menu gauche, cliquez sur **Packages d’accès**.  Si vous voyez **Accès refusé**, vérifiez qu’une licence Azure AD Premium P2 est présente dans cet annuaire.

1. Cliquez sur **Nouveau package d’accès**.

    ![Gestion des droits d’utilisation dans le portail Azure](./media/entitlement-management-access-package-first/access-packages-list.png)

1. Sous l’onglet **De base**, tapez le nom **Web project access package** (package d’accès de projet web) et la description **Access package for the Engineering web project** (package d’accès pour le projet web Ingénierie).

1. Dans la liste déroulante **Catalogue**, conservez la sélection **Général**.

    ![Nouveau package d’accès - Onglet De base](./media/entitlement-management-access-package-first/basics.png)

1. Cliquez sur **Suivant** pour ouvrir l’onglet **Rôles des ressources**.

    Sous cet onglet, sélectionnez les autorisations à inclure dans le package d’accès.

1. Cliquez sur **Groupes**.

1. Dans le volet Sélectionner les groupes, recherchez le groupe **Engineering Group** créé précédemment et sélectionnez-le.

    Par défaut, des groupes sont présents à l’intérieur et à l’extérieur du catalogue **Général**. Quand vous sélectionnez un groupe à l’extérieur du catalogue **Général**, il est ajouté au catalogue **Général**.

    ![Nouveau package d’accès - Onglet Rôles des ressources](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Cliquez sur **Sélectionner** pour ajouter le groupe à la liste.

1. Dans la liste déroulante **Rôle**, sélectionnez **Membre**.

    ![Nouveau package d’accès - Onglet Rôles des ressources](./media/entitlement-management-access-package-first/resource-roles.png)

1. Cliquez sur **Suivant** pour ouvrir l’onglet **Stratégie**.

1. En regard de **Créer la première stratégie**, sélectionnez **Plus tard**.

    Vous créerez la stratégie dans la section suivante.

    ![Nouveau package d’accès - Onglet Stratégie](./media/entitlement-management-access-package-first/policy.png)

1. Cliquez sur **Suivant** pour ouvrir l’onglet **Vérifier + créer**.

    ![Nouveau package d’accès - Onglet Vérifier + créer](./media/entitlement-management-access-package-first/review-create.png)

1. Passez en revue les paramètres du package d’accès, puis cliquez sur **Créer**.

    Un message peut apparaître pour indiquer que les utilisateurs ne peuvent pas voir le package d’accès car le catalogue n’est pas encore activé.

    ![Nouveau package d’accès - Message de non-visibilité](./media/entitlement-management-access-package-first/not-visible.png)

1. Cliquez sur **OK**.

    Après quelques instants, vous devez obtenir une notification indiquant que le package d’accès a été créé.

## <a name="step-3-create-a-policy"></a>Étape 3 : Création d’une stratégie

Une *stratégie* définit les règles ou barrières mises en place pour accéder à un package d’accès. Dans cette étape, vous créez une stratégie qui permet à un utilisateur spécifique de l’annuaire de ressources de demander le package d’accès. Vous spécifiez également que les demandes doivent être approuvées et qui sera l’approbateur.

![Créer une stratégie de package d’accès](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**Rôle prérequis :** Administrateur général ou Administrateur d’utilisateurs

1. Dans le **package d’accès de projet web**, dans le menu gauche, cliquez sur **Stratégies**.

    ![Liste de stratégies de package d’accès](./media/entitlement-management-access-package-first/policies-list.png)

1. Cliquez sur **Ajouter une stratégie** pour ouvrir Créer une stratégie.

1. Tapez le nom **Internal requestor policy** (stratégie de demandeur interne) et la description **Allows users in this directory to request access to web project resources** (permet aux utilisateurs dans cet annuaire de demander l’accès aux ressources de projet web).

1. Dans la section **Utilisateurs pouvant demander l’accès**, cliquez sur **Pour les utilisateurs dans votre annuaire**.

    ![Créer une stratégie](./media/entitlement-management-access-package-first/policy-create.png)

1. Faites défiler la page jusqu’à la section **Sélectionner des utilisateurs et des groupes** et cliquez sur **Ajouter des utilisateurs et des groupes**.

1. Dans le volet Sélectionner des utilisateurs et des groupes, sélectionnez l’utilisateur **Requestor1** que vous avez créé précédemment, puis cliquez sur **Sélectionner**.

1. Dans la section **Demande**, sélectionnez **Oui** en regard de **Exiger l’approbation**.

1. Dans la section **Sélectionner des approbateurs** , cliquez sur **Ajouter des approbateurs**.

1. Dans le volet Sélectionner des approbateurs, sélectionnez l’approbateur **Admin1** que vous avez créé précédemment, puis cliquez sur **Sélectionner** .

    Dans ce tutoriel, l’administrateur et l’approbateur ne font qu’un, mais vous pouvez désigner une autre personne comme approbateur.

1. Dans la section **Expiration**, sélectionnez **Nombre de jours** en regard de **Le package d’accès expire**.

1. Définissez **L’accès expire après** avec une valeur de **30** jours.

1. Pour **Activer la stratégie**, cliquez sur **Oui**.

    ![Paramètres de création de stratégie](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. Cliquez sur **Créer** pour créer la **Stratégie de demandeur interne**.

1. Dans le menu gauche du package d’accès de projet web, cliquez sur **Vue d’ensemble**.

1. Copiez le lien sous **Lien du portail Mon Accès**.

    Vous en aurez besoin à l’étape suivante.

    ![Vue d’ensemble du package d’accès - Lien du portail Mon Accès](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>Étape 4 : Demander l'accès

Dans cette étape, vous demandez l’accès au package d’accès en tant que **demandeur interne**. Les demandeurs envoient leurs demandes à l’aide d’un site appelé « portail Mon Accès ». Le portail Mon Accès permet aux demandeurs d’envoyer des demandes de packages d’accès, de voir ceux auxquels ils ont déjà accès et de consulter l’historique de leurs demandes.

**Rôle prérequis :** Demandeur interne

1. Déconnectez-vous du portail Azure.

1. Dans une nouvelle fenêtre de navigateur, accédez au lien du portail Mon Accès que vous avez copié à l’étape précédente.

1. Connectez-vous au portail Mon Accès en tant que **Requestor1**.

    Le **package accès de projet web** doit apparaître.

1. Si nécessaire, dans la colonne **Description**, cliquez sur la flèche pour afficher des détails sur le package d’accès.

    ![Portail Mon Accès - Packages d’accès](./media/entitlement-management-shared/my-access-access-packages.png)

1. Cochez la case pour sélectionner le package.

1. Cliquez sur **Demander l’accès** pour ouvrir le volet Demander l’accès.

1. Dans la zone **Justification professionnelle**, tapez la justification **Working on web project** (participation au projet web).

1. Sous **Demande pour une période spécifique**, sélectionnez **Oui**.

1. Définissez la **Date de début** avec la date du jour et la **Date de fin** avec la date du lendemain.

    ![Portail Mon Accès - Demander l’accès](./media/entitlement-management-shared/my-access-request-access.png)

1. Cliquez sur **Envoyer**.

1. Dans le menu gauche, cliquez sur **Historique des demandes** pour vérifier que votre demande a été envoyée.

## <a name="step-5-approve-access-request"></a>Étape 5 : Approuver la demande d’accès

Dans cette étape, vous vous connectez en tant qu’**approbateur** et approuvez la demande d’accès pour le demandeur interne. Les approbateurs utilisent le même portail Mon Accès que celui dont se servent les demandeurs pour soumettre des demandes. À l’aide du portail Mon Accès, les approbateurs peuvent afficher les approbations en attente et approuver ou refuser des demandes.

**Rôle prérequis :** Approbateur

1. Déconnectez-vous du portail Mon Accès.

1. Connectez-vous au portail [Mon Accès](https://myaccess.microsoft.com) en tant que **Admin1**.

1. Dans le menu gauche, cliquez sur **Approbations**.

1. Sous l’onglet **En attente**, recherchez **Requestor1**.

    Si vous ne voyez pas la demande à partir de Requestor1, patientez quelques minutes et réessayez.

1. Cliquez sur le lien **Afficher** pour ouvrir le volet Demande d’accès.

1. Cliquez sur **Approuver**.

1. Dans la zone **Raison**, tapez la raison **Approved access for web project** (accès approuvé pour le projet web).

    ![Portail Mon Accès - Demande d’accès](./media/entitlement-management-shared/my-access-approve-request.png)

1. Cliquez sur **Soumettre** pour soumettre votre décision.

    Un message indiquant que l’accès a été approuvé doit apparaître.

## <a name="step-6-validate-that-access-has-been-assigned"></a>Étape 6 : Valider que l’accès a été affecté

Maintenant que vous avez approuvé la demande d’accès, vous allez confirmer dans cette étape que le package d’accès a été affecté au **demandeur interne** et qu’il est désormais membre du groupe **Engineering Group**.

**Rôle prérequis :** Administrateur général ou Administrateur d’utilisateurs

1. Déconnectez-vous du portail Mon Accès.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’**Admin1**.

1. Cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu gauche, cliquez sur **Packages d’accès**.

1. Recherchez le **package d’accès de projet web** et cliquez dessus.

1. Dans le menu gauche, cliquez sur **Demandes**.

    Vous devriez voir Requestor1 et la stratégie de demandeur interne avec l’état **Remis**.

1. Cliquez sur la demande pour l’examiner en détail.

    ![Package d’accès - Détails de la demande](./media/entitlement-management-access-package-first/request-details.png)

1. Dans le volet de navigation à gauche, cliquez sur **Azure Active Directory**.

1. Cliquez sur **Groupes** et ouvrez le groupe **Engineering Group**.

1. Cliquez sur **Membres**.

    **Requestor1** doit être listé comme membre.

    ![Membres d’Engineering Group](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>Étape 7 : Supprimer des ressources

Dans cette étape, vous supprimez les modifications apportées et le **package d’accès de projet web**.

**Rôle prérequis :**  Administrateur général ou Administrateur d’utilisateurs

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Ouvrez le **package d’accès de projet web**.

1. Cliquez sur **Affectations**.

1. Pour **Requestor1**, cliquez sur le bouton de sélection ( **...** ), puis sur **Supprimer l’accès**.

    L’état passe de Remis à Expiré.

1. Cliquez sur **Stratégies**.

1. Pour **Internal requestor policy**, cliquez sur le bouton de sélection ( **...** ), puis sur **Supprimer**.

1. Cliquez sur **Rôles des ressources**.

1. Pour **Engineering Group**, cliquez sur le bouton de sélection ( **...** ), puis sur **Supprimer le rôle de ressource**.

1. Ouvrez la liste des packages d’accès.

1. Pour **Web project access package**, cliquez sur le bouton de sélection ( **...** ), puis sur **Supprimer**.

1. Dans Azure Active Directory, supprimez tous les utilisateurs que vous avez créés comme **Requestor1** et **Admin1**.

1. Supprimer le groupe **Engineering Group**.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour en savoir plus sur les étapes de scénarios courants dans la gestion des droits d’utilisation.
> [!div class="nextstepaction"]
> [Scénarios courants](entitlement-management-scenarios.md)
