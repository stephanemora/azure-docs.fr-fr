---
title: Réinitialisation de mot de passe en libre-service et authentification multifacteur en fonction des risques avec Azure Identity Protection
description: Dans ce didacticiel, vous allez activer les intégrations Azure Identity Protection, pour l’authentification multifacteur et la Réinitialisation de mot de passe en libre-service, afin de réduire les comportements à risques.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 01/31/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1a6858d5eda8227b3f7c1b90dee86f44273a258
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74846349"
---
# <a name="tutorial-use-risk-detections-to-trigger-multi-factor-authentication-and-password-changes"></a>Tutoriel : Utiliser les détections de risques pour déclencher l’authentification multifacteur et le changement du mot de passe

Dans ce didacticiel, vous allez activer des fonctionnalités d’Azure Active Directory (Azure AD) Identity Protection, une fonctionnalité d’Azure AD Premium P2 qui va au-delà d’un simple outil de surveillance et de création de rapports. Pour protéger les identités de votre organisation, vous pouvez configurer des stratégies reposant sur des risques qui répondent automatiquement aux comportements à risque. Ces stratégies peuvent automatiquement déclencher un blocage ou une correction, notamment exiger des modifications de mot de passe et appliquer l’authentification multifacteur.

Les stratégies Azure AD Identity Protection peuvent être utilisées en plus des stratégies d’accès conditionnel existantes, comme une couche supplémentaire de protection. Vos utilisateurs peuvent ne jamais déclencher un comportement à risque qu’elles nécessitant une de ces stratégies, mais en tant qu’administrateur, vous savez qu’ils sont protégés.

Voici certains éléments susceptibles de déclencher une détection de risque :

* Utilisateurs avec des informations d’identification volées
* Connexions depuis des adresses IP anonymes
* Voyage impossible vers des emplacements inhabituels
* Connexions depuis des appareils infectés
* Connexions depuis des adresses IP avec des activités suspectes
* Connexions depuis des emplacements non connus

Vous trouverez plus d’informations sur Azure AD Identity Protection dans l’article [What is Azure AD Identity Protection](../active-directory-identityprotection.md) (Qu’est-ce qu’Azure AD Identity Protection ?).

> [!div class="checklist"]
> * Activer l'inscription pour l’authentification multifacteur Azure
> * Activer les modifications de mot de passe en fonction des risques
> * Activer l’authentification multifacteur en fonction des risques

## <a name="prerequisites"></a>Prérequis

* Accès à un locataire Azure AD fonctionnel avec au moins une licence d’essai Azure AD Premium P2 attribuée.
* Un compte avec des privilèges d’administrateur général sur votre locataire Azure AD.
* Avoir suivi les didacticiels de réinitialisation de mot de passe en libre-service (SSPR) et d’authentification multifacteur (MFA).

## <a name="enable-risk-based-policies-for-sspr-and-mfa"></a>Activer des stratégies en fonction des risques pour la réinitialisation de mot de passe en libre-service et l’authentification multifacteur

L’activation de stratégies en fonction des risques est un processus simple. Les étapes ci-dessous vous guideront à travers un exemple de configuration.

### <a name="enable-users-to-register-for-multi-factor-authentication"></a>Permettre aux utilisateurs de s'inscrire à l’authentification multifacteur

Azure AD Identity Protection inclut une stratégie par défaut qui peut vous aider à inscrire les utilisateurs à l’authentification multifacteur et à identifier facilement l’état actuel de l’inscription. L’activation de cette stratégie ne requiert pas des utilisateurs qu’ils effectuent une authentification multifacteur, mais ils devront se pré-inscrire.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Cliquez sur **Tous les services**, puis accédez à **Azure AD Identity Protection**.
1. Cliquez sur **Inscription à MFA**.
1. Définissez l’option Appliquer la stratégie sur **Activé**.
   1. Cette stratégie nécessite que tous vos utilisateurs inscrivent les méthodes que devra utiliser l’authentification multifacteur.
1. Cliquez sur **Enregistrer**.

   ![Demander aux utilisateurs de s’inscrire à MFA à la connexion](./media/tutorial-risk-based-sspr-mfa/risk-based-require-mfa-registration.png)

### <a name="enable-risk-based-password-changes"></a>Activer les modifications de mot de passe en fonction des risques

Microsoft travaille avec des chercheurs, les forces de l’ordre, différentes équipes de sécurité chez Microsoft et autres sources de confiance pour trouver des paires nom d’utilisateur/mot de passe. Lorsqu’une de ces paires correspond à un compte dans votre environnement, une modification de mot de passe en fonction des risques peut être déclenchée à l’aide de la stratégie suivante.

1. Cliquez sur Stratégie de risque d’utilisateur.
1. Sous **Conditions**, sélectionnez **Risque de l’utilisateur**, puis choisissez **Moyen et supérieur**.
1. Cliquez sur « Sélectionner », puis sur « Terminé ».
1. Sous **Accès**, choisissez **Autoriser l’accès**, puis sélectionnez **Nécessite une modification du mot de passe**.
1. Cliquez sur « Sélectionner ».
1. Définissez l’option Appliquer la stratégie sur **Activé**.
1. Cliquez sur **Enregistrer**.

### <a name="enable-risk-based-multi-factor-authentication"></a>Activer l’authentification multifacteur en fonction des risques

La plupart des utilisateurs ont un comportement normal qui peut être suivi. Lorsqu’ils dévient de cette norme, il peut être risqué de les autoriser à se connecter uniquement. Vous voudrez peut-être bloquer cet utilisateur ou simplement lui demander d’effectuer une authentification multifacteur pour prouver qu’il est vraiment celui qu’il prétend être. Pour activer une stratégie nécessitant une authentification multifacteur lorsqu’une connexion à risque est détectée, activez la stratégie suivante.

1. Cliquez sur Stratégie en matière de risque à la connexion.
1. Sous **Conditions**, sélectionnez **Risque de l’utilisateur**, puis choisissez **Moyen et supérieur**.
1. Cliquez sur « Sélectionner », puis sur « Terminé ».
1. Sous **Accès**, choisissez **Autoriser l’accès**, puis sélectionnez **Exiger une authentification multifacteur**.
1. Cliquez sur « Sélectionner ».
1. Définissez l’option Appliquer la stratégie sur **Activé**.
1. Cliquez sur **Enregistrer**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous avez terminé le test et souhaitez désactiver les stratégies reposant sur des risques, retournez dans chaque stratégie à désactiver et définissez l’option **Appliquer la stratégie** sur **Désactivé**.
