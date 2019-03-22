---
title: Service Web de l’application Mobile de serveur Azure MFA - Azure Active Directory
description: Configurez le serveur Azure Multi-Factor Authentication pour envoyer des notifications Push aux utilisateurs avec l’application Microsoft Authenticator.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac59b68f1ddda695fba8f1a4ceacb90bfa4102a2
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313700"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Activation de l'authentification par application mobile avec le serveur Azure Multi-Factor Authentication

L'application Microsoft Authenticator offre une option de vérification hors bande supplémentaire. Au lieu de passer un appel téléphonique automatisé ou de rédiger un SMS à l'utilisateur lors de la connexion, Azure Multi-Factor Authentication envoie une notification à l'application Microsoft Authenticator sur le smartphone ou la tablette de l'utilisateur. L'utilisateur appuie simplement sur **Vérifier** (ou saisit un code PIN et appuie sur « Authentifier ») dans l'application pour compléter leur inscription.

Il est préférable d’utiliser une application mobile pour la vérification en deux étapes si la réception par téléphone n’est pas fiable. Si vous utilisez l’application comme un générateur de jetons OATH, aucune connexion réseau ou Internet n’est nécessaire.

> [!IMPORTANT]
> Si vous avez installé le serveur Azure Multi-Factor Authentication v8.x ou une version ultérieure, la plupart des étapes ci-dessous ne sont pas nécessaires. L’authentification d’application mobile peut être configurée en suivant la procédure décrite sous [Configuration des paramètres de l’application mobile dans le serveur Azure Multi-Factor Authentication](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).

## <a name="requirements"></a>Configuration requise

Pour utiliser l’application Microsoft Authenticator, vous devez exécuter le serveur Azure Multi-Factor Authentication v8.x ou une version ultérieure.

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configuration des paramètres de l’application mobile dans le serveur Azure Multi-Factor Authentication

1. Dans la console du serveur MFA, cliquez sur l’icône Portail de l’utilisateur. Si les utilisateurs sont autorisés à contrôler leurs méthodes d'authentification, sélectionnez **Application mobile** dans l'onglet Paramètres, sous **Autoriser les utilisateurs à sélectionner la méthode**. Sans cette fonctionnalité activée, les utilisateurs finaux doivent contacter votre support technique pour effectuer l’activation pour l’application mobile.
2. Cochez la case **Autoriser les utilisateurs à activer l'application mobile**.
3. Cochez la case **Autoriser l'inscription utilisateur**.
4. Cliquez sur l’icône de l’**application mobile**.
5. Remplissez le champ **Nom de compte** avec le nom de la société ou de l’organisation à afficher dans l’application mobile de ce compte.
   ![Configuration du serveur MFA et paramètres de l’application mobile](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>Étapes suivantes

- [Scénarios avancés avec Azure Multi-Factor Authentication et des VPN tiers](howto-mfaserver-nps-vpn.md).
