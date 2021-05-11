---
title: Génération et exportation de certificats pour les connexions VPN utilisateur | Azure Virtual WAN
description: Apprenez à créer un certificat racine auto-signé, à exporter une clé publique et à générer des certificats clients pour les connexions VPN utilisateur Virtual WAN (point à site) à l'aide de PowerShell.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: d126f68ed1eebae9dda7ad957748cb3258b5ad89
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108165296"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Génération et exportation de certificats pour les connexions VPN utilisateur

Les connexions (point à site) VPN utilisateur utilisent des certificats pour l’authentification. Cet article explique comment créer un certificat racine auto-signé et générer des certificats clients à l’aide de PowerShell sous Windows 10 ou Windows Server 2016.

Vous devez suivre les étapes de cet article sur un ordinateur exécutant Windows 10 ou Windows Server 2016. Les applets de commande PowerShell que vous utilisez pour générer des certificats font partie du système d’exploitation et ne fonctionnent pas sur d’autres versions de Windows. L’ordinateur Windows 10 ou Windows Server 2016 est nécessaire uniquement pour générer les certificats. Une fois les certificats générés, vous pouvez les charger ou les installer sur n’importe quel système d’exploitation client pris en charge.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Passez aux [étapes Virtual WAN pour la connexion VPN utilisateur](virtual-wan-about.md)
