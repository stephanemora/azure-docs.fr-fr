---
title: Activer la version d’évaluation à l’aide d’Azure AD | Azure
description: Activer le type de référencement Version d’évaluation à l’aide d’Azure Active Directory (Azure AD) dans la Place de marché Azure et AppSource pour les éditeurs d’application et de service
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
ms.openlocfilehash: 4140ba98c0c65c22674c61dc7266818af904e777
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825890"
---
# <a name="enable-trial-using-azure-ad"></a>Activer la version d’évaluation à l’aide d’Azure AD  
Azure Active Directory (Azure AD) est un service d’identité cloud qui permet de s’authentifier avec un compte professionnel ou scolaire Microsoft, à l’aide des frameworks standard OAuth et OpenID Connect.  
*   Pour plus d’informations sur Azure AD, consultez la page Azure Active Directory à l’adresse [azure.microsoft.com/services/active-directory](https://azure.microsoft.com/services/active-directory).  

Vous et vos clients vous authentifiez sur la Place de marché à l’aide d’Azure AD. Une fois que votre client sélectionne votre référencement Version d’évaluation sur la Place de marché, il est redirigé vers votre environnement d’évaluation.  Dans votre environnement d’évaluation, vous pouvez configurer votre client directement sans nécessiter des étapes de connexion supplémentaires. Votre application ou offre reçoit un jeton d’Azure AD lors de l’authentification qui inclut des informations utilisateur utiles pour la création d’un compte utilisateur dans votre application ou offre. Vous pouvez automatiser votre configuration et augmenter la probabilité de conversion.  
*   Pour plus d’informations sur le jeton envoyé depuis Azure AD lors de l’authentification, consultez la section Exemples de jetons à l’adresse [docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)

Utilisez Azure AD pour activer l’authentification en un clic dans votre application ou version d’évaluation.  
*   Simplifiez l’expérience utilisateur, de la Place de marché à la version d’évaluation.  
*   Fournissez une expérience d’utilisation réelle du produit, même lorsque l’utilisateur est redirigé de la Place de marché vers votre domaine ou l’environnement de votre version d’évaluation.  
*   Réduisez la probabilité d’un abandon après redirection, car aucune autre étape de connexion supplémentaire n’est nécessaire.  
*   Réduisez les obstacles de déploiement pour les nombreux utilisateurs d’Azure AD.  

## <a name="verify-your-azure-ad-integration-on-the-marketplace-multitenant-apps"></a>Vérifier votre intégration Azure AD pour la Place de marché : applications mutualisées  
Prenez en charge les options suivantes pour votre solution à l’aide d’Azure AD.  
*   Enregistrez votre application dans les vitrines sur la Place de marché.  
*   Activez la fonctionnalité de prise en charge multilocataire dans Azure AD pour bénéficier de l’expérience de version d’évaluation en un clic.  
    *   Pour plus d’informations sur l’inscription d’une application, consultez la page Intégration d’applications avec Azure Active Directory à l’adresse [docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Si vous n’avez jamais utilisé l’authentification unique (SSO) fédérée Azure AD, puis procédez comme suit.  
1.  Inscrivez votre application sur la Place de marché. 
2.  Développez l’authentification unique avec Azure AD à l’aide d’OpenID Connect ou d’OAuth 2.0.  
    *   Pour plus d’informations sur OAuth 2.0, consultez la page OAuth 2.0 à l’adresse [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).  
    *   Pour plus d’informations sur Open ID Connect, consultez la page OpenID Connect à l’adresse [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).  
3.  Activez la fonctionnalité de prise en charge multilocataire dans Azure AD pour fournir une expérience de version d’évaluation en un clic.  
    *   Pour plus d’informations sur la certification AppSource, consultez la page Certification AppSource à l’adresse [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

## <a name="verify-your-azure-ad-integration-on-the-marketplace-single-tenant-apps"></a>Vérifier votre intégration Azure AD pour la Place de marché : applications avec un seul locataire  
Prenez en charge une des options suivantes pour votre solution avec un seul locataire.  
*   Ajoutez des utilisateurs à votre répertoire en tant qu’utilisateurs invités à l’aide d’Azure AD B2B.  
    *   Pour plus d’informations sur Azure AD B2B, consultez la page Qu’est-ce qu’Azure AD B2B Collaboration ? à l’adresse [docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Configurez manuellement le versions d’évaluation pour les clients via l’option « Me contacter ».  
*   Développer un test drive par client.  
*   Créer un exemple d’application de démonstration mutualisée avec l’authentification unique.  

## <a name="next-steps"></a>Étapes suivantes
*   Consultez la page [Guide de l’éditeur Place de marché Azure et AppSource](./marketplace-publishers-guide.md).  
 
---  

