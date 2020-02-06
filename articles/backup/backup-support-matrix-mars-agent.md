---
title: Matrice de prise en charge pour l’agent MARS
description: Cet article décrit la prise en charge de Sauvegarde Azure quand vous sauvegardez des machines qui exécutent l’agent MARS (Microsoft Azure Recovery Services).
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: a87d778bff5a52f4251d83e401028c9949713e33
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988071"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Tableau de prise en charge de la sauvegarde avec l’agent MARS (Microsoft Azure Recovery Services)

Vous pouvez utiliser le [service Sauvegarde Azure](backup-overview.md) pour sauvegarder les machines et les applications locales, ainsi que les machines virtuelles Azure. Cet article récapitule les paramètres de prise en charge et les limitations associés à la sauvegarde de machines avec l’agent MARS (Microsoft Azure Recovery Services).

## <a name="the-mars-agent"></a>Agent MARS

Sauvegarde Azure utilise l’agent MARS pour sauvegarder les données de machines locales et de machines virtuelles Azure dans un coffre Recovery Services de sauvegarde sur Azure. L’agent MARS peut :

- Être exécuté sur des machines Windows locales pour les sauvegarder directement dans un coffre Recovery Services de sauvegarde sur Azure.
- Être exécuté sur des machines virtuelles Windows pour les sauvegarder directement dans un coffre.
- Être exécuté sur un serveur de sauvegarde Microsoft Azure (MABS) ou sur un serveur System Center Data Protection Manager (DPM). Dans ce scénario, les machines et les charges de travail sont sauvegardées vers MABS ou vers le serveur DPM. L’agent MARS sauvegarde ensuite ce serveur dans un coffre sur Azure.

> [!NOTE]
>La Sauvegarde Azure ne prend pas en charge l’ajustement automatique de l’horloge pour le passage à l’heure d’été. Modifiez la stratégie pour vous assurer que l’heure d’été est prise en compte afin d’éviter les différences entre l’heure réelle et l’heure de sauvegarde planifiée.

