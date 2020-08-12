---
title: Intégrer votre offre de la Place de marché commerciale Microsoft avec Azure Active Directory
description: Utilisez Azure Active Directory pour authentifier vos offres sur Microsoft AppSource et de la Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/24/2020
ms.openlocfilehash: 4c700a61de80968b17585faf92e268fef8d86f0e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323262"
---
# <a name="integrate-your-commercial-marketplace-listing-with-azure-active-directory"></a>Intégrer votre liste de la Place de marché commerciale avec Azure Active Directory

 Cet article explique les exigences liées à l’intégration d’une liste ou d’une offre de la Place de marché commerciale avec Azure Active Directory (Azure AD). Azure AD est un service d’identité cloud qui utilise des frameworks standard pour permettre l’authentification auprès d’un compte Microsoft. [En savoir plus sur Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Avantages d’Azure AD

Les clients de Microsoft AppSource et de la Place de marché Azure utilisent des expériences intégrées dans le produit pour rechercher dans des catalogues de référencement en vitrine. Ces actions nécessitent que les clients se connectent au produit. L’intégration Azure AD offre les avantages suivants :

- Un engagement plus rapide et une expérience client optimisée
- Une authentification unique (SSO) pour des millions d’utilisateurs en entreprise
- Une expérience d’authentification cohérente parmi les applications publiées par différents partenaires
- Une authentification multiplateforme scalable pour les applications mobiles et cloud

## <a name="offers-that-require-azure-ad"></a>Offres qui nécessitent Azure AD

Aux différents [types d’offre et options de référencement](determine-your-listing-type.md) pour AppSource et la Place de marché Azure correspondent différentes exigences pour l’implémentation d’Azure AD. Pour plus de détails, consultez le tableau suivant.

| Type d’offre    | Authentification unique Azure AD nécessaire pour Me contacter ?  | Authentification unique Azure AD nécessaire pour Essai ? | Authentification unique Azure AD nécessaire pour Version d’évaluation ?  | Authentification unique Azure AD nécessaire pour Transact |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
| Machine virtuelle | N/A | Non | Non | Non |
| Applications Azure ( modèle de solution)  | N/A | N/A | N/A | N/A |
| Applications managées  | N/A | N/A | N/A | Non |
| SaaS  | Non | Oui | Oui | Oui |
| Containers  | N/A | N/A | N/A | Non |
| Services de conseil  | Non | N/A | N/A | N/A |

Pour plus d’informations sur les exigences techniques relatives à la capacité SaaS, consultez [Azure AD et offres SaaS pouvant faire l’objet d’une transaction dans le marketplace commercial](./azure-ad-saas.md).

## <a name="azure-ad-integration"></a>Intégration Azure AD

- Pour plus d’informations sur l’intégration d’Azure AD pour vos offres SaaS (Software as a Service) pouvant faire l’objet d’une transaction, consultez [Azure AD et offres SaaS pouvant faire l’objet d’une transaction dans le marketplace commercial](./azure-ad-saas.md).
- Pour savoir comment activer l’authentification unique en intégrant Azure AD dans votre liste, consultez [Azure Active Directory pour les développeurs](../active-directory/develop/index.yml).
- Pour obtenir des détails sur l’authentification unique Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../active-directory/manage-apps/what-is-single-sign-on.md).

## <a name="enable-a-trial-listing"></a>Activer une liste d’essai

La configuration automatisée des clients peut accroître les chances de conversion. Quand un client sélectionne votre liste d’essai et qu’il est redirigé vers votre environnement d’essai, vous pouvez configurer le client directement sans avoir besoin d’étapes de connexion supplémentaires.

Pendant l’authentification, Azure AD envoie un jeton à votre application ou offre. Les informations utilisateur fournies par le jeton permettent la création d’un compte d’utilisateur dans votre application ou offre. Pour plus d’informations, consultez [Exemples de jetons](../active-directory/develop/id-tokens.md).

Quand vous utilisez Azure AD pour activer l’authentification en un clic dans votre application ou liste d’essai, vous :

- Simplifiez l’expérience du client entre la Place de marché et votre liste d’essai.
- Laissez l’impression d’une utilisation dans le produit, même quand l’utilisateur est redirigé de la Place de marché vers votre domaine ou environnement d’essai.
- Réduisez la probabilité d’un abandon quand les utilisateurs sont redirigés du fait de l’absence d’étapes de connexion supplémentaires.
- Réduisez les obstacles de déploiement pour les nombreux utilisateurs d’Azure AD.

## <a name="verify-azure-ad-integration"></a>Vérifier l’intégration Azure AD

### <a name="multitenant-solutions"></a>Solutions multi-locataires

Utilisez Azure AD pour permettre les actions suivantes :

- Inscrire votre application dans l’une des vitrines de la Place de marché. Pour plus d’informations, consultez [Inscription d’une application](../active-directory/develop/quickstart-register-app.md) ou [Certification AppSource](../active-directory/azuread-dev/howto-get-appsource-certified.md).
- Activez la fonctionnalité de prise en charge multilocataire dans Azure AD pour bénéficier de l’expérience de version d’évaluation en un clic.

Si vous n’avez jamais utilisé l’authentification unique fédérée Azure AD, effectuez les étapes suivantes :

1. Inscrivez votre application sur la Place de marché.
1. Développez l’authentification unique avec Azure AD en utilisant [OAuth 2.0](../active-directory/azuread-dev/v1-protocols-oauth-code.md) ou [OpenID Connect](../active-directory/azuread-dev/v1-protocols-openid-connect-code.md).
1. Activez la fonctionnalité de prise en charge multilocataire dans Azure AD pour fournir une expérience de version d’évaluation en un clic.

### <a name="single-tenant-solutions"></a>Solutions à un seul locataire

Utilisez Azure AD pour permettre l’une des actions suivantes :

- Ajouter des utilisateurs invités à votre annuaire en utilisant [Azure AD B2B](../active-directory/b2b/what-is-b2b.md).
- Configurer manuellement des versions d’évaluation pour les clients en utilisant l’option de publication **Me contacter**.
- Développer un test drive par client.
- Créez un exemple d’application de démonstration mutualisée utilisant le SSO.

## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas déjà fait, 

- [En savoir plus](https://azuremarketplace.microsoft.com/sell) sur la place de marché.

Pour vous inscrire dans l’Espace partenaires, commencez à créer une offre ou à en utiliser une existante :

- [Connectez-vous à l’Espace partenaires](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) pour créer ou terminer votre offre.
