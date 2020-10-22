---
title: Limites
description: Limitations du code pour les fonctionnalités du SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b500a3777ee24d6615022dae2571d021bd0d675a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92201169"
---
# <a name="limitations"></a>Limites

Un certain nombre de fonctionnalités ont une taille, un nombre ou d’autres limitations.

## <a name="azure-frontend"></a>Azure Frontend

* Nombre total d’instances AzureFrontend par processus : 16.
* Nombre total d’instances AzureSession par AzureFrontend : 16.

## <a name="objects"></a>Objets

* Nombre total d’objets autorisés d’un même type (Entity, CutPlaneComponent, etc.) : 16 777 215.
* Nombre total de plans de coupe actifs autorisés : 8.

## <a name="geometry"></a>Géométrie

* **Animation :** Les animations se limitent au processus d'animation de transformations individuelles d'objets du jeu. Les animations squelettiques avec processus de maillage (skinning) ou les animations de vertex ne sont pas prises en charge. Les pistes d'animation du fichier d'élément multimédia source ne sont pas conservées. Au lieu de cela, les animations de transformation d'objets doivent être pilotées par le code client.
* **Nuanceurs personnalisés :** La création de nuanceurs personnalisés n'est pas prise en charge. Seuls les [Matériaux de couleur](../overview/features/color-materials.md) ou les [Matériaux PBR](../overview/features/pbr-materials.md) intégrés peuvent être utilisés.
* **Nombre maximal de documents distincts** dans un élément multimédia : 65 535. Pour plus d'informations sur la réduction automatique du nombre de matériaux, reportez-vous au chapitre [Déduplication de matériaux](../how-tos/conversion/configure-model-conversion.md#material-de-duplication).
* **Dimension maximale d'une texture unique** : 16 384 x 16 384. Les textures sources de grande taille seront réduites par le processus de conversion.

### <a name="overall-number-of-polygons"></a>Nombre total de polygones

Le nombre autorisé de polygones pour tous les modèles chargés dépend de la taille de la machine virtuelle transmise à l’[API REST de gestion des sessions](../how-tos/session-rest-api.md#create-a-session) :

| Taille du serveur | Nombre maximal de polygones |
|:--------|:------------------|
|standard| 20 millions |
|Premium| aucune limite |

Pour plus d'informations sur cette limitation, consultez le chapitre consacré à la [taille de serveur](../reference/vm-sizes.md).

## <a name="platform-limitations"></a>Limitations de plateforme

**Bureau Windows 10**

* Win32/x64 est la seule plateforme Win32 prise en charge. Win32/x86 n’est pas pris en charge.

**HoloLens 2**

* La fonctionnalité de [rendu à partir de la caméra PV](/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) n’est pas prise en charge.