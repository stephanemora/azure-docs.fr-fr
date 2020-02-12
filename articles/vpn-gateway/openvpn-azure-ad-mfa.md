---
title: 'Activer MFA pour les utilisateurs du VPN : Authentification Azure AD'
description: Activer l’authentification multifacteur pour les utilisateurs du VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 771dea2d9ae2979bc71880368ed3a9a538e8a803
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964704"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Activer Azure MFA pour les utilisateurs du VPN

Si vous souhaitez que les utilisateurs soient invités à fournir un deuxième facteur d’authentification avant de leur accorder l’accès, vous pouvez configurer Microsoft Azure Multi-Factor Authentication (MFA) par utilisateur ou tirer parti de Multi-Factor Authentication (MFA) via l’[accès conditionnel](../active-directory/conditional-access/overview.md) pour un contrôle plus précis. La configuration de Multi-Factor Authentication par utilisateur peut être activée sans coût supplémentaire. Toutefois, lors de l’activation de la MFA par utilisateur, l’utilisateur est invité à utiliser l’authentification du second facteur sur toutes les applications liées au locataire Azure AD. L’accès conditionnel permet un contrôle plus précis sur la façon dont un deuxième facteur doit être promu et peut permettre l’attribution de la MFA à un VPN uniquement et non à d’autres applications liées au locataire Azure AD.

## <a name="enableauth"></a>Activer l’authentification

1. Accédez à **Azure Active Directory -> Applications d’entreprise -> Toutes les applications**.
2. Dans la page **Applications d’entreprise - Toutes les applications**, sélectionnez **VPN Azure**.

   ![ID du répertoire](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a> Configurer les paramètres de connexion

Sur la page **VPN Azure - Propriétés**, configurez les paramètres de connexion.

1. Définissez **Activé pour que les utilisateurs se connectent ?** sur **Oui**. Cela permet à tous les utilisateurs du locataire AD de se connecter au VPN.
2. Définissez **Affectation utilisateur requise ?** sur **Oui** si vous voulez limiter la connexion aux seuls utilisateurs dotés d’autorisations sur le VPN Azure.
3. Enregistrez vos modifications.

   ![Autorisations](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="option-1---enable-multi-factor-authentication-mfa-via-conditional-access"></a>Option 1 : Activer l’authentification multifacteur (MFA) via un accès conditionnel

L’accès conditionnel permet un contrôle d’accès précis pour chaque application.  Notez que pour tirer parti de l’accès conditionnel, vous devez avoir une licence Azure AD Premium 1 ou une licence supérieure appliquée aux utilisateurs qui seront soumis aux règles de l’accès conditionnel.

1. Dans la page **Applications d’entreprise – Toutes les applications**, sélectionnez **VPN Azure**, puis **Accès conditionnel**, et cliquez sur **Nouvelle stratégie**.
2. Sous Utilisateurs et groupes, dans l’onglet *Inclure*, activez les cases à cocher **Sélectionner les utilisateurs et les groupes** et **Utilisateurs et groupes**, puis sélectionnez un groupe ou un ensemble d’utilisateurs à soumettre à la MFA.  Cliquez sur **Done**.
![Affectations](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. Sous **Accorder**, activez les cases à cocher **Accorder l’accès**, **Exiger l’authentification multifacteur** et **Demander tous les contrôles sélectionnés**, puis cliquez sur le bouton **Sélectionner**.
![Accorder l’accès – MFA](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. Activez la case à cocher **Activé** sous **Activer la stratégie**, puis cliquez sur le bouton **Créer**.
![Activer la stratégie](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)

## <a name="option-2---enable-multi-factor-authentication-mfa-per-user"></a>Option 2 : Activer l’authentification multifacteur (MFA) par utilisateur

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour vous connecter à votre réseau virtuel, vous devez créer et configurer un profil de client VPN. Consultez [Configurer un client VPN pour les connexions VPN P2S](openvpn-azure-ad-client.md).
