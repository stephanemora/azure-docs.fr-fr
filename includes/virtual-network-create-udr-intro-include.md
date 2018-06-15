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
ms.openlocfilehash: 226dfd9add69e8d89a030b858c819691d7b20627
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805149"
---
Les itinéraires système permettent la circulation automatique du trafic lié au déploiement, mais il existe des cas où il peut s’avérer utile d’acheminer les paquets au moyen d’une appliance virtuelle. Pour ce faire, créez des itinéraires personnalisés qui redirigent le prochain tronçon de paquets circulant vers un sous-réseau spécifique vers votre appliance virtuelle, et activer le transfert d’adresses IP pour la machine virtuelle exécutée en tant qu’appliance virtuelle.

Voici certaines situations dans lesquelles des équipements virtuels peuvent être utilisés :

* Surveillance du trafic avec un système de détection d’intrusion
* Contrôle du trafic avec un pare-feu

Pour plus d’informations sur les itinéraires définis par l’utilisateur et sur le transfert IP, consultez [Itinéraires définis par l’utilisateur et transfert IP](../articles/virtual-network/virtual-networks-udr-overview.md).

