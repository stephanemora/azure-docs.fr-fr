---
title: Exemples de modèles Resource Manager - Azure Front Door
description: Informations sur les exemples de modèles Azure Resource Manager fournis pour Azure Front Door.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 03/24/2021
ms.openlocfilehash: 929adb0be948339af033d85b0dabd7e1cedf353e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561744"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Modèles Azure Resource Manager pour Azure Front Door

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Le tableau suivant comprend des liens vers des modèles Azure Resource Manager pour Azure Front Door, avec des architectures de référence incluant d’autres services Azure.

| Exemple | Description |
|-|-|
| [Ensemble de règles](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Crée un profil et un ensemble de règles Front Door.  |
|**Origines App Service**| **Description** |
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | Crée une application App Service avec un point de terminaison public et un profil Front Door.  |
| [App Service avec Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | Crée une application App Service avec un point de terminaison privé et un profil Front Door.  |
| [App Service Environment avec Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | Crée un environnement App Service, une application avec un point de terminaison privé et un profil Front Door.  |
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
