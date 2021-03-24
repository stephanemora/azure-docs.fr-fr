---
title: Publier une application SharePoint locale avec le proxy d’application - Azure AD
description: Aborde les bases de l’intégration d’un serveur SharePoint local au proxy d’application Azure AD pour SAML.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cadf5b7d92e26e561e570f824295e69ca421e16
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644513"
---
# <a name="integrate-with-sharepoint-saml"></a>Intégrer à SharePoint (SAML)

Ce guide pas à pas explique comment sécuriser l’accès à [SharePoint (local) intégré à Azure Active Directory (SAML)](../saas-apps/sharepoint-on-premises-tutorial.md) à l’aide du proxy d’application Azure AD, avec lequel les utilisateurs de votre organisation (Azure AD, B2B) se connectent à SharePoint sur Internet.

> [!NOTE] 
> Si vous débutez avec le proxy d’application Azure AD et que vous souhaitez en savoir plus, consultez [Accéder à distance à des applications locales par le biais du proxy d’application Azure AD](./application-proxy.md).

Cette configuration présente trois principaux avantages :

- Le proxy d’application Azure AD garantit que le trafic authentifié peut atteindre votre réseau interne et le serveur SharePoint.
- Vos utilisateurs peuvent accéder aux sites SharePoint comme d’habitude sans utiliser de VPN.
- Vous pouvez contrôler l’accès par attribution aux utilisateurs au niveau du proxy d’application Azure AD et vous pouvez renforcer la sécurité avec des fonctionnalités Azure AD telles que l’accès conditionnel et l’authentification multifacteur (MFA).

Ce processus nécessite deux applications d’entreprise. L’une est une instance SharePoint locale que vous publiez à partir de la galerie sur votre liste d’applications SaaS managées. La seconde est une application locale (qui ne figure pas dans la galerie) que vous utiliserez pour publier la première application d’entreprise figurant dans la galerie.

## <a name="prerequisites"></a>Prérequis

Pour effectuer cette configuration, vous avez besoin des ressources suivantes :
 - Une batterie de serveurs SharePoint 2013 ou version ultérieure. La batterie de serveurs SharePoint doit être [intégrée à Azure AD](../saas-apps/sharepoint-on-premises-tutorial.md).
 - Un locataire Azure AD avec un plan qui inclut le proxy d’application. En savoir plus sur les [formules et la tarification Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).
 - Un [domaine vérifié personnalisé](../fundamentals/add-custom-domain.md) dans le locataire Azure AD. Le domaine vérifié doit correspondre au suffixe de l’URL SharePoint.
 - Un certificat SSL est nécessaire. Pour obtenir des informations détaillées, consultez cet [article sur la publication avec un domaine personnalisé](./application-proxy-configure-custom-domain.md).
 - Les utilisateurs Active Directory locaux doivent être synchronisés avec Azure AD Connect et doivent être configurés pour [se connecter à Azure](../hybrid/plan-connect-user-signin.md). 
 - Pour les utilisateurs invités cloud seul et B2B, vous devez [accorder l’accès à un compte invité à SharePoint (local) dans le portail Azure](../saas-apps/sharepoint-on-premises-tutorial.md#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal).
 - Un connecteur de proxy d’application installé et en cours d’exécution sur un ordinateur au sein du domaine d’entreprise.


## <a name="step-1-integrate-sharepoint-on-premises-with-azure-ad"></a>Étape 1 : Intégrer SharePoint (local) à Azure AD 

1. Configurez l’application SharePoint locale. Pour plus d’informations, consultez [Tutoriel : Intégration de l’authentification unique Azure Active Directory à SharePoint (local)](../saas-apps/sharepoint-on-premises-tutorial.md).
2. Vérifiez la configuration avant de passer à l’étape suivante. Pour cela, essayez d’accéder au site SharePoint local à partir du réseau interne et vérifiez qu’il est accessible en interne. 


## <a name="step-2-publish-the-sharepoint-on-premises-application-with-application-proxy"></a>Étape 2 : Publier l’application SharePoint locale avec le proxy d’application

Dans cette étape, vous allez créer une application dans votre locataire Azure AD, qui utilise le proxy d’application. Vous définissez l’URL externe et spécifiez l’URL interne, les deux étant utilisées ultérieurement dans SharePoint.

> [!NOTE] 
> Les URL internes et externes doivent correspondre à l’**URL de connexion** de la configuration de l’application basée sur SAML de l’étape 1.

   ![Capture d’écran montrant la valeur de l’URL de connexion.](./media/application-proxy-integrate-with-sharepoint-server/sso-url-saml.png)


 1. Créez une application de proxy d’application Azure AD avec un domaine personnalisé. Pour obtenir des instructions pas à pas, consultez [Domaines personnalisés dans le proxy d’application Azure AD](./application-proxy-configure-custom-domain.md).

    - URL interne : https://portal.contoso.com/
    - URL externe : https://portal.contoso.com/
    - Préauthentification : Azure Active Directory
    - Traduire les URL dans les en-têtes : Non
    - Traduisez les URL dans le corps de l’application : Non

        ![Capture d’écran montrant les options utilisées pour créer l’application.](./media/application-proxy-integrate-with-sharepoint-server/create-application-azure-active-directory.png)

2. Attribuez les [mêmes groupes](../saas-apps/sharepoint-on-premises-tutorial.md#create-an-azure-ad-security-group-in-the-azure-portal) que ceux que vous avez attribués à l’application de galerie SharePoint locale.

3. Enfin, accédez à la section **Propriétés** et définissez l’option **Visible par les utilisateurs ?** sur **Non**. Cette option garantit que seule l’icône de la première application s’affiche sur le portail Mes applications (https://myapplications.microsoft.com).

   ![Capture d’écran montrant où définir l’option Visible par les utilisateurs ?](./media/application-proxy-integrate-with-sharepoint-server/configure-properties.png)
 
## <a name="step-3-test-your-application"></a>Étape 3 : Tester votre application

À l’aide d’un navigateur, à partir d’un ordinateur situé sur un réseau externe, accédez à l’URL (https://portal.contoso.com/) que vous avez configurée à l’étape de publication. Veillez à pouvoir vous connecter avec le compte de test que vous avez configuré.