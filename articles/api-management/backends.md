---
title: Serveurs principaux dans Gestion des API Azure | Microsoft Docs
description: En savoir plus sur les serveurs principaux personnalisés dans Gestion des API
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 09/21/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 008c5624f621f8598963a8f94261a9e369d0ca0e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647116"
---
# <a name="backends-in-api-management"></a>Serveurs principaux dans Gestion des API

Un *service principal* (ou *API back-end*) dans Gestion des API est un service HTTP qui implémente votre API frontale et ses opérations.

Lors de l’importation de certaines API, Gestion des API configure automatiquement l’API back-end. Par exemple, le service Gestion des API configure le back-end lors de l’importation :
* Une [spécification OpenAPI](import-api-from-oas.md).
* Une [API SOAP](import-soap-api.md).
* Des ressources Azure, comme une [application Azure Functions](import-function-app-as-api.md) ou une [application logique](import-logic-app-as-api.md) déclenchée via HTTP.

La Gestion des API prend également en charge l’utilisation d’autres ressources Azure, comme un back-end d’API, par exemple :
* Un [cluster Service Fabric](how-to-configure-service-fabric-backend.md).
* Un service personnalisé. 

Les back-ends personnalisés nécessitent une configuration supplémentaire pour autoriser les informations d’identification des requêtes adressées au service back-end et définir des opérations d’API. Configurez et gérez les back-ends personnalisés dans le portail Azure, ou bien à l’aide d’API ou d’outils Azure.

Après avoir créé un serveur principal, vous pouvez référencer l’URL du serveur principal dans vos API. Utilisez la stratégie [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) pour rediriger une requête API entrante vers le serveur principal personnalisé au lieu du serveur principal par défaut pour cette API.

## <a name="benefits-of-backends"></a>Avantages des serveurs principaux

Un serveur principal personnalisé présente plusieurs avantages, notamment :

* Résumé des informations sur le service back-end, promotion de la réutilisabilité entre les API et amélioration de la gouvernance.  
* Facilité d’utilisation en configurant une stratégie de transformation sur une API existante.
* Utilisation de la fonctionnalité de Gestion des API pour gérer les secrets dans Azure Key Vault si des [valeurs nommées](api-management-howto-properties.md) sont configurées pour l’authentification de paramètre de requête ou d’en-tête.

## <a name="limitation"></a>Limitation

Pour les niveaux **Développeur** et **Premium**, une instance Gestion des API déployée dans un [réseau virtuel interne](api-management-using-with-internal-vnet.md) peut générer des erreurs HTTP 500 `BackendConnectionFailure` quand l’URL du point de terminaison de passerelle et l’URL du back-end sont identiques. Si vous rencontrez cette limitation, suivez les instructions fournies dans l’article [Limitation des demandes de la Gestion des API autochaînées en mode réseau virtuel interne](https://techcommunity.microsoft.com/t5/azure-paas-blog/self-chained-apim-request-limitation-in-internal-virtual-network/ba-p/1940417) du blog de la communauté technique. 

## <a name="next-steps"></a>Étapes suivantes

* Configurez un [serveur principal Service Fabric](how-to-configure-service-fabric-backend.md) à l’aide du portail Azure.
* Les serveurs principaux peuvent également être configurés à l’aide de l’[API REST](/rest/api/apimanagement) Gestion des API, d’[Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend) ou de [modèles Azure Resource Manager](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

