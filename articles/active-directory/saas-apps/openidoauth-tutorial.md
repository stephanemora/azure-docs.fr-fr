---
title: Étapes pour configurer une application OpenID/OAuth à partir de la galerie Azure AD App | Microsoft Docs
description: Étapes pour configurer une application OpenID/OAuth à partir de la galerie Azure AD App.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 69e9d66458409bbc744416a58ceb508349418a76
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019551"
---
# <a name="steps-to-configure-an-openidoauth-application-from-azure-ad-app-gallery"></a>Étapes pour configurer une application OpenID/OAuth à partir de la galerie Azure AD App

## <a name="process-of-open-id-application-addition-from-gallery"></a>Processus d’ajout d’une application Open ID à partir de la galerie

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory](./media/openidoauth-tutorial/tutorial_general_01.png)

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise](./media/openidoauth-tutorial/tutorial_general_02.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](./media/openidoauth-tutorial/tutorial_general_03.png)

4. Dans la zone de recherche, tapez le **nom de l’application**, sélectionnez **l’application requise** dans le panneau de résultats et connectez-vous à l’application.

    ![Ajout de l’application](./media/openidoauth-tutorial/addfromgallery.png)

    > [!NOTE]
    > Dans le cas des applications Open ID Connect et OAuth, le bouton Ajouter est désactivé par défaut. L’administrateur du locataire (tenant) doit cliquer sur le bouton **S’inscrire** et donner son consentement à l’application. Cette application est ainsi ajoutée au locataire du client, sans qu’il soit nécessaire de l’ajouter explicitement ou de la configurer.

    ![Bouton Ajouter](./media/openidoauth-tutorial/addbutton.png)

5. Lorsque vous cliquez sur le lien d’inscription, vous accédez automatiquement à la page des informations d’identification de connexion d’Azure AD.

6. Une fois authentifié, l’utilisateur doit accepter le consentement sur la page correspondante ; la page d’accueil de l’application s’affiche alors.

    > [!NOTE]
    > Les clients ne peuvent ajouter qu’une seule instance de l’application. Si vous en avez déjà ajouté une et que vous avez tenté de fournir une nouvelle fois le consentement, l’application ne sera pas ajoutée à nouveau dans le locataire. Par conséquent, les clients ne peuvent en toute logique utiliser qu’une seule instance de l’application dans le locataire.

## <a name="authentication-flow-using-openid-connect"></a>Flux d’authentification à l’aide d’OpenID Connect

Le flux de connexion le plus simple comprend les étapes suivantes (chacune d’elles est décrite en détail ci-dessous).

