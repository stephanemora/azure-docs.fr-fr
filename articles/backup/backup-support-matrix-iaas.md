---
title: Tableau de prise en charge pour la sauvegarde de machines virtuelles Azure
description: Fournit un récapitulatif des limitations et des paramètres de prise en charge de la sauvegarde de machines virtuelles Azure avec le service Sauvegarde Azure.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: fa4e01def8db4e5fef32b644d198bad1e9beab27
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996168"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Tableau de prise en charge pour la sauvegarde de machines virtuelles Azure

Vous pouvez utiliser le [service Sauvegarde Azure](backup-overview.md) pour sauvegarder des machines et des charges de travail locales ainsi que des machines virtuelles Azure. Cet article récapitule les paramètres de prise en charge et les limitations de la sauvegarde de machines virtuelles Azure avec Sauvegarde Azure.

Autres tableaux de prise en charge :

- [Tableau de prise en charge générale](backup-support-matrix.md) pour Sauvegarde Azure
- [Tableau de prise en charge](backup-support-matrix-mabs-dpm.md) pour le serveur de sauvegarde Azure/la sauvegarde System Center Data Protection Manager
- [Tableau de prise en charge](backup-support-matrix-mars-agent.md) de la sauvegarde avec l’agent MARS (Microsoft Azure Recovery Services)

## <a name="supported-scenarios"></a>Scénarios pris en charge

Voici comment vous pouvez sauvegarder et restaurer des machines virtuelles Azure avec le service Sauvegarde Azure.

