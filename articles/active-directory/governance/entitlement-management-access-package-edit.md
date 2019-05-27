---
title: Modifier et gérer un package d’accès existant dans la gestion des droits Azure AD (version préliminaire) - Azure Active Directory
description: Découvrez comment modifier et gérer un package d’accès existant dans la gestion des droits Azure Active Directory (version préliminaire).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce51f4df50de50cef06bba699ca7c6f76bc5d166
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833286"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Modifier et gérer un package d’accès existant dans la gestion des droits Azure AD (version préliminaire)

> [!IMPORTANT]
> Gestion des habilitations Azure Active Directory (Azure AD) est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Un package d’accès vous permet de vous permettent d’effectuer une installation unique des ressources et des stratégies qui administre automatiquement l’accès pour la durée de vie du package d’accès. En tant qu’un gestionnaire de package de l’accès, vous pouvez modifier les ressources dans un package de l’accès à tout moment sans vous soucier de l’accès utilisateur aux nouvelles ressources de l’approvisionnement ou la suppression de leur accès dans les ressources précédentes. Les stratégies peuvent également être mis à jour à tout moment, toutefois, les modifications de stratégie affectent uniquement les accès à nouveau.

Cet article décrit comment modifier et gérer les packages d’accès existants.

## <a name="add-resource-roles"></a>Ajouter des rôles de ressources

Un rôle de ressource est une collection d’autorisations associées à une ressource. La que vous libérer des ressources pour les utilisateurs à demander consiste en ajoutant des rôles de ressources à votre package d’accès. Vous pouvez ajouter des rôles de ressources pour les groupes, les applications et les sites SharePoint.

**Rôle :** Utilisateur administrateur, propriétaire du catalogue ou Gestionnaire de package de l’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **accéder aux packages** , puis ouvrez le package de l’accès.

1. Dans le menu de gauche, cliquez sur **rôles de ressources**.

