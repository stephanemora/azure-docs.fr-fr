---
title: Créer des ressources Cognitive Services à l’aide de la bibliothèque de client Gestion d’Azure
titleSuffix: Azure Cognitive Services
description: Créez et gérez des ressources Azure Cognitive Services à l’aide de la bibliothèque de client Gestion d’Azure.
services: cognitive-services
keywords: services cognitifs, intelligence cognitive, solutions cognitives, services ia
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: e8628d051db7f5066a81171567f6f7e54fb0ab97
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91262448"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Démarrage rapide : Créer des ressources Cognitive Services à l’aide de la bibliothèque de client Gestion d’Azure

Utilisez ce démarrage rapide pour créer et gérer des ressources Azure Cognitive Services à l’aide de la bibliothèque de client Gestion d’Azure.

La solution Azure Cognitive Services correspond à des services cloud avec des API REST et des kits SDK de bibliothèque de client destinés à aider les développeurs à intégrer une intelligence cognitive dans des applications sans connaissances ni compétences directes en intelligence artificielle (IA) ou en science des données. Azure Cognitive Services permet aux développeurs d’ajouter facilement des fonctionnalités cognitives dans leurs applications à l’aide de solutions cognitives capables de voir, entendre, parler, comprendre et même commencer à raisonner.

Les services d’IA individuels sont représentés par des [ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) Azure que vous créez au sein de votre abonnement Azure. Après avoir créé la ressource, vous pouvez utiliser les clés et le point de terminaison générés pour authentifier vos applications.

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end
