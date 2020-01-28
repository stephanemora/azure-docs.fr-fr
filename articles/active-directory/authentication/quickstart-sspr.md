---
title: 'Démarrage rapide : Réinitialisation de mot de passe en libre-service Azure AD'
description: Ce guide de démarrage rapide vous montre comment configurer la réinitialisation de mot de passe en libre-service Azure AD pour autoriser les utilisateurs à réinitialiser leurs mots de passe eux-mêmes et réduire ainsi la charge de travail du service informatique.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 12/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd178f52665c77f03a48d87a9e73c9019390bb21
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154854"
---
# <a name="quickstart-configure-azure-active-directory-self-service-password-reset"></a>Démarrage rapide : Configurer la réinitialisation de mot de passe en libre-service Azure Active Directory

Dans ce guide de démarrage rapide, vous configurez la réinitialisation de mot de passe en libre-service Azure Active Directory (AD), ou SSPR, pour autoriser les utilisateurs à réinitialiser leurs mots de passe ou débloquer leurs comptes eux-mêmes. Avec la fonctionnalité SSPR, les utilisateurs peuvent réinitialiser leurs propres informations d’identification sans l’aide du support technique ou de l’administrateur. Cette fonctionnalité permet aux utilisateurs de récupérer l’accès à leur compte sans nécessiter une assistance supplémentaire.

> [!IMPORTANT]
> Ce guide de démarrage rapide montre comment un administrateur peut activer la réinitialisation de mot de passe en libre-service. Si vous êtes un utilisateur final déjà inscrit pour la réinitialisation de mot de passe en libre-service et que vous devez récupérer votre compte, accédez à https://aka.ms/sspr.
>
> Si votre équipe informatique n’a pas activé la réinitialisation de votre propre mot de passe, contactez votre support technique pour obtenir une assistance supplémentaire.

## <a name="prerequisites"></a>Conditions préalables requises

* Un locataire Azure AD fonctionnel avec au moins une licence d’essai active.
    * Si nécessaire, [créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un compte avec des privilèges d’administrateur général.
* Un utilisateur test non-administrateur avec un mot de passe que vous connaissez, par exemple *testuser*.
    * Si vous devez créer un utilisateur, consultez [Démarrage rapide : Ajouter de nouveaux utilisateurs à Azure Active Directory](../add-users-azure-active-directory.md).
* Un groupe pilote à tester dont cet utilisateur test non-administrateur est membre, par exemple *SSPR-Test-Group*.
    * Si vous devez créer un groupe, consultez la procédure à suivre dans [Créer un groupe et ajouter des membres dans Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Activer la réinitialisation du mot de passe libre-service

[Regardez ce processus sous forme de vidéo dans YouTube](https://youtu.be/Pa0eyqjEjvQ)

1. Dans le menu du portail Azure ou dans la page d’**accueil**, sélectionnez **Azure Active Directory**, puis choisissez **Réinitialisation du mot de passe**.

1. Dans la page **Propriétés** sous l’option **Réinitialisation de mot de passe en libre-service activée**, choisissez **Sélectionné**.
1. Choisissez **Sélectionner un groupe**, puis sélectionnez votre groupe pilote créé conformément à la section des conditions préalables requises de cet article, par exemple *SSPR-Test-Group*. Quand vous êtes prêt, sélectionnez **Enregistrer**.
1. Dans la page **Méthodes d’authentification**, effectuez les choix suivants, puis choisissez **Enregistrer** :
    * Nombre de méthodes requises pour la réinitialisation : **1**
    * Méthodes disponibles pour les utilisateurs :
        * **Code de l’application mobile**
        * **E-mail**

    > [!div class="mx-imgBorder"]
    > ![Choix des méthodes d’authentification pour SSPR][Authentication]

4. Dans la page **Inscription**, effectuez les choix suivants, puis choisissez **Enregistrer** :
   * Obliger les utilisateurs à s’inscrire durant la connexion : **Oui**
   * Définir le nombre de jours avant que les utilisateurs ne soient invités à reconfirmer leurs informations d’authentification : **365**

## <a name="test-self-service-password-reset"></a>Tester la réinitialisation de mot de passe en libre-service

Maintenant, testez votre configuration SSPR avec un utilisateur test qui fait partie du groupe que vous avez sélectionné dans la section précédente, par exemple *testuser*. Puisque Microsoft applique des spécifications d’authentification forte pour les comptes d’administrateur Azure, tester à l’aide d’un compte administrateur peut changer le résultat. Pour plus d’informations sur la stratégie de mot de passe administrateur, consultez notre article [Stratégie de mot de passe](concept-sspr-policy.md).

1. Ouvrez une nouvelle fenêtre de navigateur dans InPrivate ou en navigation privée, et accédez à [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Connectez-vous avec un utilisateur test non-administrateur (*testuser*) et inscrivez votre téléphone d’authentification.
3. Une fois cela terminé, sélectionnez le bouton marqué **Les informations semblent correctes** et fermez la fenêtre du navigateur.
4. Ouvrez une nouvelle fenêtre de navigateur dans InPrivate ou en navigation privée, et accédez à [https://aka.ms/sspr](https://aka.ms/sspr).
5. Entrez l’ID utilisateur de l’utilisateur test non-administrateur (par exemple, *testuser*), les caractères du test CAPTCHA, puis sélectionnez **Suivant**.
6. Suivez les étapes de vérification pour réinitialiser votre mot de passe.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour désactiver la réinitialisation de mot de passe en libre-service, recherchez et sélectionnez **Azure Active Directory** dans le portail Azure. Sélectionnez **Réinitialisation de mot de passe**, puis choisissez **Aucun** sous **Réinitialisation de mot de passe en libre-service activée**. Quand vous êtes prêt, sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à configurer la réinitialisation de mot de passe en libre-service pour vos utilisateurs cloud uniquement. Pour savoir comment effectuer un déploiement plus détaillé, passez à notre guide de déploiement.

> [!div class="nextstepaction"]
> [Déployer la réinitialisation du mot de passe en libre-service](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Méthodes d’authentification disponibles dans Azure Active Directory et quantité requise"

<!-- INTERNAL LINKS -->
[register-sspr]: ../user-help/active-directory-passwords-reset-register.md
[reset-password]: ../user-help/active-directory-passwords-update-your-own-password.md
