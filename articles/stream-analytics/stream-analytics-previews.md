---
title: Fonctionnalités d’évaluation Azure Stream Analytics
description: Cet article répertorie les fonctionnalités d’Azure Stream Analytics qui sont actuellement en préversion
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 55745c022038fa85f5b114f2bc347ed7292665eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589648"
---
# <a name="azure-stream-analytics-preview-features"></a>Fonctionnalités d’évaluation Azure Stream Analytics

Cet article résume toutes les fonctionnalités actuellement en préversion pour Azure Stream Analytics. L’utilisation de fonctionnalités en préversion dans un environnement de production n’est pas recommandée.

## <a name="authenticate-to-sql-database-output-with-managed-identities-preview"></a>S’authentifier auprès de la sortie SQL Database avec des identités managées (préversion)

Azure Stream Analytics prend en charge l’[authentification des identités managées](../active-directory/managed-identities-azure-resources/overview.md) pour les récepteurs de sortie Azure SQL Database. Les identités managées n’ont pas les limitations des méthodes d’authentification basée sur l’utilisateur, comme la réauthentification obligatoire après un changement de mot de passe. 

## <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Scoring haute performance en temps réel avec des modèles ML personnalisés gérés par Azure Machine Learning

Azure Stream Analytics prend en charge le scoring en temps réel haute performance en tirant parti de modèles Machine Learning préentraînés personnalisés, gérés par Azure Machine Learning et hébergés dans Azure Kubernetes Service (AKS) ou Azure Container Instances (ACI), suivant un flux de travail pour lequel il n’est pas nécessaire d’écrire du code. [Inscrivez-vous](https://aka.ms/asapreview1) pour accéder à la préversion.

## <a name="c-custom-de-serializers"></a>Désérialiseurs personnalisés C#
Les développeurs peuvent exploiter toute la puissance d’Azure Stream Analytics pour traiter des données Protobuf, XML ou dans un format personnalisé. Il est possible d’implémenter des [désérialiseurs personnalisés](custom-deserializer-examples.md) en C#, qui permettent de désérialiser des événements reçus par Azure Stream Analytics.

## <a name="extensibility-with-c-custom-code"></a>Extensibilité avec du code personnalisé C#

Les développeurs qui créent des modules Stream Analytics dans le cloud ou sur IoT Edge peuvent écrire ou réutiliser des fonctions C# personnalisées et les appeler directement dans la requête grâce à des [fonctions définies par l’utilisateur](stream-analytics-edge-csharp-udf-methods.md).

## <a name="debug-query-steps-in-visual-studio"></a>Déboguer les étapes de requêtes dans Visual Studio

Vous pouvez afficher un aperçu de l’ensemble de lignes intermédiaire sur un diagramme de données lorsque vous effectuez des tests locaux dans les outils Azure Stream Analytics pour Visual Studio. 


## <a name="live-data-testing-in-visual-studio"></a>Tests de données actives dans Visual Studio

Visual Studio Tools pour Azure Stream Analytics améliore la fonctionnalité de test en local qui vous permet de tester vos requêtes sur les flux d’événements en direct, à partir de sources cloud, telles que Event Hub ou IoT hub. Découvrez comment [Tester les données actives localement à l’aide d’Azure Stream Analytics Tools pour Visual Studio](stream-analytics-live-data-local-testing.md).

## <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code pour Azure Stream Analytics

Les travaux Azure Stream Analytics peuvent être créés dans Visual Studio Code. Consultez notre [tutoriel de prise en main de Visual Studio Code](./quick-create-visual-studio-code.md).

## <a name="local-testing-with-live-data-in-visual-studio-code"></a>Tests locaux avec des données actives dans Visual Studio Code

Vous pouvez tester vos requêtes sur des données actives sur votre ordinateur local avant de soumettre le travail sur Azure. Chaque itération de test prend moins de deux à trois secondes en moyenne, ce qui se traduit par un processus de développement très efficace.