**Scénario** | **Sauvegarde** | **Agent** |**Restauration**
--- | --- | --- | ---
sauvegarde directe de machines virtuelles Azure  | Sauvegardez la totalité de la machine virtuelle.  | Aucun agent n’est nécessaire sur la machine virtuelle Azure. La Sauvegarde Azure installe et utilise une extension l’[agent de machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) qui s’exécute sur la machine virtuelle. | Restaurez comme ceci :<br/><br/> - **Créez une machine virtuelle de base**. Ceci est pratique si la machine virtuelle n’a pas de configuration spéciale, comme plusieurs adresses IP.<br/><br/> - **Restaurer le disque de la machine virtuelle**. Restaurez le disque. Ensuite, attachez-le à une machine virtuelle existante ou créez une machine virtuelle à partir du disque avec PowerShell.<br/><br/> - **Remplacer un disque de la machine virtuelle**. Si une machine virtuelle existe et qu’elle utilise des disques managés (non chiffrés), vous pouvez restaurer un disque et l’utiliser pour remplacer un disque existant sur la machine virtuelle.<br/><br/> - **Restaurer des fichiers/dossiers spécifiques**. Vous pouvez restaurer des fichiers/dossiers d’une machine virtuelle, au lieu de la totalité de la machine virtuelle.
Sauvegarde directe de machines virtuelles Azure (Windows uniquement)  | Sauvegardez des fichiers/dossiers/volumes spécifiques. | Installation de [l'agent Azure Recovery Services](backup-azure-file-folder-backup-faq.md).<br/><br/> Vous pouvez exécuter l’agent MARS en même temps que l’extension de sauvegarde pour l’agent de machine virtuelle Azure pour sauvegarder la machine virtuelle au niveau des fichiers/dossiers. | Restaurer des fichiers/dossiers spécifiques.
Sauvegarder la machine virtuelle Azure sur le serveur de sauvegarde  | Sauvegarder des fichiers/dossiers/volumes ; état du système/fichiers complets ; données d’application sur System Center DPM ou sur le serveur de sauvegarde Microsoft Azure (MABS).<br/><br/> DPM/MABS effectue ensuite la sauvegarde dans le coffre de sauvegarde. | Installez l’agent de protection DPM/MABS sur la machine virtuelle. L’agent MARS est installé sur DPM/MABS.| Restaurer des fichiers/dossiers/volumes ; état du système/fichiers complets ; données d’application.

En savoir plus sur la sauvegarde [à l’aide d’un serveur de sauvegarde](backup-architecture.md#architecture-back-up-to-dpmmabs) et sur les [prérequis de la prise en charge](backup-support-matrix-mabs-dpm.md).

## <a name="supported-backup-actions"></a>Actions de sauvegarde prises en charge

**Action** | **Support**
--- | ---
Activer la sauvegarde quand vous créez une machine virtuelle Azure Windows | Pris en charge pour : <br/><br/> - Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> - Windows Server 2008 R2 (RTM et SP1 Standard)
Activer la sauvegarde quand vous créez une machine virtuelle Linux | Pris en charge pour :<br/><br/> - Ubuntu Server : 18.04, 17.10, 17.04, 16.04 (LTS), 14.04 (LTS)<br/><br/> - Red Hat : RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4<br/><br/> - SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3, 15 <br/><br/> - Debian : 8, 9<br/><br/> - CentOS : 6.9, 7.3<br/><br/> - Oracle Linux : 6.7, 6.8, 6.9, 7.2, 7.3
Sauvegarder une machine virtuelle arrêtée/hors connexion | Pris en charge.<br/><br/> L’instantané a une cohérence en cas d’incident uniquement, mais pas de cohérence des applications.
Sauvegarder des disques après la migration vers des disques managés | Pris en charge.<br/><br/> La sauvegarde continuera à fonctionner. Aucune action n’est requise.
Sauvegarder des disques managés après l’activation d’un verrou de groupe de ressources | Non pris en charge.<br/><br/> La Sauvegarde Azure ne peut pas supprimer les anciens points de restauration et les sauvegardes commenceront à échouer une fois la limite maximale de points de restauration atteinte.
Modifier la stratégie de sauvegarde pour une machine virtuelle | Pris en charge.<br/><br/> La machine virtuelle est sauvegardée selon la planification et les paramètres de conservation de la nouvelle stratégie. Si les paramètres de conservation sont étendus, les points de récupération existants sont marqués et conservés. S’ils sont réduits, les points de récupération existants sont nettoyés lors de la prochaine tâche de nettoyage et ils sont finalement supprimés.
Annuler un travail de sauvegarde| Pris en charge pendant le processus de capture instantanée.<br/><br/> Non pris en charge quand l’instantané est transféré vers le coffre.
Sauvegarder la machine virtuelle vers une autre région ou un autre abonnement |Non pris en charge.
Sauvegardes par jour (via l’extension de machine virtuelle Azure) | Une sauvegarde planifiée par jour.<br/><br/>Le service Sauvegarde Azure prend en charge jusqu’à neuf sauvegardes à la demande par jour, mais Microsoft recommande d’effectuer au maximum quatre sauvegardes quotidiennes à la demande afin de garantir des performances optimales.
Sauvegardes par jour (via l’agent MARS) | Trois sauvegardes planifiées par jour.
Sauvegardes par jour (via DPM/MABS) | Deux sauvegardes planifiées par jour.
Sauvegarde mensuelle/annuelle| Non pris en charge lors de la sauvegarde avec l’extension de machine virtuelle Azure. Seules les sauvegardes quotidiennes et hebdomadaires sont prises en charge.<br/><br/> Vous pouvez configurer la stratégie pour conserver des sauvegardes quotidiennes/hebdomadaires pour une période de conservation mensuelle/annuelle.
Ajustement automatique de l’horloge | Non pris en charge.<br/><br/> La Sauvegarde Azure ne s’ajuste pas automatiquement au changement d’heure lorsque vous sauvegardez une machine virtuelle.<br/><br/>  Modifiez la stratégie manuellement en fonction des besoins.
[Fonctionnalités de sécurité pour la sauvegarde hybride](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |La désactivation des fonctionnalités de sécurité n’est pas prise en charge.
Sauvegarder la machine virtuelle dont l’heure machine a été modifiée | Non pris en charge.<br/><br/> Si l’heure de la machine est remplacée par une heure ultérieure après l’activation de la sauvegarde pour cette machine virtuelle ; toutefois, même si le changement d’heure est rétabli, la réussite de la sauvegarde n’est pas garantie.  

## <a name="operating-system-support-windows"></a>Prise en charge des systèmes d’exploitation (Windows)

Le tableau suivant récapitule les systèmes d’exploitation pris en charge lors de la sauvegarde de machines virtuelles Azure Windows.

**Scénario** | **Système d’exploitation pris en charge**
--- | ---
Sauvegarder avec l’extension de l’agent de machine virtuelle Azure | - Client Windows 10 (uniquement 64 bits) <br/><br/>- Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> - Windows Server 2008 R2 (RTM et SP1 Standard)
Sauvegarder avec l’agent MARS | Systèmes d’exploitation [pris en charge](backup-support-matrix-mars-agent.md#support-for-direct-backups).
Sauvegarder avec DPM/MABS | Systèmes d’exploitation pris en charge pour la sauvegarde avec [MABS](backup-mabs-protection-matrix.md) et [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

Sauvegarde Azure ne prend pas en charge les systèmes d’exploitation 32 bits.

## <a name="support-for-linux-backup"></a>Prise en charge des sauvegardes Linux

Voici ce qui est pris en charge si vous voulez sauvegarder des machines Linux.

**Action** | **Support**
--- | ---
Sauvegarder des machines virtuelles Azure Linux avec l’agent de machine virtuelle Linux Azure Linux | Sauvegarde avec cohérence des fichiers.<br/><br/> Sauvegarde cohérente au niveau application à l’aide de [scripts personnalisés](backup-azure-linux-app-consistent.md).<br/><br/> Pendant la restauration, vous pouvez créer une machine virtuelle, restaurer un disque et l’utiliser pour créer une machine virtuelle, ou restaurer un disque et l’utiliser pour remplacer un disque sur une machine virtuelle existante. Vous pouvez également restaurer des fichiers et des dossiers individuels.
Sauvegarder des machines virtuelles Azure Linux avec l’agent MARS | Non pris en charge.<br/><br/> L’agent MARS ne peut être installé que sur des machines Windows.
Sauvegarder des machines virtuelles Azure Linux avec DPM/MABS | Non pris en charge.

## <a name="operating-system-support-linux"></a>Prise en charge du système d’exploitation (Linux)

Pour les sauvegardes de machines virtuelles Azure Linux, Sauvegarde Azure prend en charge [la liste des distributions Linux approuvées par Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Notez les points suivants :

- Sauvegarde Azure ne prend pas en charge CoreOS Linux.
- Sauvegarde Azure ne prend pas en charge les systèmes d’exploitation 32 bits.
- Les autres distributions « Bring-Your-Own-Linux » peuvent fonctionner, dès lors que l’[agent de machine virtuelle Azure pour Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) est disponible sur la machine virtuelle et que Python est pris en charge.
- La Sauvegarde Azure ne prend pas en charge une machine virtuelle Linux configurée avec un proxy si elle n’a pas Python version 2.7 installé.

## <a name="backup-frequency-and-retention"></a>Fréquence et conservation des sauvegardes

**Paramètre** | **Limites**
--- | ---
Nombre maximal de points de récupération par instance protégée (machine/charge de travail) | 9 999.
Délai d’expiration maximal pour un point de récupération | Aucune limite.
Fréquence maximale de sauvegarde dans le coffre (extension de machine virtuelle Azure) | Une fois par jour.
Fréquence maximale de sauvegarde dans le coffre (agent MARS) | Trois sauvegardes par jour.
Fréquence de sauvegarde maximale dans DPM/MABS | Toutes les 15 minutes pour SQL Server.<br/><br/> Une fois par heure pour les autres charges de travail.
Conservation des points de récupération | Quotidienne, hebdomadaire, mensuelle et annuelle.
Période de rétention maximale | Dépend de la fréquence de sauvegarde.
Points de récupération sur un disque DPM/MAB | 64 pour les serveurs de fichiers et 448 pour les serveurs d’applications.<br/><br/> Les points de récupération sur bande sont illimités pour les DPM locaux.

## <a name="supported-restore-methods"></a>Méthodes de restauration prises en charge

**Méthode de restauration** | **Détails**
--- | ---
Créer une machine virtuelle | Vous pouvez créer une machine virtuelle pendant le processus de restauration. <br/><br/> Cette option produit une machine virtuelle de base opérationnelle. Vous pouvez spécifier le nom de la machine virtuelle, le groupe de ressources, le réseau virtuel, le sous-réseau et le stockage.  
Restaurer un disque | Vous pouvez restaurer un disque et l’utiliser pour créer une machine virtuelle.<br/><br/> Quand vous sélectionnez cette option, Sauvegarde Azure copie les données depuis le coffre vers un compte de stockage que vous sélectionnez. Le travail de restauration génère un modèle. Vous pouvez télécharger ce modèle, l’utiliser pour spécifier des paramètres de machine virtuelle personnalisés et créer une machine virtuelle.<br/><br/> Cette option vous permet de spécifier plus de paramètres que l’option précédente pour créer une machine virtuelle.<br/><br/>
Remplacer un disque existant | Vous pouvez restaurer un disque, puis utiliser le disque restauré pour remplacer un disque qui se trouve actuellement sur une machine virtuelle.
Restaurer des fichiers | Vous pouvez récupérer des fichiers à partir d’un point de récupération sélectionné. Vous téléchargez un script pour monter le disque de machine virtuelle à partir du point de récupération. Ensuite, vous parcourez les volumes de disque pour rechercher les fichiers/dossiers que vous voulez récupérer, et vous démontez le disque quand vous avez terminé.

## <a name="support-for-file-level-restore"></a>Prise en charge de la restauration au niveau fichier

**Restauration** | **Pris en charge**
--- | ---
Restauration de fichiers entre systèmes d’exploitation | Vous pouvez restaurer des fichiers sur n’importe quelle machine ayant le même système d’exploitation (ou un système d’exploitation compatible) que la machine virtuelle sauvegardée. Consultez le [Tableau des systèmes d’exploitation compatibles](backup-azure-restore-files-from-vm.md#system-requirements).
Restauration de fichiers sur des machines virtuelles classiques | Non pris en charge.
Restauration de fichiers provenant de machines virtuelles chiffrées | Non pris en charge.
Restauration de fichiers provenant de comptes de stockage avec des restrictions réseau | Non pris en charge.
Restauration de fichiers sur des machines virtuelles avec des espaces de stockage Windows | Restauration non prise en charge sur la même machine virtuelle.<br/><br/> Au lieu de cela, restaurez les fichiers sur une machine virtuelle compatible.
Restaurer des fichiers sur une machine virtuelle Linux avec des baies LVM/RAID | Restauration non prise en charge sur la même machine virtuelle.<br/><br/> Restaurer sur une machine virtuelle compatible.
Restaurer des fichiers avec des paramètres réseau spéciaux | Restauration non prise en charge sur la même machine virtuelle. <br/><br/> Restaurer sur une machine virtuelle compatible.

## <a name="support-for-vm-management"></a>Prise en charge de la gestion des machines virtuelles

Le tableau suivant récapitule la prise en charge de la sauvegarde pendant les tâches de gestion des machines virtuelles, comme l’ajout ou le remplacement de disques de machine virtuelle.

**Restauration** | **Pris en charge**
--- | ---
Restaurer entre abonnements/régions/zones. | Non pris en charge.
Restaurer sur une machine virtuelle existante | Utilisez l’option de remplacement de disque.
Restaurer un disque avec un compte de stockage activé pour Azure Storage Service Encryption (SSE) | Non pris en charge.<br/><br/> Restaurez sur un compte où SSE n’est pas est activé.
Restaurer sur des comptes de stockage mixtes |Non pris en charge.<br/><br/> Selon le type de compte de stockage, tous les disques restaurés seront ou Premium ou Standard, mais pas mixtes.
Restaurer une machine virtuelle directement sur un groupe à haute disponibilité | Pour les disques managés, vous pouvez restaurer le disque et utiliser l’option de groupe à haute disponibilité dans le modèle.<br/><br/> Non pris en charge pour les disques non managés. Pour les disques non managés, restaurez le disque, puis créez une machine virtuelle dans le groupe à haute disponibilité.
Restaurer une sauvegarde de machines virtuelles non managées après leur mise à niveau en machines virtuelles managées| Pris en charge.<br/><br/> Vous pouvez restaurer les disques, puis créer une machine virtuelle managée.
Restaurer une machine virtuelle sur un point de restauration antérieur à la migration de la machine virtuelle vers des disques managés | Pris en charge.<br/><br/> Vous restaurez sur des disques non managés (par défaut), vous convertissez les disques restaurés en disques managés, et vous créez une machine virtuelle avec les disques managés.
Restaurer une machine virtuelle qui a été supprimée. | Pris en charge.<br/><br/> Vous pouvez restaurer la machine virtuelle à partir d’un point de récupération.
Restaurer une machine virtuelle de contrôleur de domaine qui fait partie d’une configuration à plusieurs contrôleurs de domaine via le portail | Pris en charge si vous restaurez le disque et que vous créez une machine virtuelle avec PowerShell.
Restaurer une machine virtuelle dans un autre réseau virtuel |Pris en charge.<br/><br/> Les réseaux virtuels doivent se trouver dans le même abonnement et la même région.

## <a name="vm-compute-support"></a>Prise en charge du calcul de machine virtuelle

**Calcul** | **Support**
--- | ---
Taille de la machine virtuelle |N’importe quelle taille de machine virtuelle Azure avec au moins 2 cœurs d’UC et 1 Go de RAM.<br/><br/> [En savoir plus.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Sauvegarder des machines virtuelles dans des [groupes à haute disponibilité](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) | Pris en charge.<br/><br/> Vous ne pouvez pas restaurer une machine virtuelle dans un groupe à haute disponibilité en utilisant l’option de création rapide d’une machine virtuelle. Au lieu de cela, quand vous restaurez la machine virtuelle, restaurez le disque et utilisez-le pour déployer une machine virtuelle, ou bien restaurez un disque et utilisez-le pour remplacer un disque existant.
Sauvegarder des machines virtuelles déployées avec [Hybrid Use Benefit (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Pris en charge.
Sauvegarder des machines virtuelles déployées dans un [groupe de machines virtuelles identiques](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |Non pris en charge.
Sauvegarder des machines virtuelles déployées à partir de la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publication par Microsoft ou des tiers) |Pris en charge.<br/><br/> La machine virtuelle doit exécuter un système d’exploitation pris en charge.<br/><br/> Lors de la récupération de fichiers sur la machine virtuelle, vous pouvez restaurer seulement sur un système d’exploitation compatible (pas un système d’exploitation antérieur ou ultérieur). Nous ne restaurons pas les machines virtuelles Azure de la Place de marché Azure sauvegardées en tant que machines virtuelles, car il faut pour cela des informations d’achat, mais uniquement en tant que disques.
Sauvegarder des machines virtuelles déployées à partir d’une image personnalisée (tiers) |Pris en charge.<br/><br/> La machine virtuelle doit exécuter un système d’exploitation pris en charge.<br/><br/> Lors de la récupération de fichiers sur la machine virtuelle, vous pouvez restaurer seulement sur un système d’exploitation compatible (pas un système d’exploitation antérieur ou ultérieur).
Sauvegarder des machines virtuelles migrées vers Azure| Pris en charge.<br/><br/> Pour sauvegarder la machine virtuelle, l’agent de machine virtuelle doit être installé sur la machine migrée.
Cohérence multimachine virtuelle de la sauvegarde | La Sauvegarde Azure ne fournit pas la cohérence des données et des applications sur plusieurs machines virtuelles.
Sauvegarde avec les [Paramètres de diagnostic](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Non prise en charge. <br/><br/> Si la restauration de la machine virtuelle Azure avec les paramètres de diagnostic est déclenchée à l’aide de l’option [Créer](backup-azure-arm-restore-vms.md#create-a-vm), la restauration échoue.
Restauration des machines virtuelles épinglées par zone | Prise en charge (pour une machine virtuelle sauvegardée après janvier 2019 et pour laquelle la [zone de disponibilité](https://azure.microsoft.com/global-infrastructure/availability-zones/) est disponible)<br/><br/>Nous prenons actuellement en charge la restauration dans la même zone que celle épinglée dans des machines virtuelles. Toutefois, si la zone n’est pas disponible, la restauration échoue.
Machines virtuelles de deuxième génération | Pris en charge <br> Sauvegarde Azure prend en charge la sauvegarde et la restauration des [machines virtuelles de deuxième génération](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). Lorsque ces machines virtuelles sont restaurées à partir du point de récupération, elles sont restaurées sous la forme de [machines virtuelles de deuxième génération](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).

## <a name="vm-storage-support"></a>Prise en charge du stockage des machines virtuelles

**Composant** | **Support**
--- | ---
Disques de données de machine virtuelle Azure | Sauvegarder une machine virtuelle avec 16 disques de données ou moins.
Taille de disque de données | La taille d’un disque individuel peut atteindre jusqu’à 32 To et un maximum de 256 To combinés pour tous les disques d’une machine virtuelle.
Type de stockage | HDD Standard, SSD Standard, SSD Premium.
Disques managés | Pris en charge.
Disques chiffrés | Pris en charge (jusqu’à 4 To).<br/><br/> Les machines virtuelles Azure activées pour Azure Disk Encryption peuvent être sauvegardées (avec ou sans l’application Azure AD).<br/><br/> Les machines virtuelles chiffrées ne peuvent pas être récupérées au niveau fichier/dossier. Vous devez récupérer la totalité de la machine virtuelle.<br/><br/> Vous pouvez activer le chiffrement sur des machines virtuelles qui sont déjà protégées par Sauvegarde Azure.
Disques avec l’accélérateur d’écriture activé | Non pris en charge.<br/><br/> La sauvegarde Azure exclut automatiquement les disques avec l’accélérateur d’écriture activé lors de la sauvegarde. Dans la mesure où ils ne sont pas sauvegardés, vous ne pourrez pas restaurer ces disques à partir des points de récupération de la machine virtuelle.
Sauvegarder et restaurer des machines virtuelles/disques dédupliqués | Sauvegarde Azure ne prend pas en charge la déduplication. Pour plus d’informations, consultez cet [article](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) <br/> <br/>  - Sauvegarde Azure n’effectue pas de déduplication entre les machines virtuelles du coffre Recovery Services <br/> <br/>  - S’il existe des machines virtuelles en état de déduplication pendant la restauration, les fichiers ne peuvent pas être restaurés, car le coffre ne comprend pas le format
Ajouter un disque à une machine virtuelle protégée | Pris en charge.
Redimensionner un disque sur une machine virtuelle protégée | Pris en charge.
Stockage partagé| La Sauvegarde des machines virtuelles à l’aide d’un Volume partagé de Cluster (CSV) ou d’un Serveur de fichiers avec montée en puissance parallèle n’est pas recommandée. En effet, il existe un risque d’échec pour les enregistreurs de volumes partagés de cluster lors de la sauvegarde. Lors de la restauration, les disques contenant des volumes partagés de cluster risquent de ne pas apparaître.

## <a name="vm-network-support"></a>Prise en charge des réseaux de machines virtuelles

**Composant** | **Support**
--- | ---
Nombre d'interfaces réseau (NIC) | Jusqu’au nombre maximal de cartes réseau prises en charge pour une taille spécifique de machine virtuelle Azure.<br/><br/> Les cartes réseau sont créées lors de la création de la machine virtuelle pendant le processus de restauration.<br/><br/> Le nombre de cartes réseau sur la machine virtuelle restaurée correspond au nombre de cartes réseau présentes sur la machine virtuelle quand vous avez activé la protection. La suppression de cartes réseau après l’activation de la protection n’affecte pas leur nombre.
Équilibreur de charge externe/interne |Pris en charge. <br/><br/> [Découvrez plus d’informations](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sur la restauration de machines virtuelles avec des paramètres réseau spéciaux.
Plusieurs adresses IP réservées |Pris en charge. <br/><br/> [Découvrez plus d’informations](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sur la restauration de machines virtuelles avec des paramètres réseau spéciaux.
Machines virtuelles avec plusieurs cartes réseau| Pris en charge. <br/><br/> [Découvrez plus d’informations](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) sur la restauration de machines virtuelles avec des paramètres réseau spéciaux.
Machines virtuelles avec des adresses IP publiques| Pris en charge.<br/><br/> Associez une adresse IP publique existante à la carte réseau, ou bien créez une adresse et associez-la à la carte réseau une fois la restauration effectuée.
Groupe de sécurité réseau sur une carte réseau/un sous-réseau. |Pris en charge.
Adresse IP statique | Non pris en charge.<br/><br/> Une machine virtuelle créée à partir d’un point de restauration reçoit une adresse IP dynamique.<br/><br/> Pour les machines virtuelles Classic, vous ne pouvez pas sauvegarder une machine virtuelle avec une adresse IP réservée et sans point de terminaison défini.
Adresse IP dynamique |Pris en charge.<br/><br/> Si la carte réseau sur la machine virtuelle source utilise l’adressage IP dynamique, par défaut, la carte réseau sur la machine virtuelle restaurée l’utilise aussi.
Azure Traffic Manager| Pris en charge.<br/><br/>Si la machine virtuelle sauvegardée est dans Traffic Manager, ajoutez manuellement la machine virtuelle restaurée à la même instance Traffic Manager.
Azure DNS |Pris en charge.
Système DNS personnalisé |Pris en charge.
Connectivité sortante via un proxy HTTP | Pris en charge.<br/><br/> Un proxy authentifié n’est pas pris en charge.
Points de terminaison de service de réseau virtuel| Pris en charge.<br/><br/> Les paramètres du compte de stockage pour le pare-feu et le réseau virtuel doivent autoriser l’accès depuis tous les réseaux.

## <a name="vm-security-and-encryption-support"></a>Prise en charge de la sécurité et du chiffrement des machines virtuelles

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
- La Sauvegarde Azure prend en charge Azure Disk Encryption, qui utilise BitLocker sur les machines virtuelles Windows et **dm-crypt** sur les machines virtuelles Linux.
- Sur le back end, la sauvegarde Azure utilise le [Chiffrement du service de stockage Azure](../storage/common/storage-service-encryption.md), ce qui protège les données au repos.

**Machine** | **En transit** | **Au repos**
--- | --- | ---
Machines Windows locales sans DPM/MAB | ![OUI][green] | ![OUI][green]
Machines virtuelles Azure | ![OUI][green] | ![OUI][green]
Machines virtuelles locales/Azure avec DPM | ![OUI][green] | ![OUI][green]
Machines virtuelles locales/Azure avec MABS | ![OUI][green] | ![OUI][green]

## <a name="vm-compression-support"></a>Prise en charge de la compression des machines virtuelles

Le service Sauvegarde prend en charge la compression du trafic de sauvegarde, comme décrit dans le tableau suivant. Notez les points suivants :

- Pour les machines virtuelles Azure, l’extension de machine virtuelle lit directement les données à partir du compte de stockage Azure via le réseau de stockage. Il n’est donc pas nécessaire de compresser ce trafic.
- Si vous utilisez DPM ou MABS, vous pouvez économiser la bande passante en compressant les données avant qu’elles soient sauvegardées dans DPM/MABS.

**Machine** | **Compresser dans MABS/DPM (TCP)** | **Compresser dans le coffre (HTTPS)**
--- | --- | ---
Machines Windows locales sans DPM/MAB | N/D | ![OUI][green]
Machines virtuelles Azure | N/D | N/D
Machines virtuelles locales/Azure avec DPM | ![OUI][green] | ![OUI][green]
Machines virtuelles locales/Azure avec MABS | ![OUI][green] | ![OUI][green]

## <a name="next-steps"></a>Étapes suivantes

- [Sauvegarder des machines virtuelles Azure](backup-azure-arm-vms-prepare.md).
- [Sauvegarder des ordinateurs Windows directement](tutorial-backup-windows-server-to-azure.md), sans serveur de sauvegarde.
- [Configurer MABS](backup-azure-microsoft-azure-backup.md) pour la sauvegarde dans Azure, puis sauvegarder des charges de travail dans MABS.
- [Configurer DPM](backup-azure-dpm-introduction.md) pour la sauvegarde dans Azure, puis sauvegarder des charges de travail dans DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
