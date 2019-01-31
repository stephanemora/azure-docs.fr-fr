---
title: Tableau de prise en charge de Sauvegarde Azure
description: Fournit un résumé des limitations et des paramètres de prise en charge pour le service Sauvegarde Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: cb3a60995a4edfe5eb00f1a5e88812146816806a
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883702"
---
# <a name="azure-backup-support-matrix"></a>Tableau de prise en charge de Sauvegarde Azure

Vous pouvez utiliser le [service Sauvegarde Azure](backup-overview.md) pour sauvegarder des données dans le cloud Microsoft Azure. Cet article énumère les limitations et les paramètres de prise en charge pour les scénarios et les déploiements de Sauvegarde Azure.

## <a name="vault-support"></a>Prise en charge des coffres

La Sauvegarde Azure utilise les coffres Recovery Services pour orchestrer et gérer les sauvegardes, ainsi que pour stocker les données sauvegardées.

**Paramètre** | **Détails**
--- | ---
Nombre de coffres | Jusqu’à 500 coffres Recovery Services dans un même abonnement.
Machines dans un coffre | Jusqu’à 1 000 machines virtuelles Azure dans un même coffre.<br/><br/> Jusqu’à 50 machines locales exécutant l’agent Sauvegarde Azure (agent Microsoft Azure Recovery Services (MARS)) peuvent être inscrites dans un même coffre.
Source de données dans le stockage de l’archivage | Au maximum 54 400 Go. Le nombre des sauvegardes de machines virtuelles Azure n’est pas limité.
Sauvegardes dans le coffre | Machines virtuelles Azure : une fois par jour ; machines protégées par DPM/MABS : deux fois par jour ; machines sauvegardées directement à l’aide de l’agent MARS : trois fois par jour.  
Déplacer le coffre | Pour déplacer un coffre Recovery Services, vous devez être inscrit dans une préversion privée. Pour l’essayer, contactez AskAzureBackupTeam@microsoft.com.
Déplacer des données entre des coffres | Le déplacement de données sauvegardées entre des coffres n’est pas pris en charge.
Type de réplication de stockage | Vous pouvez modifier le type de réplication de stockage (GRS/LRS) pour un coffre avant que les sauvegardes soient stockées. Une fois que les sauvegardes commencent dans le coffre, le type de réplication ne peut pas être modifié.



## <a name="on-premises-backup-support"></a>Prise en charge des sauvegardes locales

Voici ce qui est pris en charge si vous voulez sauvegarder des machines locales.

**Machine** | **Lieu** | **Sauvegarder** | **Caractéristiques**
--- | --- | --- | ---
**Physique/virtuelle Windows (sans serveur de sauvegarde)** | Fichiers, dossiers, état du système | Sauvegarde dans le coffre Recovery Services | Sauvegarde trois fois par jour.<br/><br/> Pas de sauvegarde tenant compte des applications.<br/><br/> Restaurer le fichier, le dossier, le volume.
**Physique/virtuelle Linux (sans serveur de sauvegarde)** | Sauvegarde non prise en charge.
**Physique/virtuelle avec DPM** | Fichiers, dossiers, volumes, état du système, données d’application. | Sauvegarde dans DPM (sur un disque connecté localement au serveur DPM ou sur bande).<br/><br/> DPM, puis sauvegarde dans le coffre. | Captures instantanées tenant compte des applications<br/><br/> Précision totale pour la sauvegarde et la récupération.<br/><br/> Linux pris en charge pour les machines virtuelles (Hyper-V/VMware).<br/><br/>. Oracle non pris en charge.
**Physique/virtuelle avec MABS** | Fichiers, dossiers, volumes, état du système, données d’application. | Sauvegarde dans MABS (sur un disque connecté localement au serveur MABS). La bande n’est pas prise en charge<br/><br/> MABS effectue ensuite la sauvegarde dans le coffre. | Captures instantanées tenant compte des applications<br/><br/> Précision totale pour la sauvegarde et la récupération.<br/><br/> Linux pris en charge pour les machines virtuelles (Hyper-V/VMware).<br/><br/>. Oracle non pris en charge.


## <a name="azure-vms"></a>Machines virtuelles Azure

### <a name="azure-vm-limits"></a>Limites des machines virtuelles Azure

**Limite** | **Détails**
--- | ---
Disques de données de machine virtuelle Azure | Nombre limité à 16.
Taille de disque de données de machine virtuelle Azure | La taille maximale d’un disque individuel est de 4 095 Go.


