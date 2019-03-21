---
title: Ajouter une application à votre locataire Azure Active Directory | Microsoft Docs
description: Ce démarrage rapide utilise le portail Azure pour ajouter une application de la galerie à votre locataire Azure Active Directory (Azure AD).
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/24/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f062596e2be93065b3c076787c2f5b3dfcedf9d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110118"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Démarrage rapide : Ajouter une application à votre locataire Azure Active Directory

Azure Active Directory (Azure AD) dispose d’une galerie contenant des milliers d’applications pré-intégrées. Certaines applications utilisées par votre organisation sont probablement dans la galerie. Ce démarrage rapide utilise le portail Azure pour ajouter une application de la galerie à votre locataire Azure Active Directory (Azure AD).

Une fois qu’une application est ajoutée à votre locataire Azure AD, vous pouvez :

- Gérer l’accès utilisateur à l’application avec une stratégie d’accès conditionnel.
- Configurer les utilisateurs pour une authentification unique à l’application avec leur compte Azure AD.

## <a name="before-you-begin"></a>Avant de commencer

Pour ajouter une application à votre locataire, vous devez :

- Un abonnement Azure AD
- Un abonnement pour lequel l’authentification unique est activée pour votre application

Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général pour votre client Azure AD, administrateur d’application cloud ou administrateur d’application.

Pour tester les étapes de ce didacticiel, nous recommandons l’utilisation d’un environnement de non-production. Si vous n’avez pas d’environnement de non-production Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Ajouter une application dans votre locataire Azure AD

Pour ajouter une application de la galerie à votre locataire Azure AD :

