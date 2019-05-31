---
title: Tableau de prise en charge de Sauvegarde Azure
description: Fournit un résumé des limitations et des paramètres de prise en charge pour le service Sauvegarde Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 99dd3c0b07307f2d0bf97dbff697e32e648705ae
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400176"
---
# <a name="azure-backup-support-matrix"></a>Tableau de prise en charge de Sauvegarde Azure

Vous pouvez utiliser [sauvegarde Azure](backup-overview.md) pour sauvegarder des données à la plateforme de cloud Microsoft Azure. Cet article résume les paramètres de prise en charge générale et les limitations pour les déploiements et les scénarios de sauvegarde Azure.

Autres matrices de prise en charge disponibles :

- Matrice de prise en charge pour [sauvegarde de machine virtuelle (VM)](backup-support-matrix-iaas.md)
- Matrice de prise en charge pour la sauvegarde à l’aide de [System Center Data Protection Manager (DPM) / Microsoft Azure sauvegarde serveur (MABS)](backup-support-matrix-mabs-dpm.md)
- Matrice de prise en charge pour la sauvegarde à l’aide de la [agent Microsoft Azure Recovery Services (MARS)](backup-support-matrix-mars-agent.md)

## <a name="vault-support"></a>Prise en charge des coffres

Sauvegarde Azure utilise des coffres Recovery Services pour orchestrer et gérer des sauvegardes. Il utilise également les coffres pour stocker les données sauvegardées.

Le tableau suivant décrit les fonctionnalités des coffres Recovery Services :

**Fonctionnalité** | **Détails**
--- | ---
**Coffres dans l’abonnement** | Jusqu’à 500 coffres Recovery Services dans un même abonnement.
**Machines dans un coffre** | Jusqu'à 1 000 machines virtuelles Azure dans un seul coffre.<br/><br/> Jusqu’à 50 serveurs MABS peuvent être inscrits dans un seul coffre.
**Source de données dans le stockage du coffre** | Maximale 54 400 Go. Le nombre des sauvegardes de machines virtuelles Azure n’est pas limité.
**Sauvegardes dans le coffre** | **Les machines virtuelles Azure :** Une fois par jour.<br/><br/>**Machines protégées par DPM/de sauvegarde AZURE :** Deux fois par jour.<br/><br/> **Les machines sauvegardées directement à l’aide de l’agent MARS :** Trois fois par jour.
**Sauvegardes entre les coffres** | La sauvegarde s’effectue dans une région.<br/><br/> Vous avez besoin d’un coffre dans chaque région Azure qui contient les machines virtuelles que vous souhaitez sauvegarder. Vous ne pouvez pas sauvegarder vers une autre région.
**Déplacer les coffres** | Vous pouvez [déplacer les coffres](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) entre des abonnements ou entre des groupes de ressources dans le même abonnement.
**Déplacer des données entre des coffres** | Déplacement des données sauvegardées entre les coffres n’est pas pris en charge.
**Modifier le type de stockage de coffre** | Vous pouvez modifier le type de réplication de stockage (stockage géo-redondant ou stockage localement redondant) pour un coffre avant que les sauvegardes sont stockées. Une fois que les sauvegardes commencent dans le coffre, le type de réplication ne peut pas être modifié.

## <a name="on-premises-backup-support"></a>Prise en charge des sauvegardes locales

Voici ce qui est pris en charge si vous souhaitez sauvegarder des machines locales :

**Machine** | **Éléments sauvegardés** | **Lieu** | **Caractéristiques**
--- | --- | --- | ---
**Sauvegarde directe de l’ordinateur Windows avec l’agent MARS** | Fichiers, dossiers, état du système | Sauvegarder sur le coffre Recovery Services. | Sauvegarder trois fois par jour<br/><br/> Aucune sauvegarde prenant en charge les applications<br/><br/> Restaurer des fichiers, dossiers, volumes
**Sauvegarde directe de l’ordinateur Linux avec l’agent MARS** | Sauvegarde non prise en charge.
**Sauvegarder sur DPM** | Fichiers, dossiers, volumes, état du système, données d’application | Sauvegarde dans le stockage DPM local. DPM, puis sauvegarde dans le coffre. | Captures instantanées tenant compte des applications<br/><br/> Granularité complète pour la sauvegarde et récupération<br/><br/> Linux prises en charge pour les machines virtuelles (Hyper-V/VMware)<br/><br/> Oracle ne pas pris en charge
**Sauvegarder sur le serveur de sauvegarde AZURE** | Fichiers, dossiers, volumes, état du système, données d’application | Sauvegarde dans le stockage MABS local. MABS effectue ensuite la sauvegarde dans le coffre. | Captures instantanées tenant compte des applications<br/><br/> Granularité complète pour la sauvegarde et récupération<br/><br/> Linux prises en charge pour les machines virtuelles (Hyper-V/VMware)<br/><br/> Oracle ne pas pris en charge

## <a name="azure-vm-backup-support"></a>Prise en charge de la sauvegarde de machines virtuelles Azure

### <a name="azure-vm-limits"></a>Limites des machines virtuelles Azure

