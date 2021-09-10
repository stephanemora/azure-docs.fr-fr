---
title: Limites d’Azure Stack Edge Pro GPU ou FPGA | Microsoft Docs
description: Découvrez les limites et les tailles recommandées lorsque vous déployez et exploitez Azure Stack Edge Pro GPU ou FPGA, notamment les limites de service, les limites d’appareil et les limites de stockage.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: 64be85db0e2d11d2aee5a61742f427087a77cef2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532030"
---
# <a name="azure-stack-edge-limits"></a>Limites Azure Stack Edge

Tenez compte de ces limites quand vous déployez et utilisez votre solution Microsoft Azure Stack Edge Pro ou FPGA. 

## <a name="azure-stack-edge-service-limits"></a>Limites du service Azure Stack Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Limites de l’appareil Azure Stack Edge

Le tableau suivant décrit les limites de l’appareil Azure Stack Edge.

| Description | Valeur |
|---|---|
|Non. de fichiers par appareil |100 millions |
|Non. de partages par conteneur |1 |
|Nombre maximal de points de terminaison de partage et REST par appareil (appareils GPU uniquement)| 24 |
|Nombre maximal de comptes de stockage hiérarchisés par appareil (appareils GPU uniquement)| 24|
|Taille de fichier maximale écrite sur un partage| 5 To |
|Nombre maximal de groupes de ressources par appareil| 800 |

## <a name="azure-storage-limits"></a>Limites du stockage Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Mises en garde relatives au chargement de données

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limites de taille d’objet et de compte de stockage Azure

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limites de taille des objets Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Préparer le déploiement d’Azure Stack Edge Pro GPU](azure-stack-edge-gpu-deploy-prep.md)
- [Préparer le déploiement d’Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-prep.md)
