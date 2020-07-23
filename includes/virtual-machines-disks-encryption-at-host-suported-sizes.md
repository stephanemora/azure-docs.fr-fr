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
ms.openlocfilehash: e5a811620de8336abd3e0df6d72db761ce18b2b6
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86230977"
---
Toutes les dernières générations de tailles de machines virtuelles prennent en charge le chiffrement sur l’hôte :

|Type  |Non pris en charge  |Prise en charge  |
|---------|---------|---------|
|Usage général     | Dv3, Dav4, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dasv4        |
|Optimisé pour le calcul     |         | Fsv2        |
|Mémoire optimisée     | Ev3, Eav4        | DSv2, Esv3, M, Mv2, Easv4        |
|Optimisé pour le stockage     |         | LS, Lsv2 (disques NVMe non chiffrés)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (préversion)        |
|Calcul haute performance     | H        | HB, HC, HBv2        |
|Générations précédentes     | F, A, D, L, G        | DS, GS, Fs, NVv2        |

La mise à niveau de la taille de la machine virtuelle entraîne une validation pour vérifier si la nouvelle taille de machine virtuelle prend en charge la fonctionnalité EncryptionAtHost.
