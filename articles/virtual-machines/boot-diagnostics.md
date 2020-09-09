---
title: Diagnostics de démarrage Azure
description: Vue d’ensemble des diagnostics de démarrage Azure et des diagnostics de démarrage managés
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: d425953b278a98af35a172d8777ab758db52709e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89257753"
---
# <a name="azure-boot-diagnostics"></a>Diagnostics de démarrage Azure

Les diagnostics de démarrage sont une fonctionnalité de débogage pour les machines virtuelles Azure qui permet de diagnostiquer les échecs de démarrage de machine virtuelle. Les diagnostics de démarrage permettent à un utilisateur d’observer l’état de la machine virtuelle lors de son démarrage en collectant des informations de journal série et des captures d’écran.

## <a name="boot-diagnostics-storage-account"></a>Compte de stockage des diagnostics de démarrage
Lorsque vous créez une machine virtuelle dans le portail Azure, les diagnostics de démarrage sont activés par défaut. Avec les diagnostics de démarrage, il est recommandé d’utiliser un compte de stockage managé, qui se révèle plus performant dans le temps pour créer une machine virtuelle Azure. Cela est dû au fait qu’un compte de stockage Azure managé est utilisé, ce qui évite de perdre du temps à créer un compte de stockage d’utilisateur pour stocker les données de diagnostics de démarrage.

Avec les diagnostics de démarrage, il est également possible d’utiliser un compte de stockage managé par l’utilisateur. Un utilisateur peut soit créer un autre compte de stockage, soit en utiliser un existant.

> [!IMPORTANT]
> Les clients Azure qui utilisent un compte de stockage managé pour les diagnostics de démarrage ne seront pas facturés pour les coûts de stockage associés jusqu’en octobre 2020.

## <a name="boot-diagnostics-view"></a>Affichage des diagnostics de démarrage
Située dans le panneau de la machine virtuelle, l’option Diagnostics de démarrage se trouve sous la section *Support et résolution des problèmes* du portail Azure. Sélectionnez Diagnostics de démarrage pour afficher une capture d’écran et des informations de journal série. Le journal série contient la messagerie du noyau, et la capture d’écran est un instantané de l’état actuel de vos machines virtuelles. Le fait que la machine virtuelle fonctionne sous Windows ou Linux détermine à quoi ressemblera la capture d’écran attendue. Pour Windows, les utilisateurs voient un arrière-plan du bureau et, pour Linux, une invite de connexion.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Capture d’écran des diagnostics de démarrage Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Capture d’écran des diagnostics de démarrage Windows":::


## <a name="limitations"></a>Limites
- Les diagnostics de démarrage sont uniquement disponibles pour les machines virtuelles Azure Resource Manager. 
- Les diagnostics de démarrage ne prennent pas en charge les comptes de stockage Premium. Si un compte de stockage Premium est utilisé pour les diagnostics de démarrage, les utilisateurs recevront une erreur `StorageAccountTypeNotSupported` lors du démarrage de la machine virtuelle. 
- La console série Azure est actuellement incompatible avec un compte de stockage managé pour les diagnostics de démarrage. Découvrez-en plus sur la [console série Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [console série Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) et sur l’utilisation des diagnostics de démarrage pour [résoudre les problèmes liés aux machines virtuelles dans Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
