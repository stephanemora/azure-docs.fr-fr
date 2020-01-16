---
title: 'Générer et exporter des certificats pour point à site : Linux : Interface de ligne de commande'
description: Créez un certificat racine auto-signé, exportez la clé publique et générez des certificats clients à l’aide de l’interface de ligne de commande Linux (strongSwan).
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: a0f996ff2805da4dd5af400642eef2506c228d33
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779754"
---
# <a name="generate-and-export-certificates"></a>Générer et exporter des certificats

Les connexions de point à site utilisent des certificats pour l’authentification. Cet article vous explique comment créer un certificat racine auto-signé et générer des certificats clients à l’aide de l’interface de ligne de commande Linux et de strongSwan. Pour des instructions sur d’autres certificats, voir les articles [PowerShell](vpn-gateway-certificates-point-to-site.md) ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Pour plus d’informations sur l’installation de strongSwan à l’aide de l’interface utilisateur graphique au lieu de l’interface de ligne de commande, consultez les étapes de l’article [Configuration du client](point-to-site-vpn-client-configuration-azure-cert.md#install).

## <a name="install-strongswan"></a>Installer strongSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Générer et exporter des certificats

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Passez à votre configuration point à site pour [Créer et installer les fichiers de configuration du client VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
