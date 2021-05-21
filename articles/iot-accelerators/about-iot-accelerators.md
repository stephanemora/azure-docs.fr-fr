---
title: Présentation des accélérateurs de solution IoT - Azure | Microsoft Docs
description: Découvrez les accélérateurs de solution Azure IoT. Les accélérateurs de solution IoT sont des solutions de bout en bout complètes et prêtes au déploiement.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 8a89462ca132ddbf20ecc7f70502747d4a664b58
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109486014"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Que sont les accélérateurs de solution IoT ?

Une solution IoT cloud utilise généralement un code personnalisé et des services cloud pour gérer la connectivité des appareils, le traitement et l’analytique de données et la présentation.

Les accélérateurs de solution IoT sont complets et prêts à déployer des solutions IoT qui implémentent des scénarios IoT courants. Lorsque vous déployez un accélérateur de solution, le déploiement inclut tous les services informatiques nécessaires ainsi que tout code d’application requis.

Les accélérateurs de solution représentent le point de départ de vos solutions IoT. Le code source de tous les accélérateurs de solution est open source et disponible sur GitHub. Nous vous invitons à télécharger et personnaliser les accélérateurs de solution afin qu’ils répondent à vos exigences.

Vous pouvez aussi utiliser les accélérateurs de solution en tant qu’outils d’apprentissage, avant de créer une solution IoT personnalisée à partir de zéro. Les accélérateurs de solution implémentent des pratiques éprouvées pour les solutions IoT informatiques, pratiques que vous pouvez suivre.

Le code d’application de chaque accélérateur de solution inclut une application web qui vous permet de gérer l’accélérateur de solution.

> [!NOTE]
> Les solutions de supervision à distance et de maintenance prédictive ont été supprimées du site [Accélérateurs de solutions Azure IoT](https://www.azureiotsolutions.com/Accelerators). Pour en savoir plus, consultez [Présentation des accélérateurs de solutions Azure IoT (version précédente)](/previous-versions/azure/iot-accelerators/about-iot-accelerators).

## <a name="supported-iot-scenarios"></a>Scénarios IoT pris en charge

À l’heure actuelle, vous pouvez déployer un seul accélérateur de solution :

### <a name="connected-factory"></a>Fabrique connectée

Utilisez l’[accélérateur de solution Fabrique connectée](iot-accelerators-connected-factory-features.md) pour collecter des données de télémétrie de ressources industrielles avec une interface d’[architecture unifiée OPC](https://opcfoundation.org/about/opc-technologies/opc-ua/) et pour les contrôler. On peut citer par exemple des stations de test et d’assemblage d’une chaîne de production.

Vous pouvez utiliser le tableau de bord d’usine connectée pour surveiller et gérer vos appareils industriels :

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="Capture d’écran montrant le tableau de bord de la solution d’usine connectée." lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

## <a name="design-principles"></a>Principes de conception

Tous les accélérateurs de solution suivent les mêmes principes de conception et les mêmes objectifs. Ils sont conçus pour être :

* **Évolutifs**, vous permettant de vous connecter et de gérer des millions d’appareils connectés.
* **Extensibles**, vous permettant de les personnaliser afin qu’ils répondent à vos exigences.
* **Compréhensibles**, vous permettant de comprendre comment ils fonctionnent et comment ils sont implémentés.
* **Modulaires**, vous permettant de permuter des services avec d’autres.
* **Sécurisés**, combinant la sécurité Azure avec des fonctionnalités de sécurité de connectivité et d’appareils intégrées.

## <a name="architectures-and-languages"></a>Architectures et langues

Les accélérateurs de solution originaux ont été conçus avec .NET et une architecture MVC (model-view-controller). Microsoft met à jour les accélérateurs de solution pour une nouvelle architecture basée sur les microservices. Le tableau suivant présente l’état actuel des accélérateurs de solution avec des liens vers les dépôts GitHub :

| Accélérateur de solution   | Architecture  | Languages     |
| ---------------------- | ------------- | ------------- |
| Fabrique connectée      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |

Pour en savoir plus sur l’architecture de microservices, consultez [Présentation de l’architecture de référence Azure IoT](/azure/architecture/reference-architectures/iot/).

## <a name="deployment-options"></a>Options de déploiement

Vous pouvez déployer les accélérateurs de solution à partir du site [Accélérateurs de solution Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#) ou à l’aide de la ligne de commande.

Le coût associé à l’exécution d’un accélérateur de solution est le [coût combiné de l’exécution des services Azure sous-jacents](https://azure.microsoft.com/pricing). Vous pouvez voir les détails des services Azure utilisés lors du choix des options de déploiement.

## <a name="next-steps"></a>Étapes suivantes

Pour essayer un des accélérateurs de solutions IoT, consultez le démarrage rapide [Essayer une solution d’usine connectée](quickstart-connected-factory-deploy.md).
