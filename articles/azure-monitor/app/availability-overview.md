---
title: Tests de disponibilité d’Application Insights
description: Configurez des tests web récurrents pour surveiller la disponibilité et la réactivité de votre application ou de votre site web.
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: 2e87d9eca75cfd507549213999882dfe6ff3ffa6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532058"
---
# <a name="application-insights-availability-tests"></a>Tests de disponibilité d’Application Insights

Après avoir déployé votre application Web ou votre site Web, vous pouvez configurer des tests réguliers pour contrôler sa disponibilité et sa réactivité. [Application Insights](./app-insights-overview.md) envoie des requêtes web à votre application à intervalles réguliers à partir de différents points du monde, Il peut vous alerter si votre application ne répond pas ou si elle répond trop lentement.

Vous pouvez configurer des tests de disponibilité pour n’importe quel point de terminaison HTTP ou HTTPS accessible à partir du réseau Internet public. Vous n’avez pas besoin d’apporter de modifications au site web que vous testez. En fait, vous n’êtes même pas tenu d’en être le propriétaire. Vous pouvez tester la disponibilité d’une API REST dont dépend votre service.

## <a name="types-of-tests"></a>Types de tests

Il existe deux types de tests de disponibilité :

* [Test ping d’URL (classique)](monitor-web-app-availability.md) : vous pouvez créer ce test simple via le portail pour vérifier si un point de terminaison répond et mesurer les performances associées à cette réponse. Vous pouvez également définir des critères de réussite personnalisés à l’aide de fonctionnalités plus avancées, comme analyser des requêtes dépendantes et autoriser de nouvelles tentatives.
* [Test standard (préversion)](availability-standard-tests.md) : ce test de requête unique est semblable au test ping d’URL. Il comprend la validité des certificats SSL, la vérification de la durée de vie proactive, le verbe de requête HTTP (par exemple `GET`, `HEAD` ou `POST`), les en-têtes personnalisés et les données personnalisées associées à votre requête HTTP.
* [Test Web à plusieurs étapes (classique)](availability-multistep.md) : vous pouvez relire cet enregistrement d’une séquence de requêtes Web pour tester des scénarios plus complexes. Les tests Web à plusieurs étapes sont créés dans Visual Studio Enterprise et chargés sur le portail pour y être exécutés.
* [Test personnalisé de suivi de la disponibilité](availability-azure-functions.md) : si vous décidez de créer une application personnalisée pour exécuter des tests de disponibilité, vous pouvez utiliser la méthode [TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)pour envoyer les résultats à Application Insights.

> [!IMPORTANT]
> Le [test ping d’URL](monitor-web-app-availability.md) et le [test Web à plusieurs étapes](availability-multistep.md) s’appuient tous deux sur l’infrastructure DNS de l’Internet public pour résoudre les noms de domaine des points de terminaison testés. Si vous utilisez un DNS privé, vous devez vous assurer que les serveurs de noms de domaine publics peuvent supprimer chaque nom de domaine de votre test. Lorsque cela n’est pas possible, vous pouvez utiliser les [tests personnalisés de suivi de la disponibilité](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) à la place.

Vous pouvez créer jusqu’à 100 tests de disponibilité par ressource Application Insights.

## <a name="troubleshooting"></a>Résolution des problèmes

Consultez l’[article sur la résolution des problèmes](troubleshoot-availability.md) dédié.

## <a name="next-steps"></a>Étapes suivantes

* [Alertes de disponibilité](availability-alerts.md)
* [Tests web à plusieurs étapes](availability-multistep.md)
* [Tests des URL](monitor-web-app-availability.md)
* [Créer et exécuter des tests de disponibilité personnalisés avec Azure Functions](availability-azure-functions.md)
* [Modèle Azure Resource Manager pour les tests web](/azure/templates/microsoft.insights/webtests?tabs=json)