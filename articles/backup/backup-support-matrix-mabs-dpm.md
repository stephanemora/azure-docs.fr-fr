---
title: Tableau de prise en charge pour le serveur de sauvegarde Microsoft Azure et System Center DPM
description: Cet article décrit la prise en charge de Sauvegarde Azure quand vous utilisez un serveur de sauvegarde Microsoft Azure ou System Center DPM pour sauvegarder des ressources locales et celles de machines virtuelles Azure.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 704bb409d2b21e2ae258dbb2d627b1c088d80db7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60254652"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Matrice de prise en charge pour la sauvegarde avec Microsoft Azure Backup Server ou System Center DPM

Vous pouvez utiliser la [service Azure Backup](backup-overview.md) pour sauvegarder des machines locales et les charges de travail et les machines virtuelles (VM) Azure. Cet article résume les paramètres de prise en charge et les limitations pour la sauvegarde des machines à l’aide de Microsoft Azure Backup Server (MABS) ou System Center Data Protection Manager (DPM) et sauvegarde Azure.

## <a name="about-dpmmabs"></a>À propos de DPM/de sauvegarde AZURE

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) est une solution d’entreprise qui configure, facilite et gère la sauvegarde et récupération des machines de l’entreprise et de données. Cette solution fait partie de la suite de produits [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing).

Serveur de sauvegarde AZURE est un produit serveur qui peut être utilisé pour sauvegarder des serveurs physiques locaux, les machines virtuelles et les applications qui s’exécutent sur ces derniers.

Serveur de sauvegarde AZURE est basé sur System Center DPM et fournit une fonctionnalité similaire avec quelques différences :
- Aucune licence System Center n’est nécessaire pour exécuter MABS.
- Pour le serveur de sauvegarde AZURE et DPM, Azure fournit un stockage de sauvegarde à long terme. DPM vous permet aussi de sauvegarder des données sur bande pour le stockage à long terme. MABS n’offre pas cette fonctionnalité.

Téléchargement de serveur de sauvegarde AZURE à partir de la [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=57520). Il peut être exécuté en local ou sur une machine virtuelle Azure.

DPM et MABS prennent en charge la sauvegarde d’un large éventail d’applications et de systèmes d’exploitation (serveur et clients). Ils proposent plusieurs scénarios de sauvegarde :

- Vous pouvez sauvegarder au niveau de la machine avec une sauvegarde de l’état du système ou une sauvegarde complète.
- Vous pouvez sauvegarder des volumes, des partages, des dossiers et des fichiers spécifiques.
- Vous pouvez sauvegarder des applications spécifiques à l’aide des paramètres application prenant en charge optimisées.

## <a name="dpmmabs-backup"></a>Sauvegarde DPM/de sauvegarde AZURE

Sauvegarde à l’aide de DPM/de sauvegarde AZURE et la sauvegarde Azure fonctionne comme suit :

1. Agent de protection de DPM/de sauvegarde AZURE est installé sur chaque ordinateur qui sera sauvegardée.
1. Les applications et les machines sont sauvegardées dans le stockage local sur DPM/de sauvegarde AZURE.
1. L’agent MARS (Microsoft Azure Recovery Services) est installé sur le serveur DPM/MABS.
1. L’agent MARS sauvegarde les disques DPM/de sauvegarde AZURE dans un coffre Recovery Services de sauvegarde dans Azure à l’aide de sauvegarde Azure.

Pour plus d'informations :

