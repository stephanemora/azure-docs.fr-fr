---
title: Lister votre application dans la galerie d’applications Azure AD | Microsoft Docs
description: Apprendre à lister une application qui prend en charge l’authentification unique dans la galerie d’applications Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/20/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 662bda03fc2d389ed316bd2c7141d5e6a30cef1a
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814609"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Lister votre application dans la galerie d’applications Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Cet article montre comment lister une application dans la galerie d’applications Azure Active Directory (Azure AD), implémenter l’authentification unique (SSO) et gérer la liste.

## <a name="what-is-the-azure-ad-application-gallery"></a>Qu’est-ce que la galerie d’applications Azure AD ?

- Les clients bénéficient de l’expérience d’authentification unique la meilleure possible.
- La configuration de l’application est simple et minime.
- Une recherche rapide permet de trouver votre application dans la galerie.
- Les clients Azure AD des abonnements Gratuit, De base et Premium peuvent tous utiliser cette intégration.
- Les clients mutuels obtiennent un tutoriel de configuration pas à pas.
- Les clients qui utilisent [SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010) (System for Cross-domain Identity Management) peuvent utiliser le provisionnement pour la même application.

## <a name="prerequisites"></a>Prérequis

- Pour des applications fédérées (Open ID et SAML/WS-Fed), l’application doit prendre en charge le modèle SaaS (Software as a Service, logiciel en tant que service) pour être répertoriée dans la galerie d’applications Azure AD. Les applications de galerie d’entreprise doivent prendre en charge plusieurs configurations client et non pas un client spécifique.
- Pour Open ID Connect, l’application doit être mutualisée et l’[infrastructure de consentement Azure AD](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) doit être correctement implémentée pour l’application. L’utilisateur peut envoyer la demande de connexion à un point de terminaison commun de façon à ce que tout client puisse donner son consentement à l’application. Vous pouvez contrôler l’accès utilisateur en fonction de l’ID de locataire et de l’UPN de l’utilisateur, reçus dans le jeton.
- Pour SAML 2.0/WS-Fed, votre application doit pouvoir effectuer l’intégration de l’authentification unique SAML/WS-Fed en mode SP ou IDP. Assurez-vous que cette fonctionnalité fonctionne correctement avant d’envoyer la demande.
- Pour une authentification unique par mot de passe, assurez-vous que votre application prend en charge l’authentification par formulaire de telle sorte que le mot de passe puisse être stocké dans le coffre pour que le travail d’authentification unique fonctionne comme prévu.
- Vous avez besoin d’un compte permanent pour les tests avec au moins deux utilisateurs inscrits.

**Comment obtenir Azure AD pour développeurs ?**

Vous pouvez obtenir un compte de test gratuit avec toutes les fonctionnalités Azure AD Premium : 90 jours gratuits qui peuvent être étendus tant que vous l’utilisez pour effectuer des tâches de développement : https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>Envoyer la demande dans le portail

