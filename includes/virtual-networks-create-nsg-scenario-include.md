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
ms.openlocfilehash: 588aa260f2ece543445bfd4da7ef4682dab8334c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309929"
---
## <a name="scenario"></a>Scénario
Pour mieux illustrer la création de Groupes de sécurité réseau (NSG), ce document utilise le scénario suivant :

![Scénario de réseau virtuel](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

Dans ce scénario, vous allez créer un NSG pour chaque sous-réseau du réseau virtuel **TestVNet** de la façon suivante : 

* **NSG-FrontEnd**. Le NSG frontal est appliqué au sous-réseau *FrontEnd* et contient deux règles :    
  * **rdp-rule**. Autorise le trafic RDP (Remote Desktop Protocol) vers le sous-réseau *FrontEnd*.
  * **web-rule**. Autorise le trafic HTTP vers le sous-réseau *FrontEnd*.
* **Back-end NSG**. Le NSG principal est appliqué au sous-réseau *BackEnd* et contient deux règles :    
  * **sql-rule**. Autorise le trafic SQL uniquement à partir du sous-réseau *FrontEnd*.
  * **web-rule**. Refuse tout trafic lié à Internet à partir du sous-réseau *BackEnd*.

La combinaison de ces règles crée un scénario de type DMZ, où le sous-réseau principal peut recevoir uniquement du trafic entrant pour SQL en provenance du sous-réseau frontal, et n’a pas accès à Internet, tandis que le sous-réseau frontal peut communiquer avec Internet et recevoir uniquement des requêtes HTTP entrantes.
