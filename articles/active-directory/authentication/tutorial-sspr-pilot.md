---
title: Activation d’un pilote SSPR Azure AD
description: Dans ce didacticiel, vous allez activer la réinitialisation de mot de passe Azure AD pour un groupe pilote d’utilisateurs.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdde4ef12c6991fad53f2286ee462fec31606ae
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846281"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>Didacticiel : Effectuer un déploiement pilote de réinitialisation de mot de passe en libre-service pour Azure AD

Dans ce didacticiel, vous activez un déploiement pilote de réinitialisation de mot de passe en libre-service (SSPR) pour Azure AD dans votre organisation et le testez à l’aide d’un compte non-administrateurs.

Il est important de réaliser les tests de réinitialisation de mot de passe en libre-service avec des comptes non-administrateurs. Microsoft gère la stratégie de réinitialisation de mot de passe pour les comptes administrateurs et nécessite l’utilisation de méthodes d’authentification plus strictes. Cette stratégie n’autorise pas l’utilisation de questions/réponses de sécurité et requiert l’utilisation de deux méthodes pour la réinitialisation.

> [!div class="checklist"]
> * Activer la réinitialisation du mot de passe libre-service
> * Tester la réinitialisation de mot de passe en libre-service en tant qu’utilisateur

## <a name="prerequisites"></a>Prérequis

* Compte d’administrateur général

## <a name="enable-self-service-password-reset"></a>Activer la réinitialisation du mot de passe libre-service

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte d’administrateur général.
1. Accédez à **Azure Active Directory** et sélectionnez **Réinitialisation de mot de passe**.
1. Démarrez avec un groupe pilote en activant le mot de passe en libre-service pour un sous-ensemble d’utilisateurs dans votre organisation.
   * Dans la page **Propriétés**, sous l’option **Réinitialisation de mot de passe en libre-service activée**, choisissez **Sélectionné** et optez pour un groupe.
      * Seuls les membres du groupe Azure AD que vous choisissez peuvent utiliser la fonctionnalité de réinitialisation de mot de passe en libre-service. Nous vous recommandons de définir un groupe d’utilisateurs et d’utiliser ce paramètre lorsque vous déployez cette fonctionnalité pour une preuve de concept. L’imbrication de groupes de sécurité est prise en charge ici.
      * Vérifiez que les utilisateurs du groupe que vous avez choisis disposent d’une licence correcte.
   * Cliquez sur **Enregistrer**.
1. Sur la page **Méthodes d'authentification**
   * Définissez l’option **Nombre de méthodes requises pour réinitialiser** sur **1**.
   * Choisissez les **méthodes disponibles pour les utilisateurs** que votre entreprise souhaite autoriser. Pour ce tutoriel, cochez les cases permettant d’activer **E-mail**, **Téléphone mobile**, **Téléphone professionnel**, **Notification d’application mobile** et **Code d’application mobile**.
   * Cliquez sur **Enregistrer**.
1. Sur la page **Inscription**
   * Sélectionnez **Oui** pour l’option **Obliger les utilisateurs à s’inscrire durant la connexion**.
   * Définissez l’option **Nombre de jours avant que les utilisateurs ne soient invités à reconfirmer leurs informations d’authentification** sur **180**.
   * Cliquez sur **Enregistrer**.
1. Sur la page **Notifications**
   * Définissez l’option **Notifier les utilisateurs lors des réinitialisations de mot de passe** sur **Oui**.
   * Définissez l’option **Notifier tous les administrateurs quand d’autres administrateurs réinitialisent leur mot de passe** sur **Oui**.
1. Sur la page **Personnalisation**
   * Microsoft recommande de définir l’option **Personnaliser le lien du support technique** sur **Oui** et de fournir une adresse e-mail ou une URL de page web où vos utilisateurs peuvent obtenir une aide supplémentaire de la part de votre organisation dans le champ **E-mail ou URL du support technique personnalisé**.
   * Pour ce tutoriel, nous conservons l’option **Personnaliser le lien du support technique** définie sur **Non**.

La réinitialisation de mot de passe en libre-service est désormais configurée pour les utilisateurs cloud de votre groupe pilote.

## <a name="test-sspr-as-a-user"></a>Tester la réinitialisation de mot de passe en libre-service en tant qu’utilisateur

Tester la réinitialisation de mot de passe en libre-service à l’aide d’un utilisateur non-administrateur test qui est membre de votre groupe pilote. **Si vous utilisez un compte qui dispose de rôles d’administrateur, les méthodes d’authentification et leur nombre peuvent varier par rapport à ce que vous avez sélectionné quand Microsoft gère la stratégie administrateur.**

1. Ouvrez une nouvelle fenêtre de navigateur dans InPrivate ou en mode privé.
1. À l’aide d’un utilisateur de test, inscrivez-vous pour la réinitialisation de mot de passe en libre-service via le portail d’inscription situé à l’adresse[https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
1. À l’aide du même utilisateur de test, parcourez le portail de réinitialisation de mot de passe en libre-service [https://aka.ms/sspr](https://aka.ms/sspr) et tentez de réinitialiser votre mot de passe avec les informations que vous avez fournies à l’étape précédente.
1. Vous devriez pouvoir réinitialiser votre mot de passe.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous décidez de ne plus utiliser la fonctionnalité que vous avez configurée dans le cadre de ce didacticiel, apportez la modification suivante.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à **Azure Active Directory** et sélectionnez **Réinitialisation de mot de passe**.
1. Sur la page **Propriétés**, sous l’option **Réinitialisation de mot de passe en libre-service activée**, choisissez **Aucun**.
1. Cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez activé la réinitialisation de mot de passe en libre-service pour Azure AD. Passez au didacticiel suivant pour savoir comment une infrastructure Active Directory Domain Services locale peut être intégrée à l’expérience de réinitialisation de mot de passe en libre-service.

> [!div class="nextstepaction"]
> [Activer l’intégration de réécriture locale pour la réinitialisation de mot de passe en libre-service](tutorial-enable-writeback.md)
