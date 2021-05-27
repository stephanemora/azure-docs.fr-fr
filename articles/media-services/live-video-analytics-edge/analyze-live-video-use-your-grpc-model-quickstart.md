---
title: Analyser une vidéo en direct à l’aide de votre propre modèle gRPC avec Live Video Analytics – Azure
description: Dans ce guide de démarrage rapide, vous allez appliquer la vision par ordinateur pour analyser le flux vidéo en direct provenant d’une caméra IP (simulée) avec Live Video Analytics.
ms.topic: quickstart
ms.date: 08/14/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 1079e374d34512e3054c4185302059ffd178c804
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371729"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-grpc-model-with-live-video-analytics"></a>Guide de démarrage rapide : Analyser une vidéo en direct à l’aide de votre propre modèle gRPC avec Live Video Analytics

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

Ce guide de démarrage rapide vous montre comment utiliser Live Video Analytics sur IoT Edge pour analyser un flux vidéo en direct à partir d’une caméra IP (simulée). Vous allez voir comment appliquer un modèle de vision par ordinateur pour détecter des objets. Un sous-ensemble d’images du flux vidéo en direct est envoyé à un service d’inférence. Les résultats sont envoyés à IoT Edge Hub.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Prérequis

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Réviser l’exemple de vidéo

::: zone pivot="programming-language-csharp"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Vue d’ensemble

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>Créer et déployer le graphe multimédia

::: zone pivot="programming-language-csharp"
[!INCLUDE [create and deploy the media graph](includes/analyze-live-video-your-grpc-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create and deploy the media graph](includes/analyze-live-video-your-grpc-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Interpréter les résultats

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez d’essayer d’autres guides de démarrage rapide, conservez les ressources que vous avez créées. Dans le cas contraire, accédez au portail Azure, puis à vos groupes de ressources. Sélectionnez ensuite le groupe de ressources où vous avez exécuté ce guide de démarrage rapide, puis supprimez toutes les ressources.

## <a name="next-steps"></a>Étapes suivantes

* Essayez d’exécuter différentes topologies de graphe multimédia à l’aide du protocole gRPC.
* **Créer et exécuter des exemples d’extensions Live Video Analytics (LVA)**
<br/>Essayez nos exemples de notebooks Jupyter pour créer et exécuter des modèles YOLO basés sur [ONNX](http://onnx.ai/) en tant qu’extension Live Video Analytics (LVA).
    * [Exemple de modèle YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/readme.md)
    * [Exemple de modèle YOLOv4](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov4/yolov4-grpc-icpu-onnx/readme.md)

