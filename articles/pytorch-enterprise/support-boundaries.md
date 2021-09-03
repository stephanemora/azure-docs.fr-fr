---
title: Limites de support pour PyTorch Enterprise sur Azure
description: Cet article définit les limites de support relatives à PyTorch Enterprise.
author: alonbochman
ms.author: alonbochman
ms.service: pytorch-enterprise
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: ea2f70af6611f6bed5a36f72324874e0461182f3
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598612"
---
# <a name="support-boundaries-for-pytorch-enterprise"></a>Limites de support pour PyTorch Enterprise

Ce bref document décrit les modules et composants pris en charge sous Pytorch Enterprise.


|Domaine|Versions prises en charge|Notes|
|----|----|----|
|Système d''exploitation|Windows 10, Debian 9, Debian 10, Ubuntu 16.04.7 LTS, Ubuntu 18.04.5 LTS|Nous prenons en charge les versions LTS des distributions Debian et Ubuntu, et uniquement l'architecture x86_64.|
|Python|3.6+||
|PyTorch|1.8.1+||
|CUDA Toolkit|10.2, 11.1||
|ONNX Runtime|1.7+||
|torchtext, torchvision, torch-tb-profiler, torchaudio| - |Pour les bibliothèques qui n'ont pas de version 1.0, nous prenons en charge les versions spécifiques qui sont compatibles avec la version PyTorch prise en charge correspondante. Par exemple, consultez les tableaux suivants : [TorchVision](https://github.com/pytorch/vision#installation), [TorchText](https://github.com/pytorch/text#installation), [TorchAudio](https://github.com/pytorch/audio/#dependencies)|
|torchserve|0.4.0+||
