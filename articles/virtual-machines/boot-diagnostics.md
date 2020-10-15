---
title: Diagnostics de démarrage Azure
description: Vue d’ensemble des diagnostics de démarrage Azure et des diagnostics de démarrage managés
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 52a2b5e27cd5857416343e559237d08ea9a591be
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972388"
---
# <a name="azure-boot-diagnostics"></a>Diagnostics de démarrage Azure

Les diagnostics de démarrage sont une fonctionnalité de débogage pour les machines virtuelles Azure qui permet de diagnostiquer les échecs de démarrage de machine virtuelle. Les diagnostics de démarrage permettent à un utilisateur d’observer l’état de la machine virtuelle lors de son démarrage en collectant des informations de journal série et des captures d’écran.

## <a name="boot-diagnostics-storage-account"></a>Compte de stockage des diagnostics de démarrage
Lorsque vous créez une machine virtuelle dans le portail Azure, les diagnostics de démarrage sont activés par défaut. Avec les diagnostics de démarrage, il est recommandé d’utiliser un compte de stockage managé, qui se révèle plus performant dans le temps pour créer une machine virtuelle Azure. Cela est dû au fait qu’un compte de stockage Azure managé est utilisé, ce qui évite de perdre du temps à créer un compte de stockage d’utilisateur pour stocker les données de diagnostics de démarrage.

Avec les diagnostics de démarrage, il est également possible d’utiliser un compte de stockage managé par l’utilisateur. Un utilisateur peut soit créer un autre compte de stockage, soit en utiliser un existant.

> [!IMPORTANT]
> Les clients Azure qui utilisent un compte de stockage managé pour les diagnostics de démarrage ne seront pas facturés pour les coûts de stockage associés jusqu’en octobre 2020.
>
> Les objets bloc de données de diagnostics de démarrage (journaux et images d’instantané) sont stockés dans un compte de stockage managé. Seuls les Gio utilisés par les objets blob seront facturés aux clients et pas la taille de disque configurée. Les compteurs d’instantané seront utilisés pour la facturation du compte de stockage managé. Les comptes managés étant créés sur des LRS ou des ZRS standard, les clients se verront facturés 0,05 USD/Go par mois pour la taille de leurs objets blob de données de diagnostic uniquement. Pour plus d’informations sur cette tarification, consultez [Tarification des disques managés](https://azure.microsoft.com/pricing/details/managed-disks/). Les clients verront ces frais liés à l’URI de ressource de leur machine virtuelle. 

## <a name="boot-diagnostics-view"></a>Affichage des diagnostics de démarrage
Située dans le panneau de la machine virtuelle, l’option Diagnostics de démarrage se trouve sous la section *Support et résolution des problèmes* du portail Azure. Sélectionnez Diagnostics de démarrage pour afficher une capture d’écran et des informations de journal série. Le journal série contient la messagerie du noyau, et la capture d’écran est un instantané de l’état actuel de vos machines virtuelles. Le fait que la machine virtuelle fonctionne sous Windows ou Linux détermine à quoi ressemblera la capture d’écran attendue. Pour Windows, les utilisateurs voient un arrière-plan du bureau et, pour Linux, une invite de connexion.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Capture d’écran des diagnostics de démarrage Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Capture d’écran des diagnostics de démarrage Linux":::


## <a name="limitations"></a>Limites
- Les diagnostics de démarrage sont uniquement disponibles pour les machines virtuelles Azure Resource Manager. 
- Les diagnostics de démarrage ne prennent pas en charge les comptes de stockage Premium. Si un compte de stockage Premium est utilisé pour les diagnostics de démarrage, les utilisateurs recevront une erreur `StorageAccountTypeNotSupported` lors du démarrage de la machine virtuelle. 
- Les comptes de stockage managés sont pris en charge dans l’API Resource Manager version « 2020-06-01 » et ultérieures.
- La console série Azure est actuellement incompatible avec un compte de stockage managé pour les diagnostics de démarrage. Découvrez-en plus sur la [console série Azure](./troubleshooting/serial-console-overview.md).
- Les diagnostics de démarrage utilisant un compte de stockage de gestion ne peuvent être appliqués actuellement que via le portail Azure. 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [console série Azure](./troubleshooting/serial-console-overview.md) et sur l’utilisation des diagnostics de démarrage pour [résoudre les problèmes liés aux machines virtuelles dans Azure](./troubleshooting/boot-diagnostics.md).