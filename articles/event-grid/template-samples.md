---
title: 'Exemples de modèles Azure Resource Manager : Event Grid | Microsoft Docs'
description: Exemples de modèles Azure Resource Manager pour Event Grid
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/04/2019
ms.author: tomfitz
ms.openlocfilehash: 788d23c7bddd90c1e12a118742c651eb9759529c
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062959"
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
