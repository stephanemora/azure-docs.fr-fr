---
title: Serveurs principaux dans Gestion des API Azure | Microsoft Docs
description: En savoir plus sur les serveurs principaux personnalisés dans Gestion des API
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a0ef3a2c1f2f1fc5cdf00737d1984f6cb13c40d0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813017"
---
# <a name="backends-in-api-management"></a>Serveurs principaux dans Gestion des API

Un *service principal* (ou *API back-end*) dans Gestion des API est un service HTTP qui implémente votre API frontale et ses opérations.

Lors de l’importation de certaines API, Gestion des API configure automatiquement l’API back-end. Par exemple, Gestion des API configure le serveur principal lors de l’importation d’une [spécification OpenAPI](import-api-from-oas.md), d’une [API SOAP](import-soap-api.md) ou de ressources Azure, telles qu’une [application de fonction](import-function-app-as-api.md) ou une [application logique Azure](import-logic-app-as-api.md) déclenchée via HTTP.

Gestion des API prend également en charge l’utilisation d’autres ressources Azure, telles qu’un [cluster Service Fabric](how-to-configure-service-fabric-backend.md) ou un service personnalisé, comme API back-end. L’utilisation de ces serveurs principaux personnalisés requiert une configuration supplémentaire, par exemple, pour autoriser les informations d’identification des requêtes adressées au service principal et pour définir des opérations d’API. Vous configurez et gérez ces serveurs principaux dans le portail Azure ou à l’aide d’API ou d’outils Azure.

Après avoir créé un serveur principal, vous pouvez référencer l’URL du serveur principal dans vos API. Utilisez la stratégie [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) pour rediriger une requête API entrante vers le serveur principal personnalisé au lieu du serveur principal par défaut pour cette API.

## <a name="benefits-of-backends"></a>Avantages des serveurs principaux

Un serveur principal personnalisé présente plusieurs avantages, notamment :

* Résumé des informations sur le service principal, promotion de la réutilisabilité entre les API et amélioration de la gouvernance  
* Facile à utiliser en configurant une stratégie de transformation sur une API existante
* Utilisation de la fonctionnalité de Gestion des API pour gérer les secrets dans Azure Key Vault si des [valeurs nommées](api-management-howto-properties.md) sont configurées pour l’authentification de paramètre de requête ou d’en-tête

## <a name="next-steps"></a>Étapes suivantes

* Configurez un [serveur principal Service Fabric](how-to-configure-service-fabric-backend.md) à l’aide du portail Azure.
* Les serveurs principaux peuvent également être configurés à l’aide de l’[API REST](/rest/api/apimanagement) Gestion des API, d’[Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend) ou de [modèles Azure Resource Manager](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

