---
title: 'Exemples de modèles Azure Resource Manager : Event Grid | Microsoft Docs'
description: Cet article fournit une liste d’exemples de modèles Azure Resource Manager pour Azure Event Grid sur GitHub.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 38d8db0bcc504760595fe51b63072f63e785577a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "76720620"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Modèles Azure Resource Manager pour Event Grid

Pour connaître la syntaxe JSON et les propriétés à utiliser dans un modèle, consultez [Types de ressources Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). Le tableau suivant inclut des liens vers des modèles Azure Resource Manager pour Event Grid.

| | |
|-|-|
|**Abonnements Event Grid**||
| [Rubrique personnalisée et abonnement avec un point de terminaison WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Déploie une rubrique personnalisée Event Grid. Crée un abonnement à cette rubrique personnalisée, qui utilise un point de terminaison WebHook. |
| [Abonnement à une rubrique personnalisée avec un point de terminaison EventHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Crée un abonnement Event Grid à une rubrique personnalisée. L’abonnement utilise un Hub d’événements pour le point de terminaison. |
| [Abonnement à un abonnement Azure ou à un groupe de ressources](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| S’abonne aux événements d’un groupe de ressources ou d’un abonnement Azure. Le groupe de ressources que vous spécifiez comme cible au cours du déploiement constitue la source d’événements. L’abonnement utilise un webhook pour le point de terminaison. |
| [Compte de stockage blob et abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Déploie un compte de stockage blob Azure et s’abonne aux événements pour ce compte de stockage. |
| | |
