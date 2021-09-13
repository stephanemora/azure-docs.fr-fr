---
title: Migrer à partir d’AWS et d’autres plateformes vers des disques managés dans Azure
description: Créez des machines virtuelles dans Azure avec des disques durs virtuels chargés à partir d’autres clouds comme AWS ou d’autres plateformes de virtualisation et utilisez des disques managés Azure.
author: roygara
manager: twooley
ms.service: storage
ms.subervice: disks
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 77d2e23b202957018ee80186dd28b6b2360698fc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524770"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrer à partir d’Amazon Web Services (AWS) et d’autres plateformes vers des disques managés dans Azure

Vous pouvez charger des fichiers VHD sur Azure à partir d’AWS ou de solutions de virtualisation locales afin de créer des machines virtuelles qui utilisent des disques managés. Avec des disques managés Azure, il n’est plus nécessaire de gérer des comptes de stockage pour les machines virtuelles IaaS Azure. Vous devez simplement spécifier le type de disque et la taille de ce disque dont vous avez besoin, et Azure crée et gère le disque pour vous. 

Vous pouvez charger des disques durs virtuels généralisés et spécialisés. 
- **Disque dur virtuel généralisé** : supprime toutes les informations de votre compte personnel avec Sysprep. 
- **Disque dur virtuel spécialisé** : gère les comptes d’utilisateur, les applications et les autres données d’état à partir de votre machine virtuelle d’origine. 

> [!IMPORTANT]
> Avant de charger un disque dur virtuel dans Azure, vous devez apprendre à [préparer un disque dur virtuel Windows ou un disque VHDX à charger sur Azure](prepare-for-upload-vhd-image.md)
>
>


| Scénario                                                                                                                         | Documentation                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Vous avez une instance AWS EC2 existante que vous voulez migrer vers des machines virtuelles Azure à l’aide de disques managés.                              | [Migrer une machine virtuelle d’Amazon Web Services (AWS) vers Azure](aws-to-azure.md)                           |
| Vous avez une machine virtuelle sur une autre plateforme de virtualisation que vous aimeriez utiliser en tant qu’image pour créer plusieurs machines virtuelles Azure. | [Charger un disque dur virtuel généralisé et l’utiliser pour créer une machine virtuelle dans Azure](upload-generalized-managed.md) |
| Vous avez une machine virtuelle unique personnalisée que vous souhaitez recréer dans Azure.                                                      | [Charger un disque dur virtuel spécialisé dans Azure et créer une machine virtuelle](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Vue d’ensemble des disques managés

Les disques managés simplifient la gestion des machines virtuelles en éliminant la nécessité de gérer des comptes de stockage. Les disques managés bénéficient également d’une meilleure fiabilité des machines virtuelles dans un groupe à haute disponibilité. Cela permet de s’assurer que les disques des différentes machines virtuelles d’un groupe à haute disponibilité sont suffisamment isolés les uns des autres pour éviter un point de défaillance unique. Les disques des différentes machines virtuelles d’un groupe à haute disponibilité sont automatiquement placés dans des unités d’échelle (tampons) de stockage distinctes, ce qui limite l’impact des défaillances d’unités d’échelle de stockage uniques dues à des défaillances matérielles et logicielles.
Selon vos besoins, vous avez le choix entre quatre types d’options de stockage. Pour en savoir plus sur les types de disques disponibles, consultez l'article [Sélectionner un type de disque](../disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Planification de la migration vers des disques managés

Cette section vous aide à prendre la meilleure décision concernant les types de machines virtuelles et de disques.

Si vous envisagez de migrer à partir de disques non managés vers des disques managés, prenez en compte le fait que les utilisateurs avec le rôle [Contributeur de machine virtuelle](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ne sera pas en mesure de modifier la taille de la machine virtuelle (comme avant la conversion). Cela s’explique par le fait que les machines virtuelles avec disques managés obligent l’utilisateur à détenir une autorisation Microsoft.Compute/disks/write sur les disques du système d’exploitation.

### <a name="location"></a>Emplacement

Choisissez un emplacement où des disques managés Azure sont disponibles. Si vous effectuez une migration vers des disques SSD premium, assurez-vous également que le stockage premium est disponible dans la région où vous prévoyez la migration. Pour obtenir des informations à jour sur les emplacements disponibles, consultez [Services Azure par région](https://azure.microsoft.com/regions/#services) .

### <a name="vm-sizes"></a>Tailles de machine virtuelle

Si vous effectuez une migration vers des disques SSD premium, vous devez mettre à jour la taille de la machine virtuelle par rapport à la capacité de stockage premium disponible dans la région où se trouve la machine virtuelle. Passez en revue les tailles de machine virtuelle compatibles avec le stockage premium. Les spécifications des tailles des machines virtuelles Azure sont répertoriées dans la section [Tailles des machines virtuelles](../sizes.md).
Passez en revue les caractéristiques de performances des machines virtuelles fonctionnant avec le stockage premium et choisissez la taille de machine virtuelle la mieux adaptée à votre charge de travail. Assurez-vous que la bande passante disponible est suffisante sur votre machine virtuelle pour gérer le trafic du disque.

### <a name="disk-sizes"></a>Tailles du disque

Pour plus d’informations sur les types et tailles de disques disponibles, consultez [Quels sont les types de disque disponibles dans Azure ?](../disks-types.md).

### <a name="disk-caching-policy"></a>Stratégie de mise en cache du disque 

**Disques managés Premium**

Par défaut, la stratégie de mise en cache est *Lecture seule* pour tous les disques de données Premium et *Lecture-écriture* pour le disque du système d’exploitation Premium attaché à la machine virtuelle. Ce paramètre de configuration est recommandé pour optimiser les performances des E/S de votre application. Pour les disques de données en écriture seule ou avec d'importantes opérations d'écriture (par ex., les fichiers journaux de SQL Server), désactivez la mise en cache du disque pour de meilleures performances de l'application.

### <a name="pricing"></a>Tarifs

Consultez les [tarifs des disques managés](https://azure.microsoft.com/pricing/details/managed-disks/).


## <a name="next-steps"></a>Étapes suivantes

- Avant de charger un disque dur virtuel dans Azure, vous devez apprendre à [préparer un disque dur virtuel Windows ou un disque VHDX à charger sur Azure](prepare-for-upload-vhd-image.md)