1. Cliquez sur **ajouter des rôles de ressources** pour ouvrir les rôles de ressource Ajouter pour accéder à la page du package.

    ![Accéder package - ajouter des rôles de ressources](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. Selon que vous souhaitez ajouter un groupe, une application ou un site SharePoint, effectuez les étapes de l’une des sections de rôle des ressources suivantes.

### <a name="add-a-group-resource-role"></a>Ajouter un rôle de ressource de groupe

Vous pouvez avoir à gestion des habilitations d’ajouter automatiquement des utilisateurs à un groupe lorsqu’ils sont attribués à un package d’accès. 

- Quand un groupe fait partie d’un package d’accès et un utilisateur est affecté à ce package de l’accès, l’utilisateur est ajouté à ce groupe, si ce n’est pas déjà présent.
- Lors de l’attribution de package d’accès d’un utilisateur arrive à expiration, ils sont supprimés du groupe, sauf s’ils disposent actuellement d’une assignation à un autre package d’accès qui inclut ce même groupe.

Vous pouvez sélectionner n’importe quel groupe Office 365 ou un groupe de sécurité Azure AD.  Les administrateurs peuvent ajouter n’importe quel groupe à un catalogue ; propriétaires de catalogue peuvent ajouter n’importe quel groupe dans le catalogue s’ils sont le propriétaire du groupe. N’oubliez pas les contraintes d’Azure AD suivantes lors de la sélection d’un groupe :

- Lorsqu’un utilisateur, y compris un invité, est ajouté en tant que membre à un groupe, ils peuvent voir tous les autres membres de ce groupe.
- Azure AD ne peut pas modifier l’appartenance d’un groupe qui a été synchronisé à partir de Windows Server Active Directory à l’aide d’Azure AD Connect.  
- L’appartenance aux groupes dynamiques ne peuvent pas être mis à jour en ajoutant ou supprimant un membre, appartenances de groupe dynamique sont donc pas appropriés pour une utilisation avec la gestion des habilitations.

1. Sur le **ajouter des rôles de ressource pour accéder au package** , cliquez sur **groupes** pour ouvrir le volet Sélectionner les groupes.

1. Sélectionnez les groupes que vous souhaitez inclure dans le package de l’accès.

    ![Accéder package - ajouter des rôles de ressource - sélectionner les groupes](./media/entitlement-management-access-package-edit/group-select.png)

1. Cliquez sur **Sélectionner**.

1. Dans le **rôle** liste, sélectionnez **propriétaire** ou **membre**.

    En règle générale, vous sélectionnez le rôle de membre. Si vous sélectionnez le rôle de propriétaire, ce qui permettra aux utilisateurs d’ajouter ou supprimer d’autres membres ou les propriétaires.

    ![Accéder package - ajouter le rôle de ressource pour un groupe](./media/entitlement-management-access-package-edit/group-role.png)

1. Cliquez sur **Add**.

    Tous les utilisateurs avec les affectations existantes pour le package d’accès deviennent automatiquement des membres de ce groupe lorsqu’il est ajouté.

### <a name="add-an-application-resource-role"></a>Ajouter un rôle de ressource d’application

Vous pouvez avoir à Azure AD d’affecter automatiquement des utilisateurs accès à une application d’entreprise Azure AD, y compris les applications SaaS et applications de votre organisation fédérées avec Azure AD, quand un utilisateur est affecté à un package d’accès. Pour les applications qui s’intègrent à Azure AD via fédérée l’authentification unique, Azure AD émet des jetons de fédération pour les utilisateurs affectés à l’application.

Les applications peuvent avoir plusieurs rôles. Lorsque vous ajoutez une application à un package de l’accès, si cette application a plusieurs rôles, vous devez spécifier le rôle approprié pour ces utilisateurs.  Si vous développez des applications, vous pouvez lire plus sur la façon dont ces rôles sont fournis à vos applications dans l’article sur la façon [configurer la revendication de rôle émise dans le jeton SAML](../develop/active-directory-enterprise-app-role-management.md).

Une fois qu’un rôle d’application fait partie d’un package d’accès :

- Lorsqu’un utilisateur est affecté à ce package de l’accès, l’utilisateur est ajouté à ce rôle d’application, si ce n’est pas déjà présent.
- Lors de l’attribution de package d’accès d’un utilisateur arrive à expiration, leur accès sera retiré l’application, sauf s’ils disposent d’une assignation à un autre package d’accès qui inclut ce rôle d’application.

Voici quelques considérations lors de la sélection d’une application :

- Applications peut-être également des groupes affectés à leurs rôles également.  Vous pouvez choisir d’ajouter un groupe à la place d’un rôle d’application dans un package d’accès, toutefois, puis l’application ne sera pas visible par l’utilisateur en tant que partie du package dans le portail mon accès accès.

1. Sur le **ajouter des rôles de ressource pour accéder au package** , cliquez sur **Applications** pour ouvrir le volet de sélection d’applications.

1. Sélectionnez les applications que vous souhaitez inclure dans le package de l’accès.

    ![Accéder package - ajouter des rôles de ressource - sélectionner des applications](./media/entitlement-management-access-package-edit/application-select.png)

1. Cliquez sur **Sélectionner**.

1. Dans le **rôle** , sélectionnez un rôle d’application.

    ![Accéder package - ajouter le rôle de ressource pour une application](./media/entitlement-management-access-package-edit/application-role.png)

1. Cliquez sur **Add**.

    Tous les utilisateurs avec les affectations existantes au package accès aura automatiquement un accès à cette application lorsqu’elle est ajoutée.

### <a name="add-a-sharepoint-site-resource-role"></a>Ajouter un rôle de ressource de site SharePoint

Azure AD peut affecter automatiquement des utilisateurs l’accès à un site SharePoint Online ou de la collection de sites SharePoint Online lorsqu’ils sont attribués à un package d’accès.

1. Sur le **ajouter des rôles de ressource pour accéder au package** , cliquez sur **sites SharePoint** pour ouvrir le volet de sites SharePoint Online de la sélectionner.

1. Sélectionnez les sites SharePoint Online que vous souhaitez inclure dans le package de l’accès.

    ![Accéder package - ajouter des rôles de ressource - sites sélectionnez SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Cliquez sur **Sélectionner**.

1. Dans le **rôle** liste, sélectionnez un rôle de site SharePoint Online.

    ![Accéder package - ajouter le rôle de ressource pour un site SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Cliquez sur **Add**.

    Tous les utilisateurs avec les affectations existantes au package accès aura automatiquement un accès à ce site SharePoint Online quand il est ajouté.

## <a name="remove-resource-roles"></a>Supprimer des rôles de ressources

**Rôle :** Utilisateur administrateur, propriétaire du catalogue ou Gestionnaire de package de l’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **accéder aux packages** , puis ouvrez le package de l’accès.

1. Dans le menu de gauche, cliquez sur **rôles de ressources**.

1. Dans la liste des rôles de ressources, recherchez le rôle de ressource que vous souhaitez supprimer.

1. Cliquez sur le bouton de sélection (**...** ) puis cliquez sur **rôle de ressource Remove**.

    Tous les utilisateurs avec les affectations existantes au package accès auront automatiquement leur accès révoqué à ce rôle de ressource lorsqu’il est supprimé.

## <a name="add-a-new-policy"></a>Ajouter une nouvelle stratégie

La façon dont vous spécifiez qui peut demander un package d’accès consiste à créer une stratégie. Vous pouvez créer plusieurs stratégies pour un package d’accès unique si vous souhaitez autoriser différents groupes d’utilisateurs qui auront des attributions avec différents d’approbation et les paramètres d’expiration. Une stratégie unique ne peut pas être utilisée pour affecter des utilisateurs internes et externes au package même accès. Toutefois, vous pouvez créer deux stratégies dans le même package accès--un pour les utilisateurs internes et un pour les utilisateurs externes. S’il existe plusieurs stratégies qui s’appliquent à un utilisateur, il seront invité au moment de leur demande de sélection de la stratégie qu’ils souhaitent être affecté à.

Le diagramme suivant illustre la procédure à suivre pour créer une stratégie pour un package d’accès existant.

![Créer un processus de stratégie](./media/entitlement-management-access-package-edit/policy-process.png)

**Rôle :** Utilisateur administrateur, propriétaire du catalogue ou Gestionnaire de package de l’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **accéder aux packages** , puis ouvrez le package de l’accès.

1. Cliquez sur **stratégies** , puis **ajouter une stratégie**.

1. Tapez un nom et une description pour la stratégie.

    ![Créer la stratégie avec le nom et la description](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. Selon votre sélection pour **les utilisateurs qui peuvent demander l’accès**, effectuez les étapes de l’une des sections suivantes de la stratégie.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Modifier une stratégie existante

Vous pouvez modifier une stratégie à tout moment. Si vous modifiez la date d’expiration pour une stratégie, la date d’expiration pour les demandes qui sont déjà en attente d’approbation ou approuvée état ne changera pas.

**Rôle :** Utilisateur administrateur, propriétaire du catalogue ou Gestionnaire de package de l’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **accéder aux packages** , puis ouvrez le package de l’accès.

1. Cliquez sur **stratégies** puis cliquez sur la stratégie que vous souhaitez modifier.

    Le **détails de la stratégie** volet s’ouvre en bas de la page.

    ![Package d’accès - volet de détails stratégie](./media/entitlement-management-access-package-edit/policy-details.png)

1. Cliquez sur **modifier** pour modifier la stratégie.

    ![Package d’accès - Modifier la stratégie](./media/entitlement-management-access-package-edit/policy-edit.png)

1. Lorsque vous avez terminé, cliquez sur **mise à jour**.

## <a name="directly-assign-a-user"></a>Attribuer directement un utilisateur

Dans certains cas, vous souhaiterez attribuer directement des utilisateurs spécifiques à un package d’accès afin que les utilisateurs n’êtes pas obligé de passer par le processus de demande le package de l’accès. Pour affecter directement des utilisateurs, le package de l’accès doit avoir une stratégie qui autorise des assignations directes administrateur.

**Rôle :** Utilisateur administrateur, propriétaire du catalogue ou Gestionnaire de package de l’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **accéder aux packages** , puis ouvrez le package de l’accès.

1. Dans le menu de gauche, cliquez sur **affectations**.

1. Cliquez sur **nouvelle attribution de** pour ouvrir Ajouter l’utilisateur au package d’accès.

    ![Ajouter des affectations - package de l’accès utilisateur](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Cliquez sur **ajouter des utilisateurs** pour sélectionner les utilisateurs que vous souhaitez affecter le package de l’accès à.

1. Dans le **sélectionnez stratégie** , sélectionnez une stratégie qui a le [None (affectations directes administrateur uniquement)](#policy-none-administrator-direct-assignments-only) paramètre.

    Si ce package d’accès n’a pas de ce type de stratégie, vous pouvez cliquer sur **créer la nouvelle stratégie** en ajouter un.

1. Définir la date et l’heure souhaitées d’affectation d’utilisateurs à commencer et se terminer. Si une date de fin n’est pas fournie, les paramètres de stratégie d’expiration seront utilisés.

1. Si vous le souhaitez fournir une justification de votre attribution directe pour l’enregistrement.

1. Cliquez sur **ajouter** pour attribuer directement les utilisateurs sélectionnés pour le package de l’accès.

    Après quelques instants, cliquez sur **Actualiser** pour voir les utilisateurs dans la liste des affectations.

## <a name="view-who-has-an-assignment"></a>Vue qui dispose d’une affectation

**Rôle :** Utilisateur administrateur, propriétaire du catalogue ou Gestionnaire de package de l’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **accéder aux packages** , puis ouvrez le package de l’accès.

1. Cliquez sur **affectations** pour afficher une liste des affectations actives.

1. Cliquez sur une affectation spécifique pour afficher des informations supplémentaires.

1. Pour afficher la liste des affectations qui n’avaient pas de tous les rôles de ressources correctement configurés, cliquez sur l’état de filtre et sélectionnez **livraison**.

    Vous pouvez afficher des informations supplémentaires sur les erreurs de remise en recherchant la demande correspondante de l’utilisateur sur le **demandes** page.

1. Pour afficher les attributions expirées, cliquez sur l’état de filtre et sélectionnez **expiré**.

1. Pour télécharger un fichier CSV de la liste filtrée, cliquez sur **télécharger**.

## <a name="view-requests"></a>Afficher les demandes

**Rôle :** Utilisateur administrateur, propriétaire du catalogue ou Gestionnaire de package de l’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **accéder aux packages** , puis ouvrez le package de l’accès.

1. Cliquez sur **demandes**.

1. Cliquez sur une demande spécifique pour afficher des informations supplémentaires.

## <a name="view-a-requests-delivery-errors"></a>Afficher les erreurs de remise d’une requête

**Rôle :** Utilisateur administrateur, propriétaire du catalogue ou Gestionnaire de package de l’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **accéder aux packages** , puis ouvrez le package de l’accès.

1. Cliquez sur **demandes**.

1. Sélectionnez la requête que vous souhaitez afficher.

    Si la requête comporte des erreurs de remise, l’état de la demande sera **non remis** et le sous-état sera **partiellement remis**.

    S’il existe des erreurs de remise, dans le volet de détails de la demande, il y aura un nombre d’erreurs de remise.

1. Cliquez sur le nombre pour afficher toutes les erreurs de remise de la demande.

## <a name="cancel-a-pending-request"></a>Annuler une demande en attente

Vous ne pouvez annuler une demande en attente qui n’a pas encore été transmise.

**Rôle :** Utilisateur administrateur, propriétaire du catalogue ou Gestionnaire de package de l’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **accéder aux packages** , puis ouvrez le package de l’accès.

1. Cliquez sur **demandes**.

1. Cliquez sur la demande que vous souhaitez annuler

1. Dans le volet détails de la demande, cliquez sur **demande d’annulation**.

## <a name="copy-my-access-portal-link"></a>Copier le lien vers le portail mon accès

La plupart des utilisateurs de votre annuaire peuvent se connecter au portail mon accès et afficher automatiquement la liste des packages d’accès qu’ils peuvent demander. Toutefois, pour les utilisateurs de partenaires commerciaux externes qui ne sont pas encore dans votre annuaire, vous devez leur envoyer un lien qu’ils peuvent utiliser pour demander un package d’accès. Tant que le package de l’accès est activé pour les utilisateurs externes et que vous avez une stratégie pour le répertoire de l’utilisateur externe, l’utilisateur externe peut utiliser le lien vers le portail mon accès pour demander le package de l’accès.

**Rôle :** Utilisateur administrateur, propriétaire du catalogue ou Gestionnaire de package de l’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **accéder aux packages** , puis ouvrez le package de l’accès.

1. Dans la page Vue d’ensemble, copiez la **lien vers le portail mon accès**.

    ![Vue d’ensemble de package d’accès - lien vers le portail mon accès](./media/entitlement-management-shared/my-access-portal-link.png)

1. Envoyer par courrier électronique ou envoyez le lien à vos partenaires commerciaux externes. Ils peuvent partager le lien avec les utilisateurs pour demander le package de l’accès.

## <a name="change-the-hidden-setting"></a>Modifiez le paramètre Hidden

Les packages d’accès sont détectables par défaut. Cela signifie que si une stratégie permet à un utilisateur demander le package de l’accès, il verra automatiquement le package d’accès répertorié dans leur portail mon accès.

**Rôle :** Utilisateur administrateur, propriétaire du catalogue ou Gestionnaire de package de l’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **accéder aux packages** , puis ouvrez le package de l’accès.

1. Dans la page Vue d’ensemble, cliquez sur **modifier**.

1. Définir le **Hidden** paramètre.

    Si la valeur **non**, le package de l’accès s’afficheront dans le portail mon accès de l’utilisateur.

    Si la valeur **Oui**, le package de l’accès ne sera pas figurer dans le portail mon accès de l’utilisateur. La seule façon d’un utilisateur peut voir le package de l’accès est s’ils ont la méthode directe **lien vers le portail mon accès** pour le package de l’accès.

## <a name="delete"></a>Supprimer

Un package d’accès peut être supprimé uniquement s’il n’a aucune affectation d’utilisateur active.

**Rôle :** Utilisateur administrateur, propriétaire du catalogue ou Gestionnaire de package de l’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **accéder aux packages** , puis ouvrez le package de l’accès.

1. Dans le menu de gauche, cliquez sur **affectations** et supprimer l’accès pour tous les utilisateurs.

1. Dans le menu de gauche, cliquez sur **vue d’ensemble** puis cliquez sur **supprimer**.

1. Dans le message de suppression qui s’affiche, cliquez sur **Oui**.

## <a name="when-are-changes-applied"></a>Lorsque les modifications sont appliquées

Dans Gestion des habilitations, Azure AD traite les modifications en bloc pour l’attribution et de ressources dans vos packages accès plusieurs fois par jour. Par conséquent, si vous réalisent une affectation ou modifiez les rôles de ressources de votre package d’accès, il peut prendre jusqu'à 24 heures pour que cette modification doit être faite dans Azure AD, ainsi que la quantité de temps il prend pour propager ces modifications à d’autres Services en ligne Microsoft ou SaaS application connectée s. Si votre modification affecte un petit nombre d’objets, la modification probablement uniquement prendra quelques minutes à appliquer dans Azure AD, après laquelle autres composants Azure AD puis détecte que modifier et mettre à jour les applications SaaS. Si votre modification affecte des milliers d’objets, la modification prendra plus de temps. Par exemple, si vous disposez d’un package de l’accès avec 2 applications et de 100 utilisateurs, et que vous décidez d’ajouter un rôle de site SharePoint pour le package de l’accès, il peut avoir un délai jusqu'à ce que tous les utilisateurs font partie de ce rôle de site SharePoint. Vous pouvez surveiller la progression via le journal d’audit Azure AD, le journal d’approvisionnement Azure AD et les journaux d’audit du site SharePoint.

## <a name="next-steps"></a>Étapes suivantes

- [Demande de processus et les notifications électroniques](entitlement-management-process.md)
