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
ms.openlocfilehash: 40b81904daabfdad7e45571d8ab86cf32cac8964
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743357"
---
## <a name="scenario"></a>Scénario
Pour mieux illustrer la création d’itinéraires définis par l’utilisateur, ce document utilise le scénario suivant :

![DESCRIPTION DE L'IMAGE](./media/virtual-network-create-udr-scenario-include/figure1.png)

Dans ce scénario, vous créez un itinéraire défini par l’utilisateur pour le *sous-réseau frontal* et un autre pour le *sous-réseau principal*, comme suit : 

* **UDR-FrontEnd**. L’itinéraire frontal défini par l’utilisateur est appliqué au sous-réseau *FrontEnd* et contient un itinéraire :    
  * **RouteToBackend**. Cet itinéraire envoie tout le trafic sur le sous-réseau principal à la machine virtuelle **FW1**.
* **UDR-BackEnd**. L’UDR principal est appliqué au sous-réseau *BackEnd* et contient un itinéraire :    
  * **RouteToFrontend**. Cet itinéraire envoie tout le trafic sur le sous-réseau frontal à la machine virtuelle **FW1**.

La combinaison de ces itinéraires garantit que tout le trafic qui transite d’un sous-réseau à un autre est routé vers la machine virtuelle **FW1**, qui fait office d’équipement virtuel. Vous devez également activer le transfert IP pour la machine virtuelle **FW1**, afin qu’elle puisse recevoir le trafic destiné aux autres machines virtuelles.
