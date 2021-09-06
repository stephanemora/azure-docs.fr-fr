---
title: Fichier Include
description: inclure fichier
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/15/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b77128033cbe0f99023dbee8a5a15e06820399b1
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112255594"
---
>[!NOTE]
>À compter du 1er juillet 2018, la passerelle VPN Azure ne prendra plus en charge TLS 1.0 et 1.1. Elle prendra uniquement en charge TLS 1.2. Pour maintenir la prise en charge, consultez les [mises à jour permettant la prise en charge de TLS 1.2](#tls1).

Par ailleurs, les algorithmes hérités suivants sont également dépréciés pour TLS depuis le 1er juillet 2018 :

* RC4 (Rivest Cipher 4)
* DES (Data Encryption Algorithm)
* 3DES (Triple Data Encryption Algorithm)
* MD5 (Message Digest 5)

### <a name="how-do-i-enable-support-for-tls-12-in-windows-81"></a><a name="tls1"></a>Comment faire pour activer la prise en charge de TLS 1.2 dans Windows 8.1 ?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