Après avoir testé le bon fonctionnement de l’intégration de votre application avec Azure AD, soumettez votre demande d’application dans le [portail Application Network Microsoft](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

Si la page suivante s’affiche une fois que vous êtes connecté, contactez l’[équipe d’intégration Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Indiquez le compte de messagerie que vous souhaitez utiliser pour envoyer la demande. Une adresse de messagerie professionnelle telle que [name@yourbusiness.com](mailto:name@yourbusiness.com) est préférable. L’équipe Azure AD ajoutera le compte dans le portail Application Network Microsoft.

![Message de demande d’accès sur le portail SharePoint](./media/howto-app-gallery-listing/errorimage.png)

Une fois le compte ajouté, vous pouvez vous connecter au portail Application Network Microsoft.

Si la page suivante apparaît après la connexion, fournissez une justification métier de votre besoin d’accès dans la zone de texte. Ensuite, sélectionnez **Demander l’accès**.

  ![Zone de justification métier sur le portail SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

Notre équipe examine ces informations et vous octroie l’accès en conséquence. Une fois votre demande approuvée, vous pouvez vous connecter au portail et envoyer la demande en sélectionnant la vignette **Submit Request (ISV)** (Envoyer la demande (ISV)) dans la page d’accueil.

![Vignette Submit Request (ISV) (Envoyer une demande (ISV)) dans la page d’accueil](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>Problèmes de connexion au portail

Si cette erreur s’affiche lors de la connexion, voici les détails du problème et la façon de le corriger.

* Si votre connexion a été bloquée comme indiqué ci-dessous :

  ![problèmes lors de la résolution d’une application dans la galerie](./media/howto-app-gallery-listing/blocked.png)

**Ce qui se passe :**

L’utilisateur invité est fédéré en locataire de base qui est également un annuaire Azure AD. L’utilisateur invité présente un risque élevé. Microsoft n’autorise pas les utilisateurs à risque élevé à accéder à ses ressources. Tous les utilisateurs à risque élevé (employés ou invités/fournisseurs) doivent corriger/fermer leur risque pour accéder aux ressources Microsoft. Pour les utilisateurs invités, ce risque utilisateur provient du locataire de base et la stratégie provient du locataire de ressource (Microsoft, dans le cas présent).
 
**Solutions sécurisées :**

* Les utilisateurs invités inscrits à l’authentification MFA corrigent leur propre risque utilisateur. L’utilisateur invité peut effectuer cette opération en procédant à un changement ou une réinitialisation sécurisés du mot de passe (https://aka.ms/sspr) sur son locataire de base (ce qui nécessite MFA et SSPR sur le locataire de base). Le changement ou la réinitialisation sécurisés du mot de passe doivent être lancés sur Azure AD, et non pas en local.

* Le risque lié aux utilisateurs invités peut être résolu par leurs administrateurs. Dans ce cas, l’administrateur effectue une réinitialisation du mot de passe (génération d’un mot de passe temporaire). Cette opération ne nécessite pas Identity Protection. L’administrateur de l’utilisateur invité peut accéder à https://aka.ms/RiskyUsers et cliquer sur « Réinitialiser le mot de passe ».

* Le risque lié aux utilisateurs invités peut être fermé/ignoré par leurs administrateurs. Une fois encore, cette opération ne nécessite pas Identity Protection. L’administrateur peut accéder à https://aka.ms/RiskyUsers et cliquer sur « Ignorer le risque lié à l’utilisateur ». Toutefois, l’administrateur doit faire preuve de diligence pour garantir qu’il s’agissait d’un faux positif d’évaluation des risques avant de fermer le risque utilisateur. Sinon, il fait courir un risque à ses ressources et à celles de Microsoft en supprimant une évaluation des risques sans investigation.

> [!NOTE]
> En cas de problème d’accès, contactez l’[équipe d’intégration Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-federation-protocol"></a>Implémenter l’authentification unique à l’aide du protocole de fédération

Pour lister une application dans la galerie d’applications Azure AD, vous devez tout d’abord implémenter l’un des protocoles de fédération suivants pris en charge par Azure AD. Vous devez également accepter les conditions générales de la galerie d’applications Azure AD. Lisez les conditions générales de la galerie d’applications Azure AD sur [ce site web](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect** : pour intégrer votre application à Azure AD en utilisant le protocole Open ID Connect, suivez les [instructions destinées aux développeurs](v1-authentication-scenarios.md).

    ![Listing d’une application OpenID Connect dans la galerie](./media/howto-app-gallery-listing/openid.png)

    * Si vous souhaitez ajouter votre application à la liste dans la galerie à l’aide d’OpenID Connect, sélectionnez **OpenID Connect & OAuth 2.0** comme illustré ici.
    * En cas de problème d’accès, contactez l’[équipe d’intégration Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **SAML 2.0** ou **WS-Fed** : Si votre application prend en charge SAML 2.0, vous pouvez l’intégrer directement dans un locataire Azure AD en suivant les [instructions pour ajouter une application personnalisée](../manage-apps/view-applications-portal.md).

  ![Listing d’une application SAML 2.0 ou WS-Fed dans la galerie](./media/howto-app-gallery-listing/saml.png)

  * Si vous souhaitez ajouter votre application à la liste dans la galerie à l’aide de **SAML 2.0** ou **WS-Fed**, sélectionnez **SAML 2.0/WS-Fed** comme illustré ici.

  * En cas de problème d’accès, contactez l’[équipe d’intégration Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-password-sso"></a>Implémenter l’authentification unique à l’aide de l’authentification unique par mot de passe

Créez une application web qui a une page de connexion HTML pour configurer l’[authentification unique par mot de passe](../manage-apps/what-is-single-sign-on.md). L'authentification unique basée sur un mot de passe, également appelée  archivage de mot de passe, vous permet de gérer l'accès utilisateur et les mots de passe pour les applications Web qui ne prennent pas en charge la fédération d'identité. Elle est également utile pour les scénarios où plusieurs utilisateurs doivent partager un seul compte, par exemple les comptes d’applications de médias sociaux de votre organisation.

![Listing d’une application avec authentification unique par mot de passe dans la galerie](./media/howto-app-gallery-listing/passwordsso.png)

* Si vous souhaitez ajouter votre application à la liste dans la galerie à l’aide de l’authentification unique par mot de passe, sélectionnez **Authentification unique par mot de passe** comme illustré ici.
* En cas de problème d’accès, contactez l’[équipe d’intégration Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="request-for-user-provisioning"></a>Demande de provisionnement d’utilisateurs

Suivez le processus illustré dans l’image suivante pour demander le provisionnement d’utilisateurs.

   ![Demande de provisionnement d’utilisateurs](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Mettre à jour ou supprimer un listing existant

Pour mettre à jour ou supprimer une application existante dans la galerie d’applications Azure AD, vous devez tout d’abord envoyer la demande dans le [portail Application Network](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Si vous avez un compte Office 365, utilisez-le pour vous connecter à ce portail. Dans le cas contraire, utilisez votre compte Microsoft, tel qu’Outlook ou Hotmail, pour vous connecter.

- Sélectionnez l’option appropriée, comme illustré dans l’image suivante.

    ![Listing d’une application SAML dans la galerie](./media/howto-app-gallery-listing/updateorremove.png)

    * Pour mettre à jour une application existante, sélectionnez l’option appropriée en fonction de vos besoins.
    * Pour supprimer une application existante à partir de la galerie Azure AD, sélectionnez **Supprimer le listing de mes applications de la galerie**.
    * En cas de problème d’accès, contactez l’[équipe d’intégration Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="list-requests-by-customers"></a>Demandes de listing par les clients

Les clients peuvent envoyer une demande pour lister une application en sélectionnant **Demandes d’application par les clients** > **Soumettre une nouvelle demande**.

![Montre la vignette des applications demandées par les clients](./media/howto-app-gallery-listing/customer-submit-request.png)

Voici le flux des applications demandées par les clients.

![Montre le flux des applications demandées par les clients](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>Chronologies

La durée du processus de listing d’une application SAML 2.0 ou WS-Fed dans la galerie est de 7 à 10 jours ouvrables.

  ![Chronologie de listing d’une application SAML dans la galerie](./media/howto-app-gallery-listing/timeline.png)

La durée du processus de listing d’une application OpenID Connect dans la galerie est de 2 à 5 jours ouvrables.

  ![Chronologie de listing d’une application OpenID Connect dans la galerie](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escalades

Pour faire remonter un problème, envoyez un e-mail à l’[équipe d’intégration Azure AD SSO](mailto:SaaSApplicationIntegrations@service.microsoft.com) à l’adresse SaaSApplicationIntegrations@service.microsoft.com, et nous vous répondrons dès que possible.