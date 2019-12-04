---
title: Guide de démarrage rapide - Ajouter une application à votre locataire Azure Active Directory
description: Ce démarrage rapide utilise le portail Azure pour ajouter une application de la galerie à votre locataire Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 650a4f643f170fc64dd0e0643d645a7dcbffdfbc
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420468"
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

1. Dans le panneau de navigation gauche du [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.

2. Dans le volet **Azure Active Directory**, sélectionnez **Applications d’entreprise**. Le volet **Toutes les applications** s’ouvre et affiche un échantillon aléatoire des applications de votre locataire Azure AD.

3. Pour ajouter une application de galerie à votre locataire, sélectionnez **Nouvelle application**. 

    ![Sélectionner Nouvelle application pour ajouter une application de galerie à votre locataire](media/add-application-portal/new-application.png)

 4. Basculez vers la nouvelle expérience de la galerie (préversion) : dans la bannière en haut de la page **Ajouter une application**, sélectionnez le lien **Cliquez ici pour essayer la Galerie d’applications nouvelle et améliorée**.

5. Le volet **Parcourir la galerie Azure AD (préversion)** s’ouvre et affiche des vignettes pour les plateformes cloud, les applications locales et les applications principales. Notez que les applications listées dans la section **Applications principales** ont des icônes indiquant si elles prennent en charge l’authentification unique fédérée et le provisionnement.

    ![Rechercher une application par nom ou par catégorie](media/add-application-portal/browse-gallery.png)

6. Vous pouvez parcourir la galerie de l’application que vous souhaitez ajouter ou rechercher l’application en entrant son nom dans la zone de recherche. Ensuite, sélectionnez l’application dans les résultats. Dans le formulaire, vous pouvez modifier le nom de l’application afin qu’il corresponde aux besoins de votre organisation. Dans cet exemple, nous avons changé le nom en **GitHub-test**.

    ![Montre comment ajouter une application à partir de la galerie](media/add-application-portal/create-application.png)

7. Sélectionnez **Create** (Créer). Une page de démarrage s’affiche avec les options de configuration de l’application pour votre organisation.

Vous avez terminé l’ajout de votre application. Les sections suivantes vous montrent comment modifier le logo et d’autres propriétés pour votre application.

## <a name="find-your-azure-ad-tenant-application"></a>Trouver votre application de locataire Azure AD

Supposons que vous deviez partir et que vous souhaitez maintenant continuer la configuration de votre application. La première chose à faire est de trouver votre application.

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.
1. Dans le volet **Azure Active Directory**, sélectionnez **Applications d’entreprise**.
1. À partir du menu déroulant **Type d’Application**, sélectionnez **Toutes les Applications**, puis sélectionnez **Appliquer**. Pour en savoir plus sur les options d’affichage, consultez [Afficher les applications du locataire](view-applications-portal.md).
1. Vous pouvez maintenant voir une liste de toutes les applications dans votre locataire Azure AD. La liste est un échantillon aléatoire. Pour voir davantage d’applications, sélectionnez **Afficher plus** une ou plusieurs fois.
1. Pour trouver rapidement une application dans votre locataire, entrez le nom de l’application dans la zone de recherche, puis sélectionnez **Appliquer**. Cet exemple recherche l’application GitHub-test ajoutée précédemment.

    ![Montre comment rechercher une application avec la zone de recherche](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Configurer les propriétés de connexion de l’utilisateur

Maintenant que vous avez trouvé l’application, vous pouvez l’ouvrir et configurer ces propriétés.

Pour modifier les propriétés de l’application :

1. Sélectionnez l’application pour l’ouvrir.
2. Sélectionnez **Propriétés** pour ouvrir le volet Propriétés pour la modification.

    ![Montre l’écran Propriétés et les propriétés modifiables de l’application](media/add-application-portal/edit-properties.png)

3. Prenez un moment pour comprendre les options de connexion. Les options déterminent la façon dont les utilisateurs qui sont assignés ou non assignés à l’application peuvent s’y connecter. De plus, les options déterminent si un utilisateur peut voir l’application dans le volet d’accès.

    - **Connexion permise pour les utilisateurs** détermine si les utilisateurs assignés à l’application peuvent se connecter.
    - **Affectation de l’utilisateur requise** détermine si les utilisateurs qui ne sont pas assignés à l’application peuvent se connecter.
    - **Visible par les utilisateurs** détermine si les utilisateurs assignés à une application peuvent la voir dans le volet d’accès et le lanceur d’applications O365.

4. Utilisez les tableaux suivants pour vous aider à choisir les meilleures options en fonction de vos besoins.

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
       | Connexion permise pour les utilisateurs ? | Assignation requise de utilisateur ? | Visible par les utilisateurs ? | Est-ce que les utilisateurs non assignés peuvent se connecter ? | Est-ce que les utilisateurs non assignés peuvent voir l’application ?* |
       | Oui | Oui | Oui | no  | no   |
       | Oui | Oui | no  | no  | no   |
       | Oui | no  | Oui | Oui | no   |
       | Oui | no  | no  | Oui | no   |
       | no  | Oui | Oui | no  | no   |
       | no  | Oui | no  | no  | no   |
       | no  | no  | Oui | no  | no   |
       | no  | no  | no  | no  | no   |

     \* Est-ce que l’utilisateur peut voir l’application dans le volet d’accès et le lanceur d’applications Office 365 ?

## <a name="use-a-custom-logo"></a>Utiliser un logo personnalisé

Pour utiliser un logo personnalisé :

1. Créez un logo de 215 x 215 pixels et enregistrez-le au format PNG.
1. Étant donné que vous avez déjà trouvé votre application, sélectionnez-la.
1. Dans le volet gauche, sélectionnez **Propriétés**.
1. Téléchargez le logo.
1. Quand vous avez terminé, Sélectionnez **Enregistrer**. 

    ![Montre comment changer le logo de la page Propriétés de l’application](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > La miniature affichée dans ce volet **Propriétés** n’est pas mise à jour immédiatement. Vous pouvez fermer et rouvrir les propriétés pour afficher l’icône mise à jour.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez ajouté l’application à votre organisation Azure AD, [choisissez une méthode d’authentification unique](what-is-single-sign-on.md#choosing-a-single-sign-on-method) que vous souhaitez utiliser et reportez-vous à l’article approprié ci-dessous :

- [Configurer l’authentification unique SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configurer l’authentification unique par mot de passe](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurer l’authentification liée](configure-linked-sign-on.md)
