---
title: Intégrer à la plateforme d’identités Microsoft | Azure
description: Découvrez les bonnes pratiques et les oublis courants lors de l’intégration à la plateforme d’identités Microsoft (v2.0).
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 33128cbece3b217778182b3831b02e2f3f654f3b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853211"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Check-list de l’intégration à la plateforme d’identités Microsoft

La check-list de l’intégration de la plateforme d’identités Microsoft est conçue pour vous guider vers une intégration sécurisée et de grande qualité. Elle met en évidence les bonnes pratiques à suivre et les oublis courants à rectifier lors de l’intégration à la plateforme d’identités Microsoft ; consultez cette liste régulièrement pour vous assurer que vous maintenez la qualité et la sécurité de l’intégration de votre application à la plateforme d’identités. La check-list n’est pas prévue pour passer en revue l’intégralité de votre application. Le contenu de la check-list est susceptible d’être modifié au fur et à mesure que nous apportons des améliorations à la plateforme.

Si vous venez tout juste de commencer, consultez la [documentation](index.yml) pour en savoir plus sur les principes fondamentaux de l’authentification, les scénarios d’application dans la plateforme d’identités Microsoft, et bien plus encore.

## <a name="testing-your-integration"></a>Tester votre intégration

Utilisez la check-list suivante pour vous assurer que votre application est intégrée convenablement à la [plateforme d’identités Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).

### <a name="basics"></a>Concepts de base

