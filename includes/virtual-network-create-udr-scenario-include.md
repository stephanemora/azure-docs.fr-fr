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
ms.openlocfilehash: b91ae155761f6357e286f4742d57b97cf96d909a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805152"
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

