---
title: Sauvegarde et restauration du système d’exploitation pour les références (SKU) de type II SAP HANA (grandes instances) sur Azure | Microsoft Docs
description: Effectuer la sauvegarde et la restauration du système d’exploitation pour les références (SKU) de type II SAP HANA (grandes instances) sur Azure
services: virtual-machines-linux
documentationcenter: ''
author: jaawasth
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: jaawasth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 245c371a002ebfdad4dd0765ba3d283c81c86f03
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110631609"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Sauvegarde et restauration du système d’exploitation pour les références (SKU) de type II avec révision 3

Ce document décrit les étapes permettant de sauvegarder et de restaurer le système de fichiers du système d’exploitation pour les **références (SKU) de type II** des grandes instances HANA de révision 3. 

>[!Important]
> **Cet article ne s’applique pas aux déploiements de références SKU de type II dans les tampons de grande instance HANA de révision 4.** Les numéros d’unité logique de démarrage des unités de grande instance HANA de type II déployées dans les tampons de grande instance HANA de révision 4 peuvent être sauvegardés avec des captures instantanées de stockage, comme c’est le cas avec les références SKU de type I déjà dans les tampons de révision 3.


>[!NOTE]
> * Les scripts de sauvegarde du système d’exploitation utilisent l’utilitaire xfsdump.  
> * Ce document prend en charge la sauvegarde Racine système de fichiers complète et les sauvegardes **non incrémentielles**.
> * Assurez-vous que lors de la création d’une sauvegarde, aucun fichier n’est écrit sur le même système.  Dans le cas contraire, les fichiers écrits pendant la sauvegarde peuvent ne pas être inclus dans la sauvegarde.
> * La sauvegarde ReaR est déconseillée pour les références SKU de type II des grandes instances HANA de la révision 3.
> * Nous avons testé cette procédure en interne dans le cas de plusieurs scénarios d’altération du système d’exploitation. Toutefois, étant donné que vous, en tant que client, êtes seul responsable du système d’exploitation, nous vous recommandons de la tester minutieusement avant de vous appuyer sur cette documentation pour votre scénario.
> * Nous avons testé ce processus sur SLES OS.
> * Les mises à niveau des versions principales, telles que SLES 12.x vers SLES 15x, ne sont pas prises en charge.
> * Pour effectuer une restauration du système d’exploitation avec ce processus, vous aurez besoin de l’assistance Microsoft, car la récupération nécessite l’accès à la console. Veuillez créer un ticket de support auprès de Microsoft pour faciliter la récupération.


## <a name="how-to-take-a-manual-backup"></a>Comment effectuer une sauvegarde manuelle ?

Pour effectuer une sauvegarde manuelle :

1. Installez l’outil de sauvegarde.
   ```
   zypper in xfsdump
   ```

2. Créez une sauvegarde complète. 
   ```
   xfsdump -l 0 -f /data1/xfs_dump /
   ```

   La capture d’écran suivante montre l’exemple d’une sauvegarde manuelle :
   
    [![capture de l’image mémoire](media/HowToHLI/OSBackupTypeIISKUs/dump-capture.png)](media/HowToHLI/OSBackupTypeIISKUs/dump-capture.png#lightbox)


3. Important : enregistrez une copie de sauvegarde dans des volumes NFS, dans le scénario où la partition données1 est également endommagée.
   ```
   cp /data1/xfs_dump /osbackup/
   ```

4. Pour exclure des répertoires et des fichiers normaux de l’image mémoire, vous devez baliser les fichiers avec chatter.
   * répertoire chatter -R +d
   * fichier chatter +d
   * Exécuter xfsdump avec l’option « -e »
   * Notez qu’il n’est pas possible d’exclure des systèmes de fichiers nfs [ntfs]




## <a name="how-to-restore-a-backup"></a>Comment restaurer une sauvegarde ?

>[!NOTE]
> * Cette étape nécessite d’engager l’équipe des opérations Microsoft.
> * Pour effectuer une restauration du système d’exploitation avec ce processus, l’assistance Microsoft est nécessaire, car la récupération nécessite l’accès à la console. Veuillez créer un ticket de support auprès de Microsoft pour faciliter la récupération.
> * Nous allons restaurer le système de fichiers complet :

1. Montez le système d’exploitation ISO sur le système.

2. Entrez en mode de récupération.

3. Montage de la partition données1 (ou du volume NFS, quel que soit l’emplacement de stockage de l’image mémoire) en mode lecture/écriture.
   ```
   mount -o rw /dev/md126p4 /mnt1
   ```
4. Montez la Racine en mode lecture/écriture.
   ```
   mount -o rw /dev/md126p2 /mnt2
   ```
5. Restaurez le système de fichiers.
   ```
   xfsrestore -f /mnt1/xfs_dump /mnt2
   ```
   [![capture d’écran de restauration](media/HowToHLI/OSBackupTypeIISKUs/restore-screenshot.png)](media/HowToHLI/OSBackupTypeIISKUs/restore-screenshot.png#lightbox)
6. Redémarrez le système.
   ```
   reboot
   ```

En cas d’échec de vérification après publication, contactez le fournisseur du système d’exploitation et Microsoft pour accéder à la console.

## <a name="post-restore-check"></a>Vérification après restauration

1. Vérifiez que les attributs complets du système ont été restaurés.
   * Le réseau est opérationnel.
   * Les volumes NFS sont montés.
2. Vérifiez que RAID est configuré ; remplacez par votre périphérique RAID.
   ```
   mdadm -D /dev/md126
   ```
   [![état du raid](media/HowToHLI/OSBackupTypeIISKUs/raid-status.png)](media/HowToHLI/OSBackupTypeIISKUs/raid-status.png#lightbox)

3. Assurez-vous que les disques RAID sont synchronisés et que la configuration est dans un état propre.
   * La synchronisation des disques RAID prend un certain temps. Elle peut se poursuivre quelques minutes avant la synchronisation de 100 %.

4. Démarrez HANA DB et vérifiez que HANA fonctionne comme prévu.

5. Vérifiez que HANA apparaît et qu’il n’y a aucune erreur.
   ```
   hdbinfo
   ```
   [![état de Hana](media/HowToHLI/OSBackupTypeIISKUs/hana-status.png)](media/HowToHLI/OSBackupTypeIISKUs/hana-status.png#lightbox)

6. En cas d’échec de vérification après publication, contactez le fournisseur du système d’exploitation et Microsoft pour accéder à la console.