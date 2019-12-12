---
title: Démarrage rapide de la réinitialisation de mot de passe libre-service Azure AD
description: Dans ce guide de démarrage rapide, vous allez rapidement configurer la réinitialisation de mot de passe en libre-service Azure AD pour permettre aux utilisateurs de réinitialiser leurs mots de passe
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 582a6a32aa4c34b2e6fef37f3de5b5414de16cf3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846655"
---
# <a name="quickstart-self-service-password-reset"></a>Démarrage rapide : Réinitialisation de mot de passe libre-service

Avec ce guide de démarrage rapide, vous serez guidé pas à pas pour configurer la réinitialisation du mot de passe en libre-service (SSPR) pour que les administrateurs informatiques disposent de moyens simples pour permettre aux utilisateurs de réinitialiser leurs mots de passe ou de déverrouiller leurs comptes.

## <a name="prerequisites"></a>Prérequis

* Un locataire Azure AD fonctionnel avec au moins une licence d’essai active.
* Un compte avec des privilèges d’administrateur général.
* Un utilisateur test non-administrateur avec un mot de passe que vous connaissez, si vous avez besoin de créer un utilisateur, consultez l’article [Démarrage rapide : Ajouter de nouveaux utilisateurs à Azure Active Directory](../add-users-azure-active-directory.md).
* Un groupe pilote avec lequel tester si l’utilisateur test non-administrateur est membre, si vous avez besoin de créer un groupe, consultez l’article [Créer un groupe et ajouter des membres dans Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Activer la réinitialisation du mot de passe libre-service

[Regardez ce processus sous forme de vidéo dans YouTube](https://youtu.be/Pa0eyqjEjvQ)

1. À partir de votre locataire Azure AD existant, dans le menu du Portail Azure ou depuis la page **d’accueil**, sélectionnez **Azure Active Directory**. Cliquez ensuite sur **Réinitialisation du mot de passe**.

2. Sur la page **Propriétés**, sous l’option **Réinitialisation de mot de passe en libre-service activée**, choisissez **Sélectionné**.
    * À partir de **Sélectionner un groupe**, choisissez votre groupe pilote créé au cours de la section conditions préalables de cet article.
    * Cliquez sur **Enregistrer**.

3. À partir de la page **Méthodes d’authentification**, sélectionnez les choix suivants :
   * Nombre de méthodes requises pour la réinitialisation : **1**
   * Méthodes disponibles pour les utilisateurs :
      * **E-mail**
      * **Code d’application mobile (préversion)**
   * Cliquez sur **Enregistrer**.

     ![Choix des méthodes d’authentification pour SSPR][Authentication]

4. À partir de la page **Inscription**, sélectionnez un des choix suivants :
   * Obliger les utilisateurs à s’inscrire durant la connexion : **Oui**
   * Définir le nombre de jours avant que les utilisateurs ne soient invités à reconfirmer leurs informations d’authentification : **365**

## <a name="test-self-service-password-reset"></a>Tester la réinitialisation de mot de passe en libre-service

À présent, testons votre configuration SSPR avec un utilisateur de test. Puisque Microsoft applique des spécifications d’authentification forte pour les comptes d’administrateur Azure, tester à l’aide d’un compte administrateur peut changer le résultat. Pour plus d’informations sur la stratégie de mot de passe administrateur, consultez notre article [Stratégie de mot de passe](concept-sspr-policy.md).

1. Ouvrez une nouvelle fenêtre de navigateur dans InPrivate ou en navigation privée, et accédez à [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Connectez-vous avec un utilisateur de test non-administrateur et inscrivez votre téléphone d’authentification.
3. Une fois cela terminé, cliquez sur le bouton marqué **Les informations semblent correctes** et fermez la fenêtre du navigateur.
4. Ouvrez une nouvelle fenêtre de navigateur dans InPrivate ou en navigation privée, et accédez à [https://aka.ms/sspr](https://aka.ms/sspr).
5. Entrez l’ID d’utilisateur de vos utilisateurs de test non administrateurs, les caractères du test CAPTCHA, puis cliquez sur **Suivant**.
6. Suivez les étapes de vérification pour réinitialiser votre mot de passe

## <a name="clean-up-resources"></a>Supprimer des ressources

Il est facile de désactiver la réinitialisation du mot de passe libre-service. Ouvrez votre locataire Azure AD et accédez à **Propriétés** > **Réinitialisation de mot de passe**, puis sélectionnez **Aucun** sous **Réinitialisation de mot de passe en libre-service activée**.

## <a name="next-steps"></a>Étapes suivantes

Avec ce guide de démarrage rapide, vous avez appris à configurer rapidement la réinitialisation de mot de passe en libre-service pour vos utilisateurs cloud uniquement. Pour savoir comment effectuer un déploiement plus détaillé, passez à notre guide de déploiement.

> [!div class="nextstepaction"]
> [Déployer la réinitialisation du mot de passe en libre-service](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Méthodes d’authentification disponibles dans Azure Active Directory et quantité requise"
