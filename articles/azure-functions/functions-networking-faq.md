---
title: Questions fréquemment posées sur la mise en réseau dans Azure Functions
description: Réponses à certaines questions et des scénarios de mise en réseau avec Azure Functions les plus courants.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 3cf6a0d080e2d8cafcab8e69a614b59a470c7aba
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682200"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Questions fréquemment posées sur la mise en réseau dans Azure Functions

Cet article répertorie les questions fréquemment posées sur la mise en réseau dans Azure Functions. Pour obtenir une présentation plus complète, consultez [fonctions des options de mise en réseau](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Comment définir une adresse IP statique dans les fonctions ?

Déploiement d’une fonction dans un environnement App Service est actuellement la seule façon d’avoir une entrante et sortante adresse IP statique pour votre fonction. Pour plus d’informations sur l’utilisation d’un environnement App Service, commencez par lire l’article [créer et utiliser un équilibreur de charge interne avec un environnement App Service](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Comment limiter les accès à internet à ma fonction ?

Vous pouvez restreindre l’accès internet de deux manières :

* [Restrictions d’adresse IP](../app-service/app-service-ip-restrictions.md): Limiter le trafic entrant à votre application de fonction par la plage d’adresses IP.
* Suppression de tous les déclencheurs HTTP. Pour certaines applications, il suffit simplement éviter des déclencheurs HTTP et utiliser toute autre source d’événement pour déclencher votre fonction.

N’oubliez pas que l’Éditeur du portail Azure nécessite un accès direct à votre fonction en cours d’exécution. Les modifications de code via le portail Azure nécessitera l’appareil que vous utilisez pour parcourir le portail pour que son dans la liste verte IP. Mais vous pouvez toujours utiliser quoi que ce soit sous l’onglet fonctionnalités de plateforme avec des restrictions de réseau en place.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Comment limiter mon application de fonction à un réseau virtuel ?

La seule façon de restreindre totalement une fonction de telle sorte que tout le trafic transite via un réseau virtuel consiste à utiliser un équilibrage de charge interne ASE. Cette option déploie votre site sur une infrastructure dédiée à l’intérieur d’un réseau virtuel et envoie tous les déclencheurs et le trafic via le réseau virtuel. 

Pour plus d’informations sur l’utilisation d’un environnement App Service, commencez par lire l’article [créer et utiliser un équilibreur de charge interne avec un environnement App Service](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Comment puis-je accéder des ressources dans un réseau virtuel à partir d’une application de fonction ?

Vous pouvez accéder aux ressources dans un réseau virtuel à partir d’une fonction en cours d’exécution à l’aide de l’intégration de réseau virtuel. Pour plus d’informations, consultez [intégration de réseau virtuel](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Comment accéder aux ressources protégées par des points de terminaison de service ?

À l’aide de l’intégration de réseau virtuel (actuellement en version préliminaire), vous pouvez accéder ressources sécurisées par point de terminaison de service à partir d’une fonction en cours d’exécution. Pour plus d’informations, consultez [afficher un aperçu de l’intégration de réseau virtuel](functions-networking-options.md#preview-version-of-virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Comment puis-je déclencher une fonction à partir d’une ressource dans un réseau virtuel ?

Vous pouvez déclencher une fonction à partir d’une ressource dans un réseau virtuel uniquement par le déploiement de votre application de fonction à un environnement App Service. Pour plus d’informations sur l’utilisation d’un environnement App Service, consultez [créer et utiliser un équilibreur de charge interne avec un environnement App Service](../app-service/environment/create-ilb-ase.md).


## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Comment puis-je déployer mon application de fonction dans un réseau virtuel ?

Déploiement dans un environnement App Service est la seule façon de créer une application de fonction qui est entièrement à l’intérieur d’un réseau virtuel. Pour plus d’informations sur l’utilisation d’un équilibreur de charge interne avec un environnement App Service, commencez par lire l’article [créer et utiliser un équilibreur de charge interne avec un environnement App Service](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Pour les scénarios où vous devez uniquement l’accès à sens unique aux ressources de réseau virtuel, ou moins d’isolation réseau complète, consultez le [mise en réseau de vue d’ensemble de fonctions](functions-networking-options.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la mise en réseau et fonctions : 

* [Suivez le didacticiel sur la mise en route avec l’intégration de réseau virtuel](./functions-create-vnet.md)
* [En savoir plus sur les options de mise en réseau dans Azure Functions](./functions-networking-options.md)
* [En savoir plus sur l’intégration de réseau virtuel avec App Service et Functions](../app-service/web-sites-integrate-with-vnet.md)
* [En savoir plus sur les réseaux virtuels dans Azure](../virtual-network/virtual-networks-overview.md)
* [Activer plusieurs fonctionnalités mise en réseau et le contrôle avec les environnements App Service](../app-service/environment/intro.md)
