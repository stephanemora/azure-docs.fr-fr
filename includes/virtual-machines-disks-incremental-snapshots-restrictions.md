---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 39a013f5f4b587137366147ade77f0be1b353c4c
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204462"
---
- Les instantanés incrémentiels ne peuvent actuellement pas être transférés d'un abonnement à un autre.
- À un moment donné, vous ne pouvez actuellement générer que des URI SAS comprenant jusqu'à cinq instantanés d'une famille d'instantanés particulière.
- Vous ne pouvez pas créer d'instantané incrémentiel pour un disque particulier en dehors de l'abonnement associé à ce disque.
- Vous pouvez créer jusqu'à sept instantanés incrémentiels par disque toutes les cinq minutes.
- Au total, 200 instantanés incrémentiels peuvent être créés pour un même disque.
- Vous ne pouvez pas récupérer les changements entre les instantanés pris avant et après le changement de taille du disque parent au-delà de 4 To. Vous devez retélécharger la copie complète de l’instantané créé après le redimensionnement. Par la suite, vous pouvez récupérer les changements entre les instantanés créés après le redimensionnement au-delà de 4 To. 
