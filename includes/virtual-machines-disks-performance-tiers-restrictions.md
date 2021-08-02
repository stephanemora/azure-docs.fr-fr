---
title: Fichier include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/13/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d5915d8628254f24343571c1adc254c548558415
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110077700"
---
- Cette fonctionnalité est actuellement prise en charge uniquement pour les disques SSD Premium.
- Vous devez libérer votre machine virtuelle ou détacher votre disque d'une machine virtuelle en cours d'exécution avant de pouvoir modifier le niveau du disque.
- Les niveaux de performance P60, P70 et P80 peuvent uniquement être utilisés par des disques d’une taille supérieure à 4 096 Gio.
- Le niveau de performance d'un disque ne peut être rétrogradé qu'une fois toutes les 12 heures.

## <a name="change-performance-tier-without-downtime-preview"></a>Changer le niveau de performance d’un disque sans temps d’arrêt (préversion)

Normalement, vous devez désallouer votre machine virtuelle ou détacher votre disque pour changer votre niveau de performance. Cependant, si vous activez cette fonctionnalité en préversion, vous n’êtes pas obligé de désallouer votre machine virtuelle ou de détacher votre disque pour changer le niveau.

La préversion a les limitations suivantes :
- Disponible uniquement dans la région USA Centre-Ouest.
- Non disponible actuellement pour les disques partagés.
- Doit utiliser l’une des méthodes suivantes pour modifier le niveau sans temps d’arrêt :
    - Des modèles Azure Resource Manager avec l’API `2020-12-01` pour changer les niveaux de performance sans temps d’arrêt.
    - Accès au portail Azure par l’intermédiaire du lien suivant : [https://aka.ms/diskPerfTiersPreview](https://aka.ms/diskPerfTiersPreview).
    - Dernière version d’Azure CLI.
- Non disponible actuellement avec le module Azure PowerShell.