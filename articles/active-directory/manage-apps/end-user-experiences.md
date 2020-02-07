---
title: Expériences d’utilisateurs finaux auprès des applications - Azure Active Directory
description: Azure Active Directory (Azure AD) offre plusieurs moyens personnalisables pour déployer des applications pour les utilisateurs finaux de votre organisation.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f9ff446bae0a010b12e84172d5af60c6ca81c0e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896906"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Expériences d’utilisateurs finaux auprès des applications dans Azure Active Directory

Azure Active Directory (Azure AD) offre plusieurs moyens personnalisables pour déployer des applications pour les utilisateurs finaux de votre organisation :

* Panneau d’accès Azure AD
* Lanceur d’applications Office 365
* Authentification directe pour les applications fédérées
* Liens ciblés vers des applications fédérées, avec mot de passe ou des applications existantes

Les méthodes que vous choisissez de déployer dans votre organisation sont à votre entière discrétion.

## <a name="azure-ad-access-panel"></a>Panneau d’accès Azure AD

Le volet d’accès à l’adresse https://myapps.microsoft.com est un portail web qui permet à un utilisateur final disposant d’un compte professionnel dans Azure Active Directory de voir et de lancer les applications cloud auxquelles ils ont été autorisés à accéder par l’administrateur Azure AD. Si vous êtes un utilisateur final avec [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), vous pouvez également utiliser les fonctionnalités de gestion de groupes en libre-service via le panneau d’accès.

![Capture d’écran montrant le portail du volet Azure AD](media/what-is-single-sign-on/azure-ad-access-panel.png)

Par défaut, toutes les applications sont répertoriées sur une seule page. Toutefois, vous pouvez utiliser des collections pour regrouper des applications associées, puis les présenter sous un onglet distinct, ce qui les rend plus faciles à trouver. Par exemple, vous pouvez utiliser des collections pour créer des regroupements logiques d’applications pour des rôles de travail, des tâches, des projets, et autres ressources spécifiques. Pour plus d’informations, voir [Comment utiliser des collections Mes applications pour personnaliser des panneaux d’accès utilisateur](access-panel-collections.md). 

Le panneau d’accès est séparé du portail Azure. Pour y accéder, les utilisateurs n’ont pas besoin d’un abonnement Azure ou Office 365.

Pour plus d'informations sur le panneau d'accès Azure AD, consultez la [Présentation du panneau d'accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="office-365-application-launcher"></a>Lanceur d’applications Office 365

Pour les organisations ayant déployé Office 365, les applications affectées aux utilisateurs par le biais d’Azure AD apparaissent également sur le portail Office 365, à l’adresse [https://portal.office.com/myapps](https://portal.office.com/myapps). Pour les utilisateurs d’une organisation, il est donc simple de lancer les applications, sans avoir à utiliser un deuxième portail. Il s’agit de la solution de lancement d’application recommandée pour les organisations qui utilisent Office 365.

![Capture d’écran montrant le portail Office 365](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Pour plus d’informations sur le lanceur d’applications Office 365, consultez [Faire apparaître votre application dans le lanceur d’applications Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

## <a name="direct-sign-on-to-federated-apps"></a>Authentification directe pour les applications fédérées

La plupart des applications fédérées qui prennent en charge la connexion SAML 2.0, WS-Federation ou OpenID prennent également en charge la capacité des utilisateurs à démarrer l’application, puis à obtenir la connexion via Azure AD, soit par une redirection automatique, soit en cliquant sur un lien. Cette connexion initiée par un fournisseur de service est prise en charge par la plupart des applications fédérées de la galerie d’applications Azure AD (voir la documentation mentionnée dans l’Assistant Configuration de l’authentification unique de l’application sur le Portail Azure pour plus d’informations).

![Exemple de page de connexion d’une application mobile](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Liens d’authentification directe

Azure AD prend également en charge les liens d’authentification unique directs vers les applications qui prennent en charge l’authentification unique par mot de passe, l’authentification unique liée et l’authentification unique fédérée.

Ces liens sont des URL spécifiquement conçues qui font passer l’utilisateur par le processus d’authentification Azure AD pour une application spécifique sans que l’utilisateur n’ait à la lancer dans le panneau d’accès Azure AD ou Office 365. Ces **URL d’accès utilisateur** se trouvent sous les propriétés des applications d’entreprise disponibles. Sur le portail Azure, sélectionnez **Azure Active Directory** > **Applications d’entreprise**. Sélectionnez l’application, puis sélectionnez **Propriétés**.

![Exemple d’URL d’accès utilisateur dans les propriétés Twitter](media/end-user-experiences/direct-sign-on-link.png)

Ces liens peuvent être copiés et collés partout où vous souhaitez fournir un lien de connexion à l’application sélectionnée. Cela peut être dans un message électronique ou dans n’importe quel portail web personnalisé que vous avez configuré pour l’accès. Voici un exemple d’URL d’authentification unique Azure AD pour Twitter :

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Comme pour les URL propres à l’entreprise pour le panneau d’accès, vous pouvez personnaliser cette URL en ajoutant un des domaines actifs ou vérifiés de votre annuaire après le domaine myapps.microsoft.com. Cela garantit que le logo est immédiatement chargé sur la page de connexion sans que l’utilisateur n’ait à entrer d’abord son ID utilisateur :

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Lorsqu’un utilisateur autorisé clique sur un de ces liens spécifiques de l’application, il voit tout d’abord la page de connexion de son organisation (en supposant qu’il n’est pas déjà connecté). Après la connexion, il est redirigé vers l’application sans s’arrêter au panneau d’accès. Si l’utilisateur ne dispose pas des éléments requis pour accéder à l’application, par exemple l’extension de navigateur d’authentification unique basée sur mot de passe, le lien l’invite à installer l’extension manquante. L’URL du lien reste constante en cas de modification de la configuration de l’authentification unique de l’application.

Ces liens utilisent les mêmes mécanismes de contrôle d’accès que le panneau d’accès et Office 365 ; seuls les utilisateurs et les groupes qui ont été affectés à l’application sur le Portail Azure seront en mesure de s’authentifier. Toutefois, tout utilisateur qui n’est pas autorisé voit un message qui explique qu’il n’a pas reçu l’accès. Lui est présenté un lien permettant de charger le panneau d’accès pour afficher les applications disponibles auxquelles il a accès.

## <a name="next-steps"></a>Étapes suivantes

Pour connaître les plans de déploiement, consultez [Plans de déploiement d’Azure Active Directory](../fundamentals/active-directory-deployment-plans.md).