![Flux d’authentification à l’aide d’OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

* **Application mutualisée** : une application mutualisée est destinée à être utilisée dans plusieurs organisations, non dans une seule. Il s’agit généralement d’applications SaaS (software-as-a-service) écrites par un éditeur de logiciels indépendant. Les applications mutualisées doivent être approvisionnées dans chaque annuaire dans lequel elles sont utilisées, ce qui suppose le consentement d’un utilisateur ou d’un administrateur pour les inscrire. Ce processus de consentement démarre quand une application a été enregistrée dans l’annuaire et accède à l’API Graph ou à une autre API web. Lorsqu’un utilisateur ou un administrateur d’une autre organisation s’inscrit pour utiliser l’application, une boîte de dialogue contenant les autorisations requises par l’application s’affiche. L’utilisateur ou l’administrateur peut alors donner son consentement à l’application, ce qui permet à cette dernière d’accéder aux données indiquées et inscrit l’application dans l’annuaire de l’utilisateur ou de l’administrateur.

    > [!NOTE]
    > Si vous mettez votre application à la disposition des utilisateurs dans plusieurs annuaires, vous devez disposer d’un mécanisme permettant de déterminer dans quel locataire ils se trouvent. Il suffit à une application à client unique de rechercher l’utilisateur dans son propre annuaire, alors qu’une application mutualisée doit tenir compte de tous les annuaires d’Azure AD pour identifier un utilisateur particulier. À cet effet, Azure AD fournit un point de terminaison d’authentification commun vers lequel une application mutualisée peut diriger les demandes de connexion, plutôt que vers un point de terminaison spécifique au client. Ce point de terminaison est [https://login.microsoftonline.com/common](https://login.microsoftonline.com/common) pour tous les annuaires d’Azure AD, tandis qu’un point de terminaison propre à un locataire pourrait être [https://login.microsoftonline.com/contoso.onmicrosoft.com](https://login.microsoftonline.com/contoso.onmicrosoft.com). Lorsque vous développez votre application, il est particulièrement important de tenir compte du point de terminaison commun, car vous aurez besoin de la logique nécessaire à la gestion de plusieurs clients lors de la connexion, de la déconnexion et de la validation des jetons.

Par défaut, l’équipe Azure AD promeut une application multi-locataire facile d’accès pour différentes organisations et simple d’utilisation une fois le consentement accepté.

## <a name="what-is-consent-framework"></a>Qu’est-ce que l’infrastructure de consentement ?

L’infrastructure de consentement de Azure AD facilite le développement d’applications clientes natives et d’applications web multi-locataires. Ces applications autorisent la connexion au moyen de comptes d’utilisateurs d’un locataire Azure AD différent de celui où l’application a été inscrite. Elles peuvent également avoir à accéder aux API web telles que l’API Microsoft Graph (pour l’accès à Azure Active Directory, à Intune et aux services d’Office 365) et d’autres API de services Microsoft, en plus de vos propres API web. L’infrastructure est basée sur le consentement d’un utilisateur ou d’un administrateur à l’inscription d’une application dans son répertoire, ce qui peut impliquer l’accès aux données du répertoire. Une fois le consentement donné, l’application cliente pourra appeler l’API Microsoft Graph au nom de l’utilisateur et utiliser les informations en fonction des besoins.

[L’API Microsoft Graph](https://graph.microsoft.io/) permet d’accéder aux données d’Office 365 (comme les calendriers et les messages Exchange, les sites et les listes SharePoint, les documents OneDrive, les blocs-notes OneNote, les tâches Organiseur, les classeurs Excel, etc.), ainsi qu’aux utilisateurs et groupes d’Azure AD, et à d’autres objets de données provenant d’autres services cloud Microsoft.

Les étapes suivantes vous montrent comment l’expérience de consentement fonctionne à la fois pour le développeur d'applications et pour l'utilisateur.

1. Supposons que vous ayez une application cliente web devant demander des autorisations spécifiques pour accéder à une ressource/API. Le Portail Azure est utilisé pour déclarer les demandes d’autorisation au moment de la configuration. Comme d’autres paramètres de configuration, elles sont incluses dans l’inscription Azure AD de l’application :

    ![API Graph](./media/openidoauth-tutorial/graphapi.png)

2. Considérons que les autorisations de votre application ont été mises à jour, que l'application s'exécute et qu’un utilisateur s'apprête à l’utiliser pour la première fois. L’application doit tout d’abord obtenir un code d’autorisation du point de terminaison /authorize d’Azure AD. Le code d’autorisation peut ensuite être utilisé pour obtenir un nouvel accès et un jeton d’actualisation.

3. Si l’utilisateur n’est pas déjà authentifié, le point de terminaison /authorize d’Azure AD l’invite à se connecter.

    ![Authentification](./media/openidoauth-tutorial/authentication.png)

4. Une fois l'utilisateur connecté, Azure AD déterminera s’il est nécessaire d'afficher une page de consentement pour l’utilisateur. La détermination est différente selon que l’utilisateur (ou l’administrateur de son organisation), a déjà accordé ou non son consentement à l'application. Si l'autorisation n'a pas encore été accordée, Azure AD invite l'utilisateur à fournir son consentement et affiche les autorisations dont il a besoin pour fonctionner. L’ensemble des autorisations qui s’affichent dans la boîte de dialogue de consentement correspond à celles sélectionnées dans la section Autorisations déléguées du Portail Azure.

    ![Page de consentement](./media/openidoauth-tutorial/consentpage.png)

Certaines autorisations peuvent être accordées par un utilisateur standard, tandis que d’autres nécessitent le consentement de l’administrateur d’un client.

## <a name="whats-the-difference-between-admin-consent-and-user-consent"></a>Quelle est la différence entre le consentement de l’administrateur et le consentement de l’utilisateur ?

En tant qu’administrateur, vous pouvez également donner votre consentement pour les autorisations déléguées d’une application pour le compte de tous les utilisateurs de votre client. Le consentement de l’administrateur empêche l’affichage d’une boîte de dialogue de consentement pour chaque utilisateur du locataire. Les utilisateurs disposant du rôle Administrateur peuvent le donner sur le Portail Azure. Sur la page Paramètres de votre application, cliquez sur Autorisations requises, puis sur le bouton Accorder des autorisations.

![Accorder l’autorisation](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Il est actuellement nécessaire d’accorder un consentement explicite à l’aide du bouton Accorder des autorisations pour les applications monopages (SPA) qui utilisent ADAL.js. Sinon, l’application échoue lorsque le jeton d’accès est demandé.

Les autorisations application seule nécessitent toujours le consentement de l’administrateur d’un client. Si votre application demande une autorisation application seule et qu’un utilisateur tente de se connecter à l’application, un message d’erreur indiquant que l’utilisateur n’est pas en mesure de donner son consentement s’affiche.

Si votre application utilise des autorisations qui nécessitent le consentement de l’administrateur, vous devez y intégrer une option comme un bouton ou un lien afin que l’administrateur puisse initier l’action. La demande envoyée par votre application pour cette action est une demande d’autorisation OAuth2/OpenID Connect ordinaire, qui comporte également le paramètre de chaîne de requête prompt=admin_consent. Une fois que l’administrateur a donné son consentement et que le principal de service est créé dans le locataire du client, les demandes de connexion ultérieures n’ont plus besoin du paramètre prompt=admin_consent. Comme l’administrateur a décidé que les autorisations demandées sont acceptables, les autres utilisateurs n’ont plus à donner leur consentement par la suite. L’administrateur d’un client peut empêcher les utilisateurs standard de donner son consentement aux applications. Si cette fonctionnalité est désactivée, le consentement de l’administrateur est toujours requis pour que l’application soit utilisée dans le client. Si vous souhaitez tester votre application avec le consentement de l’utilisateur final désactivé, vous trouverez le commutateur de configuration sur le [Portail Azure](https://portal.azure.com/), dans la section [Paramètres utilisateur](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) sous **Applications d’entreprise**.

Le paramètre prompt=admin_consent peut également être utilisé par les applications qui demandent des autorisations ne réclamant pas le consentement de l’administrateur. Un exemple d’utilisation de cela est quand l’application requiert une expérience où l’administrateur du client « s’inscrit » une fois, et qu’aucun autre utilisateur n’est invité à donner son consentement à partir de ce moment.

Si une application requiert le consentement de l’administrateur, et qu’un administrateur se connecte sans que le paramètre prompt=admin_consent soit envoyé, le consentement de l’administrateur à l’application s’applique uniquement pour son compte d’utilisateur. Les utilisateurs standard ne pourront toujours pas se connecter ou donner leur consentement à l’application. Cette fonctionnalité s’avère utile si vous souhaitez donner à l’administrateur du locataire la possibilité d’explorer votre application avant d’autoriser l’accès à d’autres utilisateurs.