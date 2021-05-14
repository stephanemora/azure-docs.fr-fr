---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e9d7b39ce267202503b90e84e934d31501d45478
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732188"
---
Toutes les dernières générations de tailles de machines virtuelles prennent en charge le chiffrement sur l’hôte :

|Type  |Non pris en charge  |Prise en charge  |
|---------|---------|---------|
|Usage général     | Dv3, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dav4, Dasv4, Ddv4, Ddsv4       |
|Optimisé pour le calcul     |         | Fsv2        |
|Mémoire optimisée     | Ev3        | Esv3, M, Mv2, Eav4, Easv4, Edv4, Edsv4        |
|Optimisé pour le stockage     |         | LS, Lsv2 (disques NVMe non chiffrés)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (préversion)        |
|Calcul haute performance     | H        | HB, HC, HBv2        |
|Générations précédentes     | F, A, D, L, G        | DS, GS, Fs, NVv2        |
