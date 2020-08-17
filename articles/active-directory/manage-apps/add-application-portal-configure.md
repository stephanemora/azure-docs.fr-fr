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
ms.openlocfilehash: beb5c7262a5475f5c1535e120fcebe4c70838c7e
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135485"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Démarrage rapide : Configurer les propriétés d’une application dans votre locataire Azure Active Directory (Azure AD)

Dans le guide de démarrage rapide précédent, vous avez ajouté une application à votre locataire Azure Active Directory (Azure AD). Quand vous ajoutez une application, vous indiquez à votre locataire Azure AD qu’il fait office de fournisseur d’identité pour l’application. À présent, vous allez configurer certaines des propriétés de l’application.
 
## <a name="prerequisites"></a>Prérequis

Pour configurer les propriétés d’une application dans votre locataire Azure AD, vous avez besoin des éléments suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou propriétaire du principal de service.
- Facultatif : effectuer [Voir vos applications](view-applications-portal.md).
- Facultatif : effectuer [Ajouter une application](add-application-portal.md).

>[!IMPORTANT]
>Pour tester les étapes de ce guide de démarrage rapide, utilisez un environnement de non-production.

## <a name="configure-app-properties"></a>Configurer les propriétés de l’application

Lorsque vous avez terminé l’ajout d’une application à votre locataire Azure AD, la page de vue d’ensemble correspondante s’affiche. Si vous configurez une application qui a déjà été ajoutée, consultez le premier guide de démarrage rapide. Il vous explique étape par étape comment afficher les applications ajoutées à votre locataire. 

Pour modifier les propriétés de l’application :

1. Dans le portail Azure AD, sélectionnez **Applications d’entreprise**. Ensuite, recherchez et sélectionnez l’application que vous souhaitez configurer.
2. Dans la section **Gérer**, sélectionnez **Propriétés** pour ouvrir le volet **Propriétés** à des fins de modification.

    ![Capture d’écran de l’écran Propriétés affichant les propriétés modifiables de l’application.](media/add-application-portal/edit-properties.png)

3. Prenez un moment afin de comprendre les options disponibles pour la configuration :
    - **Connexion permise pour les utilisateurs** détermine si les utilisateurs assignés à l’application peuvent se connecter.
    - **Affectation de l’utilisateur requise** détermine si les utilisateurs qui ne sont pas assignés à l’application peuvent se connecter.
    - **Visible par les utilisateurs** détermine si les utilisateurs assignés à une application peuvent la voir dans le [volet d’accès](https://myapps.microsoft.com) et le lanceur d’applications Office 365. (Voir le menu gaufre en haut à gauche d’un site web Office 365 ou Microsoft 365.)
    
    > [!TIP]
    > L’attribution d’utilisateurs intervient dans la section de navigation **Utilisateurs et groupes**.

    Les trois options peuvent être activées indépendamment les unes des autres et le comportement qui en résulte n’est pas toujours évident. Voici un tableau susceptible de vous aider :
    
    | Connexion permise pour les utilisateurs ? | Assignation requise de utilisateur ? | Visible par les utilisateurs ? | Comportement des utilisateurs attribués ou non à l’application. |
    |---|---|---|---|
    | Oui | Oui | Oui | Les utilisateurs attribués peuvent voir l’application et se connecter.<br>Les utilisateurs non attribués ne peuvent pas voir l’application ni se connecter. |
    | Oui | Oui | Non  | Les utilisations attribués ne peuvent pas voir l’application, mais peuvent se connecter.<br>Les utilisateurs non attribués ne peuvent pas voir l’application ni se connecter. |
    | Oui | Non  | Oui | Les utilisateurs attribués peuvent voir l’application et se connecter.<br>Les utilisateurs non attribués ne peuvent pas voir l’application, mais peuvent se connecter. |
    | Oui | Non  | Non  | Les utilisateurs attribués ne peuvent pas voir l’application, mais peuvent se connecter.<br>Les utilisateurs non attribués ne peuvent pas voir l’application, mais peuvent se connecter. |
    | Non  | Oui | Oui | Les utilisateurs attribués ne peuvent pas voir l’application ni se connecter.<br>Les utilisateurs non attribués ne peuvent pas voir l’application ni se connecter. |
    | Non  | Oui | Non  | Les utilisateurs attribués ne peuvent pas voir l’application ni se connecter.<br>Les utilisateurs non attribués ne peuvent pas voir l’application ni se connecter. |
    | Non  | Non  | Oui | Les utilisateurs attribués ne peuvent pas voir l’application ni se connecter.<br>Les utilisateurs non attribués ne peuvent pas voir l’application ni se connecter. |
    | Non  | Non  | Non  | Les utilisateurs attribués ne peuvent pas voir l’application ni se connecter.<br>Les utilisateurs non attribués ne peuvent pas voir l’application ni se connecter. |

4. Quand vous avez terminé, Sélectionnez **Enregistrer**.

## <a name="use-a-custom-logo"></a>Utiliser un logo personnalisé

Pour utiliser un logo personnalisé :

1. Créez un logo de 215 x 215 pixels et enregistrez-le au format .png.
2. Dans le portail Azure AD, sélectionnez **Applications d’entreprise**. Ensuite, recherchez et sélectionnez l’application que vous souhaitez configurer.
3. Dans la section **Gérer**, sélectionnez **Propriétés** pour ouvrir le volet **Propriétés** à des fins de modification. 
4. Sélectionnez l’icône permettant de charger le logo.
5. Quand vous avez terminé, Sélectionnez **Enregistrer**.

    ![Capture d’écran de l’écran Propriétés illustrant le changement de logo.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > La miniature affichée dans ce volet **Propriétés** n’est pas mise à jour immédiatement. Vous pouvez fermer et rouvrir le volet **Propriétés** pour voir l’icône mise à jour.


> [!TIP]
> Vous pouvez automatiser la gestion des applications à l’aide de l’API Graph. Consultez [Automatiser la gestion des applications avec l’API Microsoft Graph](https://docs.microsoft.com/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’envisagez pas de poursuivre la série de guides de démarrage rapide, pensez à supprimer l’application pour nettoyer votre locataire de test. La suppression de l’application est traitée dans le dernier guide de démarrage rapide de cette série : consultez [Supprimer une application](delete-application-portal.md).

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour découvrir comment configurer l’authentification unique pour une application.
> [!div class="nextstepaction"]
> [Configurer l’authentification unique](add-application-portal-setup-sso.md)
