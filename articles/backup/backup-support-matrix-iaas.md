---
title: Tableau de prise en charge de Sauvegarde Azure pour la sauvegarde de machines virtuelles Azure
description: Fournit un récapitulatif des limitations et des paramètres de prise en charge de la sauvegarde de machines virtuelles Azure avec le service Sauvegarde Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: raynew
ms.openlocfilehash: 512e07f2892f9c4adf47956d3a5b07debf870e46
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57849905"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Tableau de prise en charge pour la sauvegarde de machines virtuelles Azure
Vous pouvez utiliser la [service Azure Backup](backup-overview.md) pour sauvegarder des machines locales et les charges de travail et les machines virtuelles (VM) Azure. Cet article résume les paramètres de prise en charge et les limitations lors de la sauvegarde des machines virtuelles Azure avec sauvegarde Azure.

Autres tableaux de prise en charge :

- [Matrice de prise en charge générale](backup-support-matrix.md) pour la sauvegarde Azure
- [Matrice de prise en charge](backup-support-matrix-mabs-dpm.md) pour Azure Backup server/System Center Data Protection Manager (DPM) de sauvegarde
- [Matrice de prise en charge](backup-support-matrix-mars-agent.md) pour la sauvegarde avec l’agent Microsoft Azure Recovery Services (MARS)

## <a name="supported-scenarios"></a>Scénarios pris en charge

Voici comment vous pouvez sauvegarder et restaurer des machines virtuelles Azure avec le service Sauvegarde Azure.

**Scénario** | **Sauvegarde** | **Agent** |**Restauration**
--- | --- | --- | ---
sauvegarde directe de machines virtuelles Azure  | Sauvegarder la machine virtuelle entière.  | Aucun agent n’est nécessaire sur la machine virtuelle Azure. Sauvegarde Azure installe et utilise une extension pour le [agent de machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) qui s’exécute sur la machine virtuelle. | Restaurez comme ceci :<br/><br/> - **Créez une machine virtuelle de base**. Cela est utile si la machine virtuelle n’a aucune configuration spéciale tels que plusieurs adresses IP.<br/><br/> - **Restaurer le disque de la machine virtuelle**. Restaurez le disque. Puis attachez-le à une machine virtuelle existante ou créer une machine virtuelle à partir du disque à l’aide de PowerShell.<br/><br/> - **Remplacer un disque de la machine virtuelle**. Si une machine virtuelle existe et qu’elle utilise des disques managés (non chiffrés), vous pouvez restaurer un disque et l’utiliser pour remplacer un disque existant sur la machine virtuelle.<br/><br/> - **Restaurer des fichiers/dossiers spécifiques**. Vous pouvez restaurer des fichiers ou dossiers à partir d’une machine virtuelle au lieu d’à partir de la machine virtuelle entière.
Sauvegarde directe des machines virtuelles de Azure (Windows uniquement)  | Sauvegarder des fichiers/dossiers/volume spécifique. | Installer le [agent Azure Recovery Services](backup-azure-file-folder-backup-faq.md).<br/><br/> Vous pouvez exécuter l’agent MARS en même temps que l’extension de sauvegarde pour l’agent de machine virtuelle Azure pour sauvegarder la machine virtuelle au niveau des fichiers/dossiers. | Restaurer des fichiers/dossiers spécifiques.
Sauvegarder la machine virtuelle Azure au serveur de sauvegarde  | Sauvegarder des fichiers/dossiers/volumes ; État/sauvegarde complète fichiers système ; données d’application pour System Center DPM ou serveur de sauvegarde Microsoft Azure (MABS).<br/><br/> DPM/de sauvegarde AZURE sauvegarde ensuite au coffre de sauvegarde. | Installez l’agent de protection DPM/de sauvegarde AZURE sur la machine virtuelle. L’agent MARS est installé sur DPM/MABS.| Restaurer des fichiers/dossiers/volumes ; état du système/fichiers complets ; données d’application.

