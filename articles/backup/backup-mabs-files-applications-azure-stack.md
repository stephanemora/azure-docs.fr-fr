---
title: Sauvegarder des fichiers et applications Azure Stack | Microsoft Docs
description: Utilisez Sauvegarde Azure pour sauvegarder et restaurer des fichiers et applications Azure Stack dans votre environnement Azure Stack.
services: backup
documentationcenter: ''
author: adiganmsft
manager: shivamg
editor: ''
keyword: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/11/2018
ms.author: adigan,markgal
ms.openlocfilehash: 19067b40e8e87c160515d13bb490e7c1604788b6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196710"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Sauvegarder des fichiers et applications sur Azure Stack
Vous pouvez utiliser Sauvegarde Azure pour protéger (ou sauvegarder) des fichiers et applications sur Azure Stack. Pour sauvegarder des fichiers et applications, installez un serveur Sauvegarde Microsoft Azure en tant que machine virtuelle s’exécutant sur Azure Stack. Vous pouvez protéger toute application s’exécutant sur tout serveur Azure Stack dans le même réseau virtuel. Après avoir installé un serveur de sauvegarde Azure, ajoutez des disques Azure pour augmenter le stockage local disponible pour les données de sauvegarde à court terme. Un serveur de sauvegarde Azure utilise un stockage Azure pour la rétention à long terme.

> [!NOTE]
> Bien qu’un serveur de sauvegarde Azure et Microsoft System Center Data Protection Manager (DPM) soient similaires, DPM n’est pas pris en charge pour une utilisation avec Azure Stack.
>


## <a name="azure-backup-server-protection-matrix"></a>Matrice de protection du serveur de sauvegarde Azure
Le serveur de sauvegarde Azure protège les charges de travail de machine virtuelle Azure Stack suivantes.

| Source de données protégée | Protection et récupération |
| --------------------- | ----------------------- |
| Canal semi-annuel Windows Server - Centre de données/Entreprise/Standard | Volumes, fichiers, dossiers |
| Windows Server 2016 - Centre de données/Entreprise/Standard | Volumes, fichiers, dossiers |
| Windows Server 2012 R2 - Centre de données/Entreprise/Standard | Volumes, fichiers, dossiers |
| Windows Server 2012 - Centre de données/Entreprise/Standard | Volumes, fichiers, dossiers |
| Windows Server 2008 R2 - Centre de données/Entreprise/Standard | Volumes, fichiers, dossiers |
| SQL Server 2016 | Base de données |
| SQL Server 2014 | Base de données |
| SQL Server 2012 SP1 | Base de données |
| SharePoint 2013 | Batterie de serveurs, base de données, serveur frontal, serveur web |
| SharePoint 2010 | Batterie de serveurs, base de données, serveur frontal, serveur web |


## <a name="install-azure-backup-server"></a>Installer un serveur de sauvegarde Azure
Pour installer un serveur de sauvegarde Azure sur une machine virtuelle Azure Stack, voir l’article [Préparation de la sauvegarde des charges de travail à l’aide d’un serveur de sauvegarde Azure](backup-azure-microsoft-azure-backup.md). Avant d’installer et configurer un serveur de sauvegarde Azure, tenez compte de ce qui suit :

