---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 287ae3b8122dd2a1e43c31055ac0ea5b04dddb07
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190663"
---
## <a name="benefits-of-managed-disks"></a>Avantages des disques managés

Observons quelques-uns des avantages procurés par l’utilisation de disques managés.

### <a name="highly-durable-and-available"></a>Disponibilité et durabilité élevées

Les disques managés sont conçus pour offrir une disponibilité de 99,999 %. Ils y parviennent en vous fournissant trois réplicas de vos données, ce qui permet d’avoir une durabilité élevée. Si un ou même deux de vos réplicas rencontrent des problèmes, les autres réplicas peuvent assurer la persistance de vos données et offrir une grande tolérance face aux pannes. Cette architecture a permis à Azure de fournir de façon cohérente une durabilité de classe Entreprise pour les disques infrastructure as a service (IaaS), avec un taux de défaillance annuel inégalé dans le secteur de zéro %.

### <a name="simple-and-scalable-vm-deployment"></a>Déploiement de machines virtuelles simple et évolutif

Avec des disques managés, vous pouvez créer jusqu’à 50 000 **disques** de machines virtuelles d’un type dans un abonnement par région. Ainsi, vous êtes en mesure de créer des milliers de **machines virtuelles** dans un seul abonnement. Par ailleurs, cette fonctionnalité optimise l’évolutivité des [groupes de machines virtuelles identiques](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) en vous donnant les moyens de créer jusqu’à 1 000 machines virtuelles dans une instance de groupe de machines virtuelles identiques à l’aide d’une image de place de marché.

### <a name="integration-with-availability-sets"></a>Intégration avec des groupes à haute disponibilité

Les disques managés sont intégrés avec des groupes à haute disponibilité pour garantir que les disques des [machines virtuelles d’un groupe à haute disponibilité](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) sont suffisamment isolés les uns des autres pour éviter un point de défaillance unique. Les disques sont automatiquement placés dans différentes unités d’échelle de stockage (horodatages). Si un horodatage est mis en échec en raison d’une défaillance matérielle ou logicielle, seules les instances de machine virtuelle possédant des disques sur ces horodatages sont mises en échec. Par exemple, supposons qu’une de vos applications est exécutée sur 5 machines virtuelles, qui sont hébergées dans un groupe à haute disponibilité. Les disques de ces machines virtuelles ne seront pas stockés dans le même horodatage. Par conséquent, si un horodatage est mis en échec, les autres instances de l’application continuent de s’exécuter.

### <a name="azure-backup-support"></a>Support Sauvegarde Azure

