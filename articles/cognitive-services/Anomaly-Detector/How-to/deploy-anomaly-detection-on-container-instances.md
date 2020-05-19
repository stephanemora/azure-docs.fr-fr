---
title: Exécuter un conteneur Détecteur d’anomalies dans Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Déployez le conteneur détecteur d’anomalies sur Azure Container Instance et testez-le dans un navigateur web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 975efa9fdaff9175317794d8b6df7afbfc73cba8
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585678"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Déployer un conteneur détecteur d’anomalies sur Azure Container Instances

Découvrez comment déployer le conteneur [détecteur d’anomalies](../anomaly-detector-container-howto.md) de Cognitive Services sur Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Cette procédure illustre la création d’une ressource Détecteur d’anomalies. Puis nous aborderons l’extraction de l’image de conteneur associée. Enfin, nous mettrons en avant la possibilité d’exercer l’orchestration des deux à partir d’un navigateur. L’utilisation de conteneurs peut détourner l’attention des développeurs de la gestion de l’infrastructure, au lieu de se concentrer sur le développement d’applications.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Installer et exécuter des conteneurs](../anomaly-detector-container-configuration.md) pour savoir comment tirer (pull) l’image conteneur et exécuter le conteneur.
* Pour obtenir les paramètres de configuration, passez en revue [Configurer des conteneurs](../anomaly-detector-container-configuration.md).
* [En savoir plus sur le service API Détecteur d’anomalies](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
