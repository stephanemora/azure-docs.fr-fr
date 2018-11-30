---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 5c1caf87f32ddd827b85263ee634d3e15d821124
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52270015"
---
Vos machines virtuelles IaaS et instances de rôle PaaS dans un réseau virtuel reçoivent automatiquement une adresse IP privée à partir d’une plage que vous spécifiez, selon le sous-réseau auquel elles sont connectées. Cette adresse est conservée par les machines virtuelles et les instances de rôle, jusqu'à ce qu'elles soient mises hors service. Vous mettez une machine virtuelle ou une instance de rôle hors service en l'arrêtant à partir de PowerShell, l'interface de ligne de commande Azure ou le portail Azure. Dans ce cas, lorsque la machine virtuelle ou l'instance de rôle redémarre, elle reçoit une adresse IP disponible à partir de l'infrastructure Azure, qui peut ne pas être identique à celle qu'elle avait précédemment. Si vous arrêtez la machine virtuelle ou l’instance de rôle à partir du système d’exploitation invité, elle conserve l’adresse IP attribuée.  

Dans certains cas, vous souhaitez qu’une machine virtuelle ou instance de rôle ait une adresse IP statique, par exemple si votre machine virtuelle doit exécuter DNS ou faire office de contrôleur de domaine. Pour ce faire, définissez une adresse IP privée statique.

