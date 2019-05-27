---
title: Fichier Include
description: Fichier Include
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 1bcadc18172535649a0ceb482939ca6a75477e25
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170713"
---
### <a name="what-is-expressroute-direct"></a>Présentation d’ExpressRoute Direct

Avec ExpressRoute Direct, les clients ont la possibilité de se connecter directement au réseau international de Microsoft à partir d’emplacements d’appairage qui sont distribués stratégiquement dans le monde entier. ExpressRoute Direct offre une double connectivité de 100 Gbits/s qui prend en charge la connectivité Active/Active à grande échelle. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Comment les clients peuvent-ils se connecter à ExpressRoute Direct ? 

Les clients doivent se faire aider de leurs opérateurs locaux et de leurs fournisseurs de colocalisation pour obtenir une connectivité aux routeurs ExpressRoute dans le but d’utiliser ExpressRoute Direct.

### <a name="what-locations-currently-support-expressroute-direct"></a>Quels emplacements prend actuellement en charge directe de ExpressRoute ? 

Les ports disponibles sont dynamiques et sont fournis par PowerShell pour afficher la capacité. Les régions, qui *sont susceptibles d’être modifiées selon la disponibilité*, sont les suivantes :

* Amsterdam
* Canberra
* Chicago
* Washington DC
* Dallas 
* Hong Kong (R.A.S.)
* Londres
* Los Angeles
* New York
* Paris
* Perth
* Toronto
* San Antonio
* Seattle
* Séoul
* Silicon Valley
* Singapour 
* Sydney

### <a name="what-is-the-sla-for-expressroute-direct"></a>Quel contrat SLA s’applique à ExpressRoute Direct ?

ExpressRoute Direct utilise la même [version d’entreprise d’ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Quels scénarios les clients peuvent-ils envisager avec ExpressRoute Direct ?  

ExpressRoute Direct fournit aux clients des paires de ports directs 100 Gbits/s pour la structure globale de Microsoft. Les scénarios qui apportent les plus grands bénéfices sont les suivants : ingestion massive de données, isolation physique des marchés réglementés et capacité dédiée pour les scénarios de rafales, tels que le rendu. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Quel modèle de facturation s’applique à ExpressRoute Direct ? 

Un montant fixe est facturé pour la paire de ports. Les circuits standard sont compris sans frais supplémentaires, tandis que les circuits Premium engendrent un petit supplément. La sortie est facturée pour chaque circuit, en fonction de la zone où se trouve l’emplacement d’appairage.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>Lorsque vous n’est début de facturation pour les paires de port Direct d’ExpressRoute ?

Les paires de ports ExpressRoute Direct sont facturées 45 jours après la création de la ressource ExpressRoute Direct ou lorsqu'un lien, voire les deux sont activés, selon la première de ces éventualités. La période de grâce de 45 jours a été mise en place pour permettre aux clients de terminer le processus d'interconnexion avec le fournisseur de colocalisation.