- [Découvrez-en plus](backup-architecture.md#architecture-back-up-to-dpmmabs) sur l’architecture MABS.
- [Passez en revue ce qui est pris en charge](backup-support-matrix-mars-agent.md) pour l’agent MARS.

## <a name="supported-scenarios"></a>Scénarios pris en charge 

**Scénario** | **Agent** | **Lieu**
--- | --- | ---
**Sauvegarde de machines/charges de travail locales** | Agent de protection de DPM/de sauvegarde AZURE s’exécute sur les ordinateurs que vous souhaitez sauvegarder.<br/><br/> L’agent MARS sur le serveur DPM/de sauvegarde AZURE. | DPM/de sauvegarde AZURE doit être en cours d’exécution en local.
**Sauvegarde de machines virtuelles/charges de travail Azure** | Agent de protection de DPM/de sauvegarde AZURE sur l’ordinateur protégé.<br/><br/> L’agent MARS sur le serveur DPM/de sauvegarde AZURE. | MABS/DPM doit s’exécuter sur une machine virtuelle Azure.

## <a name="supported-deployments"></a>Déploiements pris en charge

DPM/de sauvegarde AZURE peut être déployé comme résumé dans le tableau suivant.

**Déploiement** | **Support** | **Détails**
--- | --- | ---
**Déploiement local** | Serveur physique<br/><br/>Machine virtuelle Hyper-V<br/><br/> Machine virtuelle VMware | Si DPM/de sauvegarde AZURE est installé comme une VM VMware, il sauvegarde uniquement les machines virtuelles VMware et des charges de travail qui sont exécutent sur ces machines virtuelles.
**Déployé comme machine virtuelle Azure Stack** | MABS uniquement | Vous ne pouvez pas utiliser DPM pour sauvegarder des machines virtuelles Azure Stack.
**Déployé comme machine virtuelle Azure** | Protège les machines virtuelles Azure et les charges de travail qui sont exécutent sur ces machines virtuelles | DPM/de sauvegarde AZURE s’exécutant dans Azure ne peut pas sauvegarder des machines locales.


## <a name="supported-mabs-and-dpm-operating-systems"></a>Systèmes d’exploitation pris en charge pour MABS et DPM

Azure peut sauvegarder les instances DPM/de sauvegarde AZURE qui exécutent un des systèmes d’exploitation suivants. Les systèmes d’exploitation doivent exécuter les derniers Service Packs et les dernières mises à jour.

**Scénario** | **DPM/MABS** 
--- | --- 
**MABS sur une machine virtuelle Azure** |  Windows Server 2012 R2.<br/><br/> Windows 2016 Datacenter.<br/><br/> Centre de données Windows 2019.<br/><br/> Nous vous recommandons de commencer avec une image à partir de la place de marché.<br/><br/> Minimum A2 Standard avec deux cœurs et 3,5 Go de RAM. 
**DPM sur une machine virtuelle Azure** | System Center 2012 R2 avec Update 3 ou ultérieur.<br/><br/> Système d’exploitation Windows conforme aux [exigences de System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Nous vous recommandons de commencer avec une image à partir de la place de marché.<br/><br/> Minimum A2 Standard avec deux cœurs et 3,5 Go de RAM. 
**MABS localement** | Systèmes d’exploitation 64 bits pris en charge :<br/><br/> Serveur de sauvegarde AZURE v3 et versions ultérieures : Windows Server 2019 (Standard, Datacenter, Essentials). <br/><br/> MABS v2 et versions ultérieures : Windows Server 2016 (Standard, Datacenter, Essentials).<br/><br/> Toutes les versions de MABS : Windows Server 2012 R2, Windows Server 2012 (Standard, Datacenter, Foundation).<br/><br/>Toutes les versions de MABS : Windows Storage Server 2012 R2, Windows Server 2012 (Standard, un groupe de travail).
**DPM localement** | Serveur physique/machine virtuelle Hyper-V : System Center 2012 SP1 ou ultérieur.<br/><br/> Machine virtuelle VMware : System Center 2012 R2 avec Update 5 ou ultérieur. 



## <a name="management-support"></a>Prise en charge de la gestion

**Problème** | **Détails**
--- | ---
**Installation** | Installez DPM/de sauvegarde AZURE sur un ordinateur à fonction unique.<br/><br/> N’installez pas DPM/de sauvegarde AZURE sur un contrôleur de domaine, sur un ordinateur avec l’installation du rôle serveur d’applications, sur un ordinateur qui exécute Microsoft Exchange Server ou System Center Operations Manager ou sur un nœud de cluster.<br/><br/> [Passez en revue toutes les exigences de système DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Domaine** | DPM/de sauvegarde AZURE doit être joint à un domaine. Effectuez d’abord l’installation, puis joignez DPM/MABS à un domaine. Le déplacement de DPM/MABS vers un nouveau domaine après le déploiement n’est pas pris en charge.
**Stockage** | Stockage de sauvegarde Modern (MBS) est prise en charge de DPM 2016/de sauvegarde AZURE v2 et versions ultérieures. Il n’est pas disponible pour MABS v1.
**Mise à niveau de MABS** | Vous pouvez installer directement MABS v3 ou effectuer la mise à niveau vers MABS v3 à partir de MABS v2. [Plus d’informations](backup-azure-microsoft-azure-backup.md#upgrade-mabs)
**Déplacement de MABS** | Il est possible de déplacer MABS sur un nouveau serveur tout en conservant le stockage si vous utilisez MBS.<br/><br/> Le serveur doit avoir le même nom que l’original. Vous ne pouvez pas changer le nom si vous souhaitez conserver le même pool de stockage et utiliser la même base de données MABS pour stocker les points de récupération de données.<br/><br/> Vous devez effectuer une sauvegarde de la base de données MABS, car vous devrez la restaurer.


## <a name="mabs-support-on-azure-stack"></a>Prise en charge de MABS sur Azure Stack

Vous pouvez déployer MABS sur une machine virtuelle Azure Stack pour gérer la sauvegarde des machines virtuelles et des charges de travail Azure Stack à partir d’un même emplacement.

**Composant** | **Détails**
--- | --- 
**MABS sur une machine virtuelle Azure Stack** | Au moins la taille A2. Nous vous recommandons de que commencer avec une image Windows Server 2012 R2 ou Windows Server 2016 à partir de la place de marché Azure.<br/><br/> N’installez pas rien d’autre sur la VM MABS.
**Stockage MABS** | Utiliser un compte de stockage distinct pour la VM de MABS. L’agent MARS s’exécutant sur le serveur de sauvegarde AZURE a besoin de stockage temporaire pour un emplacement du cache et pour stocker les données restaurées à partir du cloud.
**Pool de stockage MABS** | La taille du pool de stockage de serveur de sauvegarde AZURE est déterminée par le nombre et la taille des disques sont attachés à la VM de MABS. Chaque taille de machine virtuelle Azure Stack correspond à un nombre maximal de disques. Par exemple, la taille A2 correspond à quatre disques.
**Conservation MABS** | Ne conservent pas les données sauvegardées sur des disques locaux du serveur de sauvegarde AZURE pendant plus de cinq jours.
**Scale-up de MABS** | Pour effectuer un scale-up de votre déploiement, vous pouvez augmenter la taille de la machine virtuelle MABS. Par exemple, vous pouvez modifier de A à la série D.<br/><br/> Vous pouvez également vous assurer que vous êtes déchargement de données avec une sauvegarde sur Azure régulièrement. Si nécessaire, vous pouvez déployer des serveurs MABS supplémentaires.
**.NET framework sur le serveur de sauvegarde AZURE** | La VM MABS a besoin de .NET Framework SP1 3.3 ou version ultérieure installé dessus.
**Domaine MABS** | La machine virtuelle MABS doit être jointe à un domaine. Un utilisateur de domaine disposant de privilèges administratifs doit installer MABS sur la machine virtuelle.
**Sauvegarde des données d’une machine virtuelle Azure Stack** | Vous pouvez sauvegarder des fichiers, des dossiers et des applications.
**Sauvegarde prise en charge** | Ces systèmes d’exploitation sont pris en charge pour les machines virtuelles que vous souhaitez sauvegarder :<br/><br/> Canal semi-annuel de serveur Windows (Datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2
**Prise en charge de SQL Server pour machines virtuelles Azure Stack** | Sauvegarder SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1.<br/><br/> Sauvegarder et restaurer une base de données.
**Prise en charge de SharePoint pour les machines virtuelles Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Sauvegarder et restaurer une batterie de serveurs, une base de données, un front-end et un serveur web.
**Configuration réseau requise pour les machines virtuelles sauvegardées** | Toutes les machines virtuelles dans la charge de travail Azure Stack doivent appartenir au même réseau virtuel et appartiennent au même abonnement.

## <a name="dpmmabs-networking-support"></a>Prise en charge du réseau pour DPM/MABS

### <a name="url-access"></a>Accès URL

Le serveur DPM/MABS doit accéder à ces URL :

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.MicrosoftAzure.com
- *.microsoftonline.com
- * .windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Connectivité de DPM/MABS à Sauvegarde Azure

La connectivité au service Sauvegarde Azure est obligatoire pour que les sauvegardes fonctionnent correctement, et l’abonnement Azure doit être actif. Le tableau suivant montre ce qui se passe si ces deux conditions ne sont pas remplies.

**MABS à Azure** | **Abonnement** | **Sauvegarde/restauration** 
--- | --- | --- 
Connecté | Actif | Sauvegarder sur le disque DPM/de sauvegarde AZURE.<br/><br/> Sauvegarder sur Azure.<br/><br/> Restaurer à partir du disque.<br/><br/> Restaurer à partir d’Azure.
Connecté | Expiré/déprovisionné | Aucune sauvegarde sur disque ou Azure.<br/><br/> Si l’abonnement a expiré, vous pouvez restaurer à partir de disque ou d’Azure.<br/><br/> Si l’abonnement est désactivé, vous ne pouvez pas restaurer à partir de disque ou d’Azure. Les points de récupération Azure sont supprimés.
Aucune connectivité pendant plus de 15 jours | Actif | Aucune sauvegarde sur disque ou Azure.<br/><br/> Vous pouvez restaurer à partir d’un disque ou d’Azure.
Aucune connectivité pendant plus de 15 jours | Expiré/déprovisionné | Aucune sauvegarde sur disque ou Azure.<br/><br/> Si l’abonnement a expiré, vous pouvez restaurer à partir de disque ou d’Azure.<br/><br/> Si l’abonnement est désactivé, vous ne pouvez pas restaurer à partir de disque ou d’Azure. Les points de récupération Azure sont supprimés.

## <a name="dpmmabs-storage-support"></a>Prise en charge du stockage pour DPM/MABS

Les données sont sauvegardées dans DPM/de sauvegarde AZURE sont stockées sur le stockage sur disque local. 

**Stockage** | **Détails**
--- | ---
**MBS** | Stockage de sauvegarde Modern (MBS) est prise en charge de DPM 2016/de sauvegarde AZURE v2 et versions ultérieures. Il n’est pas disponible pour MABS v1.
**Stockage MABS sur une machine virtuelle Azure** | Données sont stockées sur des disques Azure qui sont attachés à la machine virtuelle DPM/de sauvegarde AZURE, et qui sont gérés dans DPM/de sauvegarde AZURE. Le nombre de disques qui peut être utilisé pour le pool de stockage DPM/de sauvegarde AZURE est limité par la taille de la machine virtuelle.<br/><br/> Machine virtuelle A2 : 4 disques ; machine virtuelle A3 : 8 disques ; machine virtuelle A4 : 16 disques, avec une taille maximale de 1 To pour chaque disque. Ce paramètre détermine le pool de stockage de sauvegarde total n’est disponible.<br/><br/> La quantité de données que vous pouvez sauvegarder varie selon le nombre et la taille des disques attachés.
**Conservation des données MABS sur une machine virtuelle Azure** | Nous vous recommandons de conserver les données pendant une journée sur le disque DPM/de sauvegarde AZURE Azure sauvegarder à partir de DPM/de sauvegarde AZURE dans le coffre pour la conservation à long terme. Vous pouvez ainsi protéger une plus grande quantité de données en les déchargeant sur Sauvegarde Azure.


### <a name="modern-backup-storage-mbs"></a>Stockage de sauvegarde moderne (MBS)
À partir de DPM 2016/de sauvegarde AZURE v2 (en cours d’exécution sur Windows Server 2016) et versions ultérieures, vous pouvez tirer parti du stockage de sauvegarde moderne (MBS).

- Les sauvegardes MBS sont stockées sur un disque ReFS (Resilient File System).
- MBS utilise un bloc ReFS pour les sauvegardes plus rapides et une utilisation plus efficace de l’espace de stockage de clonage.
- Lorsque vous ajoutez des volumes au pool de stockage DPM/de sauvegarde AZURE local, configurez-les avec les lettres de lecteur. Vous pouvez ensuite configurer le stockage de charge de travail sur des volumes différents.
- Quand vous créez des groupes de protection pour sauvegarder des données sur DPM/MABS, vous sélectionnez le lecteur que vous souhaitez utiliser. Par exemple, vous pourrez stocker des sauvegardes pour SQL ou autre lecteur de charges de travail hautes performances d’e/s élevé et stockez les charges de travail sauvegardées moins fréquemment sur un lecteur de performances inférieur.


## <a name="supported-backups-to-mabs"></a>Sauvegardes prises en charge sur MABS

Le tableau suivant récapitule ce que vous pouvez sauvegarder sur MABS à partir de machines locales et de machines virtuelles Azure.


**Sauvegarde** | **Versions** | **MABS** | **Détails** |
--- | --- | --- | --- |
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 bits) | MABS v3, v2, V1 | Local. | Volume/partage/dossier/fichier.<br/><br/> Volumes dédupliqués pris en charge.<br/><br/> Les volumes doivent être d’au moins 1 Go et être au format NTFS. |
**Windows Server 2016 (Datacenter, Standard, pas Nano)**<br/><br/> 64/32 bits | MABS v3, v2 | Local/machine virtuelle Azure.| Volume/partage/dossier/fichier, état système/sauvegarde complète.<br/><br/> Volumes dédupliqués pris en charge. |
**Windows Server 2012 R2 (Datacenter et Standard)**<br/><br/> 64/32 bits | MABS v3, v2, v1 | Local/machine virtuelle Azure. | **Protection locale** : Volume/partage/dossier/fichier, état système/sauvegarde complète.<br/><br/> **Protection des machines virtuelles Azure** : Volume/partage/dossier/fichier.<br/><br/> Volumes dédupliqués pris en charge. |
**Windows Server 2012 avec SP1 (Datacenter et Standard)**<br/><br/> 64/32 bits | MABS v3, v2, v1<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) doit être installé. | Local/machine virtuelle Azure. | **Protection locale** : Volume/partage/dossier/fichier, état système/sauvegarde complète.<br/><br/> **Protection des machines virtuelles Azure** : Volume/partage/dossier/fichier.<br/><br/> Volumes dédupliqués pris en charge. |
**Windows Server 2012 (Datacenter et Standard)**<br/><br/> 64/32 bits | MABS v1 | Local/machine virtuelle Azure. | **Protection locale** : Volume/partage/dossier/fichier, état système/sauvegarde complète.<br/><br/> **Protection des machines virtuelles Azure** : Volume/partage/dossier/fichier.<br/><br/> Volumes dédupliqués pris en charge. |
**Windows 2008 R2 avec SP1 (Standard et Enterprise)**<br/><br/> 64/32 bits | Pris en charge par MABS v3, v2, v1.<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) doit être installé. | Local/machine virtuelle Azure. |   **Protection locale** : Volume/partage/dossier/fichier, état système/sauvegarde complète.<br/><br/> **Protection des machines virtuelles Azure** : Volume/partage/dossier/fichier.<br/><br/> Volumes dédupliqués pris en charge. |
**Windows 2008 R2 (Standard et Enterprise)**<br/><br/> 64/32 bits | MABS v1. Pour MABS v2/v3, le système d’exploitation doit exécuter le SP1. | Local/machine virtuelle Azure. | **Protection locale** : Volume/partage/dossier/fichier, état système/sauvegarde complète.<br/><br/> **Protection des machines virtuelles Azure** : Volume/partage/dossier/fichier.<br/><br/> Volumes dédupliqués pris en charge. |
**Windows Server 2008 avec SP2**<br/><br/> 64/32 bits | MABS v1, v2, v3 | Seul v1 est pris en charge quand MABS est déployé comme machine physique locale ou machine virtuelle Hyper-V.<br/><br/> MABS v1, v2, v3 est pris en charge quand MABS est déployé comme machine virtuelle VMware.<br/><br/> Non pris en charge si MABS s’exécute sur une machine virtuelle Azure. | Volume/partage/dossier/fichier, état système/sauvegarde complète. |
**Windows Storage Server 2008** | MABS v1, v2, v3 | Serveur de sauvegarde AZURE en local physique/Hyper-V server machine virtuelle. <br/><br/> Non pris en charge si MABS s’exécute sur une machine virtuelle Azure. | Volume/partage/dossier/fichier, état système/sauvegarde complète.
**SQL Server 2017** | MABS v3 | Local/machine virtuelle Azure.| Sauvegarder une base de données SQL Server.<br/><br/> Sauvegarde de cluster SQL Server prise en charge.<br/><br/>Bases de données stockées sur des CVS non prises en charge. |
**SQL Server 2016/2016 avec SP1** | MABS v3, v2 | Local/machine virtuelle Azure.| Sauvegarder une base de données SQL Server.<br/><br/> Sauvegarde de cluster SQL Server prise en charge.<br/><br/>Bases de données stockées sur des CVS non prises en charge. |
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3, v2, v1 | Local/machine virtuelle Azure.| Sauvegarder une base de données SQL Server.<br/><br/> Sauvegarde de cluster SQL Server prise en charge.<br/><br/>Bases de données stockées sur des CVS non prises en charge. |
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3, v2, V1 | Local. | Sauvegarder le serveur autonome Exchange, base de données sous un DAG.<br/><br/> Récupérer une boîte aux lettres, des bases de données de boîtes aux lettres sous un DAG.<br/><br/> ReFS non pris en charge.<br/><br/> Sauvegarder des clusters de disque non partagés.<br/><br/> Sauvegarder Exchange Server configuré pour la réplication continue. |
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | MABS v3, v2, v1 | Local/machine virtuelle Azure. | Sauvegarder la batterie de serveurs, serveur web frontal.<br/><br/> Récupérer une batterie de serveurs, base de données, application web, fichier ou élément de liste, recherche SharePoint, serveur web frontal.<br/><br/> Vous ne pouvez pas sauvegarder une batterie de serveurs à l’aide de SQL Server AlwaysOn pour les bases de données de contenu. |
**Hyper-V sur Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (avec SP1)** | MABS v3, v2, v1 | Local. | **Agent MABS sur un hôte Hyper-V** : sauvegarder des machines virtuelles entières et des fichiers de données hôtes. Sauvegarder des machines virtuelles avec un stockage local, des machines virtuelles dans un cluster avec un stockage CSV et des machines virtuelles avec un stockage sur serveur de fichiers SMB.<br/><br/> **Agent MABS sur une machine virtuelle invitée** : sauvegarder des charges de travail exécutées sur la machine virtuelle. Volumes partagés de cluster (CSV).<br/><br/> **Récupération** : machine virtuelle, récupération au niveau de l’élément de disques durs virtuels/volumes/dossiers/fichiers.<br/><br/> **Machines virtuelles Linux** : Sauvegarder lorsque Hyper-V est en cours d’exécution sur Windows Server 2012 R2 et versions ultérieures. Pour les machines virtuelles Linux, la récupération porte sur toute la machine. |
**Machines virtuelles VMware : vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2, v1<br/><br/> MABS v1 nécessite le correctif cumulatif 1) | Local. | Sauvegarder des machines virtuelles VMware sur le stockage des volumes partagés de cluster, NFS et SAN.<br/><br/> Récupérer la machine virtuelle entière.<br/><br/> Sauvegarde Windows/Linux.<br/><br/> Récupération au niveau élément des dossiers/fichiers pour les machines virtuelles Windows uniquement.<br/><br/> Les applications virtuelles VMware ne sont pas prises en charge.<br/><br/> Pour les machines virtuelles Linux, la récupération porte sur toute la machine. | 



## <a name="supported-backups-to-dpm"></a>Sauvegardes prises en charge sur DPM

Le tableau suivant récapitule ce que vous pouvez sauvegarder sur DPM à partir de machines locales et de machines virtuelles Azure.



**Sauvegarde** | **DPM** | **Détails**
--- | --- | --- 
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 bits) | Local uniquement.<br/><br/> Pour sauvegarder Windows 10 avec DPM 2012 R2, nous vous recommandons d’installer [11 de la mise à jour](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager). | Volume/partage/dossier/fichier.<br/><br/> Volumes dédupliqués pris en charge.<br/><br/> Les volumes doivent être d’au moins 1 Go et être au format NTFS.
**Windows Server 2016 (Datacenter, Standard, pas Nano)**<br/><br/> 64/32 bits | Local/machine virtuelle Azure.<br/><br/> DPM 2016.| Volume/partage/dossier/fichier, état système/sauvegarde complète.<br/><br/> Volumes dédupliqués pris en charge.
**Windows Server 2012 R2 (Datacenter et Standard)**<br/><br/> 64/32 bits | Local/machine virtuelle Azure. | **Protection locale** : Volume/partage/dossier/fichier, état système/sauvegarde complète.<br/><br/> **Protection des machines virtuelles Azure** : Volume/partage/dossier/fichier.<br/><br/> Volumes dédupliqués compatible avec DPM 2012 R2 et versions ultérieures.
**Windows Server 2012 avec SP1 (Datacenter et Standard)**<br/><br/> 64/32 bits | Local/machine virtuelle Azure. | **Protection locale** : Volume/partage/dossier/fichier, état système/sauvegarde complète.<br/><br/> **Protection des machines virtuelles Azure** : Volume/partage/dossier/fichier.<br/><br/> Volumes dédupliqués compatible avec DPM 2012 R2 et versions ultérieures.
**Windows Server 2012 (Datacenter et Standard)**<br/><br/> 64/32 bits |  Local/machine virtuelle Azure. | **Protection locale** : Volume/partage/dossier/fichier, état système/sauvegarde complète.<br/><br/> **Protection des machines virtuelles Azure** : Volume/partage/dossier/fichier.<br/><br/> Volumes dédupliqués compatible avec DPM 2012 R2 et versions ultérieures.
**Windows 2008 R2 avec SP1 (Standard et Enterprise)**<br/><br/> 64/32 bits | Local/machine virtuelle Azure.<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) doit être installé. |   **Protection locale** : Volume/partage/dossier/fichier, état système/sauvegarde complète.<br/><br/> **Protection des machines virtuelles Azure** : Volume/partage/dossier/fichier.
**Windows 2008 R2 (Standard et Enterprise)**<br/><br/> 64/32 bits | Local.<br/><br/> DPM ne peut pas être installé comme machine virtuelle VMware.<br/><br/> DPM s’exécutant sur une machine virtuelle Azure n’est pas pris en charge. | **Protection locale** : Volume/partage/dossier/fichier, état système/sauvegarde complète.
**Windows Server 2008 avec SP2**<br/><br/> 64/32 bits | Local uniquement.<br/><br/> DPM est pris en charge quand il s’exécute comme machine virtuelle VMware. L’exécution comme serveur physique ou machine virtuelle Hyper-V n’est pas prise en charge. | Volume/partage/dossier/fichier, état système/sauvegarde complète.
**Windows Storage Server 2008** | DPM en local exécuté comme serveur physique ou machine virtuelle Hyper-V. | Volume/partage/dossier/fichier, état système/sauvegarde complète.
**SQL Server 2017** | CONSOLE SAC DPM ; DPM 2016 en cours d’exécution cumulatif 5 ou version ultérieure.<br/><br/> Local/machine virtuelle Azure.| Sauvegarder une base de données SQL Server.<br/><br/> Sauvegarde de cluster SQL Server prise en charge.<br/><br/>Bases de données stockées sur des CVS non prises en charge.
**SQL Server 2016 avec SP1** | Non pris en charge pour DPM 2012 R2 ; pris en charge pour DPM SAC, DPM 2016 exécutant le correctif cumulatif 4 ou ultérieur.<br/><br/> Local/machine virtuelle Azure.| Sauvegarder une base de données SQL Server.<br/><br/> Sauvegarde de cluster SQL Server prise en charge.<br/><br/>Bases de données stockées sur des CVS non prises en charge.
**SQL Server 2016** | Non pris en charge pour DPM 2012 R2. Prise en charge pour la console SAC de DPM, DPM 2016 à partir de la mise à jour de correctif cumulatif 2 et versions ultérieures.<br/><br/> Local/machine virtuelle Azure.| Sauvegarder une base de données SQL Server.<br/><br/> Sauvegarde de cluster SQL Server prise en charge.<br/><br/>Bases de données stockées sur des CVS non prises en charge.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | SQL Server 2014 avec DPM 2012 R2 exécutant le correctif cumulatif 4 et versions ultérieures.<br/><br/> Local/machine virtuelle Azure.| Sauvegarder une base de données SQL Server.<br/><br/> Sauvegarde de cluster SQL Server prise en charge.<br/><br/>Bases de données stockées sur des CVS non prises en charge.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Pour Exchange 2016, DPM 2012 R2 nécessite le correctif cumulatif 9 ou ultérieur.<br/><br/> Local | Sauvegarder le serveur autonome Exchange, base de données sous un DAG.<br/><br/> Récupérer une boîte aux lettres, des bases de données de boîtes aux lettres sous un DAG.<br/><br/> ReFS non pris en charge.<br/><br/> Sauvegarder des clusters de disque non partagés.<br/><br/> Sauvegarder Exchange Server configuré pour la réplication continue.
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | SharePoint 2016 sur DPM 2016 et versions ultérieures.<br/><br/>Local/machine virtuelle Azure. | Sauvegarder la batterie de serveurs, serveur web frontal.<br/><br/> Récupérer une batterie de serveurs, base de données, application web, fichier ou élément de liste, recherche SharePoint, serveur web frontal.<br/><br/> Vous ne pouvez pas sauvegarder une batterie de serveurs à l’aide de SQL Server AlwaysOn pour les bases de données de contenu.
**Hyper-V sur Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (avec SP1)** | Hyper-V sur 2016 pris en charge pour DPM 2016 et versions ultérieures.<br/><br/> Local. | **Agent MABS sur un hôte Hyper-V** : sauvegarder des machines virtuelles entières et des fichiers de données hôtes. Sauvegarder des machines virtuelles avec un stockage local, des machines virtuelles dans un cluster avec un stockage CSV et des machines virtuelles avec un stockage sur serveur de fichiers SMB.<br/><br/> **Agent MABS sur une machine virtuelle invitée** : sauvegarder des charges de travail exécutées sur la machine virtuelle. Volumes partagés de cluster (CSV).<br/><br/> **Récupération** : machine virtuelle, récupération au niveau de l’élément de disques durs virtuels/volumes/dossiers/fichiers.<br/><br/> **Machines virtuelles Linux** : Sauvegarder lorsque Hyper-V est en cours d’exécution sur Windows Server 2012 R2 et versions ultérieures. Pour les machines virtuelles Linux, la récupération porte sur toute la machine.
**Machines virtuelles VMware : vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2, v1<br/><br/> DPM 2012 R2 nécessite System Center avec le correctif cumulatif 1) <br/><br/>Local. | Sauvegarder des machines virtuelles VMware sur le stockage des volumes partagés de cluster, NFS et SAN.<br/><br/> Récupérer la machine virtuelle entière.<br/><br/> Sauvegarde Windows/Linux.<br/><br/> Récupération au niveau élément des dossiers/fichiers pour les machines virtuelles Windows uniquement.<br/><br/> Les applications virtuelles VMware ne sont pas prises en charge.<br/><br/> Pour les machines virtuelles Linux, la récupération porte sur toute la machine.


- Notez que les charges de travail en cluster sauvegardées par DPM/de sauvegarde AZURE doivent être dans le même domaine que DPM/de sauvegarde AZURE ou dans un domaine approuvé/enfant. 
- Vous pouvez utiliser l’authentification NTLM/par certificat pour sauvegarder les données dans des groupes de travail ou des domaines non approuvés.



## <a name="next-steps"></a>Étapes suivantes

- [Découvrez-en plus](backup-architecture.md#architecture-back-up-to-dpmmabs) sur l’architecture MABS.
- [Passez en revue](backup-support-matrix-mars-agent.md) ce qui est pris en charge pour l’agent MARS.
- [Configurez](backup-azure-microsoft-azure-backup.md) un serveur MABS.
- [Configurer DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