**Limite** | **Détails**
--- | ---
**Disques de données de machine virtuelle Azure** | Limite de 16
**Taille de disque de données machine virtuelle Azure** | Des disques individuels peuvent être jusqu'à 4 095 Go

### <a name="azure-vm-backup-options"></a>Options de sauvegarde d’une machine virtuelle Azure

Voici ce qui est pris en charge si vous souhaitez sauvegarder des machines virtuelles Azure :

**Machine** | **Éléments sauvegardés** | **Lieu** | **Caractéristiques**
--- | --- | --- | ---
**Sauvegarde des machines virtuelles Azure à l’aide d’extension de machine virtuelle** | Machine virtuelle complète | Sauvegarder vers le pour coffre. | Extension installée lorsque vous activez la sauvegarde pour une machine virtuelle.<br/><br/> Sauvegarder une fois par jour.<br/><br/> Sauvegarde prenant en charge les applications pour les machines virtuelles Windows ; sauvegarde cohérente des fichiers pour les machines virtuelles Linux. Vous pouvez configurer la cohérence des applications pour les machines Linux à l’aide de scripts personnalisés.<br/><br/> Restaurer une machine virtuelle ou disque.<br/><br/> Ne peut pas sauvegarder une machine virtuelle Azure vers un emplacement sur site.
**Sauvegarde des machines virtuelles Azure à l’aide de l’agent MARS** | Fichiers, dossiers, état du système | Sauvegarder vers le pour coffre. | Sauvegarder trois fois par jour.<br/><br/> Si vous souhaitez sauvegarder des fichiers spécifiques ou de dossiers plutôt que sous forme de la machine virtuelle entière, l’agent MARS peut s’exécuter en même temps que l’extension de machine virtuelle.
**Machine virtuelle Azure avec DPM** | Fichiers, dossiers, volumes, état du système, données d’application | Sauvegarder sur le stockage local de machine virtuelle Azure DPM est en cours d’exécution. DPM, puis sauvegarde dans le coffre. | Instantanés tenant compte des applications.<br/><br/> Précision totale pour la sauvegarde et la récupération.<br/><br/> Linux pris en charge pour les machines virtuelles (Hyper-V/VMware).<br/><br/> Oracle non pris en charge.
**Machine virtuelle Azure avec MABS** | Fichiers, dossiers, volumes, état du système, données d’application | Sauvegarder sur le stockage local de machine virtuelle Azure qui exécute le serveur de sauvegarde AZURE. MABS effectue ensuite la sauvegarde dans le coffre. | Instantanés tenant compte des applications.<br/><br/> Précision totale pour la sauvegarde et la récupération.<br/><br/> Linux pris en charge pour les machines virtuelles (Hyper-V/VMware).<br/><br/> Oracle non pris en charge.

## <a name="linux-backup-support"></a>Prise en charge de la sauvegarde Linux

Voici ce qui est pris en charge si vous souhaitez sauvegarder des machines Linux :

**Type de sauvegarde** | **Linux (approuvé par Azure)**
--- | ---
**Sauvegarde directe de la machine locale exécutant Linux** | Non pris en charge. L’agent MARS peut être installée uniquement sur les ordinateurs Windows.
**À l’aide d’extension de l’agent pour sauvegarder une machine virtuelle Azure exécutant Linux** | Sauvegarde cohérente avec l’application à l’aide de [des scripts personnalisés](backup-azure-linux-app-consistent.md).<br/><br/> Récupération au niveau fichier.<br/><br/> Restaurer en créant une machine virtuelle à partir d’un point de récupération ou d’un disque.
**À l’aide de DPM pour sauvegarder en local ou machine virtuelle Azure exécutant Linux** | Sauvegarde cohérente des fichiers des machines virtuelles invitées de Linux sur Hyper-V et VMWare.<br/><br/> Restauration des machines virtuelles de Hyper-V et des machines virtuelles VMWare Linux invité.<br/><br/> Sauvegarde cohérente des fichiers non disponible pour la machine virtuelle Azure.
**À l’aide du serveur de sauvegarde AZURE pour sauvegarder à la machine locale ou une machine virtuelle Azure exécutant Linux** | Sauvegarde cohérente des fichiers des machines virtuelles invitées de Linux sur Hyper-V et VMWare.<br/><br/> Restauration des machines virtuelles de Hyper-V et les machines virtuelles invitées Linux de VMWare.<br/><br/> Sauvegarde cohérente au niveau fichier non disponible pour les machines virtuelles Azure.

## <a name="daylight-saving-time-support"></a>Prise en charge de l’heure d’été

Sauvegarde Azure ne prend pas en charge les ajustement automatique horloge pour l’heure d’été pour les sauvegardes de machines virtuelles Azure. Modifiez les stratégies de sauvegarde manuellement selon les besoins.

## <a name="disk-deduplication-support"></a>Prise en charge de la déduplication de disque

La prise en charge de la déduplication de disque est la suivante :

