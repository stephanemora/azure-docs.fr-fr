---
title: Diagnostics de démarrage Azure
description: Vue d’ensemble des diagnostics de démarrage Azure et des diagnostics de démarrage managés
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: e2ba5d909a3aa43921f52295d2f7216aac76bc32
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067084"
---
# <a name="azure-boot-diagnostics"></a>Diagnostics de démarrage Azure

Les diagnostics de démarrage sont une fonctionnalité de débogage pour les machines virtuelles Azure qui permet de diagnostiquer les échecs de démarrage de machine virtuelle. Les diagnostics de démarrage permettent à un utilisateur d’observer l’état de la machine virtuelle lors de son démarrage en collectant des informations de journal série et des captures d’écran.

## <a name="boot-diagnostics-view"></a>Affichage des diagnostics de démarrage
Située dans le panneau de la machine virtuelle, l’option Diagnostics de démarrage se trouve sous la section *Support et résolution des problèmes* du portail Azure. Sélectionnez Diagnostics de démarrage pour afficher une capture d’écran et des informations de journal série. Le journal série contient la messagerie du noyau, et la capture d’écran est un instantané de l’état actuel de vos machines virtuelles. Le fait que la machine virtuelle fonctionne sous Windows ou Linux détermine à quoi ressemblera la capture d’écran attendue. Pour Windows, les utilisateurs voient un arrière-plan du bureau et, pour Linux, une invite de connexion.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Capture d’écran des diagnostics de démarrage Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Capture d’écran des diagnostics de démarrage Windows":::


## <a name="limitations"></a>Limites
- Les diagnostics de démarrage sont uniquement disponibles pour les machines virtuelles Azure Resource Manager. 
- Les diagnostics de démarrage ne prennent pas en charge les comptes de stockage Premium. Si un compte de stockage Premium est utilisé pour les diagnostics de démarrage, les utilisateurs recevront une erreur `StorageAccountTypeNotSupported` lors du démarrage de la machine virtuelle. 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [console série Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) et sur l’utilisation des diagnostics de démarrage pour [résoudre les problèmes liés aux machines virtuelles dans Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
