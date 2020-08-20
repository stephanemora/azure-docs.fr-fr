---
title: Limites d’Azure Stack Edge | Microsoft Docs
description: Découvrez les limites et les tailles recommandées lorsque vous déployez et exploitez Azure Stack Edge, notamment les limites de service, les limites d’appareil et les limites de stockage.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 8bbcf3f61121813b91cb951809992d10977a640c
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922708"
---
# <a name="azure-stack-edge-limits"></a>Limites Azure Stack Edge

Tenez compte de ces limites quand vous déployez et utilisez votre solution Microsoft Azure Stack Edge. 

## <a name="azure-stack-edge-service-limits"></a>Limites du service Azure Stack Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-device-limits"></a>Limites de l’appareil Azure Stack

Le tableau suivant décrit les limites de l’appareil Azure Stack Edge. 

| Description | Valeur |
|---|---|
|Non. de fichiers par appareil |100 millions |
|Non. de partages par appareil |24 |
|Non. de partages par conteneur |1 |
|Taille de fichier maximale écrite sur un partage| 5 To |

## <a name="azure-storage-limits"></a>Limites du stockage Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Mises en garde relatives au chargement de données

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limites de taille d’objet et de compte de stockage Azure

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limites de taille des objets Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Préparer le déploiement d’Azure Stack Edge](azure-stack-edge-deploy-prep.md)
