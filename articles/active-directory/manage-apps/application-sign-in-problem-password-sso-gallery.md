---
title: Problèmes de connexion à une application de la galerie Azure AD configurée pour l’authentification unique | Microsoft Docs
description: Comment résoudre les problèmes de connexion à une application de la galerie Azure AD configurée pour l’authentification unique par mot de passe.
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6eebde3a7c6163b7faf92be193fe442cd5b74d2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84759111"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Problèmes de connexion à une application de la galerie Azure AD configurée pour l’authentification unique

Le volet d’accès est un portail basé sur le web. Cela permet aux utilisateurs disposant de comptes professionnels ou scolaires Azure Active Directory (Azure AD) d’accéder à des applications cloud auxquelles ils sont autorisés. Les utilisateurs disposant d’éditions Azure AD peuvent également utiliser des fonctionnalités de gestion de groupes et d’applications en libre-service par le biais du volet d’accès.

Le volet d’accès est séparé du portail Azure. Les utilisateurs n’ont pas besoin d’un abonnement Azure pour utiliser le volet d’accès.

Pour utiliser l’authentification unique (SSO) par mot de passe dans le volet d’accès, l’extension du volet d’accès doit être installée dans votre navigateur. L’extension est téléchargée automatiquement lorsque vous sélectionnez une application configurée pour l’authentification unique (SSO) avec mot de passe.

## <a name="browser-requirements-for-access-panel"></a>Configuration requise du navigateur pour le volet d’accès

Le volet d’accès nécessite un navigateur qui prend en charge JavaScript et dans lequel le CSS est activé.

Les navigateurs suivants prennent en charge l’authentification unique par mot de passe :

- Internet Explorer 8, 9, 10 et 11 sur Windows 7 ou ultérieur

- Chrome sur Windows 7 ou version ultérieure, ou sur Mac OS X ou version ultérieure

- Firefox 26.0 ou version ultérieure sur Windows XP SP2 ou version ultérieure, ou sur Mac OS X 10.6 ou version ultérieure

>[!NOTE]
>L’extension de l’authentification unique par mot de passe sera disponible pour Microsoft Edge dans Windows 10 lorsque la prise en charge des extensions de navigateur a été ajoutée pour Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Installer l’extension de navigateur du volet d’accès

Procédez comme suit :

