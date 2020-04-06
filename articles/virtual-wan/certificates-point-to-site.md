---
title: Génération et exportation de certificats pour les connexions VPN utilisateur | Azure Virtual WAN
description: Créez un certificat racine auto-signé, exportez la clé publique et générez des certificats clients avec PowerShell sous Windows 10 ou Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: 0303bac88f34c895a4a680cd5bff0e9d1513d2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059959"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Génération et exportation de certificats pour les connexions VPN utilisateur

Les connexions (point à site) VPN utilisateur utilisent des certificats pour l’authentification. Cet article explique comment créer un certificat racine auto-signé et générer des certificats clients à l’aide de PowerShell sous Windows 10 ou Windows Server 2016.

Vous devez suivre les étapes de cet article sur un ordinateur exécutant Windows 10 ou Windows Server 2016. Les applets de commande PowerShell que vous utilisez pour générer des certificats font partie du système d’exploitation et ne fonctionnent pas sur d’autres versions de Windows. L’ordinateur Windows 10 ou Windows Server 2016 est nécessaire uniquement pour générer les certificats. Une fois les certificats générés, vous pouvez les charger ou les installer sur n’importe quel système d’exploitation client pris en charge.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Passez aux [étapes Virtual WAN pour la connexion VPN utilisateur](virtual-wan-about.md)
