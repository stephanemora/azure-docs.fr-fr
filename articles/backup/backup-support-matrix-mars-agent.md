---
title: Tableau de prise en charge de la sauvegarde de machines exécutant l’agent MARS (Microsoft Azure Recovery Services) avec Sauvegarde Azure
description: Cet article résume la prise en charge de la sauvegarde Azure lorsque vous sauvegardez des ordinateurs qui exécutent l’agent Microsoft Azure Recovery Services (MARS).
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 3e2c6a550a9358656fd0870c7e785d131c5b6380
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894391"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Tableau de prise en charge de la sauvegarde avec l’agent MARS (Microsoft Azure Recovery Services)

Vous pouvez utiliser la [service Azure Backup](backup-overview.md) pour sauvegarder des machines locales et des applications et pour sauvegarder des machines virtuelles (VM) Azure. Cet article résume les paramètres de prise en charge et les limitations lorsque vous utilisez l’agent Microsoft Azure Recovery Services (MARS) pour sauvegarder des machines.

## <a name="the-mars-agent"></a>L’agent MARS

Sauvegarde Azure utilise l’agent MARS pour sauvegarder des données à partir de machines locales et machines virtuelles Azure dans un coffre Recovery Services sauvegarde Azure. L’agent MARS peut :
- Exécuter sur les ordinateurs Windows en local afin qu’ils peuvent sauvegarder directement dans un coffre Recovery Services sauvegarde Azure.
- Exécuter sur des machines virtuelles Windows afin qu’ils peuvent sauvegarder directement dans un coffre.
- Exécuter sur le serveur de sauvegarde Microsoft Azure (MABS) ou un serveur de System Center Data Protection Manager (DPM). Dans ce scénario, les charges de travail et les machines sauvegarder vers MABS ou vers le serveur DPM. L’agent MARS sauvegarde puis ce serveur dans un coffre dans Azure. 

