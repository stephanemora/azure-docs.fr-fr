---
title: Ajouter et gérer des utilisateurs pour le programme Place de marché commerciale - Place de marché Azure
description: Apprenez à gérer les utilisateurs dans le programme de la place de marché commercial pour un compte place de marché commerciale Microsoft dans l’Espace partenaires.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: d5b9197bfd2526dd414406ebf1aca509d3b3fa91
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107840"
---
# <a name="add-and-manage-users-for-the-commercial-marketplace"></a>Ajouter et gérer des utilisateurs pour le programme Place de marché commerciale

**Rôles appropriés**

- Propriétaire
- Manager

La section **Utilisateurs** de l’Espace partenaires (sous **Paramètres de compte**) vous permet d’utiliser Azure AD pour gérer les utilisateurs, groupes et applications Azure AD qui ont accès à votre compte Espace partenaires. Votre compte doit disposer d’autorisations de niveau Manager pour le [compte professionnel (locataire Azure AD)](company-work-accounts.md) dans lequel vous voulez ajouter ou modifier des utilisateurs. Pour gérer les utilisateurs d’un compte professionnel/locataire différent, vous devez vous déconnecter et puis vous reconnecter en tant qu’utilisateur avec les autorisateurs **Manager** sur ce compte/locataire.

Une fois connecté avec votre compte professionnel (locataire Azure AD), vous pouvez ajouter et gérer des utilisateurs.

## <a name="add-existing-users"></a>Ajouter des utilisateurs existants

Pour ajouter des utilisateurs qui existent déjà dans le [compte professionnel (locataire Azure AD)](company-work-accounts.md) de votre entreprise à votre compte Espace partenaires :

1. Accédez à **Utilisateurs** (sous **Paramètres de compte**) et sélectionnez **Ajouter des utilisateurs**.
1. Sélectionnez un ou plusieurs utilisateurs dans la liste qui s’affiche. Utilisez la zone de recherche pour rechercher des utilisateurs spécifiques. *Si vous sélectionnez plusieurs utilisateurs à ajouter à votre compte Espace partenaires, vous devez leur attribuer le même rôle ou le même ensemble d’autorisations personnalisées. Pour ajouter plusieurs utilisateurs avec des rôles/autorisations différents, répétez ces étapes pour chaque rôle ou ensemble d’autorisations personnalisées.
1. Lorsque vous avez fini de choisir des utilisateurs, cliquez sur **Ajouter la sélection**.
1. Dans la section **Rôles**, spécifiez le(s) rôle(s) ou les autorisations personnalisées pour les utilisateurs sélectionnés.
1. Sélectionnez **Enregistrer**.

## <a name="create-new-users"></a>Créer de nouveaux utilisateurs

Pour créer de nouveaux comptes d’utilisateur, vous devez disposer d’un compte avec des autorisations d’[Administrateur général](/azure/active-directory/roles/permissions-reference).

1. Accédez à **Utilisateurs** (sous **Paramètres de compte**), sélectionnez **Ajouter des utilisateurs**, puis **Créer de nouveaux utilisateurs**.
1. Entrez le prénom, le nom de famille et le nom d’utilisateur pour chaque nouvel utilisateur.
1. Si vous souhaitez que le nouvel utilisateur dispose d’un compte d’administrateur général dans le répertoire de votre organisation, cochez la case intitulée **Faire de cet utilisateur un administrateur général dans Azure AD, avec un contrôle total sur toutes les ressources du répertoire**. Cela offrira à l’utilisateur un accès complet à toutes les fonctionnalités d’administration dans l’Azure AD de l’entreprise. Il pourra ajouter et gérer les utilisateurs dans le compte professionnel de votre organisation (locataire Azure AD), mais pas dans l’Espace partenaires, sauf si vous accordez au compte les autorisations/rôles appropriés.
1. Si vous avez coché la case **Faire de cet utilisateur un administrateur général**, vous devez fournir une *adresse e-mail de récupération de mot de passe* afin que l’utilisateur puisse récupérer son mot de passe si nécessaire.
1. Dans la section **Appartenance au groupe**, sélectionnez tous les groupes auxquels vous souhaitez que le nouvel utilisateur appartienne.
1. Dans la section **Rôles**, spécifiez le(s) rôle(s) ou les autorisations personnalisées pour l’utilisateur.
1. Sélectionnez **Enregistrer**.