En savoir plus sur sauvegarde [à l’aide d’un serveur de sauvegarde](backup-architecture.md#architecture-back-up-to-dpmmabs) et environ [prennent en charge les exigences](backup-support-matrix-mabs-dpm.md).

## <a name="supported-backup-actions"></a>Actions de sauvegarde prises en charge

**Action** | **Support**
--- | ---
Activer la sauvegarde quand vous créez une machine virtuelle Azure Windows | Pris en charge pour :  Windows Server 2019 (centre de données/Datacenter Core), Windows Server 2016 (Core du centre de données/centre de données) ; Windows Server 2012 R2 Datacenter ; Windows Server 2008 R2 (RTM et SP1)
Activer la sauvegarde quand vous créez une machine virtuelle Linux | Pris en charge pour :<br/><br/> - Ubuntu Server : 1710, 1704, 1604 (LTS), 1404 (LTS)<br/><br/> - Red Hat : RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4<br/><br/> - SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3<br/><br/> - Debian : 8, 9<br/><br/> - CentOS : 6.9, 7.3<br/><br/> -Oracle Linux : 6.7, 6.8, 6.9, 7.2, 7.3
Sauvegarder une machine virtuelle arrêtée/hors connexion/en recherche |  Pris en charge.<br/><br/> L’instantané a une cohérence en cas d’incident uniquement, mais pas de cohérence des applications.
Sauvegarder les disques après que la migration vers des disques gérés |  Pris en charge.<br/><br/> La sauvegarde continuera à fonctionner. Aucune action n’est requise.
Sauvegarder des disques managés après l’activation d’un verrou de groupe de ressources | Non pris en charge.<br/><br/> Sauvegarde Azure ne peut pas supprimer les anciens points de ressources et les sauvegardes commenceront à échouer lorsque la limite maximale de points de restauration est atteinte.
Modifier la stratégie de sauvegarde pour une machine virtuelle |  Pris en charge.<br/><br/> La machine virtuelle est sauvegardée en utilisant les paramètres de planification et de rétention dans la nouvelle stratégie. Si les paramètres de conservation sont étendus, les points de récupération existants sont marqués et conservés. S’ils sont réduits, points de récupération existants seront nettoyés lors de la prochaine tâche de nettoyage et finalement supprimés.
Annuler un travail de sauvegarde | Pris en charge pendant le processus de capture instantanée.<br/><br/> Non pris en charge quand l’instantané est transféré vers le coffre.
Sauvegarder la machine virtuelle vers une autre région ou un autre abonnement |  Non pris en charge.
Sauvegardes par jour (via l’extension de machine virtuelle Azure) | Une sauvegarde planifiée par jour.<br/><br/> Vous pouvez effectuer jusqu'à quatre sauvegardes à la demande par jour.
Sauvegardes par jour (via l’agent MARS) | Trois sauvegardes planifiées par jour.
Sauvegardes par jour (via DPM/MABS) | Deux sauvegardes planifiées par jour.
Sauvegarde mensuelle/annuelle   | Non pris en charge lors de la sauvegarde avec l’extension de machine virtuelle Azure. Seules les sauvegardes quotidiennes et hebdomadaires sont prises en charge.<br/><br/> Vous pouvez configurer la stratégie pour conserver des sauvegardes quotidiennes/hebdomadaires pour une période de conservation mensuelle/annuelle.
Ajustement automatique de l’horloge | Non pris en charge.<br/><br/> Sauvegarde Azure n’ajuste pas automatiquement les modifications de l’heure d’été lorsque vous sauvegardez une machine virtuelle.<br/><br/>  Modifiez la stratégie manuellement en fonction des besoins.
[Fonctionnalités de sécurité pour la sauvegarde hybride](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |  La désactivation des fonctionnalités de sécurité n’est pas pris en charge.

## <a name="operating-system-support-windows"></a>Prise en charge des systèmes d’exploitation (Windows)

Le tableau suivant résume les systèmes d’exploitation pris en charge lors de la sauvegarde des machines virtuelles de Windows Azure.

**Scénario** | **Système d’exploitation pris en charge**
--- | ---
Sauvegarder avec l’extension de l’agent de machine virtuelle Azure | Client Windows : Non pris en charge<br/><br/> Windows Server 2019 (centre de données/Datacenter Core), Windows Server 2016 (Core du centre de données/centre de données) ; Windows Server 2012 R2 Datacenter ; Windows Server 2008 R2 (RTM et SP1)
Sauvegarder avec l’agent MARS | Systèmes d’exploitation [pris en charge](backup-support-matrix-mars-agent.md#support-for-direct-backups).
Sauvegarder avec DPM/de sauvegarde AZURE | Systèmes d’exploitation pris en charge pour la sauvegarde avec [MABS](backup-mabs-protection-matrix.md) et [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

## <a name="support-for-linux-backup"></a>Prise en charge des sauvegardes Linux

Voici ce qui est pris en charge si vous voulez sauvegarder des machines Linux.

**Action** | **Support**
--- | ---
Sauvegarder des machines virtuelles Azure Linux avec l’agent de machine virtuelle Linux Azure Linux | Sauvegarde avec cohérence des fichiers.<br/><br/> Sauvegarde cohérente au niveau application à l’aide de [scripts personnalisés](backup-azure-linux-app-consistent.md).<br/><br/> Pendant la restauration, vous pouvez créer une machine virtuelle, restaurer un disque et utilisez-le pour créer une machine virtuelle, ou restaurer un disque et utilisez-le pour remplacer un disque sur une machine virtuelle existante. Vous pouvez également restaurer des fichiers et des dossiers individuels.
Sauvegarder des machines virtuelles Azure Linux avec l’agent MARS | Non pris en charge.<br/><br/> L’agent MARS ne peut être installé que sur des machines Windows.
Sauvegarder des machines virtuelles Azure Linux avec DPM/MABS | Non pris en charge.

## <a name="operating-system-support-linux"></a>Prise en charge du système d’exploitation (Linux)

Pour les sauvegardes de machines virtuelles Azure Linux, Sauvegarde Azure prend en charge [la liste des distributions Linux approuvées par Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Notez les points suivants :

- Sauvegarde Azure ne prend pas en charge CoreOS Linux.
- Sauvegarde Azure ne prend pas en charge les systèmes d’exploitation 32 bits.
- Autres distributions Linux apportez votre propre peuvent fonctionner tant que le [agent de machine virtuelle Azure pour Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) est disponible sur la machine virtuelle et tant que Python est pris en charge.



## <a name="backup-frequency-and-retention"></a>Fréquence et conservation des sauvegardes

**Paramètre** | **Limites**
--- | ---
Points de récupération maximale par instance protégée (machine/charge de travail) | 9999.
Délai d’expiration maximal pour un point de récupération | Aucune limite.
Fréquence maximale de sauvegarde dans le coffre (extension de machine virtuelle Azure) | Une fois par jour.
Fréquence maximale de sauvegarde dans le coffre (agent MARS) | Trois sauvegardes par jour.
Fréquence de sauvegarde maximale dans DPM/MABS | Toutes les 15 minutes pour SQL Server.<br/><br/> Une fois par heure pour les autres charges de travail.
Conservation des points de récupération | Quotidienne, hebdomadaire, mensuelle et annuelle.
Période de rétention maximale | Dépend de la fréquence de sauvegarde.
Points de récupération sur un disque DPM/MAB | 64 pour les serveurs de fichiers et 448 pour les serveurs d’applications.<br/><br/> Les points de récupération sur bande sont illimités pour les DPM locaux.

## <a name="supported-restore-methods"></a>Méthodes de restauration prises en charge

**Méthode de restauration** | **Détails**
--- | ---
Créer une machine virtuelle | Vous pouvez créer une machine virtuelle pendant le processus de restauration. <br/><br/> Cette option produit une machine virtuelle de base opérationnelle. Vous pouvez spécifier le nom de la machine virtuelle, le groupe de ressources, le réseau virtuel, le sous-réseau et le stockage.  
Restaurer un disque | Vous pouvez restaurer un disque et l’utiliser pour créer une machine virtuelle.<br/><br/> Quand vous sélectionnez cette option, Sauvegarde Azure copie les données depuis le coffre vers un compte de stockage que vous sélectionnez. Le travail de restauration génère un modèle. Vous pouvez télécharger ce modèle, utilisez-le pour spécifier des paramètres personnalisés de machine virtuelle et créer une machine virtuelle.<br/><br/> Cette option vous permet de spécifier plus de paramètres que l’option précédente pour créer une machine virtuelle.<br/><br/>
Remplacer un disque existant | Vous pouvez restaurer un disque, puis utiliser le disque restauré pour remplacer un disque qui se trouve actuellement sur une machine virtuelle.
Restaurer des fichiers | Vous pouvez récupérer des fichiers à partir d’un point de récupération sélectionné. Vous téléchargez un script pour monter le disque de machine virtuelle à partir du point de récupération. Ensuite, vous parcourez les volumes de disque pour rechercher les fichiers ou dossiers que vous souhaitez récupérer et démontez le disque lorsque vous avez terminé.

## <a name="support-for-file-level-restore"></a>Prise en charge de la restauration au niveau fichier

**Restauration** | **Pris en charge**
--- | ---
Restauration de fichiers entre systèmes d’exploitation | Vous pouvez restaurer des fichiers sur n’importe quelle machine ayant le même système d’exploitation (ou un système d’exploitation compatible) que la machine virtuelle sauvegardée. Consultez le [table de système d’exploitation Compatible](backup-azure-restore-files-from-vm.md#system-requirements).
Restauration de fichiers sur des machines virtuelles classiques | Non pris en charge.
Restauration de fichiers provenant de machines virtuelles chiffrées | Non pris en charge.
Restauration de fichiers provenant de comptes de stockage avec des restrictions réseau | Non pris en charge.
Restauration de fichiers sur des machines virtuelles avec des espaces de stockage Windows | Restauration non prise en charge sur la même machine virtuelle.<br/><br/> Au lieu de cela, restaurez les fichiers sur une machine virtuelle compatible.
Restaurer des fichiers sur une machine virtuelle Linux avec des baies LVM/RAID | Restauration non prise en charge sur la même machine virtuelle.<br/><br/> Restaurer sur une machine virtuelle compatible.
Restaurer des fichiers avec des paramètres réseau spéciaux | Restauration non prise en charge sur la même machine virtuelle. <br/><br/> Restaurer sur une machine virtuelle compatible.

## <a name="support-for-vm-management"></a>Prise en charge de la gestion des machines virtuelles

Le tableau suivant résume la prise en charge pour la sauvegarde au cours des tâches de gestion de machine virtuelle, comme l’ajout ou remplacement des disques de machine virtuelle.

**Restauration** | **Pris en charge**
--- | ---
Restaurer entre abonnements/régions/zones. | Non pris en charge.
Restaurer sur une machine virtuelle existante | Utilisez l’option de remplacement de disque.
Restaurer un disque avec un compte de stockage activé pour Azure Storage Service Encryption (SSE) | Non pris en charge.<br/><br/> Restaurez sur un compte où SSE n’est pas est activé.
Restaurer sur des comptes de stockage mixtes | Non pris en charge.<br/><br/> Selon le type de compte de stockage, tous les disques restaurés seront ou Premium ou Standard, mais pas mixtes.
Restaurer à un compte de stockage en utilisant le stockage redondant dans une zone (ZRS) | Non pris en charge.
Restaurer une machine virtuelle directement sur un groupe à haute disponibilité | Pour les disques gérés, vous pouvez restaurer le disque et utiliser l’option set de disponibilité dans le modèle.<br/><br/> Non pris en charge pour les disques non managés. Pour les disques non managés, restaurez le disque, puis créez une machine virtuelle dans le groupe à haute disponibilité.
Restaurer la sauvegarde des machines virtuelles non managées après que la mise à niveau vers géré de machine virtuelle|  Pris en charge.<br/><br/> Vous pouvez restaurer les disques, puis créer une machine virtuelle managée.
Restaurer une machine virtuelle sur un point de restauration antérieur à la migration de la machine virtuelle vers des disques managés |  Pris en charge.<br/><br/> Vous restaurez sur des disques non managés (par défaut), vous convertissez les disques restaurés en disques managés, et vous créez une machine virtuelle avec les disques managés.
Restaurer une machine virtuelle qui a été supprimée. |  Pris en charge.<br/><br/> Vous pouvez restaurer la machine virtuelle à partir d’un point de récupération.
Restaurer une machine virtuelle de contrôleur de domaine qui fait partie d’une configuration à plusieurs contrôleurs de domaine via le portail | Prise en charge si vous restaurez le disque et que vous créez une machine virtuelle à l’aide de PowerShell.
Restaurer la machine virtuelle dans un autre réseau virtuel |    Pris en charge.<br/><br/> Le réseau virtuel doit être dans le même abonnement et région.

## <a name="vm-compute-support"></a>Prise en charge du calcul de machine virtuelle

**Calcul** | **Support**
--- | ---
Taille de la machine virtuelle |   N’importe quelle taille de machine virtuelle Azure avec au moins 2 cœurs d’UC et 1 Go de RAM.<br/><br/> [En savoir plus.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Sauvegarder des machines virtuelles dans des [groupes à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets) |  Pris en charge.<br/><br/> Vous ne pouvez pas restaurer une machine virtuelle dans un jeu à l’aide de l’option pour créer rapidement une machine virtuelle. Au lieu de cela, lorsque vous restaurez la machine virtuelle, restaurer le disque et l’utiliser pour déployer une machine virtuelle, ou restaurer un disque et utilisez-le pour remplacer un disque existant.
Sauvegarder des machines virtuelles dans des [zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview) |  Non pris en charge.
Sauvegarder des machines virtuelles qui sont déployés avec [Hybrid Use Benefit (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) |  Pris en charge.
Sauvegarder des machines virtuelles qui sont déployés dans un [identique](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |  Non pris en charge.
Sauvegarder des machines virtuelles qui sont déployés à partir de la [place de marché Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publié par Microsoft, tiers) |   Pris en charge.<br/><br/> La machine virtuelle doit exécuter un système d’exploitation pris en charge.<br/><br/> Lors de la récupération de fichiers sur la machine virtuelle, vous pouvez restaurer seulement sur un système d’exploitation compatible (pas un système d’exploitation antérieur ou ultérieur).
Sauvegarder des machines virtuelles déployées à partir d’une image personnalisée (tiers) |    Pris en charge.<br/><br/> La machine virtuelle doit exécuter un système d’exploitation pris en charge.<br/><br/> Lors de la récupération de fichiers sur la machine virtuelle, vous pouvez restaurer seulement sur un système d’exploitation compatible (pas un système d’exploitation antérieur ou ultérieur).
Sauvegarder des machines virtuelles qui sont migrées vers Azure  |  Pris en charge.<br/><br/> Pour sauvegarder la machine virtuelle, l’agent de machine virtuelle doit être installé sur la machine migrée.



## <a name="vm-storage-support"></a>Prise en charge du stockage des machines virtuelles

**Composant** | **Support**
--- | ---
Disques de données de machine virtuelle Azure | Sauvegarder une machine virtuelle avec 16 disques de données ou moins.
Taille de disque de données | La taille maximale d’un disque individuel est de 4 095 Go.<br/><br/> Si vos coffres exécutent la dernière version de la sauvegarde Azure (appelée la restauration instantanée), des tailles de disque jusqu'à 4 To sont pris en charge. [Plus d’informations](backup-instant-restore-capability.md)
Type de stockage | Premium de disque dur, disque SSD standard, disque SSD standard. <br/><br/> Disque SSD standard est prise en charge si vos coffres sont mis à niveau vers la dernière version de la sauvegarde de machine virtuelle Azure (connue en tant que la restauration instantanée). [Plus d’informations](backup-instant-restore-capability.md)
Disques gérés |  Pris en charge.
Disques chiffrés |  Pris en charge.<br/><br/> Les machines virtuelles Azure activées avec Azure Disk Encryption (avec ou sans l’application Azure AD) peuvent être sauvegardées.<br/><br/> Les machines virtuelles chiffrées ne peuvent pas être récupérées au niveau fichier/dossier. Vous devez récupérer la machine virtuelle entière.<br/><br/> Vous pouvez activer le chiffrement sur des machines virtuelles qui sont déjà protégées par Sauvegarde Azure.
Disques avec l’accélérateur d’écriture activé | Non pris en charge.<br/><br/> Si vous exécutez la dernière version de la sauvegarde de machine virtuelle Azure (appelée [Restauration instantanée](backup-instant-restore-capability.md)), vous pouvez exclure de la sauvegarde les disques avec l’accélérateur d’écriture activé.
Sauvegarder les disques dédupliqués | Non pris en charge.
Ajouter un disque à une machine virtuelle protégée |  Pris en charge.
Redimensionner un disque sur une machine virtuelle protégée |  Pris en charge.

## <a name="vm-network-support"></a>Prise en charge des réseaux de machines virtuelles


**Composant** | **Support**
--- | ---
Nombre d’interfaces réseau (NIC) | Jusqu’au nombre maximal de cartes réseau prises en charge pour une taille spécifique de machine virtuelle Azure.<br/><br/> Les cartes réseau sont créées lors de la création de la machine virtuelle pendant le processus de restauration.<br/><br/> Le nombre de cartes réseau sur la machine virtuelle restaurée correspond au nombre de cartes réseau présentes sur la machine virtuelle quand vous avez activé la protection. Suppression de NIC après avoir activé la protection n’affecte pas le nombre.
Équilibreur de charge externe/interne |    Pris en charge. <br/><br/> [Découvrez plus d’informations](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sur la restauration de machines virtuelles avec des paramètres réseau spéciaux.
Plusieurs adresses IP réservées |     Pris en charge. <br/><br/> [Découvrez plus d’informations](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sur la restauration de machines virtuelles avec des paramètres réseau spéciaux.
Machines virtuelles avec plusieurs cartes réseau  |  Pris en charge. <br/><br/> [Découvrez plus d’informations](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sur la restauration de machines virtuelles avec des paramètres réseau spéciaux.
Machines virtuelles avec des adresses IP publiques    |  Pris en charge.<br/><br/> Associer une adresse IP publique existante à la carte réseau, ou créer une adresse et l’associer à la carte réseau après que la restauration est effectuée.
Groupe de sécurité réseau sur une carte réseau/un sous-réseau. |    Pris en charge.
Adresse IP réservée (statique) | Non pris en charge.<br/><br/> Vous ne pouvez pas sauvegarder une machine virtuelle avec une adresse IP réservée et aucun point de terminaison défini.
Adresse IP dynamique |     Pris en charge.<br/><br/> Si la carte réseau sur la source de la machine virtuelle utilise l’adressage IP dynamique, par défaut la carte réseau sur la machine virtuelle restaurée allez l’utiliser trop.
Azure Traffic Manager   |  Pris en charge.<br/><br/>Si la machine virtuelle sauvegardée est dans Traffic Manager, ajoutez manuellement la machine virtuelle restaurée à la même instance de Traffic Manager.
Azure DNS |  Pris en charge.
Système DNS personnalisé |     Pris en charge.
Connectivité sortante via un proxy HTTP |  Pris en charge.<br/><br/> Un proxy authentifié n’est pas pris en charge.
Points de terminaison du service Réseau virtuel   |  Pris en charge.<br/><br/> Pare-feu et des paramètres de compte de stockage de réseau virtuel doivent autoriser l’accès à partir de tous les réseaux.



## <a name="vm-security-and-encryption-support"></a>Prise en charge du chiffrement et de sécurité machine virtuelle

La Sauvegarde Azure prend en charge le chiffrement des données en transit et au repos :

Trafic réseau vers Azure :

- Le trafic de sauvegarde à partir de serveurs dans le coffre Recovery Services est chiffré à l’aide de 256 Standard de chiffrement avancé.
- Les données de sauvegarde sont envoyées via une connexion HTTPS sécurisée.
- Les données de sauvegarde sont stockées dans le coffre Recovery Services sous forme chiffrée.
- Vous êtes le seul à connaître la phrase secrète pour déverrouiller ces données. Microsoft ne peut déchiffrer les données de sauvegarde à aucun moment.

  > [!WARNING]
  > Après avoir configuré le coffre, vous avez uniquement accès à la clé de chiffrement. Microsoft ne conserve jamais de copie de la clé de chiffrement et n’y a pas accès. Si la clé est égarée, Microsoft ne peut pas récupérer les données de sauvegarde.

Sécurité des données :

- Lors de la sauvegarde des machines virtuelles Azure, vous devez configurer le chiffrement *dans* la machine virtuelle.
- Sauvegarde Azure prend en charge Azure Disk Encryption, qui utilise BitLocker sur les machines virtuelles Windows et nous **dm-crypt** sur des machines virtuelles Linux.
- Sur le back end, la sauvegarde Azure utilise le [Chiffrement du service de stockage Azure](../storage/common/storage-service-encryption.md), ce qui protège les données au repos.


**Machine** | **En transit** | **Au repos**
--- | --- | ---
Machines Windows locales sans DPM/MAB | ![Oui][green] | ![Oui][green]
Machines virtuelles Azure | ![Oui][green] | ![Oui][green]
Machines virtuelles locales/Azure avec DPM | ![Oui][green] | ![Oui][green]
Machines virtuelles locales/Azure avec MABS | ![Oui][green] | ![Oui][green]



## <a name="vm-compression-support"></a>Prise en charge de la compression des machines virtuelles

Backup prend en charge la compression du trafic de sauvegarde, comme décrit dans le tableau suivant. Notez les points suivants :

- Pour les machines virtuelles Azure, l’extension de machine virtuelle lit les données directement à partir du compte de stockage Azure sur le réseau de stockage. Il n’est pas nécessaire de compresser ce trafic.
- Si vous utilisez DPM ou MABS, vous pouvez économiser la bande passante en compressant les données avant qu’il est sauvegardée dans DPM/de sauvegarde AZURE.

**Machine** | **Compresser dans MABS/DPM (TCP)** | **Compresser dans le coffre (HTTPS)**
--- | --- | ---
Machines Windows locales sans DPM/MAB | N/D | ![Oui][green]
Machines virtuelles Azure | N/D | N/D
Machines virtuelles locales/Azure avec DPM | ![Oui][green] | ![Oui][green]
Machines virtuelles locales/Azure avec MABS | ![Oui][green] | ![OUI][green]


## <a name="next-steps"></a>Étapes suivantes

- [Sauvegarder des machines virtuelles Azure](backup-azure-arm-vms-prepare.md).
- [Sauvegarder des ordinateurs Windows directement](tutorial-backup-windows-server-to-azure.md), sans serveur de sauvegarde.
- [Configurer MABS](backup-azure-microsoft-azure-backup.md) pour la sauvegarde dans Azure, puis sauvegarder des charges de travail dans MABS.
- [Configurer DPM](backup-azure-dpm-introduction.md) pour la sauvegarde dans Azure, puis sauvegarder des charges de travail dans DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
