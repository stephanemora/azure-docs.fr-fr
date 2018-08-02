---
title: Activer une version d’évaluation dans la Place de marché Azure à l’aide d’Azure Active Directory | Azure
description: Activer une version d’évaluation à l’aide d’Azure Active Directory dans la Place de marché Azure et AppSource pour les éditeurs d’application et de service.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/04/2018
ms.author: ellacroi
ms.openlocfilehash: c5b7b4967c1acef733d366e651d50706db42aace
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160465"
---
# <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Activer un référencement Version d’évaluation à l’aide d’Azure Active Directory

Azure Active Directory (Azure AD) est un service d’identité en cloud, qui permet de s’authentifier avec un compte professionnel ou scolaire à l’aide de systèmes standards. Azure AD prend en charge les authentifications OAuth et OpenID Connect. La [Place de marché Azure](https://azuremarketplace.microsoft.com) utilise Azure AD pour vous authentifier ainsi que vos clients.

Pour plus d’informations sur Azure AD, consultez [Azure Active Directory](https://azure.microsoft.com/services/active-directory).

Une fois que votre client sélectionne votre référencement Version d’évaluation dans la Place de marché, il est redirigé vers votre environnement d’évaluation. Dans votre environnement d’évaluation, vous pouvez configurer votre client directement sans avoir besoin d’étapes de connexion supplémentaires. Votre application ou votre offre reçoit un jeton d’Azure AD lors de l’authentification. Le jeton inclut des informations utilisateur utiles qui sont utilisées pour créer un compte utilisateur dans votre application ou votre offre. Vous pouvez automatiser votre configuration de votre client et augmenter la probabilité de conversion.

Pour plus d’informations sur le jeton qui est envoyé à partir d’Azure AD lors de l’authentification, consultez [Exemples de jetons](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Utilisez Azure AD pour activer l’authentification en un clic dans votre application ou version d’évaluation. Azure AD vous offre les avantages suivants : 
*   Simplifiez l’expérience utilisateur, de la Place de marché jusqu’à la version d’évaluation.
*   Fournissez une expérience d’utilisation réelle du produit, même lorsque l’utilisateur est redirigé de la Place de marché vers votre domaine ou l’environnement de votre version d’évaluation.
*   Réduisez la probabilité d’un abandon après redirection, car aucune autre étape de connexion supplémentaire n’est nécessaire.
*   Réduisez les obstacles de déploiement pour les nombreux utilisateurs d’Azure AD.

## <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Vérifiez votre intégration Azure AD pour la Place de marché : Applications mutualisées
Utilisez Azure AD pour prendre en charge les options suivantes pour votre solution :
*   Enregistrez votre application dans les vitrines de la Place de marché.
*   Activez la fonctionnalité de prise en charge multilocataire dans Azure AD pour bénéficier de l’expérience de version d’évaluation en un clic.

Pour plus d’informations, consultez [Intégration d’applications avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

Si vous n’avez jamais utilisé l’authentification unique (SSO) fédérée Azure AD, procédez comme suit :
1.  Inscrivez votre application sur la Place de marché. 
2.  Développez l’authentification unique avec Azure AD à l’aide d’OpenID Connect ou d’OAuth 2.0.
    *   Pour plus d’informations sur OAuth 2.0, consultez [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
    *   Pour plus d’informations sur Open ID Connect, consultez [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
3.  Activez la fonctionnalité de prise en charge multilocataire dans Azure AD pour fournir une expérience de version d’évaluation en un clic.
    
    Pour plus d’informations sur la certification AppSource, consultez [Certification AppSource](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

## <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Vérifiez votre intégration Azure AD dans la Place de marché : Applications avec un seul locataire
Utilisez Azure AD pour prendre en charge l’une des options suivantes pour votre solution avec un seul locataire : 
*   Ajoutez des utilisateurs à votre répertoire en tant qu’utilisateurs invités à l’aide d’Azure Active Directory B2B (Azure AD B2B).
    
    Pour plus d’informations sur Azure AD B2B, consultez [Qu’est-ce qu’une collaboration Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Configurez manuellement les versions d’évaluation pour les clients en utilisant l’option de publication Contact Me.
*   Développer un test drive par client.
*   Créez un exemple d’application de démonstration mutualisée utilisant le SSO.

## <a name="next-steps"></a>Étapes suivantes
*   Relisez le [Guide de publication sur AppSource et la Place de marché Azure](./marketplace-publishers-guide.md).