1. Ouvrez le [Volet d’accès](https://myapps.microsoft.com) dans un navigateur pris en charge, et connectez-vous en tant qu’utilisateur dans Azure AD.

2. Sélectionnez une application compatible avec l’authentification unique par mot de passe dans le volet d’accès.

3. Lorsque vous y êtes invité, sélectionnez **Installer maintenant**.

4. Vous êtes redirigé vers un lien de téléchargement selon votre navigateur. Sélectionnez **Ajouter** pour installer l’extension de navigateur.

5. Si vous y êtes invité, sélectionnez **Activer** ou **Autoriser**.

6. Après l’installation, redémarrez votre navigateur.

7.  Connectez-vous au volet d’accès et essayez de démarrer vos applications compatibles avec l’authentification unique par mot de passe.

Vous pouvez également télécharger directement les extensions pour Chrome et Firefox via ces liens :

-   [Extension du volet d’accès pour Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extension du volet d’accès pour Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Configurer une stratégie de groupe pour Internet Explorer

Vous pouvez configurer une stratégie de groupe pour installer à distance l’extension du volet d’accès pour Internet Explorer sur les machines de vos utilisateurs.

Voici les conditions préalables requises :

-   Les [services de domaine Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) doivent être configurés, et les ordinateurs de vos utilisateurs doivent être joints à votre domaine.

-   Vous disposez de l’autorisation « Modifier les paramètres » pour modifier l’objet de stratégie de groupe (GPO). Par défaut, les membres des groupes de sécurité suivants ont cette autorisation : administrateurs de domaine, administrateurs d’entreprise et propriétaires de créateur de stratégie de groupe. [Plus d’informations](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

Pour configurer la stratégie de groupe et la déployer, consultez [Déploiement de l’extension du volet d’accès pour Internet Explorer à l’aide de la stratégie de groupe](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Résolution des problèmes liés au volet d’accès dans Internet Explorer

Pour accéder à un outil de diagnostic et aux instructions pour configurer l’extension, consultez [Résolution des problèmes liés à l’extension du volet d’accès pour Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Configurer l’authentification unique par mot de passe pour une application de la galerie Azure AD

Pour configurer une application à partir de la galerie Azure AD, vous devez effectuer les opérations suivantes :

-   Ajouter l’application depuis la galerie Azure AD
-   [Configurer l’application pour l’authentification unique basée sur un mot de passe](#configure-the-app-for-password-sso)
-   [Assigner des utilisateurs à l’application](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Ajouter l’application depuis la galerie Azure AD

Procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous en tant qu’Administrateur général ou que Coadministrateur.

2. En haut du volet de navigation situé à gauche, sélectionnez **Tous les services** pour ouvrir l’extension Azure AD.

3. Saisissez **Azure Active Directory** dans la zone de recherche de filtre, puis sélectionnez **Azure Active Directory**.

4. Sélectionnez **Applications d’entreprise** dans le volet de navigation Azure AD.

5. Sélectionnez **Ajouter** dans le coin supérieur droit du volet **Applications d’entreprise**.

6. Dans la zone de texte **Entrer un nom** de la section **Ajouter à partir de la galerie**, tapez le nom de l’application.

7. Sélectionnez l’application que vous voulez configurer pour l’authentification unique.

8. *Facultatif :* Avant d’ajouter l’application, vous pouvez modifier son nom dans la zone de texte **Nom**.

9. Cliquez sur **Ajouter** pour ajouter l’application.

   Après un court instant, vous serez en mesure d’afficher le volet de configuration de l’application.

### <a name="configure-the-app-for-password-sso"></a>Configurer l’application pour l’authentification unique basée sur un mot de passe

Procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com/) et connectez-vous en tant qu’Administrateur général ou que Coadministrateur.

2. En haut du volet de navigation situé à gauche, sélectionnez **Tous les services** pour ouvrir l’extension Azure AD.

3. Saisissez **Azure Active Directory** dans la zone de recherche de filtre, puis sélectionnez **Azure Active Directory**.

4. Sélectionnez **Applications d’entreprise** dans le volet de navigation Azure AD.

5. Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

   > [!NOTE]
   > Si vous ne voyez pas l’application souhaitée, utilisez le contrôle **Filtrer** en haut de la liste **Toutes les applications**. Définissez l’option **Afficher** sur « Toutes les applications ».

6. Sélectionnez l’application que vous voulez configurer pour l’authentification unique.

7. Après le chargement de l’application, sélectionnez **Authentification unique** dans le volet à gauche de l’application.

8. Sélectionnez le mode **Authentification par mot de passe**.

9. Assignez des utilisateurs à l’application.

10. Vous pouvez également fournir des informations d’identification pour les utilisateurs. (Sinon, les utilisateurs seront invités à entrer les informations d’identification au démarrage de l’application.) Pour ce faire, sélectionnez les lignes des utilisateurs. Sélectionnez ensuite **Mettre à jour les informations d’identification** et entrez leurs noms d’utilisateur et mots de passe.

### <a name="assign-users-to-the-app"></a>Assigner des utilisateurs à l’application

Pour assigner des utilisateurs directement à une application, procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com/) et connectez-vous en tant qu’Administrateur général.

2. Dans le volet de navigation situé à gauche, sélectionnez **Tous les services** pour ouvrir l’extension Azure AD.

3. Saisissez **Azure Active Directory** dans la zone de recherche de filtre, puis sélectionnez **Azure Active Directory**.

4. Sélectionnez **Applications d’entreprise** dans le volet de navigation Azure AD.

5. Sélectionnez **Toutes les applications** pour afficher la liste de vos applications.

   > [!NOTE]
   > Si vous ne voyez pas l’application souhaitée, utilisez le contrôle **Filtrer** en haut de la liste **Toutes les applications**. Définissez l’option **Afficher** sur « Toutes les applications ».

6. Dans la liste, sélectionnez l’application à laquelle vous voulez assigner un utilisateur.

7. Une fois l’application chargée, sélectionnez **Utilisateurs et groupes** dans le menu de navigation gauche de l’application.

8. Sélectionnez **Ajouter** en haut de la liste **Utilisateurs et groupes** pour ouvrir le volet **Ajouter une attribution**.

9. Sélectionnez **Utilisateurs et groupes** dans le volet **Ajouter une attribution**.

10. Dans la zone de texte **Rechercher par nom ou adresse de messagerie**, tapez le nom complet ou l’adresse e-mail de l’utilisateur que vous voulez assigner.

11. Survolez l’utilisateur dans la liste. Cochez la case en regard de la photo de profil ou du logo de l’utilisateur pour ajouter ce dernier à la liste **Sélectionné**.

12. *Facultatif :* Pour ajouter un autre utilisateur, tapez un autre nom ou une autre adresse e-mail dans la zone de texte **Rechercher par nom ou adresse de messagerie**, puis cochez la case pour ajouter cet utilisateur à la liste **Sélectionné**.

13. Après avoir sélectionné les utilisateurs, cliquez sur **Sélectionner** pour les ajouter à la liste des utilisateurs et des groupes assignés à l’application.

14. *Facultatif :* Cliquez sur **Sélectionner un rôle** dans le volet **Ajouter une attribution** pour sélectionner un rôle à affecter aux utilisateurs que vous avez sélectionnés.

15. Sélectionnez **Attribuer** pour affecter l’application aux utilisateurs sélectionnés.

    Après un court instant, les utilisateurs seront en mesure d’accéder à ces applications depuis le volet d’accès.

## <a name="request-support"></a>Demande d'assistance 
Si vous obtenez un message d’erreur lorsque vous configurez l’authentification unique et que vous assignez des utilisateurs, ouvrez un ticket de support. Veillez à fournir autant d’informations que possible :

-   ID d’erreur de corrélation
-   Nom d’utilisateur principal (adresse de messagerie de l’utilisateur)
-   ID de locataire
-   Type de navigateur
-   Fuseau horaire et heure ou période à laquelle l’erreur s’est produite
-   Traces Fiddler

## <a name="next-steps"></a>Étapes suivantes
[Fournir une authentification unique à vos applications avec le proxy d’application](application-proxy-configure-single-sign-on-with-kcd.md)