1. Dans le volet de navigation gauche du [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.

2. Dans le panneau **Azure Active Directory**, cliquez sur **Applications d’entreprise**.

    ![Ouvrir des applications d’entreprise](media/add-application-portal/open-enterprise-apps.png)

3. Le panneau **Toutes les applications** s’ouvre pour afficher un échantillon aléatoire des applications de votre locataire Azure AD.

    ![Panneau Toutes les applications](media/add-application-portal/applications-blade.png)


4. Cliquez sur **Nouvelle application** en haut du panneau **Toutes les applications**.

    ![Nouvelle application](media/add-application-portal/new-application.png)

5. Pour afficher la liste des applications dans la galerie, il est plus facile d’utiliser les **catégories** étant donné que les icônes sous **Applications recommandées** sont un échantillon aléatoire des applications de galerie.

    ![Rechercher par nom ou par catégorie](media/add-application-portal/categories.png)

    Pour afficher davantage d’applications, vous pouvez cliquer sur **Afficher plus**. Nous ne recommandons pas d’effectuer une recherche de cette façon, dans la mesure où il existe des milliers d’applications dans la galerie.

6. Pour rechercher une application, saisissez le nom de l’application que vous souhaitez ajouter sous **Ajouter à partir de la galerie**. Sélectionnez l’application à partir des résultats, puis cliquez sur **Ajouter**. L’exemple suivant montre le formulaire **Ajouter une application** qui apparaît après la recherche de github.com.

    ![Ajouter une application](media/add-application-portal/add-an-application.png)

6. Dans le formulaire spécifique à l’application, vous pouvez modifier les informations de propriété. Par exemple, vous pouvez modifier le nom de l’application afin qu’il corresponde aux besoins de votre organisation. Cet exemple utilise le nom **GitHub-test**.

8. Lorsque vous avez terminé de modifier les propriétés, cliquez sur **Ajouter**.

9. Une page de démarrage s’affiche avec les options de configuration de l’application pour votre organisation.

    ![Menu Démarrer](media/add-application-portal/get-started.png)

Vous avez terminé l’ajout de votre application. Faites une pause si vous le souhaitez. Les sections suivantes vous montrent comment modifier le logo et d’autres propriétés pour votre application.

## <a name="find-your-azure-ad-tenant-application"></a>Trouver votre application de locataire Azure AD

Supposons que vous deviez partir et que vous souhaitez maintenant continuer la configuration de votre application. La première chose à faire est de trouver votre application.

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur **Azure Active Directory**.

2. Dans le panneau Azure Active Directory, cliquez sur **Applications d’entreprise**.

3. À partir du menu déroulant **Type d’Application**, sélectionnez **Toutes les Applications**, puis cliquez sur **Appliquer**. Pour en savoir plus sur les options d’affichage, consultez [Afficher les applications du locataire](view-applications-portal.md).

4. Vous pouvez maintenant voir une liste de toutes les applications dans votre locataire Azure AD. La liste est un échantillon aléatoire. Pour afficher davantage d’applications, cliquez sur **Afficher plus** une ou plusieurs fois.

5. Pour trouver rapidement une application dans votre client, entrez le nom de l’application dans la zone de recherche, puis cliquez sur **Appliquer**. Cet exemple recherche l’application GitHub-test que nous avons ajoutée précédemment.

    ![Rechercher une application](media/add-application-portal/find-application.png)


## <a name="configure-user-sign-in-properties"></a>Configurer les propriétés de connexion de l’utilisateur

Maintenant que vous avez trouvé l’application, vous pouvez l’ouvrir et configurer ces propriétés.

Pour modifier les propriétés de l’application

1. Cliquez sur l’application pour l’ouvrir.
2. Cliquez sur **Propriétés** pour ouvrir le panneau Propriétés pour la modification.

    ![Panneau Modifier des propriétés](media/add-application-portal/edit-properties.png)

3. Prenez un moment pour comprendre les options de connexion. Les options **Connexion permise pour les utilisateurs**, **Affectation de l’utilisateur requise**, et **Visible par l’utilisateur** se combinent de façon à déterminer si les utilisateurs assignés ou non à l’application peuvent se connecter. Elles déterminent également si l’utilisateur peut voir l’application dans le volet d’accès.

    - **Connexion permise pour les utilisateurs** détermine si les utilisateurs assignés à l’application peuvent se connecter.
    - **Affectation de l’utilisateur requise** détermine si les utilisateurs qui ne sont pas assignés à l’application peuvent se connecter.
    - **Visible par l’utilisateur** détermine si les utilisateurs assignés à une application peuvent la voir dans le volet d’accès et le lanceur d’applications O365.

4. Utilisez les tableaux suivants pour vous aider à choisir les options qui conviennent le mieux à vos besoins.

   - Comportement pour les utilisateurs **assignés** :

       | Paramètres de propriété d'application | | | Expérience de l’utilisateur assigné | |
       |---|---|---|---|---|
       | Connexion permise pour les utilisateurs ? | Assignation requise de utilisateur ? | Visible par les utilisateurs ? | Est-ce que les utilisateurs assignés peuvent se connecter ? | Est-ce que les utilisateurs assignés peuvent voir l’application ?* |
       | Oui | Oui | Oui | Oui | Oui  |
       | Oui | Oui | no  | Oui | no   |
       | Oui | no  | Oui | Oui | Oui  |
       | Oui | no  | no  | Oui | no   |
       | no  | Oui | Oui | no  | no   |
       | no  | Oui | no  | no  | no   |
       | no  | no  | Oui | no  | no   |
       | no  | no  | no  | no  | no   |

   - Comportement pour les utilisateurs **non assignés** :

       | Paramètres de propriété d'application | | | Expérience de l’utilisateur non assigné | |
       |---|---|---|---|---|
       | Connexion permise pour les utilisateurs ? | Assignation requise de utilisateur ? | Visible par les utilisateurs ? | Est-ce que les utilisateurs non assignés peuvent se connecter ? | Est-ce que les utilisateurs non assignés peuvent voir l’application ?* |
       | Oui | Oui | Oui | no  | no   |
       | Oui | Oui | no  | no  | no   |
       | Oui | no  | Oui | Oui | no   |
       | Oui | no  | no  | Oui | no   |
       | no  | Oui | Oui | no  | no   |
       | no  | Oui | no  | no  | no   |
       | no  | no  | Oui | no  | no   |
       | no  | no  | no  | no  | no   |

     * Est-ce que l’utilisateur peut voir l’application dans le volet d’accès et le lanceur d’applications Office 365 ?

## <a name="use-a-custom-logo"></a>Utiliser un logo personnalisé

Pour utiliser un logo personnalisé :

1. Créez un logo de 215 x 215 pixels et enregistrez-le au format PNG.
2. Étant donné que vous avez déjà trouvé votre application, cliquez dessus.
2. Dans le panneau de gauche, cliquez sur **Propriétés**.
4. Téléchargez le logo.
5. Lorsque vous avez terminé, cliquez sur **Enregistrer**.

    ![Modifier le logo](media/add-application-portal/change-logo.png)


## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à ajouter une application de la galerie à votre locataire Azure AD. Vous avez appris à modifier les propriétés d’une application.

Vous êtes maintenant prêt à configurer l’application pour une authentification unique.

> [!div class="nextstepaction"]
> [Configurer l’authentification unique](configure-single-sign-on-portal.md)


