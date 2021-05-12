---
title: 'Activer MFA pour les utilisateurs du VPN : Authentification Azure AD'
titleSuffix: Azure VPN Gateway
description: Découvrez comment activer l’authentification multifacteur (MFA, Multi-Factor Authentication) pour les utilisateurs de VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 05/05/2021
ms.author: alzam
ms.openlocfilehash: 702f4259ba1003c2cf4f52f7a840558fd3abf42f
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108754754"
---
# <a name="enable-azure-ad-multi-factor-authentication-mfa-for-vpn-users"></a>Activer Azure AD Multi-Factor Authentication (MFA) pour les utilisateurs du VPN

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>Activer l’authentification

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>Configurer les paramètres de connexion

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>Option 1 : accès par utilisateur

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>Option 2  : accès conditionnel

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour vous connecter à votre réseau virtuel, vous devez créer et configurer un profil de client VPN. Consultez [Configurer un client VPN pour les connexions VPN P2S](openvpn-azure-ad-client.md).