---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: afee9450bc1485f781eb0d70b5d4dd2f50424573
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44343237"
---
Vous pouvez utiliser un certificat racine qui a été généré à l’aide d’une solution d’entreprise (recommandée), ou vous pouvez générer un certificat auto-signé. Après avoir créé le certificat racine, exportez les données de certificat public (et non la clé privée) sous la forme d’un fichier .cer X.509 encodé en base 64, puis chargez les données de certificat public dans Azure.

* **Certificat d’entreprise :** si vous utilisez une solution d’entreprise, vous pouvez utiliser votre chaîne de certificats existante. Obtenez le fichier .cer pour le certificat racine que vous souhaitez utiliser.
* **Certificat racine auto-signé :** si vous n’utilisez pas de solution de certificat d’entreprise, vous devez générer un certificat racine auto-signé. Il est important que vous suiviez les procédures décrites dans l’un des articles de certificat P2S ci-dessous. Dans le cas contraire, les certificats que vous créez ne seront pas compatibles avec les connexions P2S, et les clients recevront une erreur de connexion lorsqu’ils essaieront de se connecter. Vous pouvez utiliser Azure PowerShell, MakeCert ou OpenSSL. Les procédures décrites dans les articles indiqués permettent de générer un certificat compatible :

  * [Instructions pour PowerShell sur Windows 10](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) : ces instructions requièrent Windows 10 et PowerShell pour générer des certificats. Les certificats clients qui sont générés à partir du certificat racine peuvent être installés sur n’importe quel client P2S pris en charge.
  * [Instructions pour MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) : si vous n’avez pas accès à un ordinateur Windows 10, utilisez MakeCert pour générer des certificats. MakeCert est déconseillé, mais vous pouvez toujours l’utiliser pour générer des certificats. Les certificats clients qui sont générés à partir du certificat racine peuvent être installés sur n’importe quel client P2S pris en charge.
  * [Instructions Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)
