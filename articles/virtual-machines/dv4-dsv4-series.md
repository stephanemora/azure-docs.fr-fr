---
title: Séries Dv4 et Dsv4 - Machines virtuelles Microsoft Azure
description: Spécifications pour les machines virtuelles des séries Dv4 et Dsv4.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 32520a458a797ee9945603dd86c9efe359e4d1f6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291172"
---
# <a name="dv4-and-dsv4-series"></a>Séries Dv4 et Dsv4

Les séries Dv4 et Dsv4 s’exécutent sur les processeurs Intel&reg; ​​Xeon&reg; Platinum 8272CL (Cascade Lake) dans une configuration de type « Hyper-Threading » qui apporte davantage de valeur ajoutée à la plupart des charges de travail universelles. Elles sont dotées d’une vitesse d’horloge de Turbo cœur de 3,4 GHz. 

> [!NOTE]
> Pour accéder aux questions fréquentes (FAQ), consultez [Tailles de machine virtuelle Azure sans disque temporaire local](azure-vms-no-temp-disk.md).
## <a name="dv4-series"></a>Série Dv4

Les tailles de la série Dv4 s’exécutent sur Intel &reg;​​Xeon&reg; Platinum 8272CL (Cascade Lake). Elles offrent une combinaison de processeur virtuel, de mémoire et d’options de stockage distant adaptée à la plupart des charges de travail de production. Les machines virtuelles de série Dv4 sont dotées de la technologie Hyper-Threading d’[Intel&reg;](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).

Le stockage sur disque de données à distance est facturé séparément des machines virtuelles. Pour utiliser les disques de stockage Premium, utilisez des machines virtuelles au format Dsv4. Les tarifs et les compteurs de facturation de ces tailles sont identiques à ceux de la série Dv4.


> [!IMPORTANT]
> Ces nouvelles tailles sont actuellement en préversion publique uniquement. Vous pouvez vous inscrire pour ces séries Dv4 et Dsv4 [ici](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 


ACU : 195-210

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Migration dynamique : Prise en charge

Mises à jour avec préservation de la mémoire : Prise en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Nombre max de cartes réseau|Bande passante réseau attendue (Mbits/s) |
|---|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | Stockage distant uniquement | 4 | 2|1 000 |
| Standard_D4_v4 | 4 | 16  | Stockage distant uniquement | 8 | 2|2000 |
| Standard_D8_v4 | 8 | 32 | Stockage distant uniquement | 16 | 4|4000 |
| Standard_D16_v4 | 16 | 64 | Stockage distant uniquement | 32 | 8|8000 |
| Standard_D32_v4 | 32 | 128 | Stockage distant uniquement | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | Stockage distant uniquement | 32 | 8|24 000 |
| Standard_D64_v4 | 64 | 256 | Stockage distant uniquement | 32 | 8|30000 |

## <a name="dsv4-series"></a>Série Dsv4

Les tailles de la série Dsv4 s’exécutent sur Intel &reg;​​Xeon&reg; Platinum 8272CL (Cascade Lake). Elles offrent une combinaison de processeur virtuel, de mémoire et d’options de stockage distant adaptée à la plupart des charges de travail de production. Les machines virtuelles de série Dsv4 sont dotées de la technologie Hyper-Threading d’[Intel&reg;](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). Le stockage sur disque de données à distance est facturé séparément des machines virtuelles.

> [!IMPORTANT]
> Ces nouvelles tailles sont actuellement en préversion publique uniquement. Vous pouvez vous inscrire pour ces séries Dv4 et Dsv4 [ici](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 

ACU : 195-210

Premium Storage :  Prise en charge

Mise en cache du Stockage Premium :  Prise en charge

Migration dynamique : Prise en charge

Mises à jour avec préservation de la mémoire : Prise en charge

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit maximal mis en cache : IOPS/Mbits/s (taille du cache en Gio) | Débit du disque non mis en cache max. : IOPS/Mbits/s | Nombre max de cartes réseau|Bande passante réseau attendue (Mbits/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | Stockage distant uniquement | 4 | 19000/120 (50) | 3000/48 | 2|1 000 |
| Standard_D4s_v4 | 4 | 16 | Stockage distant uniquement | 8 | 38500/242 (100) | 6 400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | Stockage distant uniquement | 16 | 77000/485 (200) | 12 800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | Stockage distant uniquement | 32 | 154000/968 (400) | 25 600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | Stockage distant uniquement | 32 | 308000/1936 (800) | 51 200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | Stockage distant uniquement | 32 | 462000/2904 (1200) | 76 800/1152 | 8|24 000 |
| Standard_D64s_v4 | 64 | 256 | Stockage distant uniquement | 32 | 615000/3872 (1600) | 80 000/1 200 | 8|30000 |
