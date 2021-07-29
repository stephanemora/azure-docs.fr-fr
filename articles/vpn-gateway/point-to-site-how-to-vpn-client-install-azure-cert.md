---
title: Installer un certificat client point à site
titleSuffix: Azure VPN Gateway
description: Découvrez comment installer des certificats clients pour l’authentification par certificat P2S (Windows, Mac et Linux).
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/03/2021
ms.author: cherylmc
ms.openlocfilehash: d4ab1009b38d250a39455a9e8a470cd7f1156793
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2021
ms.locfileid: "111527102"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Installer des certificats client pour des connexions d’authentification par certificat P2S

Quand une passerelle VPN P2S est configurée pour exiger l’authentification par certificat, un certificat client doit être installé localement sur chaque client. Vous pouvez générer un certificat client à partir d’un certificat racine généré à l’aide d’une solution d’autorité de certification d’entreprise, ou à partir d’un certificat racine auto-signé. 

Lorsque vous générez un certificat client, le certificat est généralement installé automatiquement sur l’ordinateur client à partir duquel il a été généré. Si vous souhaitez vous connecter à votre réseau virtuel à partir d’un autre ordinateur client, vous devez installer un certificat client sur l’ordinateur à partir duquel vous vous connectez. Cela s’ajoute à la configuration du client VPN sur cet ordinateur.

Vous pouvez utiliser plusieurs méthodes pour générer et exporter des certificats auto-signés. Pour plus d’informations, consultez les articles suivants :

* [PowerShell](vpn-gateway-certificates-point-to-site.md)
* [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
* [Linux](vpn-gateway-certificates-point-to-site-linux.md) 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Les clients VPN Mac sont pris en charge avec le modèle de déploiement de Resource Manager seulement. Ils ne sont pas pris en charge avec le modèle de déploiement Classic.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Le certificat client Linux est installé sur le client dans le cadre de la configuration du client. Pour obtenir des instructions, voir [Configuration client - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).

## <a name="next-steps"></a>Étapes suivantes

Passez aux étapes de configuration point à site pour [Créer et installer les fichiers de configuration du client VPN](point-to-site-vpn-client-configuration-azure-cert.md).
