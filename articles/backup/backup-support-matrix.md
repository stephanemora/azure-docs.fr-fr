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
ms.openlocfilehash: ff4ee1d88bd13e647d0f6218d7e9c9b2c57a5a01
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429568"
---
# <a name="azure-backup-support-matrix"></a>Tableau de prise en charge de Sauvegarde Azure

Vous pouvez utiliser le [service Sauvegarde Azure](backup-overview.md) pour sauvegarder des données dans le cloud Microsoft Azure. Cet article énumère les limitations et les paramètres généraux de prise en charge pour les scénarios et les déploiements de Sauvegarde Azure.

Autres matrices de prise en charge disponibles :

[Matrice de prise en charge](backup-support-matrix-iaas.md) pour la sauvegarde de machine virtuelle Azure [Matrice de prise en charge](backup-support-matrix-mabs-dpm.md) pour la sauvegarde à l’aide de System Center DPM ou du serveur de sauvegarde Microsoft Azure (MABS) [Matrice de prise en charge](backup-support-matrix-mars-agent.md) pour la sauvegarde à l’aide de l’agent Microsoft Azure Recovery Services (MARS)

## <a name="vault-support"></a>Prise en charge des coffres

La Sauvegarde Azure utilise les coffres Recovery Services pour orchestrer et gérer les sauvegardes, ainsi que pour stocker les données sauvegardées.

**Paramètre** | **Détails**
--- | ---
**Coffres dans l’abonnement** | Jusqu’à 500 coffres Recovery Services dans un même abonnement.
**Machines dans un coffre** | Jusqu’à 1 000 machines virtuelles Azure dans un même coffre.<br/><br/> Jusqu’à 50 serveurs MABS peuvent être inscrits dans un seul coffre.
**Source de données dans le stockage du coffre** | Au maximum 54 400 Go. Le nombre des sauvegardes de machines virtuelles Azure n’est pas limité.
**Sauvegardes dans le coffre** | Machines virtuelles Azure : une fois par jour<br/><br/>Machines protégées par DPM/MABS : deux fois par jour<br/><br/> Machines sauvegardées directement à l’aide de l’agent MARS : trois fois par jour. 
**Sauvegardes entre les coffres** | La sauvegarde s’effectue dans une région.<br/><br/> Vous avez besoin d’un coffre dans chaque région Azure qui contient les machines virtuelles que vous souhaitez sauvegarder. Vous ne pouvez pas sauvegarder vers une autre région. 
**Déplacer le coffre** | Vous pouvez [déplacer les coffres](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) entre des abonnements ou entre des groupes de ressources dans le même abonnement.
**Déplacer des données entre des coffres** | Le déplacement de données sauvegardées entre des coffres n’est pas pris en charge.
**Modifier le type de stockage de coffre** | Vous pouvez modifier le type de réplication de stockage (GRS/LRS) pour un coffre avant que les sauvegardes soient stockées. Une fois que les sauvegardes commencent dans le coffre, le type de réplication ne peut pas être modifié.



## <a name="on-premises-backup-support"></a>Prise en charge des sauvegardes locales

Voici ce qui est pris en charge si vous voulez sauvegarder des machines locales.

**Machine** | **Sauvegardé** | **Lieu** | **Caractéristiques**
--- | --- | --- | ---
**Sauvegarde directe de l’ordinateur Windows avec l’agent MARS** | Fichiers, dossiers, état du système | Sauvegarde dans le coffre Recovery Services | Sauvegarde trois fois par jour.<br/><br/> Pas de sauvegarde tenant compte des applications.<br/><br/> Restaurer le fichier, le dossier, le volume.
**Sauvegarde directe de l’ordinateur Linux avec l’agent MARS** | Sauvegarde non prise en charge.
**Sauvegarde dans DPM** | Fichiers, dossiers, volumes, état du système, données d’application. | Sauvegarde dans le stockage DPM local. DPM, puis sauvegarde dans le coffre. | Captures instantanées tenant compte des applications<br/><br/> Précision totale pour la sauvegarde et la récupération.<br/><br/> Linux pris en charge pour les machines virtuelles (Hyper-V/VMware).<br/><br/>. Oracle non pris en charge.
**Sauvegarde dans MABS** | Fichiers, dossiers, volumes, état du système, données d’application. | Sauvegarde dans le stockage MABS local. MABS effectue ensuite la sauvegarde dans le coffre. | Captures instantanées tenant compte des applications<br/><br/> Précision totale pour la sauvegarde et la récupération.<br/><br/> Linux pris en charge pour les machines virtuelles (Hyper-V/VMware).<br/><br/>. Oracle non pris en charge.


## <a name="azure-vm-backup-support"></a>Prise en charge de la sauvegarde de machines virtuelles Azure

### <a name="azure-vm-limits"></a>Limites des machines virtuelles Azure

**Limite** | **Détails**
--- | ---
Disques de données de machine virtuelle Azure | Nombre limité à 16.
Taille de disque de données de machine virtuelle Azure | La taille maximale d’un disque individuel est de 4 095 Go.


### <a name="azure-vm-backup-options"></a>Options de sauvegarde d’une machine virtuelle Azure

Voici ce qui est pris en charge si vous voulez sauvegarder des machines virtuelles Azure.