La création d’un utilisateur dans l’Espace partenaires entraîne également la création d’un compte pour cet utilisateur dans le compte professionnel (locataire Azure AD) auquel vous êtes connecté. Les modifications apportées à un nom d’utilisateur dans l’Espace partenaires seront également apportées dans le compte professionnel de votre organisation (locataire Azure AD).

## <a name="invite-new-users-by-email"></a>Inviter de nouveaux utilisateurs par e-mail

Pour inviter des utilisateurs qui ne font actuellement pas partie du compte professionnel de votre entreprise (locataire Azure AD) par courrier électronique, vous devez disposer d’un compte avec des autorisations d’[Administrateur général](/azure/active-directory/roles/permissions-reference).

1. Accédez à **Utilisateurs** (sous **Paramètres de compte**), sélectionnez **Ajouter des utilisateurs**, puis **Inviter des utilisateurs par e-mail**.
1. Entrez une ou plusieurs adresses e-mail (jusqu’à dix), séparées par des virgules ou des points-virgules.
1. Dans la section **Rôles**, spécifiez le(s) rôle(s) ou les autorisations personnalisées pour l’utilisateur.
1. Sélectionnez **Enregistrer**.

Les utilisateurs que vous avez invités recevront un e-mail d’invitation à rejoindre votre compte Espace partenaires. Un nouveau compte d’utilisateur invité est créé dans votre compte professionnel (locataire Azure AD). Chaque utilisateur devra accepter l’invitation avant de pouvoir accéder à votre compte.

Si vous avez besoin de renvoyer une invitation, accédez à la page *Utilisateurs*, recherchez l’invitation dans la liste des utilisateurs, sélectionnez l’adresse e-mail (ou le texte indiquant *Invitation en attente*). Ensuite, en bas de la page, sélectionnez **Renvoyer l’invitation**.

Si votre organisation utilise l’[intégration d’annuaire](https://docs.microsoft.com/previous-versions/azure/azure-services/jj573653(v=azure.100)) pour synchroniser le service d’annuaire local avec votre compte Azure AD, vous ne pourrez pas créer de nouveaux utilisateurs, groupes ou applications Azure AD dans l’Espace partenaires. Vous (ou un autre administrateur de l’annuaire local) devez les créer directement dans le répertoire local avant de pouvoir les afficher et les ajouter dans l’Espace partenaires.

## <a name="remove-a-user"></a>Supprimer un utilisateur

Pour supprimer un utilisateur de votre compte professionnel (locataire Azure AD), accédez à **Utilisateurs** (sous **Paramètres de compte**), sélectionnez l’utilisateur que vous souhaitez supprimer à l’aide de la case à cocher dans la colonne de droite, puis sélectionnez **Supprimer** dans les actions disponibles. Une fenêtre contextuelle s’affiche pour confirmer que vous souhaitez supprimer le ou les utilisateurs sélectionnés.

## <a name="change-a-user-password"></a>Modifier le mot de passe utilisateur

Si l’un de vos utilisateurs a besoin de modifier son mot de passe, il peut le faire lui-même si vous avez fourni une *adresse e-mail de récupération de mot de passe* lors de la création du compte d’utilisateur. Vous pouvez également mettre à jour le mot de passe utilisateur en suivant les étapes ci-dessous. Pour modifier un mot de passe utilisateur dans votre compte professionnel d’entreprise (locataire Azure AD), vous devez être connecté sur un compte avec des autorisations d’[Administrateur général](/azure/active-directory/roles/permissions-reference). Cela modifiera le mot de passe utilisateur dans votre locataire Azure AD, ainsi que le mot de passe permettant d’accéder à l’Espace partenaires.

1. Depuis la page **Utilisateurs** (sous **Paramètres de compte**), sélectionnez le nom du compte d’utilisateur que vous souhaitez modifier.
1. Cliquez sur le bouton **Réinitialiser le mot de passe** en bas de la page.
1. Une page de confirmation affiche les informations de connexion de l’utilisateur, dont un mot de passe temporaire. N’oubliez pas d’imprimer ou de copier ces informations et de les fournir à l’utilisateur, comme vous ne pourrez pas accéder au mot de passe temporaire une fois que vous aurez quitté cette page.
