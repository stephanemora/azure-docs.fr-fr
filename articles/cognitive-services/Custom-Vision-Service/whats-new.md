---
title: Nouveautés de Custom Vision
titleSuffix: Azure Cognitive Services
description: Cet article présente les nouveautés de Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 10/23/2020
ms.author: pafarley
ms.openlocfilehash: a87e76d4a726f7a01b96d602e7f41d60409dae56
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521490"
---
# <a name="whats-new-in-custom-vision"></a>Nouveautés de Custom Vision

Découvrir les nouveautés du service. Ces éléments peuvent être des notes de publication, des vidéos, des billets de blog et d’autres types d’informations. Marquez cette page pour rester aux faits des nouveautés du service.

## <a name="july-2020"></a>Juillet 2020

### <a name="azure-role-based-access-control"></a>Contrôle d'accès en fonction du rôle Azure

* Custom Vision prend en charge le contrôle d’accès en fonction du rôle Azure (Azure RBAC), un système d’autorisation pour la gestion de l’accès individuel aux ressources Azure. Pour savoir comment gérer l’accès à vos projets Custom Vision, consultez [Contrôle d’accès en fonction du rôle Azure](./role-based-access-control.md).

### <a name="subset-training"></a>Entrainement d’un sous-ensemble

* Lors de l’entrainement d’un projet de détection d’objets, vous pouvez, si vous le souhaitez, vous concentrer sur un sous-ensemble de vos étiquettes appliquées. Vous pouvez procéder de la sorte si vous n’avez pas encore suffisamment appliqué certaines étiquettes, contrairement à d’autres. Pour en savoir plus, suivez le [guide de démarrage rapide de la bibliothèque de client](./quickstarts/object-detection.md) pour C# ou Python.

### <a name="azure-storage-notifications"></a>Notifications de stockage Azure

* Vous pouvez intégrer votre projet Custom Vision à une file d’attente de stockage d’objets blob Azure pour recevoir des notifications push de l’activité d’entraînement/d’exportation de projet et des copies de sauvegarde des modèles publiés. Cette fonctionnalité évite de devoir interroger continuellement les résultats du service lors de l’exécution d’opérations de longue durée. En effet, vous pouvez intégrer les notifications de la file d’attente de stockage à votre workflow. Pour en savoir plus, consultez le guide de l’[intégration du stockage](./storage-integration.md).

### <a name="copy-and-move-projects"></a>Copier et déplacer des projets

* Vous pouvez maintenant copier des projets d’un compte Custom Vision dans d’autres. Vous souhaiterez peut-être déplacer un projet d’un environnement de développement vers un environnement de production ou sauvegarder un projet vers un compte dans une autre région Azure pour une sécurité accrue des données. Pour en savoir plus, consultez le guide [Copier et déplacer des projets](./copy-move-projects.md).

## <a name="september-2019"></a>Septembre 2019

### <a name="suggested-tags"></a>Etiquettes suggérées

* L’outil Smart Labeler sur le [site web Custom Vision](https://www.customvision.ai/) génère des étiquettes suggérées pour vos images d’entraînement. Vous pourrez ainsi étiqueter un grand nombre d’images plus rapidement lorsque vous formez un modèle Custom Vision. Pour obtenir des instructions sur l’utilisation de cette fonctionnalité, consultez [Etiquettes suggérées](./suggested-tags.md).

## <a name="cognitive-service-updates"></a>Mises à jour du service cognitif

[Annonces de mise à jour Azure pour Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)