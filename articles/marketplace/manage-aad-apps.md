---
title: Ajouter et gérer des applications Azure AD - Place de marché Azure
description: Apprenez à ajouter et gérer des applications Azure AD dans le cadre du programme Place de marché commerciale au sein de l'Espace partenaires.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 04/06/2021
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.openlocfilehash: 77f6b2ec71df63da1fe5ff52f948ded9502c5edb
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110076273"
---
# <a name="add-and-manage-azure-ad-applications"></a>Ajouter et gérer des applications Azure AD

**Rôles appropriés**

- Propriétaire
- Manager

Vous pouvez autoriser des applications ou services qui font partie de la plateforme Azure Active Directory (Azure AD) de votre entreprise à accéder à votre compte Espace partenaires.

## <a name="add-existing-azure-ad-applications"></a>Ajouter une application Azure AD existante

Pour ajouter des applications qui existent déjà dans Azure Active Directory :

1. Depuis la page **Utilisateurs** (sous **Paramètres de compte**), sélectionnez **Ajouter des applications Azure AD**.
1. Sélectionnez une ou plusieurs applications Azure AD dans la liste qui s’affiche. Utilisez la zone de recherche pour rechercher des applications Azure AD spécifiques. Si vous sélectionnez plusieurs applications Azure AD à ajouter à votre compte Espace partenaires, vous devez leur attribuer le même rôle ou le même ensemble d’autorisations personnalisées. Pour ajouter plusieurs applications Azure AD avec des rôles/autorisations différents, répétez ces étapes pour chaque rôle ou ensemble d’autorisations personnalisées.
1. Une fois les applications Azure AD sélectionnées, sélectionnez **Ajouter la sélection**.
1. Dans la section **Rôles**, spécifiez le(s) rôle(s) ou les autorisations personnalisées pour les applications Azure AD sélectionnées.
1. Sélectionnez **Enregistrer**.

## <a name="add-new-azure-ad-applications"></a>Ajouter de nouvelles applications Azure AD

Si vous souhaitez accorder l’accès à l’Espace partenaires à un tout nouveau compte d’application Azure AD, vous pouvez en créer un dans la section **Utilisateurs**. Cela a pour effet de créer un nouveau compte dans le compte professionnel de votre organisation (locataire Azure AD), pas uniquement dans votre compte Espace partenaires. Si vous utilisez principalement cette application Azure AD pour l’authentification à l’Espace partenaires, et que vous ne souhaitez pas que les utilisateurs y accèdent directement, vous pouvez entrer une adresse valide pour l’**URL de réponse** et l’**URI ID d’application**, tant que ces valeurs ne sont pas utilisées par une autre application Azure AD de votre répertoire.

1. Depuis la page **Utilisateurs** (sous **Paramètres de compte**), sélectionnez **Ajouter des applications Azure AD**.
1. Sur la page suivante, sélectionnez **Nouvelle application Azure AD**.
1. Entrez l’**URL de réponse** pour la nouvelle application Azure AD. Il s’agit de l’URL grâce à laquelle les utilisateurs peuvent se connecter et utiliser votre application Azure AD (parfois également appelée URL de l’application ou URL de connexion). L’*URL de réponse* ne peut pas dépasser 256 caractères et doit être unique dans votre répertoire.
1. Entrez l’**URI ID d’application** pour la nouvelle application Azure AD. Il s’agit d’un identificateur logique de l’application Azure AD qui est présenté lorsqu’une demande d’authentification unique est envoyée à Azure AD. L’*URI ID d’application* doit être unique pour chaque application Azure AD figurant dans votre annuaire. Il ne doit pas comporter plus de 256 caractères. Pour plus d’informations sur l’URI ID d’application, consultez [Intégration d’applications dans Azure Active Directory](/azure/active-directory/develop/howto-modify-supported-accounts#change-the-application-registration-to-support-different-accounts).
1. Dans la section **Rôles**, spécifiez le(s) rôle(s) ou les autorisations personnalisées pour l’application Azure AD.
1. Sélectionnez **Enregistrer**.

Une fois que vous avez ajouté ou créé une application Azure AD, vous pouvez revenir à la section **Utilisateurs** et sélectionner le nom de l’application pour passer en revue les paramètres de l’application, tels que l’ID du locataire, l’ID client, l’URL de réponse et l’URI ID d’application.

## <a name="remove-an-azure-ad-application"></a>Supprimer une application Azure AD

Pour supprimer une application de votre compte professionnel (locataire Azure AD), accédez à **Utilisateurs** (sous **Paramètres de compte**), sélectionnez l’application que vous souhaitez supprimer à l’aide de la case à cocher dans la colonne de droite, puis sélectionnez **Supprimer** dans les actions disponibles. Une fenêtre contextuelle s’affiche pour confirmer que vous souhaitez supprimer la ou les applications sélectionnées.

## <a name="manage-keys-for-an-azure-ad-application"></a>Gérer les clés d’une application Azure AD

Si votre application Azure AD lit et écrit des données dans Microsoft Azure AD, elle aura besoin d’une clé. Vous pouvez créer des clés pour une application Azure AD en modifiant ses informations dans l’Espace partenaires. Vous pouvez également supprimer les clés qui ne sont plus nécessaires.

1. Depuis la page **Utilisateurs** (sous **Paramètres de compte**), sélectionnez le nom de l’application Azure AD. Vous verrez toutes les clés actives pour l’application Azure AD, ainsi que leurs dates de création et d’expiration.
1. Pour supprimer une clé qui n’est plus nécessaire, sélectionnez **Supprimer**.
1. Pour ajouter une nouvelle clé, sélectionnez **Ajouter une nouvelle clé**.
1. Vous verrez un écran affichant l’**ID client** et les **valeurs de clé**. Veillez à imprimer ou copier ces informations, car vous ne pourrez plus y accéder une fois que vous aurez quitté cette page.
1. Si vous souhaitez créer plus de clés, sélectionnez **Ajouter une autre clé**.
