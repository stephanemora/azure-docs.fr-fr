---
title: Forum aux Questions sur la mise en réseau dans Azure Functions
description: Réponses à certaines questions et des scénarios de mise en réseau avec Azure Functions les plus courants.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 126b9ccefedee1f5cefdac8a8666a58e7a4a1fef
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548632"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Questions fréquemment posées sur la mise en réseau dans Azure Functions

Voici une liste de forum aux questions de mise en réseau. Pour obtenir une présentation plus complète, consultez le [fonctions document options mise en réseau](functions-networking-options.md)

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Comment définir une adresse IP statique dans les fonctions ?

Actuellement, le déploiement d’une fonction dans un environnement App Service (ASE) est le seul moyen d’avoir une entrante et sortante adresse IP statique pour votre fonction. Pour plus d’informations sur l’utilisation d’un ASE, commencez par l’article ici : [Création et utilisation d’un environnement ASE](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Comment limiter les accès à internet à ma fonction ?

Vous pouvez restreindre l’accès internet dans un nombre de méthodes, répertoriées ci-dessous.

* [Restrictions d’adresse IP](../app-service/app-service-ip-restrictions.md): restreindre le trafic entrant à votre application de fonction par la plage d’adresses IP.
* Supprimer tous les déclencheurs HTTP. Pour certaines applications, il suffit simplement éviter des déclencheurs HTTP et utiliser toute autre source d’événement pour déclencher votre fonction.

Le plus important lors de cette opération consiste à n’oubliez pas que l’Éditeur du portail Azure nécessite un accès direct à votre fonction en cours d’exécution à utiliser. Les modifications de code via le portail Azure nécessitera l’appareil que vous utilisez pour parcourir le portail pour que son dans la liste verte IP. Vous pouvez toujours, cependant, utiliser quoi que ce soit sous l’onglet fonctionnalités de plateforme avec des restrictions de réseau en place.

## <a name="how-do-i-restrict-my-function-app-to-a-vnet"></a>Comment limiter mon application de fonction à un réseau virtuel ?

La seule façon de restreindre totalement une fonction de telle sorte que tout le trafic transite par un réseau virtuel consiste à utiliser un équilibrage de charge d’interne (ILB) App Service Environment (ASE). Cette option déploie votre site sur une infrastructure dédiée à l’intérieur d’un réseau virtuel et envoie tous les déclencheurs et le trafic via le réseau virtuel. Pour plus d’informations sur l’utilisation d’un ASE, commencez par l’article ici : [Création et utilisation d’un environnement ASE](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-vnet-from-a-function-app"></a>Comment puis-je accéder des ressources dans un réseau virtuel à partir d’une application de fonction ?

Vous pouvez accéder aux ressources un réseau virtuel à partir d’une fonction en cours d’exécution à l’aide d’intégration au réseau virtuel. Pour plus d’informations, consultez [intégration au réseau virtuel](functions-networking-options.md#vnet-integration)

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Comment accéder aux ressources protégées par des points de terminaison de Service ?

La nouvelle intégration de réseau virtuel (actuellement en version préliminaire), vous pouvez accéder aux point de terminaison de Service des ressources à partir d’une fonction en cours d’exécution sécurisées. Pour plus d’informations, consultez [afficher un aperçu d’intégration au réseau virtuel](functions-networking-options.md#preview-vnet-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-vnet"></a>Comment puis-je déclencher une fonction à partir d’une ressource dans un réseau virtuel ?

Vous pouvez uniquement déclencher une fonction à partir d’une ressource dans un réseau virtuel en déployant votre application de fonction dans un environnement App Service. Pour plus d’informations sur l’utilisation d’un ASE, consultez [création et utilisation d’un environnement ASE](../app-service/environment/create-ilb-ase.md).


## <a name="how-can-i-deploy-my-function-app-in-a-vnet"></a>Comment puis-je déployer mon application de fonction dans un réseau virtuel ?

Déploiement dans un environnement App Service est la seule façon de créer une application de fonction qui est entièrement à l’intérieur d’un réseau virtuel pour plus d’informations sur l’utilisation d’un environnement ASE, commencez par lire l’article suivant : [Création et utilisation d’un environnement ASE](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Pour les scénarios où vous devez uniquement accès à sens unique aux ressources de réseau virtuel, ou moins d’isolation réseau complète, consultez le [mise en réseau de vue d’ensemble de fonctions](functions-networking-options.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la mise en réseau et les fonctions : 

* [Suivez notre didacticiel de l’intégration démarrage réseau virtuel](./functions-create-vnet.md)
* [En savoir plus sur les options de mise en réseau dans les fonctions ici](./functions-networking-options.md)
* [En savoir plus sur l’intégration au réseau virtuel avec App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [En savoir plus sur les réseaux virtuels dans Azure](../virtual-network/virtual-networks-overview.md)
* [Activer plusieurs fonctionnalités mise en réseau et le contrôle avec les environnements App Service](../app-service/environment/intro.md)
