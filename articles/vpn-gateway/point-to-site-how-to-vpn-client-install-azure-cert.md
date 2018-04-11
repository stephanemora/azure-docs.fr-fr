---
title: Installer un certificat client P2S | Azure
description: Installez un certificat client Mac ou Windows pour une connexion P2S avec authentification par certificat.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager, azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: cherylmc
ms.openlocfilehash: bf2788fff64ab8b3a5ccf75b8a80f2bd5aba5151
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>Installer un certificat client pour des connexions point à site avec authentification par certificat Azure

Tous les clients qui se connectent à un réseau virtuel à l’aide d’une connexion point à site avec authentification par certificat Azure nécessitent un certificat client. Cet article vous aide à installer un certificat client utilisé pour l’authentification à la connexion à réseau virtuel via une connexion P2S.

## <a name="generate"></a>Générer et exporter un certificat client

Vous pouvez générer un certificat client à partir d’un certificat racine généré à l’aide d’une solution d’autorité de certification d’entreprise, ou à partir d’un certificat racine auto-signé. Consultez les instructions [PowerShell](vpn-gateway-certificates-point-to-site.md) ou [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) pour continuer. Après avoir généré les certificats client, exportez-les en tant que fichiers .pfx. Veillez à inclure l’ensemble de la chaîne du certificat lors de l’exportation.

## <a name="installwin"></a>Installer le certificat - Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Installer le certificat - Mac

Les clients VPN Mac sont pris en charge avec le modèle de déploiement de Resource Manager seulement. Ils ne sont pas pris en charge avec le modèle de déploiement Classic.

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Poursuivez votre configuration point à site.

* [Portail Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Portail Azure (classique)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
