---
title: Limites
description: Limitations du code pour les fonctionnalités du SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417689"
---
# <a name="limitations"></a>Limites

Un certain nombre de fonctionnalités ont une taille, un nombre ou d’autres limitations.

## <a name="azure-frontend"></a>Azure Frontend

* Nombre total d’instances AzureFrontend par processus : 16.
* Nombre total d’instances AzureSession par AzureFrontend : 16.

## <a name="objects"></a>Objets

* Nombre total d’objets autorisés d’un même type (Entity, CutPlaneComponent, etc.) : 16 777 215.
* Nombre total de plans de coupe actifs autorisés : 8.

## <a name="materials"></a>Matériaux

* Nombre total des matériaux autorisés dans une ressource : 65 535.

## <a name="overall-number-of-polygons"></a>Nombre total de polygones

Le nombre autorisé de polygones pour tous les modèles chargés dépend de la taille de la machine virtuelle transmise à l’[API REST de gestion des sessions](../how-tos/session-rest-api.md#create-a-session) :

| Taille de la machine virtuelle | Nombre maximal de polygones |
|:--------|:------------------|
|standard| 20 millions |
|Premium| aucune limite |


## <a name="platform-limitations"></a>Limitations de plateforme

**Bureau Windows 10**

* Un déploiement « PC autonome » à partir d’Unity n’est pas pris en charge. Utilisez UWP à la place.
* UWP/x86 est la seule plateforme UWP prise en charge. UWP/x64 n’est pas pris en charge.

**HoloLens 2**

* La fonctionnalité de [rendu à partir de la caméra PV](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) n’est pas prise en charge.
