---
title: Modifiez et gérez l’accès à un package d’accès existant dans la gestion des droits d’utilisation d’Azure Active Directory (préversion) - Azure Active Directory
description: Découvrez comment modifier et gérer un package d’accès existant dans la gestion des droits d’utilisation d’Azure Active Directory (préversion).
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
ms.openlocfilehash: 73c1717249abac30847f96073ee6daf7d98112d7
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190349"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Modifier et gérer l’accès à un package d’accès existant dans la gestion des droits d’utilisation d’Azure Active Directory (préversion)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Un package d'accès vous permet d'effectuer une configuration unique des ressources et des stratégies qui gèrent automatiquement l'accès pendant toute la durée de vie du package d'accès. En tant que gestionnaire des packages d'accès, vous pouvez à tout moment modifier les ressources d'un package d'accès sans vous soucier de donner à l'utilisateur l’accès aux nouvelles ressources ou de supprimer son accès aux ressources précédentes. Les stratégies peuvent également être mises à jour à tout moment ; cependant, les changements de stratégie n'affectent que les nouveaux accès.

Cet article explique comment modifier et gérer des packages d'accès existants.

## <a name="add-resource-roles"></a>Ajouter des rôles de ressources

Un rôle de ressources est un ensemble d’autorisations associées à une ressource. Pour mettre des ressources à la disposition des utilisateurs, ajoutez des rôles de ressources à votre package d'accès. Vous pouvez ajouter des rôles de ressources pour des groupes, des applications et des sites SharePoint.

**Rôle prérequis :** administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Dans le menu de gauche, cliquez sur **Rôles de ressources**.