- La déduplication de disque est pris en charge en local lorsque vous utilisez DPM ou serveur de sauvegarde Azure pour sauvegarder des machines virtuelles Hyper-V qui exécutent Windows. Windows Server effectue la déduplication des données (au niveau de l’hôte) sur les disques durs virtuels (VHD) qui sont attachés à la machine virtuelle en tant que stockage de sauvegarde.
- La déduplication n’est prise en charge dans Azure pour aucun des composants de Sauvegarde. Lorsque DPM et serveur de sauvegarde AZURE est déployés dans Azure, les disques de stockage attachés à la machine virtuelle ne peut pas être dédupliqués.

## <a name="security-and-encryption-support"></a>Prise en charge de chiffrement et de sécurité

Sauvegarde Azure prend en charge le chiffrement des données en transit et au repos.

### <a name="network-traffic-to-azure"></a>Trafic réseau vers Azure

- Le trafic de sauvegarde à partir de serveurs dans le coffre Recovery Services est chiffré à l’aide de 256 Standard de chiffrement avancé.
- Les données de sauvegarde sont envoyées via une connexion HTTPS sécurisée.
- Données de sauvegarde sont stockées dans le coffre Recovery Services sous forme chiffrée.
- Vous êtes le seul à connaître la phrase secrète pour déverrouiller ces données. Microsoft ne peut déchiffrer les données de sauvegarde à aucun moment.

    > [!WARNING]
    > Une fois le coffre configuré, vous êtes le seul à avoir accès à la clé de chiffrement. Microsoft ne conserve jamais de copie de la clé de chiffrement et n’y a pas accès. Si la clé est égarée, Microsoft ne peut pas récupérer les données de sauvegarde.

### <a name="data-security"></a>Sécurité des données

- Lorsque vous effectuez des sauvegardes des machines virtuelles Azure, vous devez configurer le chiffrement *dans* la machine virtuelle.
- La sauvegarde Azure prend en charge Azure Disk Encryption, qui utilise BitLocker sur les machines virtuelles Windows et **dm-crypt** sur les machines virtuelles Linux.
- Sur le serveur principal, la sauvegarde Azure utilise [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md), ce qui protège les données au repos.

**Machine** | **En transit** | **Au repos**
--- | --- | ---
**Ordinateurs Windows en local sans DPM/de sauvegarde AZURE** | ![Oui][green] | ![Oui][green]
**Machines virtuelles Azure** | ![Oui][green] | ![Oui][green]
**Les ordinateurs Windows en local ou machines virtuelles Azure avec DPM** | ![Oui][green] | ![Oui][green]
**Les ordinateurs Windows en local ou machines virtuelles Azure avec le serveur de sauvegarde AZURE** | ![Oui][green] | ![Oui][green]

## <a name="compression-support"></a>Prise en charge de la compression

Backup prend en charge la compression du trafic de sauvegarde, comme décrit dans le tableau suivant.

- Pour les machines virtuelles Azure, l’extension de machine virtuelle lit les données directement à partir du compte de stockage Azure via le réseau de stockage, afin qu’il n’est pas nécessaire de compresser ce trafic.
- Si vous utilisez DPM ou MABS, vous pouvez économiser la bande passante en compressant les données avant qu’il est sauvegardé.

**Machine** | **Compresser dans MABS/DPM (TCP)** | **Compresser dans le coffre (HTTPS)**
--- | --- | ---
**Sauvegarde directe des ordinateurs Windows locaux** | N/D | ![Oui][green]
**Sauvegarde des machines virtuelles Azure à l’aide d’extension de machine virtuelle** | N/D | N/D
**Sauvegarde sur des machines sur site/Azure à l’aide du serveur de sauvegarde AZURE/DPM** | ![Oui][green] | ![Oui][green]

## <a name="retention-limits"></a>Limites de rétention

**Paramètre** | **Limites**
--- | ---
**Nombre maximal des points de récupération par instance protégée (machine ou charge de travail)** | 9,999
**Délai d’expiration maximal pour un point de récupération** | Aucune limite
**Fréquence de sauvegarde maximale à DPM/de sauvegarde AZURE** | Toutes les 15 minutes pour SQL Server<br/><br/> Une fois par heure pour les autres charges de travail
**Fréquence de sauvegarde maximale vers le coffre** | **En local les ordinateurs Windows ou des machines virtuelles Azure exécutant MARS :** Trois par jour<br/><br/> **DPM/DE SAUVEGARDE AZURE :** deux fois par jour<br/><br/> **Sauvegarde de machine virtuelle Azure :** Un par jour
**Conservation des points de récupération** | Quotidienne, hebdomadaire, mensuelle, annuelle
**Période de rétention maximale** | Dépend de la fréquence de sauvegarde
**Points de récupération sur disque DPM/de sauvegarde AZURE** | 64 pour les serveurs de fichiers. 448 pour les serveurs d’applications <br/><br/>Points de récupération de bande illimitée pour les DPM en local

## <a name="next-steps"></a>Étapes suivantes

- [Passez en revue la matrice de prise en charge](backup-support-matrix-iaas.md) de la sauvegarde des machines virtuelles Azure.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
