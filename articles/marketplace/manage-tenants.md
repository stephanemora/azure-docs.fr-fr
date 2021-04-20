---
title: Gérer les locataires sur la Place de marché commerciale - Place de marché Azure
description: Découvrez comment gérer les locataires pour le programme de la Place de marché commerciale dans l’Espace partenaires.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: 446792b26527126a4db99da14a2585c17cf8610c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107863"
---
# <a name="manage-tenants-in-the-commercial-marketplace"></a>Gérer les locataires dans la Place de marché commerciale

**Rôles appropriés**

- Manager

Un locataire Azure Active Directory (AD), également appelé *compte professionnel* dans cette documentation, est une représentation de la configuration de votre organisation dans le Portail Azure et vous aide à gérer une instance spécifique de services de cloud Microsoft pour les utilisateurs internes et externes. Si votre organisation est abonnée à un service cloud Microsoft comme Azure, Microsoft Intune ou Microsoft 365, un locataire Azure AD a été établi pour vous.

Vous pouvez configurer plusieurs locataires à utiliser avec l’Espace partenaires. Vous souhaiterez peut-être effectuer cette opération si votre entreprise possède plusieurs locataires (par exemple, contoso.com, contoso.uk, etc.). Vous pouvez lier les locataires supplémentaires ici. Cette association vous permettrait d’ajouter et de gérer des utilisateurs à partir des locataires supplémentaires sur votre compte de la Place de marché commerciale.

Les utilisateurs disposant du rôle Manager dans le compte de l’Espace partenaires peuvent ajouter et supprimer des locataires Azure AD du compte.

## <a name="add-an-existing-tenant"></a>Ajouter un locataire existant

Pour associer un autre locataire Azure AD à votre compte Espace partenaires :

1. Dans le coin supérieur droit de l’espace partenaires, sélectionnez **Paramètres** > **Paramètres du compte**.
1. Sous **Profil de l’organisation**, sélectionnez **Locataires**. Les associations de locataire actuelles sont affichées.
1. Dans l’onglet **Développeur**, sélectionnez **Associer**.
1. Saisissez les informations d’identification Azure AD du locataire que vous souhaitez associer.
1. Passez en revue l’organisation et le nom de domaine du locataire Azure AD. Pour compléter l’association, sélectionnez **Confirmer**.

Si l’association est réussie, vous pourrez ajouter et gérer les utilisateurs de compte dans la section Utilisateurs dans l’Espace partenaires. Pour en savoir plus sur l’ajout d’utilisateurs, consultez [Gérer les utilisateurs](add-manage-users.md).

## <a name="create-a-new-tenant"></a>Créer un nouveau locataire

Pour créer un nouveau locataire Azure AD avec votre compte Espace partenaires :

1. Dans le coin supérieur droit de l’espace partenaires, sélectionnez **Paramètres** > **Paramètres du compte**.
1. Sous **Profil de l’organisation**, sélectionnez **Locataires**. Les associations de locataire actuelles sont affichées.
1. Sous l’onglet Développeur, sélectionnez **Créer**.
1. Entrez les informations de répertoire de votre nouveau locataire Azure AD :
    - **Nom de domaine** : Le nom unique que nous allons utiliser pour le domaine Azure AD, avec « .onmicrosoft.com ». Par exemple, si vous entrez « exemple », votre domaine Azure AD sera « exemple.onmicrosoft.com ».
    - **E-mail du contact** : Une adresse e-mail à laquelle nous pouvons vous contacter sur votre compte si nécessaire.
    - **Informations du compte utilisateur de l’administrateur général** : Le prénom, nom de famille, nom d’utilisateur et mot de passe que vous souhaitez utiliser pour le nouveau compte d’administrateur général.
1. Sélectionnez Créer pour confirmer les nouvelles informations du compte et du domaine.
1. Connectez-vous avec vos nouveaux nom d’utilisateur d’administrateur général Azure AD et mot de passe pour commencer à [ajouter et gérer les utilisateurs](add-manage-users.md).

Pour plus d’informations sur la création de nouveaux locataires au sein du Portail Azure, plutôt que via le portail Espace partenaires, consultez l’article [Créer un nouveau locataire dans Azure Active Directory](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

## <a name="remove-a-tenant"></a>Supprimer un locataire

Pour supprimer un locataire depuis votre compte Espace partenaires, recherchez son nom dans la page **Locataires** (dans **Paramètres de compte**), puis sélectionnez **Supprimer**. Un message va s’afficher pour vous demander de confirmer la suppression du locataire. Une fois cette opération effectuée, les utilisateurs de ce locataire ne pourront plus se connecter au compte de l’Espace partenaires et les autorisations que vous avez configurées pour ces utilisateurs seront supprimées.

> [!TIP]
> Vous ne pouvez pas supprimer un locataire si vous avez ouvert une session dans l’Espace partenaires avec un compte de ce locataire. Pour supprimer un locataire, vous devez vous connecter à l’Espace partenaires en tant que **Manager** pour un autre locataire associé au compte. S’il n’existe qu’un seul locataire associé au compte, ce locataire peut être supprimé uniquement après vous être connecté avec le compte Microsoft qui a ouvert le compte.