Vos options de sauvegarde varient selon lequel l’agent est installé. Pour plus d’informations, consultez [architecture de sauvegarde Azure à l’aide de l’agent MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Pour plus d’informations sur l’architecture de sauvegarde de serveur de sauvegarde AZURE et DPM, consultez [sauvegarder dans DPM ou MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Consultez également [exigences](backup-support-matrix-mabs-dpm.md) pour l’architecture de sauvegarde.

**Installation** | **Détails**
--- | ---
Télécharger le dernier agent MARS | Vous pouvez télécharger la dernière version de l’agent à partir du coffre ou [le télécharger directement](https://aka.ms/azurebackup_agent).
Installer directement sur un ordinateur | Vous pouvez installer l’agent MARS directement sur un serveur de Windows en local ou sur une machine virtuelle Windows qui exécute l’un de le [les systèmes d’exploitation pris en charge](https://docs.microsoft.com/en-us/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Installer sur un serveur de sauvegarde | Quand vous configurez DPM ou MABS pour la sauvegarde sur Azure, vous téléchargez et installez l’agent MARS sur le serveur. Vous pouvez installer l’agent sur [les systèmes d’exploitation pris en charge](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) dans la matrice de prise en charge du serveur de sauvegarde.

> [!NOTE]
> Par défaut, les machines virtuelles Azure qui sont activées pour la sauvegarde ont une installation de l’extension Azure Backup. Cette extension sauvegarde la totalité de la machine virtuelle. Si vous souhaitez plutôt sauvegarder des dossiers et des fichiers spécifiques, vous pouvez installer et exécuter l’agent MARS sur une machine virtuelle Azure aux côtés de l’extension.
> Lorsque vous exécutez l’agent MARS sur une machine virtuelle Azure, il sauvegarde de fichiers ou dossiers qui se trouvent dans le stockage temporaire sur la machine virtuelle. Les sauvegardes échouent si les fichiers ou dossiers sont supprimées du stockage temporaire ou si le stockage temporaire est supprimé.


## <a name="cache-folder-support"></a>Prise en charge du dossier de cache

Lorsque vous utilisez l’agent MARS pour sauvegarder des données, l’agent prend un instantané des données et les stocke dans un dossier de cache local avant d’envoyer les données vers Azure. Le dossier du cache (zéro) présente plusieurs conditions :

**Cache** | **Détails**
--- | ---
Taille |  Espace libre dans le dossier du cache doit être au moins de 5 à 10 % de la taille globale de vos données de sauvegarde. 
Lieu | Le dossier du cache doit être stocké localement sur l’ordinateur qui est en cours de sauvegarde, et il doit être en ligne. Le dossier du cache ne doit pas être sur un partage réseau, sur un support amovible ou sur un volume hors connexion. 
Dossier | Le dossier du cache doit être chiffré sur un volume dédupliqué ou dans un dossier compressé, qui est partiellement alloué, ou qui a une nouvelle analyse de point.
Changements d’emplacement | Vous pouvez modifier l’emplacement du cache en arrêtant le moteur de sauvegarde (`net stop bengine`) et de copier le dossier du cache sur un nouveau lecteur. (Assurez-vous que le nouveau lecteur possède suffisamment d’espace). Puis mettez à jour deux entrées de Registre sous **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**Config/ScratchLocation** et **CloudBackupProvider/Config/ScratchLocation**) au nouvel emplacement et redémarrez le moteur.

## <a name="networking-and-access-support"></a>Prise en charge du réseau et de l’accès

### <a name="url-access"></a>Accès URL

L’agent MARS doit avoir accès à ces URL :

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.MicrosoftAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

### <a name="throttling-support"></a>Prise en charge de la limitation

**Fonctionnalité** | **Détails**
--- | ---
Contrôle de la bande passante |  Pris en charge. Dans l’agent MARS, utilisez **modifier les propriétés** pour ajuster la bande passante.
Limitation du réseau | Non disponible pour les machines de sauvegarde qui exécutent Windows Server 2008 R2, Windows Server 2008 SP2 ou Windows 7.

## <a name="support-for-direct-backups"></a>Prise en charge des sauvegardes directes

Vous pouvez utiliser l’agent MARS pour sauvegarder directement sur Azure, sur certains systèmes d’exploitation qui s’exécutent sur des machines locales et machines virtuelles Azure. Les systèmes d’exploitation doit être de 64 bits et doit être en cours d’exécution les derniers service packs et mises à jour. Le tableau suivant récapitule ces systèmes d’exploitation :

**Système d’exploitation** | **Fichiers/dossiers** | **État du système**
--- | --- | ---
Windows 10 (Entreprise, Professionnel, Famille) | Oui | Non 
Windows 8.1 (Entreprise, Professionnel)| Oui |Non 
Windows 8 (Entreprise, Professionnel) | Oui | Non 
Windows 7 (Édition Intégrale, Entreprise, Professionnel, Édition Familiale Premium/Basique, Édition Starter) | Oui | Non 
Windows Server 2016 (Standard, Datacenter, Essentials) | Oui | Oui
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Oui | Oui
Windows Server 2012 (Standard, Datacenter, Foundation) | Oui | Oui
Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Oui | Oui
Windows Server 2008 SP2 (Standard, Datacenter, Foundation) | Oui | Non 
Windows Storage Server 2016/2012 R2/2012 (Standard, un groupe de travail) | Oui | Non 

Pour plus d’informations, consultez [systèmes d’exploitation pris en charge de MABS et DPM](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

## <a name="backup-limits"></a>Limites Azure Backup

Azure Backup limite la taille de fichier ou dossier source de données qui peut être sauvegardée. Les éléments que vous sauvegardez un volume unique ne peut pas dépasser les tailles résumées dans cette table :

**Système d’exploitation** | **Limite de taille**
--- | ---
Windows Server 2012 ou version ultérieure |  54 400 Go
Windows Server 2008 R2 SP1 |    1 700 GO
Windows Server 2008 SP2 | 1 700 GO
Windows 8 ou version ultérieure  | 54 400 Go
Windows 7   | 1 700 GO


## <a name="supported-file-types-for-backup"></a>Types de fichiers pris en charge pour la sauvegarde

**Type** | **Support** 
--- | --- 
Chiffré   |  Pris en charge. 
Compressé |  Pris en charge.
Partiellement alloué |  Pris en charge.
Compressé et partiellement alloué |  Pris en charge.
Liens physiques  | Non pris en charge. Ignoré.
Point d’analyse   | Non pris en charge. Ignoré.
Chiffré et partiellement alloué |  Non pris en charge. Ignoré.
Flux compressé   | Non pris en charge. Ignoré.
Flux partiellement alloué   | Non pris en charge. Ignoré.
OneDrive (fichiers synchronisés sont éparses flux)  | Non pris en charge. 

## <a name="supported-drives-or-volumes-for-backup"></a>Prise en charge des lecteurs ou volumes pour la sauvegarde

**Lecteur/volume** | **Support** | **Détails**
--- | --- | ---
Volumes en lecture seule   | Non pris en charge | Volume Copy Shadow Service (VSS) ne fonctionne que si le volume est accessible en écriture.
Volumes hors connexion | Non pris en charge |   VSS ne fonctionne que si le volume est en ligne.
Partage réseau   | Non pris en charge |   Le volume doit être local sur le serveur.
Volumes protégés par BitLocker | Non pris en charge |   Le volume doit être déverrouillé avant le démarrage de la sauvegarde.
Identification du système de fichiers  | Non pris en charge |   Seul le système NTFS est pris en charge.
Médias amovibles | Non pris en charge |   Toutes les sources de l’élément de sauvegarde doivent avoir un *fixe* état.
Lecteurs dédupliqués | Pris en charge | Sauvegarde Azure convertit les données dédupliquées en données normales. Elle optimise, chiffre, stocke et envoie les données dans le coffre.

## <a name="support-for-initial-offline-backup"></a>Prise en charge de la sauvegarde hors connexion initiale

Sauvegarde Azure prend en charge *amorçage hors connexion* pour transférer des données de sauvegarde initiales vers Azure à l’aide de disques. Cette prise en charge est utile si votre sauvegarde initiale est susceptible d’être dans la plage de tailles de téraoctets (To). La sauvegarde hors connexion est prise en charge dans les cas suivants :

- Sauvegarde directe des fichiers et dossiers sur les ordinateurs locaux qui exécutent l’agent MARS.
- Sauvegarde de charges de travail et de fichiers à partir d’un serveur DPM ou MABS.

Sauvegarde hors connexion ne peut pas être utilisée pour les fichiers d’état système.

## <a name="support-for-data-restoration"></a>Prise en charge de la restauration de données

À l’aide de la [la restauration instantanée](backup-instant-restore-capability.md) fonctionnalité de sauvegarde Azure, vous pouvez restaurer les données avant d’être copiée dans le coffre. L’ordinateur que vous effectuez une sauvegarde doit être en cours d’exécution .NET Framework 4.5.2 ou version ultérieure.

Impossible de restaurer les sauvegardes sur un ordinateur cible qui exécute une version antérieure du système d’exploitation. Par exemple, une sauvegarde effectuée à partir d’un ordinateur qui exécute Windows 7 peut être restaurée sur Windows 8 ou version ultérieure. Mais une sauvegarde effectuée à partir d’un ordinateur qui exécute Windows 8 ne peuvent pas être restaurée sur un ordinateur qui exécute Windows 7.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [sauvegarde architecture qui utilise l’agent MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Découvrez ce qui a pris en charge lorsque vous [exécuter l’agent MARS sur le serveur de sauvegarde AZURE ou sur un serveur DPM](backup-support-matrix-mabs-dpm.md).
