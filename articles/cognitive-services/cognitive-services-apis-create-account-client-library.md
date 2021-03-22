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
ms.date: 03/15/2021
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: e042ac263d3a30a9790ba6a3a3d404e5e9cb9aad
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472156"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Démarrage rapide : Créer des ressources Cognitive Services à l’aide de la bibliothèque de client Gestion d’Azure

Utilisez ce démarrage rapide pour créer et gérer des ressources Azure Cognitive Services à l’aide de la bibliothèque de client Gestion d’Azure.

Azure Cognitive Services est une famille de services cloud dotés d’API REST et de bibliothèques de client qui aident les développeurs à intégrer une intelligence cognitive à leurs applications. Les développeurs n’ont besoin d’aucune compétence ou connaissance directe en matière d’intelligence artificielle ou de science des données pour voir leurs projets couronnés de succès. Azure Cognitive Services permet aux développeurs d’ajouter facilement des fonctionnalités cognitives dans leurs applications à l’aide de solutions cognitives capables de voir, entendre, parler, comprendre et même commencer à raisonner.

Les services d’IA individuels sont représentés par des [ressources](../azure-resource-manager/management/manage-resources-portal.md) Azure que vous créez au sein de votre abonnement Azure. Après avoir créé la ressource, vous pouvez utiliser les clés et le point de terminaison générés pour authentifier vos applications.

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