**Machine** | **Sauvegardé** | **Lieu** | **Caractéristiques**
--- | --- | --- | ---
**Sauvegarde de machine virtuelle Azure à l’aide de l’extension de machine virtuelle** | Machine virtuelle complète | Sauvegarde dans le coffre | Extension installée lorsque vous activez la sauvegarde pour une machine virtuelle.<br/><br/> Sauvegarde une fois par jour.<br/><br/> Sauvegarde tenant compte des applications pour les machines virtuelles Windows, sauvegarde cohérente au niveau fichier pour les machines virtuelles Linux. Vous pouvez configurer la cohérence des applications pour les machines Linux à l’aide de scripts personnalisés.<br/><br/> Restaurer une machine virtuelle/un disque.<br/><br/> Sauvegarde sur une machine virtuelle Azure impossible vers un emplacement locale.
**Sauvegarde de machine virtuelle Azure à l’aide de l’agent MARS** | Fichiers, dossiers | Sauvegarde dans le coffre | Sauvegarde trois fois par jour.<br/><br/> L’agent MARS peut s’exécuter en même temps que l’extension de machine virtuelle si vous souhaitez sauvegarder des fichiers ou dossiers spécifiques plutôt que la machine virtuelle entière.
**Machine virtuelle Azure avec DPM** | Fichiers, dossiers, volumes, état du système, données d’application. | Sauvegarde dans le stockage local de la machine virtuelle Azure exécutant DPM. DPM, puis sauvegarde dans le coffre. | Captures instantanées tenant compte des applications<br/><br/> Précision totale pour la sauvegarde et la récupération.<br/><br/> Linux pris en charge pour les machines virtuelles (Hyper-V/VMware).<br/><br/>. Oracle non pris en charge.
**Machine virtuelle Azure avec MABS** | Fichiers, dossiers, volumes, état du système, données d’application. | Sauvegardé dans le stockage local de la machine virtuelle Azure exécutant MABS. MABS effectue ensuite la sauvegarde dans le coffre. | Captures instantanées tenant compte des applications<br/><br/> Précision totale pour la sauvegarde et la récupération.<br/><br/> Linux pris en charge pour les machines virtuelles (Hyper-V/VMware).<br/><br/> Oracle non pris en charge.





## <a name="linux-backup-support"></a>Prise en charge de la sauvegarde Linux

Voici ce qui est pris en charge si vous voulez sauvegarder des machines Linux.

**Sauvegarde** | **Linux (approuvé par Azure)**
--- | ---
**Sauvegarde directe de la machine locale exécutant Linux**. |  Non. L’agent MARS ne peut être installé que sur des machines Windows.
**Sauvegarde de la machine virtuelle Azure exécutant Linux (à l’aide de l’extension de l’agent)** | Sauvegarde cohérente au niveau application à l’aide de [scripts personnalisés](backup-azure-linux-app-consistent.md).<br/><br/> Récupération au niveau fichier.<br/><br/> Restaurer en créant une machine virtuelle à partir d’un point de récupération ou d’un disque.
**Sauvegarde de la machine virtuelle locale ou Azure exécutant Linux à l’aide de DPM** | Sauvegarde cohérente au niveau fichier de machines virtuelles invitées Linux sur Hyper-V et VMware<br/><br/> Restauration de machines virtuelles invitées Linux Hyper-V et VMware</br></br> Sauvegarde cohérente au niveau fichier non disponible pour les machines virtuelles Azure
**Sauvegarde de la machine locale ou de la machine virtuelle Azure exécutant Linux à l’aide de MABS** | Sauvegarde cohérente au niveau fichier de machines virtuelles invitées Linux sur Hyper-V et VMware<br/><br/> Restauration de machines virtuelles invitées Linux Hyper-V et VMware</br></br> Sauvegarde cohérente au niveau fichier non disponible pour les machines virtuelles Azure.

## <a name="daylight-saving-support"></a>Prise en charge de l’heure d’été

Sauvegarde Azure ne prend pas en charge l’ajustement automatique de l’horloge lors du passage à l’heure d’été pour les sauvegardes de machines virtuelles Azure. Modifiez les stratégies de sauvegarde manuellement selon les besoins.


## <a name="disk-deduplication-support"></a>Prise en charge de la déduplication de disque

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
Machines Windows locales sans DPM/MAB | ![OUI][green] | ![OUI][green]
Machines virtuelles Azure | ![OUI][green] | ![OUI][green]
Machines virtuelles locales/Azure avec DPM | ![OUI][green] | ![OUI][green]
Machines virtuelles locales/Azure avec MABS | ![OUI][green] | ![OUI][green]



## <a name="compression-support"></a>Prise en charge de la compression

Le service Sauvegarde prend en charge la compression du trafic de sauvegarde, comme décrit dans le tableau ci-dessous. 

- Pour les machines virtuelles Azure, l’extension de machine virtuelle lit directement les données à partir du compte de stockage Azure via le réseau de stockage. Il n’est donc pas nécessaire de compresser ce trafic.
- Si vous utilisez DPM ou MABS, vous pouvez compresser les données avant qu’elles soient sauvegardées dans DPM/MABS, afin d’économiser la bande passante.

**Machine** | **Compresser dans MABS/DPM (TCP)** | **Compresser (HTTPS) dans le coffre**
--- | --- | ---
**Sauvegarde directe des ordinateurs Windows locaux** | N/D | OUI
**Sauvegarde des machines virtuelles Azure à l’aide de l’extension de machine virtuelle** | N/D | N/D
**Sauvegarde sur des machines locales/Azure à l’aide de MABS/DPM | ![OUI][green] | ![OUI][green]



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

- [Passez en revue la matrice de prise en charge](backup-support-matrix-iaas.md) de la sauvegarde des machines virtuelles Azure.


[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
