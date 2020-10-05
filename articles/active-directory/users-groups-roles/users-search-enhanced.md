---
title: Améliorations de la gestion des utilisateurs (préversion) - Azure Active Directory | Microsoft Docs
description: Décrit comment Azure Active Directory permet d’effectuer des recherches d’utilisateurs et un filtrage, et d’obtenir d’autres informations sur vos utilisateurs.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 09/03/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e0c8e6fb3bab179483d03320e6d90ab712ec528
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89493273"
---
# <a name="user-management-enhancements-preview-in-azure-active-directory"></a>Améliorations de la gestion des utilisateurs (préversion) dans Azure Active Directory

Cet article explique comment utiliser la préversion de gestion des utilisateurs améliorée dans le portail Azure Active Directory (Azure AD). Les pages **Tous les utilisateurs** et **Utilisateurs supprimés** ont été mises à jour afin de fournir plus d’informations et de faciliter la recherche d’utilisateurs. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les modifications apportées à la préversion sont les suivantes :

- Propriétés utilisateur plus visibles, notamment l’ID d’objet, l’état de synchronisation d’annuaire, le type de création et l’émetteur d’identité
- La fonctionnalité de recherche autorise désormais la recherche combinée de noms, d’e-mails et d’ID d’objets
- Filtrage amélioré par type d’utilisateur (membre et invité), état de synchronisation d’annuaire et type de création

> [!NOTE]
> Cette préversion n’est actuellement pas disponible pour les locataires Azure AD B2C.

## <a name="find-the-preview"></a>Rechercher la préversion

La préversion étant activée par défaut, vous pouvez l’utiliser immédiatement. Vous pouvez découvrir les fonctionnalités et améliorations les plus récentes en sélectionnant **Fonctionnalités préliminaires** dans la page **Tous les utilisateurs**. Toutes les pages qui ont été mises à jour dans le cadre de cette préversion comportent une étiquette de préversion. En cas de problème, vous pouvez revenir à l’expérience existante :

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com) et sélectionnez **Utilisateurs**.
1. En haut de la page **Utilisateurs - Tous les utilisateurs**, sélectionnez la bannière.
1. Dans le volet **Fonctionnalités préliminaires**, désactivez **Gestion améliorée des utilisateurs**.

   ![Comment et où activer et désactiver la gestion améliorée des utilisateurs ?](./media/users-search-enhanced/enable-preview.png)

Vos commentaires sont les bienvenus, car ils nous permettent d’améliorer l’expérience.

## <a name="more-user-properties"></a>Propriétés utilisateur supplémentaires

Nous avons apporté des modifications aux colonnes disponibles dans les pages **Tous les utilisateurs** et **Utilisateurs supprimés**. En plus des colonnes existantes disponibles pour gérer votre liste d’utilisateurs, nous avons ajouté quelques colonnes supplémentaires.

### <a name="all-users-page"></a>Page Tous les utilisateurs

Voici la liste des propriétés utilisateur affichées dans la page **Tous les utilisateurs** :

- Nom : Nom complet de l'utilisateur.
- Nom d’utilisateur principal : Nom d’utilisateur principal (UPN) de l’utilisateur.
- Type d’utilisateur : type de l’utilisateur, Membre ou Invité.
- Annuaire synchronisé : indique si l’utilisateur est synchronisé à partir d’un annuaire local.
- Émetteur d’identité : émetteurs de l’identité utilisée pour se connecter à un compte d’utilisateur.
- ID d’objet : ID d’objet de l’utilisateur.
- Type de création : indique comment le compte d’utilisateur a été créé.
- Nom de l’entreprise : nom de l’entreprise à laquelle l’utilisateur est associé.
- État de l’invitation : état de l’invitation pour un utilisateur invité.
- Messagerie : adresse e-mail de l’utilisateur.

   ![nouvelles propriétés utilisateur affichées dans les pages Tous les utilisateurs et Utilisateurs supprimés](./media/users-search-enhanced/user-properties.png)

### <a name="deleted-users-page"></a>Page Utilisateurs supprimés

La page **Utilisateurs supprimés** comprend toutes les colonnes qui sont disponibles dans la page **Tous les utilisateurs**, ainsi que quelques colonnes supplémentaires, à savoir :

- Date de suppression : date à laquelle l’utilisateur a été supprimé pour la première fois de l’organisation (l’utilisateur peut être restauré).
- Date de suppression permanente : date à laquelle l’utilisateur a été définitivement supprimé de l’organisation.

Certaines colonnes sont affichées par défaut. Pour ajouter d’autres colonnes, sélectionnez **Colonnes** dans la page, sélectionnez les noms des colonnes que vous souhaitez ajouter, puis sélectionnez **OK** pour enregistrer vos préférences.

### <a name="identity-issuers"></a>Émetteurs d’identité

Sélectionnez une entrée dans la colonne **Émetteur d’identité** pour un utilisateur afin d’afficher des détails supplémentaires sur l’émetteur, notamment le type de connexion et l’ID attribué par l’émetteur. Les entrées figurant dans la colonne **Émetteur d’identité** peuvent être à valeurs multiples. Si l’identité utilisateur a plusieurs émetteurs, le mot Multiple apparaît dans la colonne **Émetteur d’identité** dans les pages **Tous les utilisateurs** et **Utilisateurs supprimés**, et le volet d’informations liste tous les émetteurs.

