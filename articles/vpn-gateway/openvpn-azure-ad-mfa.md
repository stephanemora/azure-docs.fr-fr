---
title: 'Activer MFA pour les utilisateurs du VPN : Authentification Azure AD'
description: Activer l’authentification multifacteur pour les utilisateurs du VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: b22581d012b2c69081bc7b4eee093227c060b4c2
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169710"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Activer Azure MFA pour les utilisateurs du VPN

Si vous souhaitez que les utilisateurs soient invités à fournir un deuxième facteur d’authentification pour obtenir l’accès, vous pouvez configurer Azure Multi-Factor Authentication (MFA) pour votre locataire Azure AD. La procédure décrite dans cet article permet d’activer une exigence pour la vérification en deux étapes.

## <a name="prereq"></a>Configuration requise

La configuration requise est un locataire Azure AD configuré à l’aide des étapes décrites dans [Configurer un locataire](openvpn-azure-ad-tenant.md).

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="enablesign"></a> Configurer les paramètres de connexion

Sur la page **VPN Azure - Propriétés**, configurez les paramètres de connexion.

1. Définissez **Activé pour que les utilisateurs se connectent ?** sur **Oui**. Cela permet à tous les utilisateurs du locataire AD de se connecter au VPN.
2. Définissez **Affectation utilisateur requise ?** sur **Oui** si vous voulez limiter la connexion aux seuls utilisateurs dotés d’autorisations sur le VPN Azure.
3. Enregistrez vos modifications.

   ![Autorisations](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Étapes suivantes

Pour vous connecter à votre réseau virtuel, vous devez créer et configurer un profil de client VPN. Consultez [Configurer un client VPN pour les connexions VPN P2S](openvpn-azure-ad-client.md).
