---
title: Intégrer à la plateforme d’identité Microsoft | Azure
description: Découvrez les meilleures pratiques et les oublis courantes lors de l’intégration avec la plateforme d’identité Microsoft (v2.0).
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
ms.custom: aaddev
ms.openlocfilehash: e9070127780659142ab8f956a8016622ecfea144
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540155"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Liste de vérification de l’intégration à plateforme Microsoft identity

La liste de vérification de Microsoft identity plateforme intégration vise à vous guider pour une intégration sécurisée et de grande qualité. Il met en évidence les meilleures pratiques et oublis courantes lors de l’intégration avec la plateforme d’identité Microsoft Veuillez consulter la liste régulièrement pour vous assurer de que vous maintenir la qualité et la sécurité de l’intégration de votre application avec la plateforme d’identité. La liste de vérification n’est pas destinée à examiner toute votre application. Le contenu de la liste de vérification est susceptibles d’être modifiées que nous apportons des améliorations à la plateforme.

Si vous n’êtes pas familiarisé, consultez le [documentation](index.yml) pour en savoir plus sur les principes fondamentaux de l’authentification, les scénarios d’application dans la plateforme d’identité Microsoft et bien plus encore.

## <a name="testing-your-integration"></a>Test de votre intégration

Utilisez la liste de vérification suivante pour vous assurer que votre application est intégrée efficacement avec le [plateforme d’identité Microsoft](https://docs.microsoft.com/legal/mdsa).

### <a name="basics"></a>Concepts de base

|   |   |
|---|---|
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Lire et comprendre le [politiques de la plateforme Microsoft](https://docs.microsoft.com/legal/mdsa). Assurez-vous que votre application respecte les conditions décrites comme elles sont conçues pour protéger les utilisateurs et la plateforme. |

### <a name="ownership"></a>Propriété

|   |   |
|---|---|
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Assurez-vous que les informations associées au compte utilisé pour vous inscrire et gérer des applications sont à jour. |

### <a name="branding"></a>Personnalisation

|   |   |
|---|---|
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Respecter la [instructions pour les applications de personnalisation](howto-add-branding-in-azure-ad-apps.md). |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Fournir un nom significatif et le logo de votre application. Ces informations s’affichent sur l’invite de consentement de votre application. Assurez-vous que votre nom et le logo sont représentatives de votre société/produit afin que les utilisateurs peuvent prendre des décisions avisées. Assurez-vous que vous êtes violent pas les marques déposées. |

### <a name="privacy"></a>Confidentialité

|   |   |
|---|---|
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Fournissent des liens vers les termes du contrat de votre application de service et déclaration de confidentialité. |

### <a name="security"></a>Sécurité

|   |   |
|---|---|
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Conserver la propriété de tous les votre URI de redirection et maintenir à jour les enregistrements DNS pour eux. N’utilisez pas les caractères génériques (*) dans votre URI. Pour les applications web, assurez-vous que tous les URI sont sécurisées et chiffrées (par exemple, à l’aide de schémas de https). Pour les clients publics, utilisez URI de redirection spécifique à la plateforme le cas échéant (principalement pour iOS et Android). Sinon, utilisez URI de redirection avec une grande quantité de caractère aléatoire pour empêcher les collisions lors de l’appel précédent à votre application. Si votre application est utilisée à partir d’un agent web isolé, vous pouvez utiliser https://login.microsoftonline.com/nativeclient. Passez en revue et supprimer tous les inutiles ou inutilisés URI de redirection sur une base régulière. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Si votre application est inscrite dans un répertoire, réduire et surveiller manuellement la liste des propriétaires de l’inscription d’application. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | N’activez pas la prise en charge pour le [flux d’octroi implicite OAuth2](v2-oauth2-implicit-grant-flow.md) , sauf si explicitement requis. En savoir plus sur le scénario valid [ici](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | N’utilisez pas [(ROPC) le flux d’informations d’identification de mot de passe propriétaire ressource](v2-oauth-ropc.md), qui gère les mots de passe utilisateurs directement. Ce flux exige un degré élevé de l’exposition de confiance et d’utilisateur et doit être utilisé uniquement lorsque le flux d’autres, plus sécurisés, ne peut pas être utilisés. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Protégez et gérez vos informations d’identification de l’application. Utilisez [informations d’identification de certificat](active-directory-certificate-credentials.md), pas mot de passe (clés secrètes de client). Si vous devez utiliser un mot de passe, ne la définissez pas manuellement. Ne stocker les informations d’identification dans le code ou de configuration et jamais les autoriser à être gérées par l’homme. Si possible, utilisez [gérés d’identités pour les ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ou [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) pour stocker et faire tourner régulièrement vos informations d’identification. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Assurez-vous que votre application demande les autorisations avec des privilèges minimum. Demander uniquement les autorisations que votre application doit absolument et uniquement lorsque vous en avez besoin. Comprendre les différents [types d’autorisations](v1-permissions-and-consent.md#types-of-permissions). Utilisez uniquement des autorisations d’application si nécessaire ; Utilisez les autorisations déléguées lorsque cela est possible. Pour obtenir une liste complète des autorisations pour Microsoft Graph, consultez ce [référence des autorisations](https://docs.microsoft.com/graph/permissions-reference). |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Si vous sécurisez une API à l’aide de la plateforme Microsoft identity, étudier soigneusement les autorisations qu’il doit exposer. Déterminez le bon niveau de granularité pour votre solution et les autorisations nécessitent le consentement de l’administrateur. Vérifier les autorisations attendues dans les jetons entrants avant de prendre des décisions d’autorisation. |

### <a name="implementation"></a>Implémentation

|   |   |
|---|---|
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Utiliser des solutions d’authentification moderne (OAuth 2.0, [OpenID Connect](v2-protocols-oidc.md)) pour se connecter en toute sécurité des utilisateurs. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Ne pas implémenter les protocoles vous-même – utilisez [bibliothèques d’authentification pris en charge de Microsoft](reference-v2-libraries.md) (MSAL, intergiciel (middleware) de serveur). Assurez-vous que vous utilisez la dernière version de la bibliothèque d’authentification que vous avez intégré à. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Si les données requises par votre application seront disponibles via [Microsoft Graph](https://developer.microsoft.com/graph), demander des autorisations pour ces données en utilisant le point de terminaison Microsoft Graph, plutôt que l’API individuel. |

### <a name="end-user-experience"></a>Expérience de l’utilisateur final

|   |   |
|---|---|
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | [Comprendre l’expérience de consentement](application-consent-experience.md) et configurer les éléments de l’invite de consentement de votre application afin que les utilisateurs finaux et les administrateurs ont suffisamment d’informations pour déterminer si elles font confiance à votre application. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Réduire le nombre de fois où un utilisateur doit entrer des informations d’identification de connexion lors de l’utilisation de votre application en essayant d’en mode silencieux l’authentification (acquisition de jeton en mode silencieux) avant les flux interactifs. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | N’utilisez pas « prompt = consentement » pour chaque connexion. Utiliser uniquement invite = consentement si vous avez déterminé que vous devez demander à donner son consentement pour des autorisations supplémentaires (par exemple, si vous avez modifié les autorisations requises de votre application). |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Le cas échéant, enrichir votre application avec des données utilisateur. Utilisez le [API Microsoft Graph](https://developer.microsoft.com/graph) est un moyen simple de le faire. Le [Explorateur graphique](https://developer.microsoft.com/graph/graph-explorer) outil qui peut vous aider à démarrer. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Inscrire le jeu complet d’autorisations dont votre application a besoin pour les administrateurs peuvent donner leur consentement facilement à leur client. Utilisez [consentement incrémentiel](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) en cours d’exécution pour aider les utilisateurs à comprendre pourquoi votre application demande des autorisations qui peuvent concerner ou confusion chez les utilisateurs lorsque demandé au premier démarrage. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Implémentez un [nettoyer l’expérience de déconnexion unique](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Il est une exigence de sécurité et de la déclaration de confidentialité et permet une expérience utilisateur optimale. |

### <a name="testing"></a>Test

|   |   |
|---|---|
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Tester [stratégies d’accès conditionnel](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) qui peut affecter la capacité des utilisateurs à utiliser votre application. |
| ![case à cocher](./media/active-directory-integration-checklist/checkbox-two.svg) | Tester votre application avec tous les comptes possibles que vous prévoyez de prendre en charge (par exemple, Professionnel ou scolaire comptes, les comptes Microsoft personnels, comptes d’enfants et souverains). |

## <a name="additional-resources"></a>Ressources supplémentaires

Informations approfondies sur v2.0 :

* [Plateforme d’identité Microsoft (vue d’ensemble de la version 2.0)](v2-overview.md)
* [Référencent des protocoles de plateforme d’identité Microsoft](active-directory-v2-protocols.md)
* [Référence sur les jetons d’accès](access-tokens.md)
* [Référence sur les jetons d’ID](id-tokens.md)
* [Référencent des bibliothèques d’authentification](reference-v2-libraries.md)
* [Autorisations et consentement dans la plateforme d’identité Microsoft](v2-permissions-and-consent.md)
* [API Microsoft Graph](https://developer.microsoft.com/graph)