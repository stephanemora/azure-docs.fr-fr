---
title: 'Démarrage rapide : Configurer les propriétés d’une application dans votre locataire Azure Active Directory (Azure AD)'
description: Ce guide de démarrage rapide utilise le portail Azure pour configurer une application inscrite auprès de votre locataire Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82a3310f6fc2169a515b4b13d81c88d187bd0f9c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956150"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Démarrage rapide : Configurer les propriétés d’une application dans votre locataire Azure Active Directory (Azure AD)

Dans le guide de démarrage rapide précédent, vous avez ajouté une application à votre locataire Azure AD. Quand vous ajoutez une application, vous indiquez à votre locataire Azure AD qu’il fait office de fournisseur d’identité pour l’application. À présent, vous allez configurer certaines des propriétés de l’application.
 
## <a name="prerequisites"></a>Prérequis

Pour configurer les propriétés d’une application dans votre locataire Azure AD, vous avez besoin des éléments suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.
- (Facultatif : effectuer [Voir vos applications](view-applications-portal.md)).
- (Facultatif : effectuer [Ajouter une application](add-application-portal.md)).

>[!IMPORTANT]
>Nous vous recommandons d’utiliser un environnement de non-production pour tester les étapes de ce guide de démarrage rapide.

## <a name="configure-app-properties"></a>Configurer les propriétés de l’application

Quand vous avez terminé d’ajouter une application à votre locataire Azure AD, la page de vue d’ensemble correspondante s’affiche immédiatement. Si vous configurez une application qui a déjà été ajoutée, regardez le premier guide de démarrage rapide, qui vous guide dans l’affichage des applications ajoutées à votre locataire. 

Pour modifier les propriétés de l’application :

1. Dans le portail Azure AD, sélectionnez **Applications d’entreprise** puis recherchez et sélectionnez l’application que vous souhaitez configurer.
2. Dans la section Gérer, sélectionnez **Propriétés** pour ouvrir le volet des propriétés à des fins de modification.
    ![Montre l’écran Propriétés et les propriétés modifiables de l’application](media/add-application-portal/edit-properties.png)
3. Prenez un moment afin de comprendre les options disponibles pour la configuration.
    - **Connexion permise pour les utilisateurs** détermine si les utilisateurs assignés à l’application peuvent se connecter.
    - **Affectation de l’utilisateur requise** détermine si les utilisateurs qui ne sont pas assignés à l’application peuvent se connecter.
    - **Visible par les utilisateurs** détermine si les utilisateurs attribués à une application peuvent la voir dans le volet d’accès (https://myapps.microsoft.com) et le lanceur d’applications O365 (menu Gaufre en haut à gauche d’un site web Office 365 ou Microsoft 365).
4. Utilisez les tableaux suivants pour vous aider à choisir les meilleures options en fonction de vos besoins.

   - Comportement pour les utilisateurs **assignés** :

       | Propriété d’application | Propriété d’application | Propriété d’application | Expérience de l’utilisateur assigné | Expérience de l’utilisateur assigné |
       |---|---|---|---|---|
       | Connexion permise pour les utilisateurs ? | Assignation requise de utilisateur ? | Visible par les utilisateurs ? | Est-ce que les utilisateurs assignés peuvent se connecter ? | Est-ce que les utilisateurs assignés peuvent voir l’application ?* |
       | Oui | Oui | Oui | Oui | Oui  |
       | Oui | Oui | non  | Oui | non   |
       | Oui | non  | Oui | Oui | Oui  |
       | Oui | non  | non  | Oui | non   |
       | non  | Oui | Oui | non  | non   |
       | non  | Oui | non  | non  | non   |
       | non  | non  | Oui | non  | non   |
       | non  | non  | non  | non  | non   |

   - Comportement pour les utilisateurs **non assignés** :

       | Propriété d’application | Propriété d’application | Propriété d’application | Expérience de l’utilisateur non assigné | Expérience de l’utilisateur non assigné |
       |---|---|---|---|---|
       | Connexion permise pour les utilisateurs ? | Assignation requise de utilisateur ? | Visible par les utilisateurs ? | Est-ce que les utilisateurs non assignés peuvent se connecter ? | Est-ce que les utilisateurs non assignés peuvent voir l’application ?* |
       | Oui | Oui | Oui | non  | non   |
       | Oui | Oui | non  | non  | non   |
       | Oui | non  | Oui | Oui | non   |
       | Oui | non  | non  | Oui | non   |
       | non  | Oui | Oui | non  | non   |
       | non  | Oui | non  | non  | non   |
       | non  | non  | Oui | non  | non   |
       | non  | non  | non  | non  | non   |

     \* Est-ce que l’utilisateur peut voir l’application dans le volet d’accès et le lanceur d’applications Office 365 ?

## <a name="use-a-custom-logo"></a>Utiliser un logo personnalisé

Pour utiliser un logo personnalisé :

1. Créez un logo de 215 x 215 pixels et enregistrez-le au format PNG.
2. Dans le portail Azure AD, sélectionnez **Applications d’entreprise** puis recherchez et sélectionnez l’application que vous souhaitez configurer.
3. Dans la section Gérer, sélectionnez **Propriétés** pour ouvrir le volet des propriétés à des fins de modification. 
4. Sélectionnez l’icône permettant de charger le logo.
5. Quand vous avez terminé, Sélectionnez **Enregistrer**. 
    ![Montre comment changer le logo de la page Propriétés de l’application](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > La miniature affichée dans ce volet **Propriétés** n’est pas mise à jour immédiatement. Vous pouvez fermer et rouvrir les propriétés pour afficher l’icône mise à jour.

## <a name="next-steps"></a>Étapes suivantes

Les propriétés d’une application étant configurées, vous pouvez passer à la configuration de l’authentification unique.

- [Configurer l’authentification unique](add-application-portal-setup-sso.md)
- [Supprimer une application](delete-application-portal.md)