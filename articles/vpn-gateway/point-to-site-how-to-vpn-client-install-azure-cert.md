---
title: Installer un certificat client point à site
titleSuffix: Azure VPN Gateway
description: Découvrez comment installer des certificats clients pour l’authentification par certificat P2S (Windows, Mac et Linux).
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2021
ms.author: cherylmc
ms.openlocfilehash: 917f60440d98924e5339f29fb99587eacf40b415
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766252"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Installer des certificats client pour des connexions d’authentification par certificat P2S

Quand une passerelle VPN P2S est configurée pour exiger l’authentification par certificat, un certificat client doit être installé localement sur chaque ordinateur client. Cet article vous aide à installer un certificat client localement sur un ordinateur client. Vous pouvez également utiliser [Intune](/mem/intune/configuration/vpn-settings-configure) pour installer certains profils et certificats de client VPN.

Si vous souhaitez générer un certificat client à partir d’un certificat racine auto-signé, consultez l’un des articles suivants :

* [Générer des certificats - PowerShell](vpn-gateway-certificates-point-to-site.md)
* [Générer des certificats - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
* [Générer des certificats - Linux](vpn-gateway-certificates-point-to-site-linux.md) 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Les clients VPN Mac sont pris en charge avec le [modèle de déploiement de Resource Manager](../azure-resource-manager/management/deployment-models.md) seulement. Ils ne sont pas pris en charge avec le modèle de déploiement Classic.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Le certificat client Linux est installé sur le client dans le cadre de la configuration du client. Pour obtenir des instructions, voir [Configuration client - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).

## <a name="next-steps"></a>Étapes suivantes

Passez aux étapes de configuration point à site pour [Créer et installer les fichiers de configuration du client VPN](point-to-site-vpn-client-configuration-azure-cert.md).