---
title: Tableau de prise en charge de la sauvegarde de machines exécutant l’agent MARS (Microsoft Azure Recovery Services) avec Sauvegarde Azure
description: Cet article décrit la prise en charge de Sauvegarde Azure quand vous sauvegardez des machines exécutant l’agent MARS (Microsoft Azure Recovery Services).
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 9d0f751e8d0bc0275cc5fd2c47aaba7a6058931c
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430933"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Tableau de prise en charge de la sauvegarde avec l’agent MARS (Microsoft Azure Recovery Services)

Vous pouvez utiliser le [service Sauvegarde Azure](backup-overview.md) pour sauvegarder des machines et des applications locales ainsi que des machines virtuelles Azure. Cet article récapitule les paramètres de prise en charge et les limitations associés à la sauvegarde de machines avec l’agent MARS (Microsoft Azure Recovery Services).

## <a name="about-the-mars-agent"></a>À propos de l’agent MARS

L’agent MARS est utilisé par Sauvegarde Azure pour sauvegarder les données de machines locales et de machines virtuelles Azure dans un coffre Recovery Services de sauvegarde sur Azure. Vous pouvez utiliser l’agent MARS comme ceci :
- Exécutez l’agent sur des machines Windows locales pour les sauvegarder directement dans un coffre Recovery Services de sauvegarde sur Azure.
- Exécutez l’agent sur des machines virtuelles Azure Windows pour les sauvegarder directement dans un coffre.
- Exécutez l’agent sur un serveur de sauvegarde Microsoft Azure (MABS) ou sur un serveur System Center Data Protection Manager (DPM). Dans ce scénario, les machines et les charges de travail sont sauvegardées sur un serveur MABS/DPM, lequel est sauvegardé dans un coffre sur Azure à l’aide de l’agent MARS. 

Les éléments que vous pouvez sauvegarder varient en fonction de l’endroit où l’agent est installé.

