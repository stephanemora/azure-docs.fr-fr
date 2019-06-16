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
ms.openlocfilehash: 40b81904daabfdad7e45571d8ab86cf32cac8964
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170867"
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

