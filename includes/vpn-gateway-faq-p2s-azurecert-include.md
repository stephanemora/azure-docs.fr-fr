---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: e2e91dc91cf0fbe6827808785a4c3cc25b06542b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320152"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Puis-je utiliser ma propre AC racine PKI interne pour une connectivité point à site ?

Oui. Auparavant, seuls les certificats racines auto-signés pouvaient être utilisés. Vous pouvez toujours charger 20 certificats racine.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Quels outils puis-je utiliser pour créer des certificats ?

Vous pouvez utiliser votre solution de PKI d’entreprise (votre PKI interne), Azure PowerShell, MakeCert et OpenSSL.

### <a name="certsettings"></a>Y a-t-il des instructions pour les paramètres de certificat ?

* **Solution d’infrastructure à clé publique/Enterprise PKI interne :** Consultez les étapes pour [générer des certificats](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell :** Consultez le [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) article pour obtenir des instructions.

* **MakeCert :** Consultez le [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) article pour obtenir des instructions.

* **OpenSSL :** 

    * Lors de l’exportation de certificats, veillez à convertir le certificat racine en Base64.

    * Pour le certificat client :

      * Lorsque vous créez la clé privée, spécifiez la longueur 4096.
      * Lors de la création du certificat, pour le paramètre *-extensions*, spécifiez *usr_cert*.