### <a name="azure-vm-backup-options"></a>Options de sauvegarde d’une machine virtuelle Azure

Voici ce qui est pris en charge si vous voulez sauvegarder des machines virtuelles Azure.

**Machine** | **Lieu** | **Sauvegarder** | **Caractéristiques**
--- | --- | --- | ---
**Machines virtuelles Azure (sans serveur de sauvegarde)** | Fichiers, dossiers, état du système | Sauvegarde dans le coffre. | Sauvegarde une fois par jour.<br/><br/> Sauvegarde tenant compte des applications pour les machines virtuelles Windows, sauvegarde cohérente au niveau fichier pour les machines virtuelles Linux. Vous pouvez configurer la cohérence des applications pour les machines Linux à l’aide de scripts personnalisés.<br/><br/> Restaurer une machine virtuelle/un disque.<br/><br/> Sauvegarde sur une machine virtuelle Azure impossible vers un emplacement locale.
**Machine virtuelle Azure avec DPM** | Fichiers, dossiers, volumes, état du système, données d’application. | Sauvegarde dans DPM en cours d’exécution dans Azure (sur un disque connecté localement au serveur DPM). La bande n’est pas prise en charge.<br/><br/> DPM, puis sauvegarde dans le coffre. | Captures instantanées tenant compte des applications<br/><br/> Précision totale pour la sauvegarde et la récupération.<br/><br/> Linux pris en charge pour les machines virtuelles (Hyper-V/VMware).<br/><br/>. Oracle non pris en charge.
**Machine virtuelle Azure avec MABS** | Fichiers, dossiers, volumes, état du système, données d’application. | Sauvegarde dans MABS en cours d’exécution dans Azure (sur un disque connecté localement au serveur MABS). La bande n’est pas prise en charge<br/><br/> MABS effectue ensuite la sauvegarde dans le coffre. | Captures instantanées tenant compte des applications<br/><br/> Précision totale pour la sauvegarde et la récupération.<br/><br/> Linux pris en charge pour les machines virtuelles (Hyper-V/VMware).<br/><br/>. Oracle non pris en charge.





## <a name="linux-backup-support"></a>Prise en charge de la sauvegarde Linux

Voici ce qui est pris en charge si vous voulez sauvegarder des machines Linux.

**Sauvegarde** | **Linux (approuvé par Azure)**
--- | ---
**Machine Linux locale (sans DPM ou MABS)**. |  Non. L’agent MARS ne peut être installé que sur des machines Windows.
**Machine virtuelle Azure (sans DPM ou MABS)** | Sauvegarde cohérente au niveau application à l’aide de [scripts personnalisés](backup-azure-linux-app-consistent.md).<br/><br/> Récupération au niveau fichier.<br/><br/> Restaurer en créant une machine virtuelle à partir d’un point de récupération ou d’un disque.
**Machine locale/machine virtuelle Azure avec DPM** | Sauvegarde cohérente au niveau fichier de machines virtuelles invitées Linux sur Hyper-V et VMware<br/><br/> Restauration de machines virtuelles invitées Linux Hyper-V et VMware</br></br> Sauvegarde cohérente au niveau fichier non disponible pour les machines virtuelles Azure
**Machine locale/machine virtuelle Azure avec MABS** | Sauvegarde cohérente au niveau fichier de machines virtuelles invitées Linux sur Hyper-V et VMware<br/><br/> Restauration de machines virtuelles invitées Linux Hyper-V et VMware</br></br> Sauvegarde cohérente au niveau fichier non disponible pour les machines virtuelles Azure.

## <a name="disk-support"></a>Prise en charge des disques

La prise en charge de la déduplication de disque est la suivante :
- La déduplication de disque est prise en charge localement quand vous utilisez DPM ou MABS pour sauvegarder des machines virtuelles Hyper-V exécutant Windows. Windows Server effectue la déduplication (au niveau de l’hôte) sur les disques durs virtuels attachés en tant que stockage de sauvegarde à la machine virtuelle.
- La déduplication n’est prise en charge dans Azure pour aucun des composants de Sauvegarde. Quand System Center DPM et le serveur de sauvegarde sont déployés dans Azure, les disques de stockage connectés à la machine virtuelle ne peuvent pas être dédupliqués.


## <a name="securityencryption-support"></a>Prise en charge de la sécurité/du chiffrement

La Sauvegarde Azure prend en charge le chiffrement des données en transit et au repos :