1. Cliquez sur **Ajouter des rôles de ressources** pour ouvrir la page Ajouter des rôles de ressources à un package d'accès.

    ![Package d'accès - Ajouter des rôles de ressources](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. Selon que vous souhaitiez ajouter un groupe, une application ou un site SharePoint, effectuez les étapes de l'une des sections suivantes.

### <a name="add-a-group-resource-role"></a>Ajouter un rôle de ressources de groupe

Vous pouvez configurer la gestion des droits d'utilisation afin d’ajouter automatiquement des utilisateurs à un groupe lorsqu'un package d'accès leur est attribué. 

- Si un groupe fait partie d'un package d'accès et qu'un utilisateur est affecté à ce package d'accès, l'utilisateur est ajouté à ce groupe, s'il n'y figure pas déjà.
- À l'expiration de l'affectation du package d'accès d'un utilisateur, ce dernier est retiré du groupe, sauf si un autre package d'accès incluant ce même groupe lui est actuellement affecté.

Vous pouvez sélectionner n'importe quel groupe de sécurité Office 365 ou Azure AD.  Les administrateurs peuvent ajouter n'importe quel groupe à un catalogue ; les propriétaires de catalogue peuvent ajouter n'importe quel groupe au catalogue si ce groupe leur appartient. Gardez à l'esprit les contraintes Azure AD suivantes lors de la sélection d'un groupe :

- Si un utilisateur, y compris un invité, est ajouté en tant que membre à un groupe, il peut voir tous les autres membres de ce groupe.
- Azure AD ne peut pas modifier l'appartenance d'un groupe qui a été synchronisé à partir de Windows Server Active Directory en utilisant Azure AD Connect.  
- L'adhésion à des groupes dynamiques ne peut pas être mise à jour par l'ajout ou la suppression d'un membre, de sorte que l'adhésion à des groupes dynamiques ne convient pas pour la gestion des droits d'utilisation.

1. Sur la page **Ajouter des rôles de ressources à un package d'accès**, cliquez sur **Groupes** pour ouvrir le volet Sélectionner les groupes.

1. Sélectionnez les groupes que vous voulez inclure dans le package d'accès.

    ![Package d'accès - Ajouter des rôles de ressources - Sélectionner les groupes](./media/entitlement-management-access-package-edit/group-select.png)

1. Cliquez sur **Sélectionner**.

1. Dans la liste **Rôle**, Sélectionnez **Propriétaire** ou **Membre**.

    En règle générale, sélectionnez le rôle Membre. Si vous sélectionnez le rôle Propriétaire, les utilisateurs pourront ajouter ou supprimer d'autres membres ou propriétaires.

    ![Paquet d'accès - Ajouter un rôle de ressources pour un groupe](./media/entitlement-management-access-package-edit/group-role.png)

1. Cliquez sur **Add**.

    Tous les utilisateurs ayant déjà des affectations au package d'accès deviendront automatiquement membres de ce groupe lorsqu'il sera ajouté.

### <a name="add-an-application-resource-role"></a>Ajouter un rôle de ressources d'application

Vous pouvez demander à Azure AD d'accorder automatiquement aux utilisateurs l'accès à une application d'entreprise Azure AD, y compris les applications SaaS ainsi que les applications de votre organisation fédérées sur Azure AD, lorsqu'un package d'accès est affecté à un utilisateur. Pour les applications qui s'intègrent à Azure AD par le biais de l'authentification unique fédérée, Azure AD émettra des jetons de fédération pour les utilisateurs affectés à l'application.

Les applications peuvent avoir plusieurs rôles. Lorsque vous ajoutez une application à un package d'accès, si cette application comporte plusieurs rôles, vous devez spécifier le rôle approprié pour ces utilisateurs.  Si vous développez des applications, vous pouvez en savoir plus sur la façon dont ces rôles sont fournis à vos applications dans l'article expliquant comment [configurer les réclamations de rôle émises dans le jeton SAML](../develop/active-directory-enterprise-app-role-management.md).

Une fois qu'un rôle d'application fait partie d'un package d'accès :

- Si un utilisateur se voit attribuer ce package d'accès, il est ajouté à ce rôle d'application, s'il n’y figure pas déjà.
- À l'expiration de l'affectation du package d'accès d'un utilisateur, son accès sera retiré du groupe, sauf si un autre package d'accès incluant ce rôle d’application lui est affecté.

Voici quelques considérations à prendre en compte lors de la sélection d'une application :

- Des groupes peuvent également être affectés aux rôles d’applications.  Vous pouvez choisir d'ajouter un groupe au lieu d'un rôle d'application dans un package d'accès, mais l'application ne sera pas visible pour l'utilisateur dans le package d'accès du portail Mon Accès.

1. Sur la page **Ajouter des rôles de ressources à un package d'accès**, cliquez sur **Applications** pour ouvrir le volet Sélectionner les applications.

1. Sélectionnez les applications que vous voulez inclure dans le package d'accès.

    ![Package d'accès - Ajouter des rôles de ressources - Sélectionner les applications](./media/entitlement-management-access-package-edit/application-select.png)

1. Cliquez sur **Sélectionner**.

1. Dans la liste **Rôle**, sélectionnez un rôle d'application.

    ![Package d'accès - Ajouter des rôles de ressources pour une application](./media/entitlement-management-access-package-edit/application-role.png)

1. Cliquez sur **Add**.

    Tous les utilisateurs ayant déjà des affectations au package d'accès auront automatiquement accès à cette application lorsqu'elle est ajoutée.

### <a name="add-a-sharepoint-site-resource-role"></a>Ajouter un rôle de ressources de site SharePoint

Azure AD peut automatiquement donner aux utilisateurs l'accès à un site SharePoint Online ou à une collection de sites SharePoint Online lorsqu'un package d'accès leur est affecté.

1. Sur la page **Ajouter des rôles de ressources à un package d'accès**, cliquez sur **Sites SharePoint** pour ouvrir le volet Sélectionner les sites SharePoint Online.

1. Sélectionnez les sites SharePoint Online que vous voulez inclure dans le package d'accès.

    ![Package d'accès - Ajouter des rôles de ressources - Sélectionner les sites SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Cliquez sur **Sélectionner**.

1. Dans la liste **Rôle**, sélectionnez un rôle de site SharePoint Online.

    ![Package d'accès - Ajouter un rôle de ressource pour un site SharePoint Online](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Cliquez sur **Add**.

    Tous les utilisateurs ayant déjà des affectations au package d'accès auront automatiquement accès à ce site SharePoint Online lorsqu'il est ajouté.

## <a name="remove-resource-roles"></a>Supprimer des rôles de ressources

**Rôle prérequis :** administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Dans le menu de gauche, cliquez sur **Rôles de ressources**.

1. Dans la liste des rôles de ressources, recherchez le rôle de ressources que vous souhaitez supprimer.

1. Cliquez sur le bouton de sélection ( **...** ), puis sur **Supprimer le rôle de ressources**.

    Tous les utilisateurs ayant déjà des affectations au package d'accès verront leur accès automatiquement révoqué à ce rôle de ressources lorsque ce dernier est supprimé.

## <a name="add-a-new-policy"></a>Ajouter une nouvelle stratégie

Pour choisir les personnes autorisées à demander un package d'accès, créez une stratégie. Vous pouvez créer plusieurs stratégies pour un même package d'accès si vous souhaitez permettre à différents groupes d'utilisateurs de se voir attribuer des affectations avec différents paramètres d'approbation et d'expiration. Une stratégie unique ne permet pas d’affecter des utilisateurs internes et externes à un même package d'accès. Cependant, vous pouvez créer deux stratégies dans un même package d'accès : une pour les utilisateurs internes et une autre pour les utilisateurs externes. Si plusieurs stratégies s'appliquent à un utilisateur, celui-ci sera invité, au moment de sa requête, à sélectionner la stratégie à laquelle il souhaite être affecté.

Le diagramme suivant montre le processus de haut niveau permettant de créer une stratégie pour un package d'accès existant.

![Créer un processus de stratégie](./media/entitlement-management-access-package-edit/policy-process.png)

**Rôle prérequis :** administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Stratégies**, puis sur **Ajouter une stratégie**.

1. Entrez un nom et une description pour la stratégie.

    ![Créer une stratégie avec un nom et une description](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. Selon votre sélection pour **Utilisateurs qui peuvent demander l'accès**, effectuez les étapes de l'une des sections de stratégie suivantes.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Modifier une stratégie existante

Vous pouvez à tout moment modifier une stratégie. Si vous modifiez la date d'expiration d'une stratégie, la date d'expiration des demandes en attente d'approbation ou approuvées ne change pas.

**Rôle prérequis :** administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Stratégies**, puis sélectionnez la stratégie à modifier.

    Le volet **Détails de la stratégie** s'ouvre au bas de la page.

    ![Package d’accès - Volet des détails de la stratégie](./media/entitlement-management-access-package-edit/policy-details.png)

1. Cliquez sur **Modifier** pour modifier la stratégie.

    ![Package d'accès - Modifier la stratégie](./media/entitlement-management-access-package-edit/policy-edit.png)

1. Lorsque vous avez terminé, cliquez sur **Mettre à jour**.

## <a name="directly-assign-a-user"></a>Affecter directement un utilisateur

Dans certains cas, vous pouvez affecter directement des utilisateurs spécifiques à un package d'accès pour leur éviter d’avoir à passer par le processus de demande du package d'accès. Pour affecter directement des utilisateurs, le package d'accès doit avoir une stratégie qui autorise les affectations directes par l'administrateur.

**Rôle prérequis :** administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Dans le menu gauche, cliquez sur **Affectations**.

1. Cliquez sur **Nouvelle affectation** pour ouvrir la fenêtre Ajouter un utilisateur au package d’accès.

    ![Affectations - Ajouter un utilisateur au package d’accès](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Cliquez sur **Ajouter des utilisateurs** pour sélectionner les utilisateurs auxquels vous voulez affecter le package d'accès.

1. Dans la liste **Sélectionner une stratégie**, choisissez une stratégie avec le paramètre [Aucun (attributions directes d'administrateur uniquement)](#policy-none-administrator-direct-assignments-only).

    Si ce package d'accès n'offre pas ce type de stratégie, vous pouvez cliquer sur **Créer une nouvelle stratégie** pour en ajouter une.

1. Définissez la date et l'heure de début et de fin de l'affectation des utilisateurs sélectionnés. Si aucune date de fin n'est fournie, les paramètres d'expiration de la stratégie seront utilisés.

1. Si vous le souhaitez, fournissez une justification de votre affectation directe à des fins d’archivage.

1. Cliquez sur **Ajouter** pour affecter directement les utilisateurs sélectionnés au package d'accès.

    Après quelques instants, cliquez sur **Actualiser** pour voir les utilisateurs dans la liste des affectations.

## <a name="view-who-has-an-assignment"></a>Afficher les utilisateurs qui ont une affectation

**Rôle prérequis :** administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Affectations** pour afficher une liste des affectations actives.

1. Cliquez sur une affectation spécifique pour voir plus de détails.

1. Pour afficher une liste des affectations dont les rôles de ressources n’étaient pas correctement provisionnés, cliquez sur filtre d’état et sélectionnez **Livraison en cours**.

    Vous pouvez voir des détails supplémentaires sur les erreurs de remise en recherchant la requête correspondante de l'utilisateur sur la page **Requêtes**.

1. Pour voir les affectations expirées, cliquez sur le filtre d’état et sélectionnez **Expiré**.

1. Pour télécharger un fichier CSV contenant la liste filtrée, cliquez sur **Télécharger**.

## <a name="view-requests"></a>Afficher les requêtes

**Rôle prérequis :** administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Demandes**.

1. Cliquez sur une requête spécifique pour voir plus de détails.

## <a name="view-a-requests-delivery-errors"></a>Afficher les erreurs de remise d’une requête

**Rôle prérequis :** administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Demandes**.

1. Sélectionnez la requête à afficher.

    Si la requête contient des erreurs de remise, l’état de la demande sera **Non remis** et le sous-état sera **Partiellement livré**.

    Le cas échéant, le panneau des détails de la requête indique le nombre d’erreurs de livraison.

1. Cliquez sur ce nombre pour voir toutes les erreurs de livraison de la requête.

## <a name="cancel-a-pending-request"></a>Annuler une demande en attente

Vous pouvez uniquement annuler une requête en attente qui n'a pas encore été remise.

**Rôle prérequis :** administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Demandes**.

1. Cliquez sur la requête à annuler.

1. Dans le volet des détails de la requête, cliquez sur **Annuler la demande**.

## <a name="copy-my-access-portal-link"></a>Copier le lien du portail Mon Accès

La plupart des utilisateurs de votre répertoire peuvent se connecter au portail Mon Accès et voir automatiquement la liste des packages d’accès qu’ils peuvent demander. Toutefois, pour les utilisateurs partenaires externes qui ne figurent pas encore dans votre répertoire, vous devrez leur envoyer un lien qui leur servira à demander un package d'accès. Tant que le package d'accès est activé pour les utilisateurs externes et que vous avez défini une stratégie pour le répertoire de l'utilisateur externe, ce dernier peut utiliser le lien du portail Mon Accès afin de demander le package d'accès.

**Rôle prérequis :** administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Sur la page Vue d’ensemble, copiez le **lien du portail Mon Accès**.

    ![Vue d’ensemble du package d’accès - Lien du portail Mon Accès](./media/entitlement-management-shared/my-access-portal-link.png)

1. Envoyez par e-mail ou tout autre moyen le lien à votre partenaire commercial externe. Ce dernier peut partager le lien avec ses utilisateurs afin de demander le package d'accès.

## <a name="change-the-hidden-setting"></a>Modifier le paramètre Hidden (Masqué)

Par défaut, les packages d'accès sont détectables. Cela signifie que si une stratégie permet à un utilisateur de demander le package d'accès, il verra automatiquement ce package d’accès dans son portail Mon Accès.

**Rôle prérequis :** administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Sur la page Vue d’ensemble, cliquez sur **Modifier**.

1. Définissez le paramètre **Hidden** (Masqué).

    Si ce paramètre est défini sur **No** (Non), le package d'accès apparaîtra dans le portail Mon Accès de l'utilisateur.

    S’il est défini sur **Yes** (Oui), le package d'accès n’apparaîtra pas dans le portail Mon Accès de l'utilisateur. Un utilisateur peut uniquement voir le package d'accès s’il dispose du **lien du portail Mon Accès** vers ce package d'accès.

## <a name="delete"></a>Supprimer

Un package d’accès ne peut être supprimé que s’il n’est affecté à aucun utilisateur.

**Rôle prérequis :** administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Dans le menu de gauche, cliquez sur **Affectations** et supprimez l’accès pour tous les utilisateurs.

1. Dans le menu de gauche, cliquez sur **Vue d’ensemble**, puis sur **Supprimer**.

1. Dans le message de suppression qui apparaît, cliquez sur **Yes** (Oui).

## <a name="when-are-changes-applied"></a>Quand les changements sont-ils appliqués ?

Dans le cadre de la gestion des droits d'utilisation, Azure AD traitera plusieurs fois par jour les modifications en masse apportées aux affectations et ressources de vos packages d'accès. Ainsi, si vous effectuez une affectation ou modifiez les rôles de ressources de votre package d'accès, la mise en application de ce changement dans Azure AD peut prendre 24 heures, plus le délai nécessaire pour propager ces modifications à d'autres services en ligne Microsoft ou applications SaaS connectées. Si votre changement n'affecte que quelques objets, quelques minutes suffiront pour l'appliquer à Azure AD, après quoi d'autres composants Azure AD détecteront ce changement et mettront à jour les applications SaaS. Si votre modification affecte des milliers d'objets, elle prendra plus de temps. Par exemple, si vous avez un package d'accès avec 2 applications et 100 affectations d'utilisateurs, et que vous décidez d'ajouter un rôle de site SharePoint à ce package d'accès, un certain délai peut s’écouler avant que tous les utilisateurs fassent partie de ce rôle de site SharePoint. Vous pouvez suivre l'état d'avancement grâce au journal d'audit Azure AD, au journal d’approvisionnement Azure AD et aux journaux d'audit du site SharePoint.

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter un propriétaire de catalogue ou un gestionnaire de package d’accès](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [Processus de requête et notifications par e-mail](entitlement-management-process.md)
