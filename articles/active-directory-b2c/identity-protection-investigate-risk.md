---
title: Examiner le risque avec Azure Active Directory B2C Identity Protection
description: Découvrez comment examiner des utilisateurs à risque et des détections dans Azure AD B2C Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 05/13/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 072278ab70484eb427bafafc3e644e39e47eb8a8
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111960491"
---
# <a name="investigate-risk-with-identity-protection-in-azure-ad-b2c"></a>Examiner les risques avec Identity Protection dans Azure AD B2C

Identity Protection assure la détection des risques en continu pour votre locataire Azure AD B2C. Le service permet aux organisations de découvrir, d’examiner et de corriger les risques basés sur l’identité. Identity Protection est fourni avec des rapports sur les risques qui peuvent être utilisés pour examiner les risques liés à l’identité dans les locataires Azure AD B2C. Dans cet article, vous allez apprendre à examiner et à atténuer les risques.

## <a name="overview"></a>Vue d’ensemble

Azure AD B2C Identity Protection fournit deux rapports. Le rapport *Utilisateurs à risque* permet aux administrateurs de savoir quels utilisateurs sont à risque et de connaître les détails relatifs aux détections. Le rapport *Détections de risque* fournit des informations sur chaque détection de risque, notamment le type, les autres risques déclenchés au même moment, l’emplacement de la tentative de connexion et bien plus encore.

Chaque rapport démarre avec une liste de toutes les détections pour la période indiquée en haut du rapport. Les rapports peuvent être filtrés à l’aide des filtres situés dans la partie supérieure du rapport. Les administrateurs peuvent choisir de télécharger les données ou d’utiliser [l’API MS Graph et le Kit de développement logiciel (SDK) Microsoft Graph PowerShell](../active-directory/identity-protection/howto-identity-protection-graph-api.md) pour exporter les données en continu.

## <a name="service-limitations-and-considerations"></a>Limitations et considérations relatives au service

Lorsque vous utilisez Identity Protection, tenez compte des éléments suivants :

- Identity Protection est activé par défaut.
- Identity Protection est disponible pour les identités locales et sociales, telles que Google ou Facebook. Pour les identités sociales, l’accès conditionnel doit être activé. La détection est limitée, car les informations d’identification des comptes sociaux sont gérées par le fournisseur d’identité externe.
- Dans les locataires Azure AD B2C, seul un sous-ensemble des [détections de risque d’Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) est disponible. Les détections de risque suivantes sont prises en charge par Azure AD B2C :  

|Type de détection des risques  |Description  |
|---------|---------|
| Voyage inhabituel     | Connexion à partir d’un emplacement inhabituel par rapport aux dernières connexions de l’utilisateur.        |
|Adresse IP anonyme     | Connexion à partir d’une adresse IP anonyme (par exemple : navigateur Tor, VPN anonymes).        |
|Adresse IP liée à un programme malveillant     | Connexion à partir d’une adresse IP liée à un programme malveillant.         |
|Propriétés de connexion inhabituelles     | Connexion avec des propriétés inhabituelles pour l’utilisateur concerné.        |
|L’administrateur a confirmé que cet utilisateur est compromis    | Un administrateur a indiqué qu’un utilisateur a été compromis.             |
|Pulvérisation de mots de passe     | Connexion par le biais d’une attaque par pulvérisation de mots de passe.      |
|Azure AD Threat Intelligence     | Les sources de renseignements sur les menaces internes et externes de Microsoft ont identifié un modèle d’attaque connu.        |

## <a name="pricing-tier"></a>Niveau tarifaire

Azure AD B2C Premium P2 est requis pour certaines fonctionnalités d’Identity Protection. Si nécessaire, [passez au niveau tarifaire Azure AD B2C Premium P2](./billing.md). Le tableau suivant récapitule les fonctionnalités d’Identity Protection et le niveau tarifaire requis.  

|Fonctionnalité   |P1   |P2|
|----------|:-----------:|:------------:|
|Rapport sur les utilisateurs à risque     |&#x2713; |&#x2713; |
|Détails du rapport sur les utilisateurs à risque  | |&#x2713; |
|Correction du rapport sur les utilisateurs à risque    | &#x2713; |&#x2713; |
|Rapport sur les détections de risques   |&#x2713;|&#x2713;|
|Détails du rapport sur les détections de risque  ||&#x2713;|
|Téléchargement du rapport |  &#x2713;| &#x2713;|
|Accès à l’API MS Graph |  &#x2713;| &#x2713;|

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="investigate-risky-users"></a>Examiner des utilisateurs à risque

Les informations indiquées dans le rapport Utilisateurs à risque permettent aux administrateurs de trouver :

- L’**état du risque**, indiquant quels utilisateurs sont **à risque**, ont vu leur risque **corrigé** ou ont vu leur risque **ignoré**
- Détails sur les détections
- Historique de toutes les connexions à risque
- Historique des risques
 
Les administrateurs peuvent ensuite choisir d’agir sur ces événements. Les administrateurs peuvent choisir d’effectuer les opérations suivantes :

- Réinitialiser le mot de passe de l’utilisateur
- Confirmer la compromission de l’utilisateur
- Ignorer le risque lié à l’utilisateur
- Empêcher l’utilisateur de se connecter
- Effectuer d’autres examens au moyen d’Azure ATP

Un administrateur peut choisir d’ignorer le risque d’un utilisateur dans le portail Azure ou par programmation par le biais de l’API Microsoft Graph [Ignorer le risque lié à l’utilisateur](/graph/api/riskyusers-dismiss?preserve-view=true&view=graph-rest-beta). Des privilèges d’administrateur sont nécessaires pour ignorer le risque lié à un utilisateur. La correction d’un risque peut être effectuée par l’utilisateur à risque ou par un administrateur au nom de l’utilisateur, notamment par le biais d’une réinitialisation de mot de passe.

### <a name="navigating-the-risky-users-report"></a>Consultation du rapport sur les utilisateurs à risque

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez le répertoire qui contient votre locataire Azure AD B2C.

1. Sous **Services Azure**, sélectionnez **Azure AD B2C**. Vous pouvez également utiliser la zone de recherche pour rechercher et sélectionner **Azure AD B2C**.

1. Sous **Sécurité**, sélectionnez **Utilisateurs à risque**.

   ![Utilisateurs à risque](media/identity-protection-investigate-risk/risky-users.png)

    La sélection d’entrées individuelles développe une fenêtre de détails sous les détections. L’affichage des détails permet aux administrateurs d’investiguer et d’effectuer des actions lors de chaque détection.

    ![Actions des utilisateurs à risque](media/identity-protection-investigate-risk/risky-users-report-actions.png)


## <a name="risk-detections-report"></a>Rapport sur les détections de risques

Le rapport sur les détections de risque contient des données filtrables correspondant aux 90 derniers jours (3 mois).

Les informations indiquées dans le rapport des détections de risques permettent aux administrateurs de trouver :

- Des informations sur chaque détection de risques, y compris le type
- Les autres risques déclenchés en même temps.
- L’emplacement de la tentative de connexion.

Les administrateurs peuvent ensuite choisir de revenir au rapport des risques ou des connexions de l’utilisateur pour effectuer des actions en fonction des informations recueillies.

### <a name="navigating-the-risk-detections-report"></a>Consultation du rapport sur les détections de risque

1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Sécurité**, sélectionnez **Détections de risque**.

   ![Détections de risques](media/identity-protection-investigate-risk/risk-detections.png)


## <a name="next-steps"></a>Étapes suivantes

- [Ajouter l’accès conditionnel à un flux d’utilisateur](conditional-access-user-flow.md)