Trafic réseau vers Azure :
- Le trafic de sauvegarde entre les serveurs et le coffre Recovery Services est chiffré à l’aide du protocole AES (Advanced Encryption Standard) 256.
- Les données de sauvegarde sont envoyées via une connexion HTTPS sécurisée.
- Les données de sauvegarde sont stockées dans le coffre Recovery Services sous forme chiffrée.
- Vous êtes le seul à connaître la phrase secrète pour déverrouiller ces données. Microsoft ne peut déchiffrer les données de sauvegarde à aucun moment.
    > [!WARNING]
    > Une fois le coffre configuré, vous êtes le seul à avoir accès à la clé de chiffrement. Microsoft ne conserve jamais de copie de la clé de chiffrement et n’y a pas accès. Si la clé est égarée, Microsoft ne peut pas récupérer les données de sauvegarde.
Sécurité des données :
- Lors de la sauvegarde de machines virtuelles Azure, vous devez configurer le chiffrement *dans* la machine virtuelle.
- La sauvegarde Azure prend en charge Azure Disk Encryption, qui utilise BitLocker sur les machines virtuelles Windows et **dm-crypt** sur les machines virtuelles Linux.
- Sur le back end, la sauvegarde Azure utilise le [Chiffrement du service de stockage Azure](../storage/common/storage-service-encryption.md), ce qui protège les données au repos.


**Machine** | **En transit** | **Au repos**
--- | --- | ---
Machines Windows locales sans DPM/MAB | ![Oui][green] | ![Oui][green]
Machines virtuelles Azure | ![Oui][green] | ![Oui][green]
Machines virtuelles locales/Azure avec DPM | ![Oui][green] | ![Oui][green]
Machines virtuelles locales/Azure avec MABS | ![Oui][green] | ![Oui][green]



## <a name="compression-support"></a>Prise en charge de la compression

Le service Sauvegarde prend en charge la compression du trafic de sauvegarde, comme décrit dans le tableau ci-dessous. Notez les points suivants :

- Pour les machines virtuelles Azure, l’extension de machine virtuelle lit directement les données à partir du compte de stockage Azure via le réseau de stockage. Il n’est donc pas nécessaire de compresser ce trafic.
- Si vous utilisez DPM ou MABS, vous pouvez compresser les données avant qu’elles soient sauvegardées dans DPM/MABS, afin d’économiser la bande passante.

**Machine** | **Compresser dans MABS/DPM (TCP)** | **Compresser (HTTPS) dans le coffre**
--- | --- | ---
Machines Windows locales sans DPM/MAB | N/D | Oui
Machines virtuelles Azure | N/D | N/D
Machines virtuelles locales/Azure avec DPM | ![Oui][green] | ![Oui][green]
Machines virtuelles locales/Azure avec MABS | ![Oui][green] | ![Oui][green]



## <a name="retention-limits"></a>Limites de rétention

**Paramètre** | **Limites**
--- | ---
Nombre maximal de points de récupération par instance protégée (machine/charge de travail) | 9 999
Temps d’expiration maximal pour un point de récupération | Aucune limite
Fréquence de sauvegarde maximale dans DPM/MABS | Toutes les 15 minutes pour SQL Server<br/><br/> Une fois par heure pour les autres charges de travail.
Fréquence de sauvegarde maximale dans le coffre | Machines Windows locales/machines virtuelles Azure exécutant MARS : trois fois par jour<br/><br/> DPM/MABS : deux fois par jour<br/><br/> Sauvegarde des machines virtuelles Azure : 1 par jour
Conservation des points de récupération | Quotidienne, hebdomadaire, mensuelle, annuelle.
Période de rétention maximale | Dépend de la fréquence de sauvegarde.
Points de récupération sur un disque DPM/MAB | 64 pour les serveurs de fichiers, 448 pour les serveurs d’applications.<br/><br/> Les points de récupération sur bande sont illimités pour les DPM locaux.

## <a name="next-steps"></a>Étapes suivantes

- [Sauvegarder des machines virtuelles Azure](backup-azure-arm-vms-prepare.md)
- [Sauvegarder des ordinateurs Windows directement](tutorial-backup-windows-server-to-azure.md), sans serveur de sauvegarde.
- [Configurer MABS](backup-azure-microsoft-azure-backup.md) pour la sauvegarde dans Azure, puis sauvegarder des charges de travail dans MABS.
- [Configurer DPM](backup-azure-dpm-introduction.md) pour la sauvegarde dans Azure, puis sauvegarder des charges de travail dans DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