Pour vous protéger contre les sinistres régionaux, [Sauvegarde Azure](../articles/backup/backup-introduction-to-azure-backup.md) peut être utilisé pour créer un travail de sauvegarde avec des sauvegardes périodiques et des stratégies de rétention de sauvegarde. Ceci vous permet d’effectuer des restaurations des machines virtuelles à volonté. Actuellement, le service Sauvegarde Azure prend en charge les tailles de disque jusqu’à quatre tébioctets (TiB). Pour plus d’informations, consultez [Utilisation de Sauvegarde Azure pour les machines virtuelles avec disques managés](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

### <a name="granular-access-control"></a>Contrôle d’accès granulaire

Utilisez le [contrôle d’accès en fonction du rôle Azure](../articles/role-based-access-control/overview.md) afin d’affecter à un ou plusieurs utilisateurs des autorisations spécifiques d’accès à un disque managé. Les disques managés exposent différentes opérations, notamment la lecture, l’écriture (création/mise à jour), la suppression et la récupération d’un [URI de signature d’accès partagé](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) pour le disque. N’accordez l’accès qu’aux opérations dont une personne a besoin pour exécuter son travail. Par exemple, si vous voulez empêcher un utilisateur de copier un disque managé sur un compte de stockage, vous pouvez décider de lui interdire l’accès à l’action d’exportation sur ce disque managé. De la même manière, si vous voulez empêcher un utilisateur d’employer un URI de signature d’accès partagé pour copier un disque managé, vous pouvez décider de ne pas lui octroyer l’autorisation d’accès à ce disque managé.

## <a name="disk-roles"></a>Rôles de disque

### <a name="data-disks"></a>Disques de données

Un disque de données est un disque managé attaché à une machine virtuelle pour stocker des données d’application ou d’autres données que vous devez conserver. Les disques de données sont enregistrés en tant que disques SCSI et sont nommés avec la lettre de votre choix. Chaque disque de données a une capacité maximale de 4 095 gibioctets (Gio). La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher et le type de stockage que vous pouvez utiliser pour héberger les disques.

### <a name="os-disks"></a>Disques du système d’exploitation

Chaque machine virtuelle dispose d’un disque de système d’exploitation attaché. Ce disque de système d’exploitation est doté d’un système d’exploitation préinstallé qui a été sélectionnée lors de la création de la machine virtuelle.

Ce disque a une capacité maximale de 2 048 Gio.

### <a name="temporary-disk"></a>Disque temporaire

Chaque machine virtuelle contient un disque temporaire qui n’est pas un disque managé. Il fournit un stockage à court terme pour les applications et les processus, et est destiné à stocker seulement des données comme les fichiers de pagination ou d’échange. Données sur le disque temporaire peuvent être perdues pendant un [événement de maintenance](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) événement ou lorsque vous [redéployer une machine virtuelle](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json). Lors d’un redémarrage standard réussi de la machine virtuelle, les données présentes sur le disque temporaire sont conservées. 

## <a name="managed-disk-snapshots"></a>Captures instantanées de disque managé

La capture instantanée d’un disque managé est une copie en lecture seule d’un disque managé qui est stockée comme disque managé standard par défaut. Avec des captures instantanées, vous pouvez sauvegarder vos disques managés à tout moment dans le temps. Ces captures instantanées existent indépendamment du disque source et peuvent être utilisées pour créer des disques managés par la suite. Elles sont facturées en fonction de la taille utilisée. Par exemple, si vous créez une capture instantanée d’un disque managé avec une capacité approvisionnée de 64 Gio et une taille des données utilisées réelle de 10 Gio, cet instantané est facturé uniquement pour la taille des données utilisées de 10 Gio.  

Pour en savoir plus sur la création de captures instantanées avec des disques managés, consultez les ressources suivantes :

* [Créer une copie d’un disque dur virtuel stocké en tant que disque managé à l’aide de la fonction Instantanés dans Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Créer une copie d’un disque dur virtuel stocké en tant que disque managé à l’aide de la fonction Instantanés dans Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Images

Les disques managés prennent également en charge la création d’une image personnalisée gérée. Vous pouvez créer une image à partir de votre disque dur virtuel (VHD) personnalisé dans un compte de stockage ou directement à partir d’une machine virtuelle généralisée (préparée à l’aide de Sysprep). Ce processus capture une seule image. Cette image contient tous les disques managés associés à une machine virtuelle, notamment les disques de données et les disques du système d’exploitation. Cette image personnalisée managée permet de créer des centaines de machines virtuelles à l’aide de votre image personnalisée, sans qu’il soit nécessaire de copier ou de gérer un compte de stockage.

Pour plus d’informations sur la création des images, consultez les articles suivants :

* [Procédure de capture d’une image managée d’une machine virtuelle généralisée dans Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Guide pratique de généralisation et de capture d’une machine virtuelle Linux avec Azure CLI](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Images et captures instantanées

Il est important de bien saisir la différence entre des images et des captures instantanées. Les disques managés vous permettent de saisir une image d’une machine virtuelle généralisée qui a été libérée. Cette image comprend l’ensemble des disques attachés à la machine virtuelle. Vous pouvez l’utiliser pour créer une machine virtuelle qui intègre l’ensemble des disques.

Une capture instantanée est une copie d’un disque à un instant t. Elle s’applique uniquement à un disque. Si vous possédez une machine virtuelle qui présente un disque (le disque du système d’exploitation), vous pouvez en saisir une capture instantanée ou une image à partir desquelles vous créez une machine virtuelle.

Une capture instantanée n’a connaissance d’aucun autre disque que celui qu’elle contient. Il est donc difficile de l’utiliser dans des scénarios qui nécessitent la coordination de plusieurs disques, tels que l’agrégation par bandes. Pour cela, les captures instantanées devraient être capables de se coordonner entre elles, ce qui n’est actuellement pas pris en charge.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les offres de types de disques individuels Azure et sur le type de disque bien adapté à vos besoins en lisant notre article sur les types de disques.
