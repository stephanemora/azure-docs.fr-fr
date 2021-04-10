---
title: Fichier include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7151c110fd50f7485aa0b130832aace4f3143ad9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750989"
---
- Cette fonctionnalité est actuellement prise en charge uniquement pour les disques SSD Premium.
- Vous devez libérer votre machine virtuelle ou détacher votre disque d'une machine virtuelle en cours d'exécution avant de pouvoir modifier le niveau du disque.
- Les niveaux de performance P60, P70 et P80 peuvent uniquement être utilisés par des disques d’une taille supérieure à 4 096 Gio.
- Le niveau de performance d'un disque ne peut être rétrogradé qu'une fois toutes les 12 heures.

## <a name="change-performance-tier-without-downtime-preview"></a>Changer le niveau de performance d’un disque sans temps d’arrêt (préversion)

Normalement, vous devez désallouer votre machine virtuelle ou détacher votre disque pour changer votre niveau de performance. Cependant, si vous activez cette fonctionnalité en préversion, vous n’êtes pas obligé de désallouer votre machine virtuelle ou de détacher votre disque pour changer le niveau. Vous pouvez vous inscrire à la préversion [ici](https://aka.ms/liveperftiersignup).

La préversion a les limitations suivantes :
- Disponible seulement dans la région EastUS2EUAP.
- Non disponible actuellement pour les disques partagés
- Doit utiliser des modèles Azure Resource Manager avec l’API `2020-12-01` pour changer les niveaux de performance sans temps d’arrêt.