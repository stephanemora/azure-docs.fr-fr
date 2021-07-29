---
title: Résoudre les problèmes d’IoT Edge sur Azure Stack Edge Pro avec GPU | Microsoft Docs
description: Explique comment résoudre les erreurs avec IoT Edge survenant sur votre appareil Azure Stack Edge Pro avec GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/19/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: 405a9c62a551a011eb6d7b00025c6ae0a563e858
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987812"
---
# <a name="troubleshoot-iot-edge-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Résoudre les problèmes avec IoT Edge survenant sur votre appareil Azure Stack Edge Pro avec GPU 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment résoudre les erreurs liées au calcul sur un appareil Azure Stack Edge Pro avec GPU en examinant les réponses du runtime pour l’agent IoT Edge et les erreurs pour le service IoT Edge installé sur votre appareil.

## <a name="review-iot-edge-runtime-responses"></a>Examiner les réponses du runtime IoT Edge

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]

## <a name="troubleshoot-iot-edge-service-errors"></a>Résoudre les erreurs de service d’IoT Edge

L’erreur suivante est liée au service IoT Edge sur votre appareil Azure Stack Edge Pro GPU.

[!INCLUDE [Troubleshoot IoT Edge errors](../../includes/azure-stack-edge-iot-troubleshoot-compute-error-detail.md)]


## <a name="next-steps"></a>Étapes suivantes

- [Déboguer les problèmes Kubernetes liés à IoT Edge](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge).
- [Résoudre les problèmes d’appareil](azure-stack-edge-gpu-troubleshoot.md).