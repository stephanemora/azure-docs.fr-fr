---
title: Exemples de modèles Resource Manager - Azure Front Door
description: Informations sur les exemples de modèles Azure Resource Manager fournis pour Azure Front Door.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 05/11/2021
ms.openlocfilehash: 7be1c0852528033103c46c3de8cceccf6b5c24e0
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110058471"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Modèles Azure Resource Manager pour Azure Front Door

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Le tableau suivant comprend des liens vers des modèles Azure Resource Manager pour Azure Front Door, avec des architectures de référence incluant d’autres services Azure.

| Exemple | Description |
|-|-|
| [Front Door (création rapide)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium/) | Crée un profil Front Door de base incluant un point de terminaison, un groupe d’origine, une origine et une route.  |
| [Ensemble de règles](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Crée un profil et un ensemble de règles Front Door.  |
| [Stratégie WAF avec l’ensemble de règles managées](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-waf-managed/) | Crée un profil Front Door et une stratégie WAF avec un ensemble de règles managées.  |
| [Stratégie WAF avec une règle personnalisée](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-waf-custom/) | Crée un profil Front Door et une stratégie WAF avec une règle personnalisée.  |
| [Stratégie WAF avec limite de débit](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rate-limit/) | Crée un profil Front Door et un pare-feu d’applications web avec une règle personnalisée pour limiter le débit.  |
| [Stratégie WAF avec géofiltrage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-geo-filtering/) | Crée un profil Front Door et un pare-feu d’applications web avec une règle personnalisée pour appliquer un géofiltrage.  |
|**Origines App Service**| **Description** |
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | Crée une application App Service avec un point de terminaison public et un profil Front Door.  |
| [App Service avec Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | Crée une application App Service avec un point de terminaison privé et un profil Front Door.  |
|**Origines Azure Functions**| **Description** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | Crée une application Azure Functions avec un point de terminaison public et un profil Front Door.  |
| [Azure Functions avec Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | Crée une application Azure Functions avec un point de terminaison privé et un profil Front Door.  |
|**Origines API Management**| **Description** |
| [Gestion des API (externe)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | Crée une instance de Gestion des API avec une intégration au réseau virtuel VNet externe et un profil Front Door.  |
|**Origines Stockage**| **Description** |
| [Site web statique de stockage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | Crée un compte Stockage Azure et un site web statique avec un point de terminaison public et un profil Front Door.  |
| [Objets blob de stockage avec Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | Crée un compte Stockage Azure et un conteneur d’objets blob avec un point de terminaison privé et un profil Front Door.  |
|**Origines Application Gateway**| **Description** |
| [Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-application-gateway-public) | Crée une passerelle applicative et un profil Front Door. |
|**Origines Machine virtuelle**| **Description** |
| [Machine virtuelle avec service Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-vm-private-link) | Crée une machine virtuelle et un service Private Link ainsi qu’un profil Front Door. |
| | |
