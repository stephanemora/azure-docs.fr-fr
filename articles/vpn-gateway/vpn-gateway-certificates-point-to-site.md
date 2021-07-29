---
title: 'Générer et exporter des certificats pour P2S : PowerShell'
titleSuffix: Azure VPN Gateway
description: Découvrez comment créer un certificat racine auto-signé, exporter une clé publique et générer des certificats clients pour des connexions de point à site de la passerelle VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/03/2021
ms.author: cherylmc
ms.openlocfilehash: 48240793cee233d8e97ab79e6421b02eddc86f23
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2021
ms.locfileid: "111527565"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Générer et exporter des certificats pour les connexions de point à site à l’aide de PowerShell

Les connexions de point à site utilisent des certificats pour l’authentification. Cet article explique comment créer un certificat racine auto-signé et générer des certificats clients à l’aide de PowerShell sous Windows 10 ou Windows Server 2016. Pour des instructions sur d’autres certificats, voir [Certificats - Linux](vpn-gateway-certificates-point-to-site-linux.md) ou [Certificats - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Les étapes décrites dans cet article s’appliquent à Windows 10 ou Windows Server 2016. Les applets de commande PowerShell que vous utilisez pour générer des certificats font partie du système d’exploitation et ne fonctionnent pas sur d’autres versions de Windows. L’ordinateur Windows 10 ou Windows Server 2016 est nécessaire uniquement pour générer les certificats. Une fois les certificats générés, vous pouvez les charger ou les installer sur n’importe quel système d’exploitation client pris en charge.

Si vous n’avez pas accès à un ordinateur Windows 10 ou Windows Server 2016, vous pouvez utiliser [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) pour générer des certificats. Les certificats générés à l’aide de l’une de ces deux méthodes peuvent être installés sur n’importe quel système d’exploitation client [pris en charge](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq).

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install-an-exported-client-certificate"></a><a name="install"></a>Installer un certificat client exporté

Chaque client qui se connecte au réseau virtuel via une connexion P2S a besoin d’un certificat client installé localement.

Pour installer un certificat client, consultez [Installer un certificat client pour des connexions point à site](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Étapes suivantes

Poursuivez votre configuration point à site.

* Pour connaître les étapes du modèle de déploiement **Resource Manager**, consultez [Configurer P2S à l’aide de l’authentification par certificat Azure native](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Pour connaître les étapes du modèle de déploiement **Classic**, consultez la page [Configurer une connexion VPN de point à site à un réseau virtuel (Classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).