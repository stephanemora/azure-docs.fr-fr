---
title: 'Activer MFA pour les utilisateurs du VPN : Authentification Azure AD'
description: Activer l’authentification multifacteur pour les utilisateurs du VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 7f05b850a0d886ac0df5c542de647f91fe62eb05
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382217"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Activer Azure MFA pour les utilisateurs du VPN

Si vous souhaitez que les utilisateurs soient invités à fournir un deuxième facteur d’authentification pour obtenir l’accès, vous pouvez configurer Azure Multi-Factor Authentication (MFA) pour votre locataire Azure AD. La procédure décrite dans cet article permet d’activer une exigence pour la vérification en deux étapes.

## <a name="prereq"></a>Configuration requise

La configuration requise est un locataire Azure AD configuré à l’aide des étapes décrites dans [Configurer un locataire](openvpn-azure-ad-tenant.md).

## <a name="mfa"></a>Ouvrir la page MFA

1. Connectez-vous au portail Azure.
2. Accédez à **Azure Active Directory > Tous les utilisateurs**.
3. Sélectionnez **Multi-Factor Authentication** pour ouvrir la page correspondante.

   ![Se connecter](./media/openvpn-azure-ad-mfa/mfa1.jpg)

## <a name="users"></a> Sélectionner des utilisateurs

1. Sur la page **authentification multifacteur**, sélectionnez les utilisateurs pour lesquels vous souhaitez activer MFA.
2. Sélectionnez **Activer**.

   ![Sélectionnez](./media/openvpn-azure-ad-mfa/mfa2.jpg)

## <a name="enableauth"></a>Activer l’authentification

1. Accédez à **Azure Active Directory -> Applications d’entreprise -> Toutes les applications**.
2. Dans la page **Applications d’entreprise - Toutes les applications**, sélectionnez **VPN Azure**.

   ![ID du répertoire](./media/openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a> Configurer les paramètres de connexion

Sur la page **VPN Azure - Propriétés**, configurez les paramètres de connexion.

1. Définissez **Activé pour que les utilisateurs se connectent ?** sur **Oui**. Cela permet à tous les utilisateurs du locataire AD de se connecter au VPN.
2. Définissez **Affectation utilisateur requise ?** sur **Oui** si vous voulez limiter la connexion aux seuls utilisateurs dotés d’autorisations sur le VPN Azure.
3. Enregistrez vos modifications.

   ![Autorisations](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Étapes suivantes

Pour vous connecter à votre réseau virtuel, vous devez créer et configurer un profil de client VPN. Consultez [Configurer un client VPN pour les connexions VPN P2S](openvpn-azure-ad-client.md).