> [!NOTE]
> La colonne **Source** est remplacée par plusieurs colonnes, notamment **Type de création**, **Annuaire synchronisé** et **Émetteur d’identité** pour un filtrage plus précis.

## <a name="user-list-search"></a>Recherche dans la liste d’utilisateurs

Lorsque vous entrez une chaîne de recherche, la fonctionnalité de recherche utilise « commence par », qui peut désormais établir une correspondance avec des noms, des e-mails ou des ID d’objets dans une recherche unique. Vous pouvez entrer n’importe lequel de ces attributs dans la zone de recherche, et la recherche se fera automatiquement sur toutes ces propriétés afin de retourner les résultats correspondants. Vous pouvez effectuer la même recherche dans les pages **Tous les utilisateurs** et **Utilisateurs supprimés**.

## <a name="user-list-filtering"></a>Filtrage de la liste d’utilisateurs

Les capacités de filtrage ont été améliorées afin de fournir davantage d’options de filtrage pour les pages **Tous les utilisateurs** et **Utilisateurs supprimés**. Vous pouvez désormais filtrer d’après plusieurs propriétés simultanément, et filtrer d’après davantage de propriétés.

### <a name="filtering-all-users-list"></a>Filtrage de la liste Tous les utilisateurs

Voici la liste des propriétés filtrables dans la page **Tous les utilisateurs** :

- Type d’utilisateur : Membre ou Invité
- État de synchronisation d’annuaire : oui
- Type de création : Invitation, E-mail vérifié, Compte local
- État de l’invitation : Acceptation en attente, Acceptée
- Unité administrative : sélectionnez cette option pour limiter l’étendue des utilisateurs que vous affichez à une seule unité administrative. Pour plus d’informations, consultez [Gestion des unités administratives (préversion)](directory-administrative-units.md).

## <a name="filtering-deleted-users-list"></a>Filtrage de la liste Utilisateurs supprimés

La page **Utilisateurs supprimés** contient des filtres supplémentaires qui ne figurent pas dans la page **Tous les utilisateurs**. Voici la liste des propriétés filtrables dans la page **Utilisateurs supprimés** :

- Type d’utilisateur : Membre ou Invité
- État de synchronisation d’annuaire : oui
- Type de création : Invitation, E-mail vérifié, Compte local
- État de l’invitation : Acceptation en attente, Acceptée
- Date de suppression : 7, 14 ou 30 derniers jours
- Date de suppression permanente : 7, 14 ou 30 derniers jours

## <a name="frequently-asked-questions-faq"></a>Forum Aux Questions (FAQ)

Question | Réponse
-------- | ------
Qu’en est-il des fonctionnalités d’opérations en bloc pour les utilisateurs et les invités ? | Les opérations en bloc sont toutes encore disponibles pour les utilisateurs et les invités, notamment la création en bloc, l’invitation en bloc, la suppression en bloc et le téléchargement d’utilisateurs. Nous venons de les fusionner dans un menu appelé **Opérations en bloc**. Les options d’**Opérations en bloc** figurent en haut de la page **Tous les utilisateurs**.
Qu’en est-il de la colonne Source ? | La colonne **Source** a été remplacée par d’autres colonnes qui fournissent des informations similaires, tout en vous permettant de filtrer ces valeurs de manière indépendante. Il s’agit notamment de **Type de création**, **Annuaire synchronisé** et **Émetteur d’identité**.
Qu’en est-il de la colonne Nom d’utilisateur ? | La colonne **Nom d’utilisateur** est toujours présente, mais elle a été renommée **Nom d’utilisateur principal**. Cela reflète mieux les informations contenues dans cette colonne. Vous remarquerez également que le nom d’utilisateur principal complet est désormais affiché pour les invités B2B. Cela correspond à ce que vous auriez dans MS Graph.  
Pourquoi je ne peux effectuer qu’une recherche « commence par » et pas une recherche « contient » ? | Certaines limitations nous empêchent de vous permettre d’effectuer une recherche « contient ». Nous avons bien enregistré votre feedback, nous nous en occupons.
Pourquoi je n’arrive pas à trier les colonnes ? | Certaines limitations nous empêchent de vous permettre de trier les colonnes. Nous avons bien enregistré votre feedback, nous nous en occupons.
Pourquoi je peux filtrer la colonne **Annuaire synchronisé** uniquement par la valeur Oui ? | Certaines limitations nous empêchent de vous permettre de filtrer cette propriété par la valeur Non. Nous avons bien enregistré votre feedback, nous nous en occupons.

## <a name="next-steps"></a>Étapes suivantes

Opérations de l’utilisateur

- [Ajouter ou modifier les informations de profil](../fundamentals/active-directory-users-profile-azure-portal.md)
- [Ajouter ou supprimer des utilisateurs](../fundamentals/add-users-azure-active-directory.md)

Opérations en bloc

- [Télécharger une liste d’utilisateurs](users-bulk-download.md)
- [Ajouter des utilisateurs en bloc](users-bulk-add.md)
- [Supprimer des utilisateurs en bloc](users-bulk-delete.md)
- [Restaurer des utilisateurs en bloc](users-bulk-restore.md)
