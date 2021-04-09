---
title: Renforcer la résilience de l’authentification des utilisateurs externes avec Azure Active Directory
description: Guide destiné aux administrateurs informatiques et aux architectes pour créer une authentification résiliente pour les utilisateurs externes
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 487efce1fe57413dda740c42a7fd3d5ea91cfa49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724957"
---
# <a name="build-resilience-in-external-user-authentication"></a>Renforcer la résilience de l’authentification des utilisateurs externes

[Azure Active Directory B2B Collaboration](../external-identities/what-is-b2b.md) (Azure AD B2B) est une caractéristique des [identités externes](../external-identities/delegate-invitations.md) qui permet la collaboration avec d’autres organisations et individus. Elle permet l’intégration sécurisée d’utilisateurs invités dans votre locataire Azure AD sans avoir à gérer leurs informations d’identification. Les utilisateurs externes apportent leur identité et leurs informations d’identification d’un fournisseur d’identité (IdP) externe, de sorte qu’ils n’ont pas à mémoriser de nouvelles informations d’identification. 

## <a name="ways-to-authenticate-external-users"></a>Méthodes d’authentification des utilisateurs externes

Vous pouvez choisir les méthodes d’authentification des utilisateurs externes pour votre répertoire. Vous pouvez utiliser des IdP Microsoft ou d’autres fournisseurs.

Avec chaque IdP externe, vous dépendez de la disponibilité de cet IdP. Certaines méthodes de connexion aux IdP vous permettent de prendre certaines mesures pour accroître votre résilience.

> [!NOTE] 
> Azure AD B2B a la capacité intégrée d’authentifier tout utilisateur à partir de n’importe quel locataire [Azure Active Directory](../index.yml) ou avec un [compte Microsoft](https://account.microsoft.com/account) personnel. Vous n’avez pas à effectuer de configuration avec ces options intégrées.

### <a name="considerations-for-resilience-with-other-idps"></a>Considérations relatives à la résilience avec d’autres IdP

Lorsque vous utilisez des IdP externes pour l’authentification des utilisateurs invités, vous devez vous assurer que vous disposez de certaines configurations pour éviter toute interruption.

| Méthode d'authentification| Considérations relatives à la résilience |
| - | - |
| Fédération avec des IdP de réseaux sociaux comme [Facebook](../external-identities/facebook-federation.md) ou [Google](../external-identities/google-federation.md).| Vous devez tenir à jour votre compte avec l’IdP et configurer votre ID client et votre clé secrète client. |
| [Fédération directe avec les fournisseurs d’identité SAML et WS-Federation](../external-identities/direct-federation.md)| Vous devez collaborer avec le propriétaire de l’IdP pour accéder à ses points de terminaison, dont vous dépendez. <br>Vous devez gérer les métadonnées que contiennent les certificats et les points de terminaison. |
| [Code secret à usage unique d’e-mail](../external-identities/one-time-passcode.md)| Avec cette méthode, vous dépendez du système de messagerie de Microsoft, du système de messagerie de l’utilisateur et du client de messagerie de l’utilisateur. |


 

## <a name="self-service-sign-up-preview"></a>Inscription en libre-service (préversion)

Au lieu d’envoyer des invitations ou des liens, vous pouvez activer [l’inscription en libre-service](../external-identities/self-service-sign-up-overview.md).  Cela permet aux utilisateurs externes de demander l’accès à une application. Vous devez créer un [connecteur d’API](../external-identities/self-service-sign-up-add-api-connector.md) et l’associer à un flux d’utilisateurs. Vous associez des flux d’utilisateur qui définissent l’expérience utilisateur avec une ou plusieurs applications. 

Il est possible d’utiliser des [connecteurs d’API](../external-identities/api-connectors-overview.md) pour intégrer votre flux d’utilisateurs d’inscription en libre-service à des API de systèmes externes. Cette intégration d’API peut être utilisée pour les [workflows d’approbation personnalisés](../external-identities/self-service-sign-up-add-approvals.md), la [vérification d’identité](../external-identities/code-samples-self-service-sign-up.md) et d’autres tâches telles que l’écrasement des attributs utilisateur. L’utilisation des API requiert la gestion des dépendances suivantes.

* **Authentification du connecteur d’API** : La configuration d’un connecteur requiert une URL de point de terminaison, un nom d’utilisateur et un mot de passe. Configurez un processus grâce auquel ces informations d’identification sont conservées et collaborez avec le propriétaire de l’API pour vous assurer de connaître le calendrier d’expiration.

* **Réponse du connecteur d’API** : Concevez des connecteurs d’API dans le flux d’inscription pour qu’ils échouent correctement si l’API n’est pas disponible. Examinez et fournissez à vos développeurs d’API ces [exemples de réponses d’API](../external-identities/self-service-sign-up-add-api-connector.md) et les [meilleures pratiques pour la résolution des problèmes](../external-identities/self-service-sign-up-add-api-connector.md). Collaborez avec l’équipe de développement des API pour tester tous les scénarios de réponse possibles, y compris les réponses de continuation, d’erreur de validation et de blocage. 

## <a name="next-steps"></a>Étapes suivantes
Ressources de résilience pour les administrateurs et les architectes
 
* [Renforcer la résilience grâce à la gestion des informations d’identification](resilience-in-credentials.md)

* [Renforcer la résilience grâce aux états des appareils](resilience-with-device-states.md)

* [Renforcer la résilience en utilisant Évaluation continue de l’accès (CAE)](resilience-with-continuous-access-evaluation.md)

* [Renforcer la résilience de votre authentification hybride](resilience-in-hybrid.md)

* [Renforcer la résilience de l’accès aux applications avec Proxy d’application](resilience-on-premises-access.md)

Ressources de résilience pour les développeurs

* [Renforcer la résilience de l’IAM dans vos applications](resilience-app-development-overview.md)

* [Renforcer la résilience de vos systèmes CIAM](resilience-b2c.md)
