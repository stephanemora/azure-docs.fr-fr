---
title: Créer un espace de noms Service Bus à l’aide du portail Azure | Microsoft Docs
description: Créez un espace de noms Service Bus à l’aide du portail Azure.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: a56a13db5df1234a8011caf10a82ac561b6877cf
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47390186"
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Créer un espace de noms Service Bus à l’aide du Portail Azure

Un espace de noms est un conteneur d’étendue pour tous les composants de messagerie. Plusieurs files d’attente et rubriques peuvent résider dans un seul espace de noms, et les espaces de noms servent souvent de conteneurs d’applications. Il existe deux façons de créer un espace de noms Service Bus :

1. Portail Azure (cet article)
2. [Modèles Microsoft Azure Resource Manager][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Créer un espace de noms dans le Portail Azure

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Félicitations ! Vous venez de créer un espace de noms de messagerie Service Bus.

## <a name="next-steps"></a>Étapes suivantes

Consultez les [exemples Services Bus GitHub][github-samples], qui montrent certaines des fonctionnalités plus avancées de la messagerie Service Bus.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples
