---
title: Fichier Include
description: Fichier Include
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 2b1f9990985951a4e6ef260954968c0e1466c298
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597043"
---
## <a name="scenario"></a>Scénario

Pour illustrer la création d’un réseau virtuel et de sous-réseaux, ce document utilise le scénario suivant :

![Scénario de réseau virtuel](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

Dans ce scénario, vous créez un réseau virtuel nommé **TestVNet** avec un bloc CIDR réservé de **192.168.0.0./16**. Le réseau virtuel contient les sous-réseaux suivants : 

* **FrontEnd**, qui utilise **192.168.1.0/24** comme bloc CIDR.
* **BackEnd**, qui utilise **192.168.2.0/24** comme bloc CIDR.