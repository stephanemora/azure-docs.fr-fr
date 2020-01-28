---
title: Activation du pilote Azure Multi-Factor Authentication
description: Dans ce didacticiel, vous allez activer Azure Multi-Factor Authentication pour un groupe pilote d’utilisateurs.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68474738aabde1b14752aa33789d7e40c3831908
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154837"
---
# <a name="tutorial-complete-an-azure-multi-factor-authentication-pilot-roll-out"></a>Tutoriel : Déploiement du pilote Azure Multi-Factor Authentication

Dans ce didacticiel, vous configurez une stratégie d’accès conditionnel activant Azure Multi-Factor Authentication (Azure MFA) lors de la connexion au portail Azure. La stratégie est déployée et testée sur un groupe spécifique d’utilisateurs pilotes. Le déploiement d’Azure MFA à l’aide de l’accès conditionnel offre une grande flexibilité pour les organisations et les administrateurs par rapport à la méthode traditionnellement appliquée.

> [!div class="checklist"]
> * Activation d’Azure Multi-Factor Authentication
> * Tester Azure Multi-Factor Authentication

## <a name="prerequisites"></a>Conditions préalables requises

* Un locataire Azure AD fonctionnel avec au moins une licence d’essai active.
* Un compte avec des privilèges d’administrateur général.
* Un utilisateur test non-administrateur avec un mot de passe que vous connaissez à des fins de test ; si vous avez besoin de créer un utilisateur, consultez l’article [Démarrage rapide : Ajouter de nouveaux utilisateurs à Azure Active Directory](../add-users-azure-active-directory.md).
* Un groupe pilote avec lequel tester si l’utilisateur non-administrateur est membre (si vous avez besoin de créer un groupe, consultez l’article [Créer un groupe et ajouter des membres dans Azure Active Directory](../active-directory-groups-create-azure-portal.md)).

## <a name="enable-azure-multi-factor-authentication"></a>Activation d’Azure Multi-Factor Authentication

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte d’administrateur général.
1. Accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel**.
1. Sélectionnez **Nouvelle stratégie**.
1. Nommez votre stratégie **Pilote MFA**.
1. Sous **Utilisateurs et groupes**, choisissez la case d’option **Sélectionner des utilisateurs et des groupes**.
    * Sélectionnez le groupe pilote créé selon la section de conditions préalables de cet article.
    * Cliquez sur **Terminé**.
1. Sous **Applications Cloud**, activez la case d'option**Sélectionner les applications**.
    * L’application cloud pour le portail Azure est **Microsoft Azure Management**.
    * Cliquez sur **Sélectionner**
    * Cliquez sur **Terminé**.
1. Ignorez la section **Conditions**.
1. Sous **Accord**, assurez-vous que la case d’option **Accorder l’accès** est sélectionnée
    * Cochez la case **Exiger une authentification multifacteur**
    * Cliquez sur **Sélectionner**
1. Ignorez la section **Session**.
1. Basculez **Activer la stratégie** sur **Activé**
1. Cliquez sur **Créer**

## <a name="test-azure-multi-factor-authentication"></a>Tester Azure Multi-Factor Authentication

Pour confirmer que votre stratégie d’accès conditionnel fonctionne, testez la connexion à une ressource qui ne nécessite pas l’authentification multifacteur, puis la connexion au portail Azure qui exige l’authentification multifacteur.

1. Ouvrez une nouvelle fenêtre de navigateur dans InPrivate ou en mode de navigation privée, et accédez à [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Connectez-vous à l’utilisateur test créé dans le cadre de la section conditions préalables de cet article et notez qu’il ne devrait pas vous demander d’effectuer l’authentification multifacteur.
   * Fermez la fenêtre du navigateur.
2. Ouvrez une nouvelle fenêtre de navigateur dans InPrivate ou en mode de navigation privée, et accédez à [https://portal.azure.com](https://portal.azure.com).
   * Connectez-vous avec l’utilisateur test créé dans le cadre de la section conditions préalables de cet article et notez que vous devez à présent vous inscrire pour utiliser Azure Multi-Factor Authentication.
   * Fermez la fenêtre du navigateur.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous décidez de ne plus utiliser la fonctionnalité que vous avez configurée dans le cadre de ce didacticiel, apportez la modification suivante.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel.**
1. Sélectionnez la stratégie d’accès conditionnel que vous avez créée.
1. Cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez activé Azure Multi-Factor Authentication. Continuez avec le tutoriel suivant pour savoir comment Azure Identity Protection peut être intégré à la réinitialisation de mot de passe en libre-service et aux expériences Multi-Factor Authentication.

> [!div class="nextstepaction"]
> [Évaluer les risques lors de la connexion](tutorial-risk-based-sspr-mfa.md)
