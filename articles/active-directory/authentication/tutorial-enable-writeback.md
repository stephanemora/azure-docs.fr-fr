---
title: Activer la réécriture du mot de passe Azure AD
description: Dans ce didacticiel, vous allez activer la réécriture du mot de passe pour obtenir les modifications de mot de passe initiées sur le cloud vers l’instance AD locale dans le cadre d’Azure AD Connect.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1ec439696c1ebd9d5929a4c8b2d9925fc1a71cc
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846469"
---
# <a name="tutorial-enabling-password-writeback"></a>Didacticiel : Activation de la réécriture du mot de passe

Dans ce didacticiel, vous allez activer la réécriture du mot de passe pour votre environnement hybride. La réécriture du mot de passe permet de synchroniser les modifications de mot de passe dans Azure Active Directory (Azure AD) vers votre environnement de Active Directory Domain Services (AD DS) local. La réécriture du mot de passe est activée dans le cadre d’Azure AD Connect pour fournir un mécanisme sécurisé qui renvoie les modifications de mot de passe à un répertoire local existant à partir d’Azure AD. Vous trouverez plus de détails sur le fonctionnement interne de la réécriture du mot de passe dans l’article [What is password writeback](concept-sspr-writeback.md) (Qu’est-ce que la réécriture du mot de passe ?).

> [!div class="checklist"]
> * Activer l’option de réécriture du mot de passe dans Azure AD Connect
> * Activer l’option de réécriture du mot de passe lors de la réinitialisation de mot de passe en libre-service (SSPR)

## <a name="prerequisites"></a>Prérequis

* Accès à un locataire Azure AD fonctionnel avec au moins une licence d’essai attribuée.
* Un compte avec des privilèges d’administrateur général sur votre locataire Azure AD.
* Un serveur existant configuré, exécutant une version actuelle d’[Azure AD Connect](../hybrid/how-to-connect-install-express.md).
* Vous devez avoir suivi les didacticiels précédents sur la réinitialisation de mot de passe en libre-service (SSPR).

## <a name="enable-password-writeback-option-in-azure-ad-connect"></a>Activer l’option de réécriture du mot de passe dans Azure AD Connect

Pour activer la réécriture du mot de passe, il faut d’abord activer la fonctionnalité à partir du serveur sur lequel vous avez installé Azure AD Connect.

1. Pour configurer et activer l’écriture différée des mots de passe, connectez-vous à votre serveur Azure AD Connect et démarrez l’Assistant de configuration **Azure AD Connect**.
2. Sur la page d’**accueil**, sélectionnez **Configurer**.
3. Sur la page **Tâches supplémentaires**, sélectionnez **Personnalisation des options de synchronisation**, puis cliquez sur **Suivant**.
4. Sur la page **Connexion à Azure AD**, entrez des informations d’identification d’administrateur général et sélectionnez **Suivant**.
5. Sur les pages **Connexion des annuaires** et **Filtrage par domaine/unité d’organisation**, sélectionnez **Suivant**.
6. Sur la page **Fonctionnalités facultatives**, cochez la case située à côté de l’option **Écriture différée de mot de passe**, puis sélectionnez **Suivant**.
7. Sur la page **Prêt à configurer**, sélectionnez **Configurer** et attendez la fin du processus.
8. Lorsque la configuration prend fin, sélectionnez **Quitter**.

## <a name="enable-password-writeback-option-in-sspr"></a>Activer l’option de réécriture du mot de passe lors de la réinitialisation de mot de passe en libre-service

L’activation de la fonctionnalité de réécriture du mot de passe dans Azure AD Connect ne représente que la moitié de l’opération. Pour boucler la boucle, il faut autoriser la réinitialisation de mot de passe en libre-service à utiliser la réécriture de mot de passe, ce qui permet aux utilisateurs de modifier ou de réinitialiser leur mot de passe pour le définir également en local.

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte d’administrateur général.
2. Accédez à **Azure Active Directory**, cliquez sur **Réinitialisation de mot de passe**, puis choisissez **Intégration locale**.
3. Définissez l’option **Réécrire des mots de passe dans votre répertoire local** sur **Oui**.
4. Définissez l’option **Autoriser les utilisateurs à déverrouiller les comptes sans réinitialiser leur mot de passe** sur **Oui**.
5. Cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez activé la réécriture du mot de passe pour la réinitialisation de mot de passe en libre-service. Laissez la fenêtre du portail Azure ouverte et passez au didacticiel suivant pour configurer les paramètres supplémentaires liés à la réinitialisation de mot de passe en libre-service avant de déployer la solution dans un projet pilote.

> [!div class="nextstepaction"]
> [Évaluer les risques lors de la connexion](tutorial-risk-based-sspr-mfa.md)
