---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671494"
---
* Les systèmes multilocataires qui prennent en charge l’ensemble des plans de tarification, excepté « Isolé »
* L’environnement ASE (App Service Environment), qui se déploie dans votre réseau virtuel et prend en charge les applications à plan tarifaire Isolé.

Ce document décrit la fonctionnalité d’intégration au réseau virtuel, qui est destinée à être utilisée dans les applications mutualisées. Si votre application se trouve dans [Azure App Service Environment][ASEintro], elle est déjà dans un réseau virtuel et ne nécessite pas l’utilisation de la fonctionnalité d’intégration au réseau virtuel pour accéder aux ressources du même réseau virtuel. Pour plus d’informations sur toutes les fonctionnalités réseau, voir [Fonctionnalités de mise en réseau App Service][Networkingfeatures]

L’intégration au réseau virtuel permet à votre application d’accéder aux ressources de votre réseau virtuel, mais sans pour autant accorder d’accès privé entrant à votre application à partir du réseau virtuel. L’accès au site privé fait référence au fait de rendre l’application accessible uniquement à partir d’un réseau privé, par exemple, un réseau virtuel Azure. L'intégration au réseau virtuel sert uniquement à passer des appels sortants de votre application vers votre réseau virtuel. La fonctionnalité d’intégration au réseau virtuel se comporte différemment lorsqu’elle est utilisée avec des réseaux virtuels de la même région, et avec ceux d’autres régions. La fonctionnalité d’intégration au réseau virtuel présente deux variantes.

* Intégration au réseau virtuel régional : lors de la connexion aux réseaux virtuels Resource Manager de la même région, vous devez disposer d’un sous-réseau dédié dans le réseau virtuel que vous intégrez. 
* Intégration au réseau virtuel avec passerelle obligatoire : lors de la connexion à des réseaux virtuels d’autres régions, ou à un réseau virtuel classique dans la même région, vous avez besoin d’une passerelle de réseau virtuel provisionnée dans le réseau virtuel cible.

Les fonctionnalités d’intégration au réseau virtuel :

* Demandent un plan tarifaire Standard, Premium, PremiumV2 ou Élastique Premium 
* Prennent en charge les protocoles TCP et UDP
* Fonctionnent avec les applications App Service et les applications de fonction

L’intégration au réseau virtuel ne prend pas en charge certaines choses, notamment :

* montage d’un lecteur ;
* intégration AD ; 
* NetBios ;

L’intégration au réseau virtuel avec passerelle obligatoire fournit uniquement un accès aux ressources du réseau virtuel cible, ou des réseaux connectés au réseau virtuel cible avec un peering ou des réseaux privés virtuels. L’intégration au réseau virtuel avec passerelle obligatoire n’autorise pas l’accès aux ressources disponibles sur les connexions ExpressRoute, ou fonctionne avec des points de terminaison de service. 

Quelle que soit la version utilisée, l’intégration au réseau virtuel permet à votre application d’accéder aux ressources de votre réseau virtuel, mais n’accorde pas d’accès privé entrant à votre application à partir du réseau virtuel. L’accès au site privé fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. L'intégration au réseau virtuel sert uniquement à passer des appels sortants de votre application vers votre réseau virtuel. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features