---
title: Fichier include
description: Fichier include
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 01/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 5658b68081fae8dab528030f7fc1cd6fe4935730
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178356"
---
- Le bursting à la demande ne peut pas être activé sur un disque SSD Premium contenant moins de 512 Gio. Les disques SSD Premium de moins de 512 Gio utilisent toujours le bursting basé sur les crédits.
- Le bursting à la demande est uniquement pris en charge sur les disques SSD Premium. Si vous basculez un disque SSD Premium avec le bursting à la demande activé sur un autre type de disque, le bursting de disque est désactivé.
- Le bursting à la demande ne se désactive pas automatiquement lorsque le niveau de performance change. Si vous voulez changer votre niveau de performance sans conserver le bursting de disque, vous devez le désactiver.
- Le bursting à la demande peut être activé seulement si le disque est détaché d’une machine virtuelle ou si la machine virtuelle est arrêtée. Le bursting à la demande peut être désactivé 12 heures après son activation.