|   |   |
|---|---|
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Lisez et comprenez les [Stratégies de la plateforme Microsoft](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Assurez-vous que votre application respecte les conditions décrites, car celles-ci sont conçues pour protéger les utilisateurs et la plateforme. |

### <a name="ownership"></a>Propriété

|   |   |
|---|---|
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Assurez-vous que les informations associées au compte utilisé pour vous inscrire et gérer des applications sont à jour. |

### <a name="branding"></a>Personnalisation

|   |   |
|---|---|
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Conformez-vous aux [Directives de personnalisation des applications](howto-add-branding-in-azure-ad-apps.md). |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Fournissez un nom et un logo clairs pour votre application. Ces informations s’affichent dans l’invite de consentement de votre application. Assurez-vous que votre nom et le logo associé sont représentatifs de votre société/produit, afin que les utilisateurs puissent se décider en toute connaissance de cause. Assurez-vous que vous ne violez aucune marque commerciale. |

### <a name="privacy"></a>Confidentialité

|   |   |
|---|---|
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Fournissez les liens vers les conditions d’utilisation du service et la déclaration de confidentialité de votre application. |

### <a name="security"></a>Sécurité

|   |   |
|---|---|
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Maintenez la propriété de tous vos URI de redirection et tenez à jour les enregistrements DNS connexes. N’utilisez pas les caractères génériques (*) dans vos URI. Pour les applications web, assurez-vous que tous les URI sont sécurisés et chiffrés (au moyen, par exemple, de schémas https). Pour les clients publics, utilisez les URI de redirection spécifiques à la plateforme, si nécessaire (principalement pour iOS et Android). Sinon, utilisez les URI de redirection avec une grande quantité de caractère aléatoire pour empêcher les collisions lors du rappel à votre application. Si votre application est utilisée à partir d’un agent web isolé, vous pouvez utiliser https://login.microsoftonline.com/nativeclient. Passez en revue régulièrement tous les URI de redirection et supprimez ceux qui sont inutiles ou inutilisés. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Si votre application est inscrite dans un annuaire, réduisez et supervisez manuellement la liste des propriétaires d’inscription d’application. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | N’activez pas la prise en charge du [flux d’octroi implicite OAuth2](v2-oauth2-implicit-grant-flow.md), sauf si elle est explicitement demandée. Apprenez-en davantage sur le scénario valide [ici](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | N’utilisez pas le [flux d’informations d’identification du mot de passe du propriétaire de la ressource (ROPC)](v2-oauth-ropc.md), qui gère directement les mots de passe des utilisateurs. Ce flux nécessite un degré élevé de confiance et d’exposition des utilisateurs ; il devrait être utilisé seulement lorsqu’il est impossible d’utiliser d’autres flux plus sécurisés. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Protégez et gérez les informations d’identification de votre application. Utilisez les [informations d’identification du certificat](active-directory-certificate-credentials.md), et non pas celles du mot de passe (clés secrètes clientes). Si vous devez utiliser une information d’identification de mot de passe, ne la définissez pas manuellement. Ne stockez pas les informations d’identification dans le code ou la configuration, et n’autorisez jamais leur gestion par des personnes. Si possible, utilisez les [identités managées des ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ou [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) pour stocker et faire tourner régulièrement vos informations d’identification. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Assurez-vous que votre application demande des autorisations avec des privilèges minimum. Demandez uniquement les autorisations dont votre application a absolument besoin, et seulement lorsque vous en avez besoin. Connaissez les différents [types d’autorisations](v1-permissions-and-consent.md#types-of-permissions). Utilisez uniquement les autorisations d’application si elles sont exigées ; servez-vous des autorisations déléguées lorsque cela est possible. Pour obtenir la liste complète des autorisations Microsoft Graph, consultez [Informations de référence sur les autorisations](https://docs.microsoft.com/graph/permissions-reference). |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Si vous sécurisez une API à l’aide de la plateforme des identités Microsoft, réfléchissez longuement aux autorisations qui devront être exposées. Déterminez le bon niveau de granularité pour votre solution, et la ou les autorisations nécessitant le consentement de l’administrateur. Vérifiez les autorisations attendues dans les jetons entrants avant de prendre une décision en matière d’autorisation. |

### <a name="implementation"></a>Implémentation

|   |   |
|---|---|
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Utilisez des solutions d’authentification moderne (OAuth 2.0, [OpenID Connect](v2-protocols-oidc.md)) pour connecter les utilisateurs en toute sécurité. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | N’implémentez pas les protocoles vous-même, utilisez les [bibliothèques d’authentification prises en charge par Microsoft](reference-v2-libraries.md) (MSAL, intergiciel de serveur). Assurez-vous que vous utilisez la version la plus récente de la bibliothèque d’authentification avec laquelle vous avez procédé à l’intégration. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Si les données exigées par votre application sont disponibles par le biais de [Microsoft Graph](https://developer.microsoft.com/graph), demandez les autorisations pour ces données avec le point de terminaison Microsoft Graph, plutôt qu’avec l’API individuelle. |

### <a name="end-user-experience"></a>Expérience de l’utilisateur final

|   |   |
|---|---|
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | [Comprenez l’expérience de consentement](application-consent-experience.md) et configurez les éléments de l’invite de consentement de votre application, afin que les utilisateurs finals et les administrateurs aient suffisamment d’informations pour déterminer s’ils font confiance à votre application. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Réduisez le nombre de fois où un utilisateur doit entrer des informations d’identification de connexion lorsqu’il utilise votre application ; essayez pour cela l’authentification silencieuse (acquisition de jeton en mode silencieux) avant les flux interactifs. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | N’utilisez pas « prompt=consent » pour chaque connexion. Utilisez uniquement prompt=consent si vous avez déterminé que vous deviez demander le consentement pour des autorisations supplémentaires (par exemple, si vous avez modifié les autorisations exigées de votre application). |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Le cas échéant, enrichissez votre application avec des données utilisateur. L’utilisation de l’[API Microsoft Graph](https://developer.microsoft.com/graph) est un moyen simple de le faire. L’outil [Afficheur Graph](https://developer.microsoft.com/graph/graph-explorer) peut vous aider à démarrer. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Inscrivez le jeu complet d’autorisations dont votre application a besoin, afin que les administrateurs puissent donner leur consentement facilement à leur locataire. Utilisez le [consentement incrémentiel](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) au moment de l’exécution pour aider les utilisateurs à comprendre pourquoi votre application demande des autorisations qui, sinon, peuvent inquiéter ou désorienter les utilisateurs lorsqu’elles sont demandées au premier démarrage. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Implémentez une [expérience de déconnexion unique, normale](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Il s’agit d’une exigence de sécurité et de confidentialité, et elle contribue à une bonne expérience utilisateur. |

### <a name="testing"></a>Test

|   |   |
|---|---|
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Testez les [stratégies d’accès conditionnel](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) qui peuvent avoir une incidence sur la capacité de vos utilisateurs à utiliser votre application. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Testez votre application avec tous les comptes possibles que vous prévoyez de prendre en charge (par exemple, les comptes professionnels ou scolaires, les comptes Microsoft personnels, les comptes enfants et les comptes souverains). |

## <a name="additional-resources"></a>Ressources supplémentaires

Informations approfondies sur v2.0 :

* [Plateforme d’identités Microsoft (vue d’ensemble de v2.0)](v2-overview.md)
* [Informations de référence sur les protocoles de la plateforme d’identités Microsoft](active-directory-v2-protocols.md)
* [Référence sur les jetons d’accès](access-tokens.md)
* [Référence sur les jetons d’ID](id-tokens.md)
* [Informations de référence sur les bibliothèques d’authentification](reference-v2-libraries.md)
* [Autorisations et consentement dans la plateforme d’identités Microsoft](v2-permissions-and-consent.md)
* [API Microsoft Graph](https://developer.microsoft.com/graph)
