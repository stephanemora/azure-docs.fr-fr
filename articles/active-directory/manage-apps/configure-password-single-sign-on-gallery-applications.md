---
title: Configurer l’authentification unique par mot de passe pour une application de la galerie Azure AD | Microsoft Docs
description: Comment configurer une application pour l’authentification unique basée sur un mot de passe sécurisé quand elle est déjà listée dans la galerie d’applications Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 9fb33c4110a590539c85364885da9a27853f6bd0
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146888"
---
# <a name="configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Configurer l’authentification unique par mot de passe pour une application de la galerie Azure AD

Lorsque vous ajoutez une application à partir de la [galerie d’applications Azure AD (Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), vous pouvez choisir la façon dont vous souhaitez que vos utilisateurs s’y connectent. Vous pouvez configurer ce choix à tout moment en sélectionnant **Authentification unique** sur une application d’entreprise dans le [portail Azure](https://portal.azure.com/).

L’une des options d’authentification unique (SSO) disponibles est l’[authentification unique avec mot de passe](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Il s’agit d’un excellent moyen de commencer à intégrer rapidement des applications dans Azure AD. Cette option :

-   Stocke et relit en toute sécurité des noms d’utilisateur et mots de passe pour l’application que vous avez intégrée à Azure AD

-   Prend en charge des applications qui requièrent plusieurs champs de connexion au-delà des champs de nom d’utilisateur et de mot de passe

-   Vous permet de personnaliser les étiquettes des champs de nom d’utilisateur et de mot de passe que vos utilisateurs voient dans le [volet d’accès aux applications](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) lorsqu’ils entrent leurs informations d’identification

-   Autorise vos utilisateurs à fournir leurs propres noms d’utilisateur et mots de passe pour tout compte d’application existant qu’ils entrent manuellement dans le [volet d’accès aux applications](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Autorise un membre du groupe d’entreprise à utiliser la fonctionnalité [Accès aux applications en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) pour spécifier les noms d’utilisateur et mots de passe affectés à un utilisateur

-   Permet à un administrateur de spécifier les noms d’utilisateur et mots de passe affectés à un utilisateur à l’aide de la fonctionnalité Mettre à jour les informations d’identification lors de l’[affectation d’un utilisateur à une application](#assign-a-user-to-an-application-directly)

-   Permet à un administrateur d’utiliser la fonctionnalité Mettre à jour les informations d’identification pour spécifier le nom d’utilisateur ou mot de passe partagé pour un groupe de personnes lors de l’[affectation d’un groupe à une application](#assign-an-application-to-a-group-directly)

La section ci-après décrit la procédure d’activation de l’[authentification unique avec mot de passe](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) pour une application qui figure déjà dans la [galerie d’applications Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="overview-of-required-steps"></a>Vue d’ensemble des étapes nécessaires
Pour configurer une application à partir de la galerie Azure AD, vous devez effectuer les opérations suivantes :

-   [Ajouter une application à partir de la galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurer l’application pour l’authentification unique basée sur un mot de passe](#configure-the-application-for-password-single-sign-on)

-   Affecter l’application à un utilisateur ou à un groupe :

    -   [Affecter un utilisateur directement à une application](#assign-a-user-to-an-application-directly)

    -   [Affecter une application directement à un groupe](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Ajouter une application à partir de la galerie Azure AD

Pour ajouter une application à partir de la galerie Azure AD, procédez comme suit :

1.  Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous comme **administrateur général** ou **coadministrateur**.

2.  Ouvrez l’**extension Azure Active Directory** en sélectionnant **Tous les services** en haut du menu sur la gauche.

3.  Entrez **azure active directory** dans la zone de recherche, puis sélectionnez l’élément **Azure Active Directory**.

4.  Sélectionnez **Applications d’entreprise** dans le menu Azure AD sur la gauche.

5.  Sélectionnez le bouton **Ajouter** dans le coin supérieur droit du volet **Applications d’entreprise**.

6.  Dans la zone **Entrer un nom** de la section **Ajouter à partir de la galerie**, entrez le nom de l’application.

7.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

8.  Avant d’ajouter l’application, vous pouvez changer son nom dans la zone **Nom**.

9.  Sélectionnez **Ajouter** pour ajouter l’application.

Après un court délai, vous pouvez voir apparaître le volet de configuration de l’application.

## <a name="configure-the-application-for-password-single-sign-on"></a>Configurer l’application pour l’authentification unique par mot de passe

Pour configurer l’authentification unique pour une application, procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com/) et connectez-vous comme **administrateur général** ou **coadministrateur**.

2. Ouvrez l’**extension Azure Active Directory** en sélectionnant **Tous les services** en haut du menu sur la gauche.

3. Entrez **azure active directory** dans la zone de recherche, puis sélectionnez l’élément **Azure Active Directory**.

4. Sélectionnez **Applications d’entreprise** dans le menu Azure Active Directory sur la gauche.

5. Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

   Si l’application que vous recherchez ne figure pas dans la liste, utilisez le contrôle **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6. Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7. Une fois l’application chargée, sélectionnez **Authentification unique** dans le menu de l’application sur la gauche.

8. Sélectionnez le mode **Authentification par mot de passe**.

9. [Affectez des utilisateurs à l’application](#assign-a-user-to-an-application-directly).

10. Vous pouvez également fournir des informations d’identification pour le compte des utilisateurs en sélectionnant la ligne d’un utilisateur, **Mettre à jour les informations d’identification**, puis en entrant le nom d’utilisateur et le mot de passe. Dans le cas contraire, les utilisateurs sont invités à entrer leurs informations d’identification lorsqu’ils démarrent l’application.

## <a name="assign-a-user-to-an-application-directly"></a>Affecter un utilisateur directement à une application

Pour affecter un ou plusieurs utilisateurs directement à une application, procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com/) et connectez-vous comme **administrateur général**.

2. Ouvrez l’**extension Azure Active Directory** en sélectionnant **Tous les services** en haut du menu sur la gauche.

3. Entrez **azure active directory** dans la zone de recherche, puis sélectionnez l’élément **Azure Active Directory**.

4. Sélectionnez **Applications d’entreprise** dans le menu Azure Active Directory sur la gauche.

5. Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

   Si l’application que vous recherchez ne figure pas dans la liste, utilisez le contrôle **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6. Sélectionnez l’application à laquelle vous souhaitez affecter un utilisateur.

7. Une fois l’application chargée, sélectionnez **Utilisateurs et groupes** dans le menu de l’application sur la gauche.

8. Sélectionnez le bouton **Ajouter** en haut de la liste **Utilisateurs et groupes** pour ouvrir le volet **Ajouter une attribution**.

9. Sélectionnez **Utilisateurs et groupes** dans le volet **Ajouter une attribution**.

10. Entrez le nom complet ou l’adresse e-mail de l’utilisateur dans la zone de recherche **Rechercher par nom ou adresse de messagerie**.

11. Pointez sur l’utilisateur dans la liste, puis cochez la case en regard de la photo de profil ou du logo de l’utilisateur pour ajouter ce dernier à la liste **Sélectionné**.

12. Facultatif : Si vous souhaitez ajouter plusieurs utilisateurs, entrez un autre nom complet ou une autre adresse e-mail dans la zone **Rechercher par nom ou adresse de messagerie**, puis cochez la case correspondant à cet utilisateur pour l’ajouter à la liste **Sélectionné**.

13. Après avoir sélectionné les utilisateurs, utilisez le bouton **Sélectionner** pour les ajouter à la liste des utilisateurs et des groupes à affecter à l’application.

14. Facultatif : Utilisez la commande **Sélectionner un rôle** dans le volet **Ajouter une attribution** pour sélectionner un rôle à affecter aux utilisateurs que vous avez sélectionnés.

15. Sélectionnez **Attribuer** pour affecter l’application aux utilisateurs sélectionnés.

## <a name="assign-an-application-to-a-group-directly"></a>Affecter une application directement à un groupe

Pour affecter un ou plusieurs groupes directement à une application, procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com/) et connectez-vous comme **administrateur général**.

2. Ouvrez l’**extension Azure Active Directory** en sélectionnant **Tous les services** en haut du menu sur la gauche.

3. Entrez **azure active directory** dans la zone de recherche, puis sélectionnez l’élément **Azure Active Directory**.

4. Sélectionnez **Applications d’entreprise** dans le menu Azure AD sur la gauche.

5. Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

   Si l’application que vous recherchez ne figure pas dans la liste, utilisez le contrôle **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6. Sélectionnez l’application à laquelle vous souhaitez affecter un utilisateur.

7. Une fois l’application chargée, sélectionnez **Utilisateurs et groupes** dans le menu de l’application sur la gauche.

8. Sélectionnez le bouton **Ajouter** en haut de la liste **Utilisateurs et groupes** pour ouvrir le volet **Ajouter une attribution**.

9. Sélectionnez **Utilisateurs et groupes** dans le volet **Ajouter une attribution**.

10. Entrez le nom complet du groupe que vous souhaitez affecter dans la zone de recherche **Rechercher par nom ou adresse de messagerie**.

11. Pointez sur le **groupe** dans la liste, puis cochez la case en regard de la photo de profil ou du logo du groupe pour ajouter ce dernier à la liste **Sélectionné**.

12. Facultatif : Si vous souhaitez ajouter plusieurs groupes, entrez un autre nom de groupe complet dans la zone de recherche **Rechercher par nom ou adresse de messagerie**, puis cochez la case correspondante pour ajouter ce groupe à la liste **Sélectionné**.

13. Après avoir sélectionné les groupes, utilisez le bouton **Sélectionner** pour les ajouter à la liste des utilisateurs et des groupes à affecter à l’application.

14. Facultatif : Utilisez la commande **Sélectionner un rôle** dans le volet **Ajouter une attribution** pour sélectionner un rôle à affecter aux groupes que vous avez sélectionnés.

15. Sélectionnez **Attribuer** pour affecter l’application aux groupes sélectionnés.

Après une courte période, les utilisateurs que vous avez sélectionnés doivent être en mesure de lancer ces applications à partir du volet d’accès.

## <a name="next-steps"></a>Étapes suivantes
[Fournir une authentification unique à vos applications via le proxy d’application](application-proxy-configure-single-sign-on-with-kcd.md).
