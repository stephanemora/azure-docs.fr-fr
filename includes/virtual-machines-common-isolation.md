---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 0f528117b19a3f26b964dee7ae453fc184c8dddb
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123073"
---
Le Calcul Azure propose des tailles de machines virtuelles qui sont isolées pour un type de matériel spécifique et dédiées à un seul et même client.  Ces tailles de machines virtuelles conviennent mieux aux charges de travail qui nécessitent un niveau élevé d’isolation par rapport aux autres clients pour les charges de travail qui impliquent des éléments tels que les exigences réglementaires et de conformité.  Les clients peuvent également choisir de subdiviser les ressources de ces machines virtuelles isolées à l’aide de la [prise en charge d’Azure pour les machines virtuelles imbriquées](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

L’utilisation d’une taille isolée garantit que votre machine virtuelle sera la seule en cours d’exécution sur cette instance de serveur spécifique.  Les offres actuelles de machines virtuelles isolées sont les suivantes :
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Pour plus d’informations sur chaque taille isolée disponible, [cliquez ici](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).