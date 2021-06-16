---
title: Vue d’ensemble de la disponibilité d’Application Insights
description: Configurez des tests web récurrents pour surveiller la disponibilité et la réactivité de votre application ou de votre site web.
ms.topic: conceptual
ms.date: 05/04/2021
ms.openlocfilehash: 94fe55a5bce0e915a092fd32ad5acf6936d59e61
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111967018"
---
# <a name="availability-tests-overview"></a>Vue d’ensemble des tests de disponibilité

Après avoir déployé votre application web ou votre site web, vous pouvez configurer des tests réguliers pour superviser sa disponibilité et sa réactivité. [Application Insights](./app-insights-overview.md) envoie des requêtes web à votre application à intervalles réguliers à partir de différents points du monde, et vous alerte si votre application ne répond pas ou si elle répond trop lentement.

Vous pouvez configurer des tests de disponibilité pour n’importe quel point de terminaison HTTP ou HTTPS accessible à partir du réseau Internet public. Vous n’avez pas besoin d’apporter de modifications au site web que vous testez. En fait, vous n’êtes même pas tenu d’en être le propriétaire. Vous pouvez tester la disponibilité d’une API REST dont dépend votre service.

## <a name="types-of-availability-tests"></a>Types de tests de disponibilité

Il existe trois types de tests de disponibilité :

* [Test Ping d’URL](monitor-web-app-availability.md) : Cette catégorie comporte deux tests simples que vous pouvez créer via le portail.
* [Tests web multiétapes](availability-multistep.md) : enregistrement d’une séquence de requêtes web, qui peuvent être répétées pour tester des scénarios plus complexes. Les tests web multiétapes sont créés dans Visual Studio Enterprise et chargés sur le portail pour y être exécutés.
* [Tests personnalisés de suivi de la disponibilité](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) : Si vous décidez de créer une application personnalisée pour exécuter des tests de disponibilité, la méthode `TrackAvailability()` peut être utilisée pour envoyer les résultats à Application Insights.

> [!IMPORTANT]
> Le [test ping d’URL](monitor-web-app-availability.md) et le [test web multiétapes](availability-multistep.md) s’appuient tous deux sur l’infrastructure DNS de l’Internet public pour résoudre les noms de domaine des points de terminaison testés. Cela signifie que si vous utilisez DNS privé, vous devez vous assurer que chaque nom de domaine de votre test peut également être résolu par les serveurs de noms de domaine publics ou, si ce n’est pas possible, vous pouvez utiliser des [tests personnalisés de suivi de la disponibilité](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) à la place.

**Vous pouvez créer jusqu’à 100 tests de disponibilité par ressource Application Insights.**

## <a name="troubleshooting"></a>Dépannage

Consultez l’[article dédié au dépannage](troubleshoot-availability.md).

## <a name="next-step"></a>Étape suivante

* [Availability alerts](availability-alerts.md) (Alertes de disponibilité)
* [Tests web à plusieurs étapes](availability-multistep.md)
* [Tests des URL](monitor-web-app-availability.md)
* [Créer et exécuter des tests de disponibilité personnalisés avec Azure Functions](availability-azure-functions.md)
* [Modèle Azure Resource Manager pour les tests web](/azure/templates/microsoft.insights/webtests?tabs=json)