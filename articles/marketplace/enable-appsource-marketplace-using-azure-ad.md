---
title: Activer une liste Microsoft AppSource et Place de marché Microsoft Azure à l’aide d’Azure Active Directory | Azure
description: Activer un type de liste à l’aide d’Azure Active Directory dans la Place de marché Azure et AppSource pour les éditeurs d’application et de service.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/12/2018
ms.author: qianw211
ms.openlocfilehash: d7fd09928c0a687755d216e7f10f7eac23677c63
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45987340"
---
# <a name="enable-a-microsoft-appsource-and-azure-marketplace-listing-by-using-azure-active-directory"></a>Activer une liste Microsoft AppSource et Place de marché Microsoft Azure à l’aide d’Azure Active Directory

Microsoft Azure Active Directory (Azure AD) est un service d’identité cloud, qui permet de s’authentifier avec un compte Microsoft à l’aide de systèmes standards.  Pour plus d’informations sur Azure AD, consultez [Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="benefits-of-using-azure-active-directory"></a>Avantages d’Azure Active Directory

Les clients de Microsoft AppSource et de la place de marché Azure utilisent des fonctions intégrées au produit pour effectuer des recherches dans les catalogues. Pour ce faire, ils doivent se connecter au produit.  En intégrant votre application avec Azure AD, vous pouvez accélérer l’engagement et optimiser l’expérience client. Azure AD :

- Permet l’authentification unique (SSO) pour des millions d’utilisateurs en entreprise.
- Permet une expérience d’authentification utilisateur cohérente entre les applications publiées par différents partenaires.
- Permet une authentification multiplateforme évolutive pour vos applications mobiles et cloud.

Comme indiqué dans la section ci-dessous, certaines offres requièrent l’implémentation d’Azure AD pour publier sur la place de marché.

## <a name="azure-active-directory-requirements"></a>Configuration requise d’Azure Active Directory

Il existe différents [options de liste et types d’offres](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) pour Microsoft AppSource et la place de marché Azure.  La configuration requise d’Azure AD pour ces options de liste et ces types d’offres est présentée ci-dessous :

| **Type d’offre**    | **Authentification unique AAD requise ?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Me contacter | Version d’évaluation | Version d’évaluation | Transaction |
| Machine virtuelle | N/A | Non  | Non  | Non  |
| Applications Azure ( modèle de solution)  | N/A | N/A | N/A | N/A |
| Applications managées  | N/A | N/A | N/A | Non  |
| SaaS  | Non  | OUI | OUI | Oui |
| Containers  | N/A | N/A | N/A | Non  |
| Services de conseil  | Non  | N/A | N/A | N/A |

Pour plus d’informations sur les exigences techniques SaaS, consultez le [guide de publication de l’offre des applications SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="integration-with-azure-active-directory"></a>Intégration avec Azure Active Directory

Pour plus d’informations sur l’intégration avec Azure AD pour activer l’authentification unique, consultez https://aka.ms/aaddev.

Pour en savoir plus sur l’authentification unique Azure AD, consultez la rubrique [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Activer un référencement Version d’évaluation à l’aide d’Azure Active Directory

Une fois que votre client sélectionne votre référencement Version d’évaluation dans la Place de marché, il est redirigé vers votre environnement d’évaluation. Dans votre environnement d’évaluation, vous pouvez configurer votre client directement sans avoir besoin d’étapes de connexion supplémentaires. Votre application ou votre offre reçoit un jeton d’Azure AD lors de l’authentification. Le jeton inclut des informations utilisateur utiles qui sont utilisées pour créer un compte utilisateur dans votre application ou votre offre. Vous pouvez automatiser votre configuration de votre client et augmenter la probabilité de conversion.

Pour plus d’informations sur le jeton qui est envoyé à partir d’Azure AD lors de l’authentification, consultez [Exemples de jetons](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Utilisez Azure AD pour activer l’authentification en un clic dans votre application ou version d’évaluation. Azure AD vous offre les avantages suivants : 
*   Simplifiez l’expérience utilisateur, de la Place de marché jusqu’à la version d’évaluation.
*   Fournissez une expérience d’utilisation réelle du produit, même lorsque l’utilisateur est redirigé de la Place de marché vers votre domaine ou l’environnement de votre version d’évaluation.
*   Réduisez la probabilité d’un abandon après redirection, car aucune autre étape de connexion supplémentaire n’est nécessaire.
*   Réduisez les obstacles de déploiement pour les nombreux utilisateurs d’Azure AD.

### <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Vérifiez votre intégration Azure AD pour la Place de marché : Applications mutualisées
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

### <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Vérifiez votre intégration Azure AD dans la Place de marché : Applications avec un seul locataire
Utilisez Azure AD pour prendre en charge l’une des options suivantes pour votre solution avec un seul locataire : 
*   Ajoutez des utilisateurs à votre répertoire en tant qu’utilisateurs invités à l’aide d’Azure Active Directory B2B (Azure AD B2B). Pour plus d’informations sur Azure AD B2B, consultez [Qu’est-ce qu’une collaboration Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Configurez manuellement les versions d’évaluation pour les clients en utilisant l’option de publication Contact Me.
*   Développer un test drive par client.
*   Créez un exemple d’application de démonstration mutualisée utilisant le SSO.

## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas déjà fait, 
- [Inscrivez-vous](https://azuremarketplace.microsoft.com/sell) sur la place de marché.

Si vous êtes inscrit et créez une nouvelle offre ou travaillez sur une offre existante,
- [Connectez-vous au portail Cloud Partner](https://cloudpartner.azure.com/) pour créer ou terminer votre offre.