- [Découvrez-en plus](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) sur l’architecture de sauvegarde à l’aide de l’agent MARS.
- [Découvrez-en plus]() sur l’[architecture de sauvegarde](backup-architecture.md#architecture-back-up-to-dpmmabs) MABS/DPM et les [exigences](backup-support-matrix-mabs-dpm.md) associées.


## <a name="supported-installation"></a>Installation prise en charge

**Installer** | **Détails**
--- | ---
**Télécharger le dernier agent MARS** | Vous pouvez télécharger la dernière version de l’agent à partir du coffre ou [le télécharger directement](https://aka.ms/azurebackup_agent).
**Installer directement sur une machine** | Vous pouvez installer l’agent MARS directement sur un serveur Windows local ou une machine virtuelle Azure exécutant l’un des [systèmes d’exploitation Windows pris en charge]().
**Installer sur un serveur de sauvegarde** | Quand vous configurez DPM ou MABS pour la sauvegarde sur Azure, vous téléchargez et installez l’agent MARS sur le serveur. L’agent peut être installé sur les [systèmes d’exploitation](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) listés dans le tableau de prise en charge des serveurs de sauvegarde.

> [!NOTE]
> L’extension Sauvegarde Azure est installée par défaut sur les machines virtuelles Azure activées pour la sauvegarde. Cette extension sauvegarde la totalité de la machine virtuelle. Si vous souhaitez plutôt sauvegarder des dossiers et des fichiers spécifiques, vous pouvez installer et exécuter l’agent MARS sur une machine virtuelle Azure aux côtés de l’extension.
> Quand vous exécutez l’agent MARS sur une machine virtuelle Azure, celui-ci sauvegarde les fichiers/dossiers situés dans le stockage temporaire de la machine virtuelle. La sauvegarde échoue si des fichiers/dossiers sont supprimés du stockage temporaire ou si ce dernier est supprimé.


## <a name="cache-folder-support"></a>Prise en charge du dossier de cache

Quand vous utilisez l’agent MARS pour la sauvegarde, l’agent prend un instantané des données et les stocke dans un dossier de cache local avant de les envoyer à Azure. Le dossier de cache (ou dossier de travail) a un certain nombre d’exigences.

**Cache** | **Détails**
--- | ---
**Taille du cache** |  L’espace libre dans le dossier de cache doit représenter au moins 5 à 10 % de la taille globale de vos données de sauvegarde. 
**Emplacement du cache** | Le dossier de cache doit être local sur la machine en cours de sauvegarde et être en ligne.<br/><br/> Le dossier de cache ne doit pas se trouver sur un partage réseau, sur un média amovible ou sur un volume hors connexion. 
**Dossier de cache** | Le dossier de cache doit être chiffré sur un volume dédupliqué ou sur un dossier compressé/partiellement alloué/contenant un point d’analyse.
**Modifier l’emplacement du cache** | Vous pouvez changer l’emplacement du cache en arrêtant le moteur de sauvegarde (net stop bengine), puis en copiant le dossier de cache sur un nouveau lecteur (ayant suffisamment d’espace). Remplacez ensuite les deux entrées de Registre sous HKLM\SOFTWARE\Microsoft\Windows Azure Backup (Config/ScratchLocation et Config/CloudBackupProvider/ScratchLocation) par le nouvel emplacement et redémarrez le moteur.

## <a name="networking-and-access-support"></a>Prise en charge du réseau et de l’accès

### <a name="url-access"></a>Accès URL

L’agent MARS doit avoir accès à ces URL :

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.MicrosoftAzure.com
- *.microsoftonline.com
- * .windows.net

### <a name="throttling-support"></a>Prise en charge de la limitation

**Fonctionnalité** | **Détails**
--- | ---
Contrôle de la bande passante | Pris en charge<br/><br/> Utilisez **Changer les propriétés** dans l’agent MARS pour ajuster la bande passante.
Limitation du réseau | Non disponible pour les machines sauvegardées exécutant Windows Server 2008 R2, Windows Server 2008 SP2 ou Windows 7.

## <a name="support-for-direct-backups"></a>Prise en charge des sauvegardes directes

Le tableau suivant récapitule les systèmes d’exploitation que vous devez exécuter sur les machines locales et les machines virtuelles Azure pour pouvoir les sauvegarder directement sur Azure avec l’agent MARS.

- Tous les systèmes d’exploitation sont 64 bits.
- Tous les systèmes d’exploitation doivent exécuter les derniers Service Packs et les dernières mises à jour.
- Pour obtenir des détails sur les serveurs DPM et MABS pouvant être sauvegardés avec l’agent MARS, passez en revue [ce tableau](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

**Système d’exploitation** | **Fichiers/dossiers** | **État du système**
--- | --- | ---
Windows 10 (Entreprise, Professionnel, Famille) | OUI | Non 
Windows 8.1 (Entreprise, Professionnel)| OUI |Non 
Windows 8 (Entreprise, Professionnel) | OUI | Non 
Windows 7 (Édition Intégrale, Entreprise, Professionnel, Édition Familiale Premium/Basique, Édition Starter) | OUI | Non 
Windows Server 2016 (Standard, Datacenter, Essentials) | OUI | OUI
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | OUI | OUI
Windows Server 2012 (Standard, Datacenter, Foundation) | OUI | OUI
Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | OUI | OUI
Windows Server 2008 SP2 (Standard, Datacenter, Foundation) | OUI | Non 
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | OUI | Non 


## <a name="backup-limits"></a>Limites Azure Backup

Sauvegarde Azure limite la taille d’une source de données (fichier/dossier) pouvant être sauvegardée. La taille des éléments sélectionnés pour la sauvegarde à partir d’un volume unique ne peut pas dépasser les tailles indiquées dans le tableau.

**Système d’exploitation** | **Limite de taille**
--- | ---
Windows Server 2012 ou version ultérieure |  54 400 Go
Windows Server 2008 R2 SP1 |    1 700 Go
Windows Server 2008 SP2 | 1 700 Go
Windows 8 ou version ultérieure  | 54 400 Go
Windows 7   | 1 700 Go


## <a name="supported-file-types-for-backup"></a>Types de fichiers pris en charge pour la sauvegarde

**Type** | **Pris en charge** 
--- | --- 
Chiffré   | OUI 
Compressé | OUI
Partiellement alloué | OUI 
Compressé et partiellement alloué | OUI
Liens physiques  | Non pris en charge<br/><br/> Ignoré
Point d’analyse   | Non pris en charge<br/><br/> Ignoré
Chiffré et partiellement alloué |  Non pris en charge<br/><br/> Ignoré
Flux compressé   | Non pris en charge<br/><br/> Ignoré
Flux partiellement alloué   | Non pris en charge<br/><br/> Ignoré
One Drive (les fichiers synchronisés sont des flux partiellement alloués)    | Non pris en charge 

## <a name="supported-drivesvolumes-for-backup"></a>Lecteurs/volumes pris en charge pour la sauvegarde

**Lecteur/volume** | **Pris en charge** | **Détails**
--- | --- | ---
Volumes en lecture seule   | Non pris en charge | Le volume doit être accessible en écriture pour que le service VSS fonctionne.
Volumes hors connexion | Non pris en charge |   Le volume doit être en ligne pour que le service VSS fonctionne.
Partage réseau   | Non pris en charge |   Le volume doit être local sur le serveur pour la sauvegarde.
Volumes protégés par BitLocker | Non pris en charge |   Le volume doit être au préalable déverrouillé pour que la sauvegarde fonctionne.
Identification du système de fichiers  | Non pris en charge |   NTFS uniquement.
Médias amovibles | Non pris en charge |   Toutes les sources d’éléments de sauvegarde doivent avoir un état fixe.
Lecteurs dédupliqués |  Pris en charge.<br/><br/> Sauvegarde Azure convertit les données dédupliquées en données normales. Le service optimise les données, les chiffre, les stocke et les envoie au coffre.

## <a name="support-for-initial-offline-backup"></a>Prise en charge de la sauvegarde hors connexion initiale

Sauvegarde Azure prend en charge « l’essaimage hors connexion » pour transférer les données de la sauvegarde initiale vers Azure à l’aide de disques. Cette technique est utile si votre sauvegarde initiale est susceptible d’être de l’ordre de plusieurs téraoctets (To). La sauvegarde hors connexion est prise en charge dans les cas suivants :

- Sauvegarde directe de fichiers et de dossiers sur des machines locales exécutant l’agent MARS.
- Sauvegarde de charges de travail et de fichiers à partir d’un serveur DPM ou MABS.
- La sauvegarde hors connexion ne peut pas être utilisée pour les fichiers d’état système.


## <a name="support-for-restore"></a>Prise en charge de la restauration

- La nouvelle version [Restauration instantanée](/backup-instant-restore-capability.md) de Sauvegarde Azure vous permet de restaurer des données avant leur copie dans le coffre.<br/><br/> Pour utiliser cette fonctionnalité, la machine en cours de sauvegarde doit exécuter le .NET Framework 4.5.2 ou ultérieur.
- Il est impossible de restaurer les sauvegardes sur un ordinateur cible qui fonctionne avec une version antérieure du système d’exploitation. Vous pouvez par exemple restaurer une sauvegarde effectuée à partir d’un ordinateur Windows 7 sur Windows 8 ou ultérieur. En revanche, il n’est pas possible de restaurer sur un ordinateur Windows 7 une sauvegarde effectuée à partir d’un ordinateur Windows 8.


## <a name="next-steps"></a>Étapes suivantes
- [Découvrez-en plus](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) sur l’architecture de sauvegarde avec l’agent MARS.
- [Découvrez](backup-support-matrix-mabs-dpm.md) ce qui est pris en charge quand vous exécutez l’agent MARS sur le serveur de sauvegarde Microsoft Azure (MABS) ou System Center DPM.