Vos options de sauvegarde varient en fonction de l’agent installé. Pour plus d’informations, consultez [Architecture de Sauvegarde Azure avec l’agent MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Pour plus d’informations sur l’architecture de sauvegarde de serveur de sauvegarde Azure et DPM, consultez [Sauvegarder dans DPM ou MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Consultez également les [exigences](backup-support-matrix-mabs-dpm.md) applicables à l’architecture de sauvegarde.

**Installation** | **Détails**
--- | ---
Télécharger le dernier agent MARS | Vous pouvez télécharger la dernière version de l’agent à partir du coffre ou [le télécharger directement](https://aka.ms/azurebackup_agent).
Installer directement sur une machine | Vous pouvez installer l’agent MARS directement sur un serveur Windows local ou sur une machine virtuelle Azure qui exécutent un des [systèmes d’exploitation pris en charge](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Installer sur un serveur de sauvegarde | Quand vous configurez DPM ou MABS pour la sauvegarde sur Azure, vous téléchargez et installez l’agent MARS sur le serveur. Vous pouvez installer l’agent peut être installé sur les [systèmes d’exploitation pris en charge](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) listés dans le tableau de prise en charge des serveurs de sauvegarde.

> [!NOTE]
> L’extension Sauvegarde Azure est installée par défaut sur les machines virtuelles Azure activées pour la sauvegarde. Cette extension sauvegarde la totalité de la machine virtuelle. Si vous souhaitez plutôt sauvegarder des dossiers et des fichiers spécifiques, vous pouvez installer et exécuter l’agent MARS sur une machine virtuelle Azure aux côtés de l’extension.
> Quand vous exécutez l’agent MARS sur une machine virtuelle Azure, celui-ci sauvegarde les fichiers ou dossiers situés dans le stockage temporaire de la machine virtuelle. La sauvegarde échoue si des fichiers ou des dossiers sont supprimés du stockage temporaire ou si ce dernier est supprimé.

## <a name="cache-folder-support"></a>Prise en charge du dossier de cache

Quand vous utilisez l’agent MARS pour sauvegarder des données, l’agent prend un instantané des données et les stocke dans un dossier de cache local avant de les envoyer à Azure. Le dossier de cache (ou dossier de travail) présente plusieurs exigences :

**Cache** | **Détails**
--- | ---
Size |  L’espace disponible dans le dossier de cache doit représenter au moins 5 à 10 % de la taille globale de vos données de sauvegarde.
Location | Le dossier de cache doit être stocké en local sur la machine en cours de sauvegarde et il doit être en ligne. Le dossier de cache ne doit pas se trouver sur un partage réseau, sur un média amovible ou sur un volume hors connexion.
Dossier | Le dossier de cache ne doit pas être chiffré sur un volume dédupliqué ou dans un dossier compressé, partiellement alloué ou contenant un point d’analyse.
Changements d’emplacement | Vous pouvez modifier l’emplacement du cache en arrêtant le moteur de sauvegarde (`net stop bengine`) et en copiant le dossier de cache sur un nouveau lecteur. (Assurez-vous que le nouveau lecteur possède suffisamment d’espace.) Remplacez ensuite les deux entrées de Registre sous **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**Config/ScratchLocation** et **Config/CloudBackupProvider/ScratchLocation**) par le nouvel emplacement et redémarrez le moteur.

## <a name="networking-and-access-support"></a>Prise en charge du réseau et de l’accès

### <a name="url-and-ip-access"></a>URL et accès IP

L’agent MARS doit avoir accès à ces URL :

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.MicrosoftAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

Et à ces adresses IP :

- 20.190.128.0/18
- 40.126.0.0/18

L’accès à toutes les URL et adresses IP listées ci-dessus utilise le protocole HTTPS sur le port 443.

### <a name="throttling-support"></a>Prise en charge de la limitation

**Fonctionnalité** | **Détails**
--- | ---
Contrôle de la bande passante | Pris en charge. Dans l’agent MARS, utilisez **Changer les propriétés** pour ajuster la bande passante.
Limitation du réseau | Non disponible pour les machines sauvegardées qui exécutent Windows Server 2008 R2, Windows Server 2008 SP2 ou Windows 7.

## <a name="support-for-direct-backups"></a>Prise en charge des sauvegardes directes

>[!NOTE]
> L’agent MARS ne prend pas en charge les références SKU Windows Server Core.

Vous pouvez utiliser l’agent MARS pour sauvegarder directement vers Azure sur les systèmes d’exploitation listés ci-dessous qui s’exécutent sur :

1. Serveurs Windows locaux
2. Machines virtuelles Azure exécutant Windows

Les systèmes d’exploitation doivent être des systèmes d’exploitation 64 bits et doivent utiliser les derniers Service Packs et les dernières mises à jour. Ces systèmes d’exploitation sont décrits dans le tableau suivant :

**Système d’exploitation** | **Fichiers/dossiers** | **État du système** | **Configuration requise concernant les logiciels et les modules**
--- | --- | --- | ---
Windows 10 (Entreprise, Professionnel, Famille) | Oui | Non |  Vérifier la version serveur correspondante pour la configuration requise concernant les logiciels et les modules
Windows 8.1 (Entreprise, Professionnel)| Oui |Non | Vérifier la version serveur correspondante pour la configuration requise concernant les logiciels et les modules
Windows 8 (Entreprise, Professionnel) | Oui | Non | Vérifier la version serveur correspondante pour la configuration requise concernant les logiciels et les modules
Windows 7 (Édition Intégrale, Entreprise, Professionnel, Édition Familiale Premium/Basique, Édition Starter) | Oui | Non | Vérifier la version serveur correspondante pour la configuration requise concernant les logiciels et les modules
Windows Server 2016 (Standard, Datacenter, Essentials) | Oui | Oui | - .NET 4.5 <br> - Windows PowerShell <br> - Dernier package redistribuable Microsoft VC + + compatible <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Oui | Oui | - .NET 4.5 <br> - Windows PowerShell <br> - Dernier package redistribuable Microsoft VC + + compatible <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 (Standard, Datacenter, Foundation) | Oui | Oui |- .NET 4.5 <br> \- Windows PowerShell <br> - Dernier package redistribuable Microsoft VC + + compatible <br> - Microsoft Management Console (MMC) 3.0 <br> - Deployment Image Servicing and Management (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Oui | Non | - .NET 4.5 <br> - Windows PowerShell <br> - Dernier package redistribuable Microsoft VC + + compatible <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2019 (Standard, Datacenter, Essentials) | Oui | Oui | - .NET 4.5 <br> - Windows PowerShell <br> - Dernier package redistribuable Microsoft VC + + compatible <br> - Microsoft Management Console (MMC) 3.0

Pour plus d’informations, consultez [Systèmes d’exploitation pris en charge pour MABS et DPM](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

## <a name="backup-limits"></a>Limites Azure Backup

### <a name="size-limits"></a>Limites de taille

Sauvegarde Azure limite la taille des sources de données (fichier ou dossier) pouvant être sauvegardées. Les éléments que vous sauvegardez à partir d’un volume unique ne peuvent pas dépasser les tailles indiquées dans ce tableau :

**Système d’exploitation** | **Limite de taille**
--- | ---
Windows Server 2012 ou version ultérieure |54 400 Go
Windows Server 2008 R2 SP1 |1 700 Go
Windows Server 2008 SP2| 1 700 Go
Windows 8 ou version ultérieure| 54 400 Go
Windows 7| 1 700 Go

### <a name="other-limitations"></a>Autres limitations

- MARS ne prend pas en charge la protection de plusieurs machines portant le même nom dans un même coffre.

## <a name="supported-file-types-for-backup"></a>Types de fichiers pris en charge pour la sauvegarde

**Type** | **Support**
--- | ---
Chiffré| Pris en charge.
Compressé | Pris en charge.
Partiellement alloué | Pris en charge.
Compressé et partiellement alloué |Pris en charge.
Liens physiques| Non pris en charge. Ignoré.
Point d’analyse| Non pris en charge. Ignoré.
Chiffré et partiellement alloué |Non pris en charge. Ignoré.
Flux compressé| Non pris en charge. Ignoré.
Flux partiellement alloué| Non pris en charge. Ignoré.
OneDrive (les fichiers synchronisés sont des flux partiellement alloués)| Non pris en charge.

## <a name="supported-drives-or-volumes-for-backup"></a>Lecteurs ou volumes pris en charge pour la sauvegarde

**Lecteur/volume** | **Support** | **Détails**
--- | --- | ---
Volumes en lecture seule| Non pris en charge | Volume Copy Shadow Service (VSS) ne fonctionne que si le volume est accessible en écriture.
Volumes hors connexion| Non pris en charge |VSS ne fonctionne que si le volume est en ligne.
Partage réseau| Non pris en charge |Le volume doit être local sur le serveur.
Volumes verrouillés par BitLocker| Non pris en charge |Le volume doit être déverrouillé pour que la sauvegarde démarre.
Identification du système de fichiers| Non pris en charge |Seul le système NTFS est pris en charge.
Médias amovibles| Non pris en charge |Toutes les sources d’éléments de sauvegarde doivent avoir un état *fixe*.
Lecteurs dédupliqués | Prise en charge | Sauvegarde Azure convertit les données dédupliquées en données normales. Le service optimise les données, les chiffre, les stocke et les envoie au coffre.

## <a name="support-for-initial-offline-backup"></a>Prise en charge de la sauvegarde hors connexion initiale

Sauvegarde Azure prend en charge l’*essaimage hors connexion* pour transférer les données de la sauvegarde initiale vers Azure à l’aide de disques. Cette technique est utile si votre sauvegarde initiale est susceptible de représenter plusieurs téraoctets (To). La sauvegarde hors connexion est prise en charge dans les cas suivants :

- Sauvegarde directe de fichiers et de dossiers sur des machines locales exécutant l’agent MARS.
- Sauvegarde de charges de travail et de fichiers à partir d’un serveur DPM ou MABS.

La sauvegarde hors connexion ne peut pas être utilisée pour les fichiers d’état système.

## <a name="support-for-data-restoration"></a>Prise en charge de la restauration de données

À l’aide de la fonction de [restauration instantanée](backup-instant-restore-capability.md) de Sauvegarde Azure, vous pouvez restaurer les données avant qu’elles ne soient copiées dans le coffre. L’ordinateur dont vous effectuez la sauvegarde doit exécuter .NET Framework 4.5.2 ou version ultérieure.

Il est impossible de restaurer les sauvegardes sur un ordinateur cible qui fonctionne avec une version antérieure du système d’exploitation. Vous pouvez par exemple restaurer une sauvegarde effectuée à partir d’un ordinateur Windows 7 sur Windows 8 ou version ultérieure. Mais les sauvegardes effectuées à partir d’un ordinateur qui exécute Windows 8 ne peuvent pas être restaurées sur les ordinateurs qui exécutent Windows 7.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’[architecture de sauvegarde avec l’agent MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Découvrez ce qui est pris en charge lorsque vous [exécutez l’agent MARS sur le serveur de sauvegarde AZURE ou sur un serveur DPM](backup-support-matrix-mabs-dpm.md).
