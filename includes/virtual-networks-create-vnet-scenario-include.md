---
title: Fichier Include
description: Fichier Include
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 29b2ac1a5dc128028dbd40e683c1b45e6208d124
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>Scénario

Pour illustrer la création d’un réseau virtuel et de sous-réseaux, ce document utilise le scénario suivant :

![Scénario de réseau virtuel](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

Dans ce scénario, vous créez un réseau virtuel nommé **TestVNet** avec un bloc CIDR réservé de **192.168.0.0./16**. Le réseau virtuel contient les sous-réseaux suivants : 

* **FrontEnd**, qui utilise **192.168.1.0/24** comme bloc CIDR.
* **BackEnd**, qui utilise **192.168.2.0/24** comme bloc CIDR.