### <a name="determining-size-of-virtual-machine"></a>Détermination de la taille de machine virtuelle
Pour exécuter un serveur de sauvegarde Azure sur une machine virtuelle Azure Stack, optez pour la taille A2 ou une taille supérieure. Pour obtenir de l’assistance pour le choix de la taille d’une machine virtuelle, téléchargez le [calculateur de taille de machine virtuelle Azure Stack](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Réseaux virtuels sur machines virtuelles Azure Stack
Toutes les machines virtuelles utilisées dans une charge de travail Azure Stack doivent appartenir aux mêmes réseau virtuel et abonnement Azure.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Stockage de données de sauvegarde sur disque local et dans Azure
Un serveur de sauvegarde Azure stocke les données de sauvegarde sur des disques Azure attachés à la machine virtuelle à des fins de récupération opérationnelle. Une fois les disques et l’espace de stockage attachés à la machine virtuelle, le serveur de sauvegarde Azure gère le stockage pour vous. Le volume de stockage de données de sauvegarde varie selon le nombre et la taille des disques attachés à chaque [machine virtuelle Azure Stack](../azure-stack/user/azure-stack-storage-overview.md). Chaque taille de machine virtuelle Azure Stack est associée à un nombre maximal de disques pouvant être attachés à la machine virtuelle. Par exemple, la taille A2 correspond à quatre disques. La taille A3 correspond à huit disques. La taille A4 correspond à seize disques. Ici encore, la taille et le nombre de disques déterminent la capacité totale du pool de stockage de sauvegarde.

> [!IMPORTANT]
> Vous ne devez **pas** conserver des données de récupération (sauvegarde) opérationnelle sur des disques attachés à un serveur de sauvegarde Azure pendant plus de cinq jours.
>

Stocker des données de sauvegarde dans Azure réduit l’infrastructure de sauvegarde sur Azure Stack. Les données de plus de cinq jours doivent être stockées dans Azure.

Pour stocker des données de sauvegarde dans Azure, créez ou utilisez un coffre Recovery Services. Lors de la préparation de la sauvegarde de la charge de travail d’un serveur de sauvegarde Azure, vous [configurez le coffre Recovery Services](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Une fois la configuration effectuée, chaque fois qu’une opération de sauvegarde a lieu, un point de récupération est créé dans le coffre. Chaque coffre Recovery Services conserve jusqu’à 9 999 points de récupération. En fonction du nombre de points de récupération créés et de la durée de leur conservation, vous pouvez conserver des données de sauvegarde pendant de nombreuses années. Par exemple, vous créez des points de récupération mensuellement et les conserver pendant cinq ans.
 
### <a name="using-sql-server"></a>Utilisation de SQL Server
Si vous souhaitez utiliser un serveur SQL Server distant pour la base de données d’un serveur de sauvegarde Azure, sélectionnez uniquement une machine virtuelle Azure Stack exécutant SQL Server.

### <a name="azure-backup-server-vm-performance"></a>Performances de machine virtuelle de serveur de sauvegarde Azure
En cas de partage avec d’autres machines virtuelles, la taille du compte de stockage et les limites d’E/S par seconde peuvent avoir une incidence sur les performances de la machine virtuelle d’un serveur de sauvegarde Azure. C’est pourquoi vous devez utiliser un compte de stockage distinct pour la machine virtuelle du serveur de sauvegarde Azure. L’agent Sauvegarde Azure s’exécutant sur le serveur de sauvegarde Azure a besoin d’un stockage temporaire pour :
    - son propre usage (emplacement de cache) ;
    - les données restaurées à partir du cloud (zone de transit locale).
  
### <a name="configuring-azure-backup-temporary-disk-storage"></a>Configuration d’un stockage sur disque temporaire pour Sauvegarde Azure
Chaque machine virtuelle Azure Stack est fournie avec un stockage sur disque temporaire qui est disponible pour l’utilisateur en tant que volume `D:\`. Il est possible de configurer la zone de transit locale dont Sauvegarde Azure a besoin de façon à ce qu’elle réside sur `D:\`, et de placer le cache sur `C:\`. De cette façon, aucun stockage ne doit être effectué à l’écart des disques de données attachés à la machine virtuelle d’un serveur de sauvegarde Azure.

### <a name="scaling-deployment"></a>Déploiement avec mise à l’échelle
Si vous souhaitez mettre à l’échelle votre déploiement, vous disposez des options suivantes :
  - Monter en puissance : augmenter la taille de la machine virtuelle du serveur de sauvegarde Azure en passant de la série A à la série D, et augmenter le stockage local [conformément aux instructions relatives à la machine virtuelle Azure Stack](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Décharger des données : envoyer des données plus anciennes au serveur de sauvegarde Azure en ne conservant que les données les plus récentes sur le stockage attaché au serveur de sauvegarde Azure.
  - Monter en charge : ajouter des serveurs de sauvegarde Azure pour protéger les charges de travail.

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur l’utilisation d’un serveur de sauvegarde Azure pour protéger d’autres charges de travail, voir les articles suivants :
- [Sauvegarder un batterie de serveurs SharePoint](backup-azure-backup-sharepoint-mabs.md)
- [Sauvegarder SQL Server](backup-azure-sql-mabs.md)
