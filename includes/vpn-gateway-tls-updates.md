---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320117"
---
>[!NOTE]
>À compter du 1er juillet 2018, la passerelle VPN Azure ne prendra plus en charge TLS 1.0 et 1.1. Elle prendra uniquement en charge TLS 1.2. Pour maintenir la prise en charge, consultez les [mises à jour permettant la prise en charge de TLS 1.2](#tls1).

Par ailleurs, les algorithmes hérités suivants sont également dépréciés pour TLS depuis le 1er juillet 2018 :

* RC4 (Rivest Cipher 4)
* DES (Data Encryption Algorithm)
* 3DES (Triple Data Encryption Algorithm)
* MD5 (Message Digest 5)

### <a name="tls1"></a>Comment activer la prise en charge de TLS 1.2 dans Windows 7 et Windows 8.1 ?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
