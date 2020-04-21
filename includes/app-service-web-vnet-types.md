---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: c31a5aaa9866a4ce97cd3cd59a8e363834f70587
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312837"
---
* Les systèmes multilocataires qui prennent en charge l’ensemble des plans de tarification, excepté « Isolé ».
* La fonctionnalité App Service Environment qui opère un déploiement dans votre réseau virtuel et prend en charge les applications à plan tarifaire Isolé.

La fonctionnalité d’intégration au réseau virtuel est utilisée dans les applications mutualisées. Si votre application se trouve dans [Azure App Service Environment][ASEintro], elle est déjà dans un réseau virtuel et ne nécessite pas l’utilisation de la fonctionnalité d’intégration au réseau virtuel pour accéder aux ressources du même réseau virtuel. Pour plus d’informations sur toutes les fonctionnalités de mise en réseau, consultez [Fonctionnalités de mise en réseau App Service][Networkingfeatures].

L’intégration au réseau virtuel permet à votre application d’accéder aux ressources de votre réseau virtuel, sans pour autant accorder d’accès privé entrant à votre application à partir du réseau virtuel. L’accès aux sites privés fait référence au fait de rendre une application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. L’intégration au réseau virtuel sert uniquement à passer des appels sortants de votre application vers votre réseau virtuel. La fonctionnalité d’intégration au réseau virtuel se comporte différemment lorsqu’elle est utilisée avec un réseau virtuel situé dans la même région et dans d’autres régions. La fonctionnalité d’intégration au réseau virtuel présente deux variantes :

* **Intégration au réseau virtuel régional** : Quand vous vous connectez à des réseaux virtuels Azure Resource Manager dans la même région, vous devez disposer d’un sous-réseau dédié dans le réseau virtuel avec lequel vous vous intégrez.
* **Intégration au réseau virtuel avec passerelle obligatoire** : Lors de la connexion à des réseaux virtuels dans d’autres régions ou à un réseau virtuel classique dans la même région, vous avez besoin d’une passerelle de réseau virtuel Azure approvisionnée dans le réseau virtuel cible.

Les fonctionnalités d’intégration au réseau virtuel :

* Nécessitent un plan tarifaire Standard, Premium, PremiumV2 ou Élastique Premium.
* Prennent en charge les protocoles TCP et UDP.
* Fonctionnent avec les applications Azure App Service et les applications de fonction.

L’intégration au réseau virtuel ne prend pas en charge certaines choses, notamment :

* Montage d’un lecteur.
* Intégration d’Active Directory.
* NetBIOS.

L’intégration au réseau virtuel avec passerelle obligatoire fournit un accès aux ressources uniquement du réseau virtuel cible, ou des réseaux connectés au réseau virtuel cible avec un peering ou des réseaux privés virtuels. L’intégration au réseau virtuel avec passerelle obligatoire n’autorise pas l’accès aux ressources disponibles sur les connexions ExpressRoute Azure ou fonctionne avec des points de terminaison de service.

Quelle que soit la version utilisée, l’intégration au réseau virtuel permet à votre application d’accéder aux ressources de votre réseau virtuel, mais n’accorde pas d’accès privé entrant à votre application à partir du réseau virtuel. L’accès à un site privé fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé tel qu’un réseau virtuel Azure. L'intégration au réseau virtuel sert uniquement à passer des appels sortants de votre application vers votre réseau virtuel.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
