---
title: Fichier Include
description: Fichier Include
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 873549442284ede2e9f020bd90879f721b9c1a18
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805215"
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

