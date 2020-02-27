---
title: 'Démarrage rapide : Installation manuelle d’un système SAP HANA à une instance sur des machines virtuelles Azure | Microsoft Docs'
description: Guide de démarrage rapide pour l’installation manuelle d’un système SAP HANA à instance unique sur des machines virtuelles Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 0090ffe977dee3e493d726c9eb4d151bcbeb503f
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617251"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Démarrage rapide : Installation manuelle d’un système SAP HANA à une instance sur des machines virtuelles Azure
## <a name="introduction"></a>Introduction
Ce guide vous aide à configurer un système SAP HANA à instance unique sur des machines virtuelles Azure pendant l’installation manuelle de SAP NetWeaver 7.5 et SAP HANA 1.0 SP12. Ce guide se concentre sur le déploiement de SAP HANA sur Azure. Il ne remplace pas la documentation SAP. 

> [!NOTE]
> Ce guide décrit des déploiements de SAP HANA sur des machines virtuelles Azure. Pour plus d’informations sur le déploiement de SAP HANA sur des instances HANA de grande taille, consultez [Utilisation de SAP sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Prérequis
Ce guide suppose que vous maîtrisez certaines bases d’IaaS (infrastructure as a service), notamment :
 * Comment déployer des machines virtuelles ou des réseaux virtuels avec le portail Azure ou PowerShell.
 * L’interface de ligne de commande multiplateforme (CLI) Azure, qui comprend la possibilité d’utiliser des modèles JSON (JavaScript Object Notation).

Ce guide suppose également que vous êtes familiarisé avec :
* Les systèmes SAP HANA et SAP NetWeaver et comment les installer localement.
* L’installation et l’utilisation d’instances d’application SAP HANA et SAP sur Azure.
* Les concepts et les procédures suivants :
   * La planification du déploiement de SAP sur Azure, ce qui comprend la planification du réseau virtuel Azure et l’utilisation du stockage Azure. Consultez [SAP NetWeaver sur Azure Virtual Machines – Guide de planification et d’implémentation](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Principes de déploiement et modalités de déploiement sur des machines virtuelles dans Azure. Consultez [Déploiement de machines virtuelles Azure pour SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Haute disponibilité pour ASCS (SAP NetWeaver ABAP Central Services), SCS (SAP Central Services) et ERS (Enqueue Replication Server) sur Azure. Consultez [Haute disponibilité pour SAP NetWeaver sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Détails sur la façon d’améliorer l’efficacité dans une installation multi-SID d’ASCS/SCS sur Azure. Consultez [Créer une configuration SAP NetWeaver multi-SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Principes de l’exécution de SAP NetWeaver basée sur des machines virtuelles pilotées par Linux dans Azure. Consultez [Exécution de SAP NetWeaver sur des machines virtuelles Microsoft Azure SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Ce guide fournit des paramètres spécifiques pour Linux sur les machines virtuelles Azure. Il fournit également des informations sur comment attacher correctement des disques de stockage Azure aux machines virtuelles Linux.

Les types de machines virtuelles Azure qui peuvent être utilisés pour les scénarios de production sont répertoriés dans la [documentation SAP pour IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Pour les scénarios hors production, une plus large gamme de types de machines virtuelles Azure natives est disponible.
Pour plus d’informations sur la configuration et les opérations liées aux machines virtuelles, consultez [Configurations et opérations de l’infrastructure SAP HANA sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
Pour plus d’informations sur la haute disponibilité de SAP HANA, consultez [Haute disponibilité de SAP HANA pour Machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Si vous souhaitez déployer rapidement une instance SAP HANA ou un système S/4HANA ou BW/4HANA, envisagez d’utiliser la [Bibliothèque d’appliances cloud SAP](https://cal.sap.com). Vous trouverez dans [ce guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) de la documentation sur le déploiement d’un système S/4HANA par le biais de la bibliothèque d’appliances cloud SAP sur Azure. Il suffit que vous ayez un abonnement Azure et qu’un utilisateur SAP puisse être enregistré dans la bibliothèque d’appliances cloud SAP.

## <a name="additional-resources"></a>Ressources supplémentaires
### <a name="sap-hana-backup"></a>Sauvegarde SAP HANA
Pour plus d’informations sur la sauvegarde des bases de données SAP HANA sur des machines virtuelles Azure, consultez :
* [Guide de sauvegarde pour SAP HANA sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide).
* [Sauvegarde SAP HANA sur Azure au niveau fichier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level).
* [Sauvegarde SAP HANA à partir de captures instantanées de stockage](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots).

### <a name="sap-cloud-appliance-library"></a>Bibliothèque d’appliances cloud SAP
Pour plus d’informations sur l’utilisation de la bibliothèque d’appliances cloud SAP pour déployer S/4HANA ou BW/4HANA, consultez [Déploiement de SAP S/4HANA ou BW/4HANA sur Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>Systèmes d’exploitation pris en charge par SAP HANA
Pour plus d’informations sur les systèmes d’exploitation pris en charge par SAP HANA, consultez [Note SAP #2235581 - SAP HANA : Systèmes d’exploitation pris en charge](https://launchpad.support.sap.com/#/notes/2235581/E). Les machines virtuelles Azure ne prennent en charge qu’un sous-ensemble de ces systèmes d’exploitation. Les systèmes d’exploitation suivants sont pris en charge pour déployer SAP HANA sur Azure : 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Pour obtenir de la documentation SAP supplémentaire sur SAP HANA et les différents systèmes d’exploitation Linux, consultez :

* [Note SAP 171356 : Logiciels SAP sur Linux : Informations générales](https://launchpad.support.sap.com/#/notes/1984787).
* [Note SAP 1944799 : Instructions SAP HANA pour l’installation du système d’exploitation SLES](http://service.sap.com/sap/support/notes/1944799).
* [Note SAP 2205917 : Paramètres de système d’exploitation SAP HANA DB recommandés pour SLES 12, ainsi que les applications SAP](https://launchpad.support.sap.com/#/notes/2205917/E).
* [Note SAP 1391070 : Solutions Linux UUID](https://launchpad.support.sap.com/#/notes/1391070).
* [Note SAP 2009879 : Instructions SAP HANA pour les systèmes d’exploitation Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879).
* [Note SAP 2292690 : Base de données SAP HANA : Paramètres de système d’exploitation recommandés pour RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E).

### <a name="sap-monitoring-in-azure"></a>Surveillance SAP dans Azure
Pour plus d’informations sur la surveillance SAP dans Azure :

* [La note SAP 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) aborde la « surveillance améliorée » SAP avec des machines virtuelles Linux sur Azure. 
* [La note SAP 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) contient des informations relatives à SAPOSCOL sur Linux. 
* [La note SAP 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) aborde les métriques de surveillance clés pour SAP sur Microsoft Azure.

### <a name="azure-vm-types"></a>Types de machines virtuelles Azure
Les types de machines virtuelles Azure et les scénarios de charge de travail pris en charge par SAP utilisés par SAP HANA sont documentés dans [SAP certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html) (plateforme IaaS certifiée SAP). 

Les types de machines virtuelles Azure certifiées par SAP pour SAP NetWeaver ou la couche Application S/4HANA sont documentés dans [Note de support SAP 1928533 : Applications SAP sur Azure : Produits et types de machines virtuelles Azure pris en charge](https://launchpad.support.sap.com/#/notes/1928533/E).

> [!NOTE]
> L’intégration SAP-Linux-Azure est prise en charge uniquement sur Azure Resource Manager ; le modèle de déploiement classique n’est pas compatible. 

## <a name="manual-installation-of-sap-hana"></a>Installation manuelle de SAP HANA

> [!IMPORTANT]
> Vérifiez que le système d’exploitation que vous sélectionnez est certifié SAP pour SAP HANA sur les types de machine virtuelle spécifiques que vous utilisez. La liste des types de machine virtuelle certifiés SAP HANA et des systèmes d’exploitation correspondants pour ces machines virtuelles peut être consultée dans la page [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Cliquez sur les détails du type de machine virtuelle listé pour obtenir la liste complète des versions de système d’exploitation SAP HANA prises en charge pour le type de machine virtuelle spécifique. Dans l’exemple de ce document, nous avons utilisé une version du système d’exploitation SLES (SUSE Linux Enterprise Server) qui n’est pas prise en charge par SAP pour SAP HANA sur les machines virtuelles de série M.
>

Ce guide explique comment installer manuellement SAP HANA sur des machines virtuelles Azure de deux manières :

* Utilisez SAP Software Provisioning Manager (SWPM) dans le cadre d’une installation NetWeaver distribuée à l’étape d’installation de l’instance de base de données.
* Utilisez l’outil de gestion du cycle de vie des bases de données SAP HANA (HDBLCM), puis installez NetWeaver.

Vous pouvez également utiliser SWPM pour installer tous les composants comme SAP HANA, le serveur d’applications SAP et l’instance ASCS sur une seule machine virtuelle. Les étapes sont décrites dans cette [annonce du blog SAP HANA](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Cette option n’est pas décrite dans ce guide de démarrage rapide, mais les problèmes sont les mêmes.

Avant de commencer une installation, nous vous recommandons de lire la section « Préparation des machines virtuelles Azure pour une installation manuelle de SAP HANA » plus loin dans ce guide. Cela vous aidera à prévenir plusieurs erreurs de base qui peuvent survenir lorsque vous utilisez seulement une configuration de machine virtuelle Azure par défaut.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Étapes clés de l’installation de SAP HANA quand vous utilisez SAP SWPM
Cette section répertorie les étapes clés pour l’installation manuelle d’un système SAP HANA à instance unique lorsque vous utilisez SAP SWMP pour réaliser une installation distribuée de SAP NetWeaver 7.5. Les différentes étapes sont expliquées plus en détail dans les captures d’écran plus loin dans ce guide.

1. Créer un réseau virtuel Azure qui inclut deux machines virtuelles de test.
2. Déployez les deux machines virtuelles Azure avec les systèmes d’exploitation selon le modèle Azure Resource Manager. Cet exemple utilise SUSE Linux Enterprise Server et SLES for SAP Applications 12 SP1. 
3. Joindre deux disques de stockage Azure standard ou premium, par exemple des disques de 75 Go ou 500 Go, à la machine virtuelle du serveur d’applications.
4. Joindre des disques de stockage premium à la machine virtuelle du serveur HANA DB. Pour plus d’informations, consultez la section Configuration des disques plus loin dans ce guide.
5. En fonction des besoins de taille ou de débit, joignez plusieurs disques. Créez ensuite des volumes agrégés. Utilisez la gestion des volumes logiques (LVM) ou un outil d’administration d’appareils multiples (mdadm) au niveau du système d’exploitation dans la machine virtuelle.
6. Créer des systèmes de fichiers XFS sur les disques ou volumes logiques joints.
7. Monter les nouveaux systèmes de fichiers XFS au niveau du système d’exploitation. Utiliser un des systèmes de fichiers pour tous les logiciels SAP. Utiliser l’autre pour le répertoire /sapmnt et les sauvegardes, par exemple. Sur le serveur de base de données SAP HANA, monter les systèmes de fichiers XFS sur les disques de stockage Premium en tant que /hana et /usr/sap. Cette opération est nécessaire pour empêcher la saturation du système de fichiers racine. Le système de fichiers racine est peu volumineux sur les machines virtuelles Azure Linux. 
8. Entrer les adresses IP locales des machines virtuelles de test dans le fichier /etc/hosts.
9. Entrer le paramètre **nofail** dans le fichier /etc/fstab.
10. Définissez les paramètres du noyau Linux en fonction de la version du système d’exploitation Linux que vous utilisez. Pour plus d’informations, consultez les notes SAP qui abordent HANA et la section Paramètres de noyau dans ce guide.
11. Ajouter un espace d’échange.
12. Installer éventuellement un bureau graphique sur les machines virtuelles de test. Sinon, utiliser une installation SAPinst distante.
13. Télécharger les logiciels SAP à partir de SAP Service Marketplace.
14. Installer l’instance SAP ASCS sur la machine virtuelle du serveur d’applications.
15. Partager le répertoire /sapmnt entre les machines virtuelles de test à l’aide de NFS. La machine virtuelle du serveur d’applications est le serveur NFS.
16. Avec l’outil SWPM, installer l’instance de base de données, qui comprend HANA, sur la machine virtuelle du serveur de base de données.
17. Installer le serveur d’applications principal (PAS) sur la machine virtuelle du serveur d’applications.
18. Démarrer la console de gestion SAP. Se connecter avec l’interface graphique utilisateur SAP ou HANA Studio, par exemple.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Étapes clés de l’installation de SAP HANA quand vous utilisez HDBLCM
Cette section répertorie les étapes clés pour l’installation manuelle d’un système SAP HANA à instance unique quand vous utilisez SAP HDBLCM pour réaliser une installation distribuée de SAP NetWeaver 7.5. Les différentes étapes sont expliquées plus en détail dans les captures d’écran tout au long de ce guide.

1. Créer un réseau virtuel Azure qui inclut deux machines virtuelles de test.
2. Déployer deux machines virtuelles Azure avec les systèmes d’exploitation selon le modèle Azure Resource Manager. Cet exemple utilise SLES et SLES for SAP Applications 12 SP1.
3. Joignez deux disques de stockage Azure standard ou premium, par exemple des disques de 75 Go ou 500 Go, à la machine virtuelle du serveur d’applications.
4. Joindre des disques de stockage premium à la machine virtuelle du serveur HANA DB. Pour plus d’informations, consultez la section Configuration des disques plus loin dans ce guide.
5. En fonction des besoins de taille ou de débit, joignez plusieurs disques. Créez des volumes agrégés par bandes à l’aide de la gestion des volumes logiques ou d’un outil de mdadm au niveau du système d’exploitation dans la machine virtuelle.
6. Créer des systèmes de fichiers XFS sur les disques ou volumes logiques joints.
7. Monter les nouveaux systèmes de fichiers XFS au niveau du système d’exploitation. Utiliser un des systèmes de fichiers pour tous les logiciels SAP. Utiliser l’autre pour le répertoire /sapmnt et les sauvegardes, par exemple. Sur le serveur de base de données SAP HANA, monter les systèmes de fichiers XFS sur les disques de stockage Premium en tant que /hana et /usr/sap. Cette opération est nécessaire pour empêcher la saturation du système de fichiers racine. Le système de fichiers racine est peu volumineux sur les machines virtuelles Azure Linux.
8. Entrer les adresses IP locales des machines virtuelles de test dans le fichier /etc/hosts.
9. Entrer le paramètre **nofail** dans le fichier /etc/fstab.
10. Définissez les paramètres du noyau en fonction de la version du système d’exploitation Linux que vous utilisez. Pour plus d’informations, consultez les notes SAP qui abordent HANA et la section Paramètres de noyau dans ce guide.
11. Ajouter un espace d’échange.
12. Installer éventuellement un bureau graphique sur les machines virtuelles de test. Sinon, utiliser une installation SAPinst distante.
13. Télécharger les logiciels SAP à partir de SAP Service Marketplace.
14. Créer un groupe, sapsys, avec l’ID de groupe 1001 sur la machine virtuelle du serveur de base de données HANA.
15. Installer SAP HANA sur la machine virtuelle du serveur de base de données à l’aide du gestionnaire de cycle de vie des bases de données HANA.
16. Installer l’instance SAP ASCS sur la machine virtuelle du serveur d’applications.
17. Partager le répertoire /sapmnt entre les machines virtuelles de test à l’aide de NFS. La machine virtuelle du serveur d’applications est le serveur NFS.
18. Avec l’outil SWPM, installer l’instance de base de données, qui comprend HANA, sur la machine virtuelle du serveur de base de données HANA.
19. Installer le serveur d’applications principal sur la machine virtuelle du serveur d’applications.
20. Démarrer la console de gestion SAP. Se connecter par le biais de l’interface graphique utilisateur SAP ou HANA Studio.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Préparation des machines virtuelles Azure pour une installation manuelle de SAP HANA
Cette section couvre les sujets suivants :

* Mises à jour de système d’exploitation
* Configuration des disques
* Paramètres de noyau
* Systèmes de fichiers
* Fichier /etc/hosts
* Fichier /etc/fstab

### <a name="os-updates"></a>Mises à jour de système d’exploitation
Vérifiez les mises à jour et les correctifs du système d’exploitation Linux avant d’installer des logiciels supplémentaires. En installant un correctif, vous pourrez peut-être éviter un appel au support technique.

Veillez à utiliser :
* SUSE Linux Enterprise Server pour applications SAP ;
* Red Hat Enterprise Linux pour applications SAP ou Red Hat Enterprise Linux pour SAP HANA. 

Si vous ne l’avez pas déjà fait, enregistrez le déploiement du système d’exploitation dans votre abonnement Linux auprès du fournisseur de Linux. SUSE dispose d’images de système d’exploitation pour les applications SAP qui incluent déjà des services et qui sont enregistrées automatiquement.

Voici un exemple de recherche de correctifs disponibles pour SUSE Linux à l’aide de la commande **zypper** :

 `sudo zypper list-patches`

Suivant le genre de problème, les correctifs sont classés par catégorie et par gravité. Les valeurs couramment utilisées pour la catégorie sont : 
- Sécurité
- Recommandé
- Facultatif
- Fonctionnalité
- Document
- Yast

Les valeurs couramment utilisées pour la sévérité sont :

- Critique
- Important
- Modéré
- Faible
- Non spécifié

La commande **zypper** recherche uniquement les mises à jour nécessaires aux packages installés. Par exemple, vous pouvez utiliser cette commande :

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Vous pouvez ajouter le paramètre `--dry-run` pour tester la mise à jour sans réellement mettre à jour le système.


### <a name="disk-setup"></a>Configuration des disques
La taille du système de fichiers racine d’une machine virtuelle Linux sur Azure est limitée. Par conséquent, vous devez joindre plus d’espace disque aux machines virtuelles Azure qui doivent exécuter SAP. Pour les machines virtuelles Azure de serveur d’applications SAP, l’utilisation de disques de stockage Azure standard peut être suffisante. Pour les machines virtuelles Azure SGBD SAP HANA, l’utilisation de disques de stockage Azure Premium dans les implémentations de production et hors production est obligatoire.

D’après les [exigences de stockage TDI SAP HANA](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), la configuration Azure Premium suivante est suggérée : 

| Référence de la machine virtuelle | RAM |  /hana/data et /hana/log <br /> agrégés avec LVM ou MDADM | /hana/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 Go | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

Dans la configuration de disque suggérée, le volume de données et le volume de journal HANA sont placés sur le même ensemble de disques de stockage Azure premium qui sont agrégés avec LVM ou MDADM. Il n’est pas nécessaire de définir un niveau de redondance RAID, car le stockage Azure Premium conserve trois images des disques à des fins de redondance. 

Pour vérifier que vous configurez suffisamment de stockage, consultez [SAP HANA TDI storage Requirements](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Exigences de stockage TDI SAP HANA) et [SAP HANA Server Installation and Update Guide](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) (Guide d’installation et de mise à jour du serveur SAP HANA). Prenez également en compte les différents volumes de débit de disque dur virtuel (VHD) des divers disques de stockage Azure premium, comme expliqué dans [Stockage Premium hautes performances et disques managés pour machines virtuelles](../../windows/disks-types.md). 

Vous pouvez ajouter des disques de stockage premium aux machines virtuelles HANA SGBD, pour stocker les sauvegardes du journal des transactions ou de bases de données.

Pour plus d’informations sur les deux principaux outils utilisés pour configurer l’agrégation par bandes, consultez :

* [Configuration logicielle de RAID sur Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Configurer LVM sur une machine virtuelle Linux dans Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pour plus d’informations sur la façon de joindre des disques à des machines virtuelles Azure exécutant Linux comme système d’exploitation invité, consultez [Ajouter un disque à une machine virtuelle Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Les disques SSD Azure Premium vous permettent de définir les modes de mise en cache des disques. Pour l’ensemble de disques agrégés contenant /hana/data et /hana/log, désactivez la mise en cache des disques. Pour les autres volumes, c’est-à-dire les disques, le mode de mise en cache doit être **ReadOnly**.

Pour rechercher des exemples de modèles JSON pour créer des machines virtuelles, consultez [Modèles de démarrage rapide Microsoft Azure](https://github.com/Azure/azure-quickstart-templates).
Le modèle vm-simple-sles est un modèle de base. Il inclut une section de stockage, avec un disque de données de 100 Go supplémentaires. Utilisez ce modèle comme point de départ. Vous pouvez adapter le modèle à votre propre configuration.

> [!NOTE]
> Il est essentiel de joindre le disque de stockage Azure à l’aide d’un UUID, comme expliqué dans [Exécution de SAP NetWeaver sur des machines virtuelles Microsoft Azure SUSE Linux](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Dans l’environnement de test, deux disques de stockage standard Azure ont été attachés à la machine virtuelle du serveur applications SAP, comme illustré dans la capture d’écran suivante. Un des disques était utilisé pour stocker tous les logiciels SAP (notamment NetWeaver 7.5, GUI SAP et SAP HANA) à installer. Le deuxième disque permet de s’assurer que suffisamment d’espace est disponible pour les besoins supplémentaires. Par exemple, les données de sauvegarde et de test et le répertoire/sapmnt, autrement dit les profils SAP, doivent être partagés entre toutes les machines virtuelles qui appartiennent au même paysage SAP.

![Fenêtre des disques du serveur d’applications SAP HANA montrant deux disques de données et leur taille](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Paramètres de noyau
SAP HANA exige des paramètres de noyau Linux spécifiques. Ces paramètres de noyau ne font pas partie des images de galerie Azure standard et doivent être définis manuellement. Selon que vous utilisez SUSE ou Red Hat, les paramètres peuvent différer. Les notes SAP mentionnées précédemment donnent des informations sur ces paramètres. Dans les captures d’écran présentées, SUSE Linux 12 SP1 a été utilisé. 

SLES for SAP Applications 12 en disponibilité générale et SLES for SAP Applications 12 SP1 offrent un nouvel outil, **tuned-adm**, qui remplace l’ancien outil **sapconf**. Un profil SAP HANA spécial est disponible pour **tuned-adm**. Pour paramétrer le système pour SAP HANA, entrez le profil suivantes en tant qu’utilisateur racine :

   `tuned-adm profile sap-hana`

Pour plus d’informations sur **tuned-adm**, consultez la [documentation de SUSE sur tuned-adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Dans la capture d’écran suivante, vous pouvez voir comment **tuned-adm** a modifié les valeurs `transparent_hugepage` et `numa_balancing` en fonction des paramètres SAP HANA requis :

![L’outil tuned-adm modifie les valeurs en fonction des paramètres SAP HANA requis](./media/hana-get-started/image005.jpg)

Pour conserver les paramètres de noyau SAP HANA, utilisez **grub2** sur SLES 12. Pour plus d’informations sur **grub2**, consultez la section [Structure du fichier de configuration](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) de la documentation SUSE.

La capture d’écran suivante montre comment les paramètres de noyau ont été modifiés dans le fichier config, puis compilés à l’aide de **grub2-mkconfig** :

![Paramètres de noyau modifiés dans le fichier config et compilés à l’aide de grub2-mkconfig](./media/hana-get-started/image006.jpg)

Vous pouvez également modifier les paramètres à l’aide de YaST et des réglages de **Chargeur de démarrage** > **Paramètres du kernel** :

![Onglet Paramètres du kernel dans le chargeur de démarrage YaST](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Systèmes de fichiers
La capture d’écran suivante montre les deux systèmes de fichiers qui ont été créés sur la machine virtuelle du serveur d’applications SAP, sur les deux disques de stockage standard Azure. Les deux systèmes de fichiers présentent le type XFS et sont montés sur /sapdata et /sapsoftware.

Vous n’êtes pas obligé de structurer vos systèmes de fichiers de cette façon. D’autres options sont possibles pour structurer l’espace disque. Le plus important est d’empêcher le système de fichiers racine de manquer d’espace libre.

![Deux systèmes de fichiers créés sur la machine virtuelle du serveur d’applications SAP](./media/hana-get-started/image008.jpg)

Pour la machine virtuelle SAP HANA DB, pendant une installation de base de données, quand vous utilisez SAPinst avec SWPM et l’option d’installation **par défaut**, tous les éléments sont installés sous /hana et /usr/sap. L’emplacement par défaut de la sauvegarde des fichiers journaux SAP HANA se trouve sous /usr/sap. Encore une fois, il est important d’empêcher le système de fichiers racine de manquer d’espace de stockage. Vous devez vous assurer que l’espace libre sous /hana et /usr/sap est suffisant avant d’installer SAP HANA via SWPM.

Pour obtenir une description du système de fichiers standard de SAP HANA, consultez le document [Guide d’installation et de mise à jour du serveur SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Systèmes de fichiers supplémentaires créés sur la machine virtuelle du serveur d’applications SAP](./media/hana-get-started/image009.jpg)

Durant l’installation de SAP NetWeaver sur une image de galerie Azure SLES/SLES for SAP Applications 12 standard, un message s’affiche pour indiquer l’absence d’espace d’échange, comme l’illustre la capture d’écran suivante. Pour faire disparaître ce message, vous pouvez ajouter manuellement un fichier d’échange à l’aide des options **dd**, **mkswap** et **swapon**. Pour en savoir plus, recherchez « Ajout manuel d’un fichier d’échange » dans la section [Utilisation du partitionneur YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) de la documentation SUSE.

Vous pouvez également configurer l’espace d’échange à l’aide de l’agent de machine virtuelle Linux. Pour plus d’informations, consultez le [Guide d’utilisateur de l’agent Linux Azure](../../extensions/agent-linux.md).

![Message contextuel indiquant que l’espace d’échange est insuffisant](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>Fichier /etc/hosts
Avant de commencer l’installation de SAP, pensez à inclure les noms d’hôtes et les adresses IP des machines virtuelles SAP dans le fichier /etc/hosts. Déployez toutes les machines virtuelles SAP sur un même réseau virtuel Azure. Utilisez ensuite les adresses IP internes, comme illustré ici :

![Noms d’hôte et adresses IP des machines virtuelles SAP répertoriés dans le fichier/etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Fichier /etc/fstab

Il est utile d’ajouter le paramètre **nofail** au fichier fstab. En effet, en cas de problème avec les disques, la machine virtuelle ne reste pas bloquée dans le processus de démarrage. Cependant, gardez à l’esprit que l’espace disque supplémentaire n’est peut-être pas disponible et les processus peuvent saturer le système de fichiers racine. Si /hana est manquant, SAP HANA ne démarrera pas.

![Ajouter le paramètre nofail au fichier fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Bureau graphique GNOME sur SLES 12/SLES for SAP Applications 12
Cette section explique comment :

* Installer le bureau GNOME et xrdp sur SLES 12/SLES for SAP Applications 12.
* Exécuter la console de gestion SAP basée sur Java avec Firefox sur SLES 12/SLES for SAP Applications 12.

Vous pouvez également utiliser des alternatives telles que Xterminal ou VNC, qui ne sont pas décrites dans ce guide.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Installer le bureau GNOME et xrdp sur SLES 12/SLES for SAP Applications 12
Si vous êtes habitué à Windows, vous pouvez facilement utiliser un bureau graphique directement dans les machines virtuelles Linux de SAP pour exécuter Firefox, SAPinst, l’interface utilisateur graphique SAP, la console de gestion SAP ou HANA Studio. Vous pouvez ensuite vous connecter à la machine virtuelle via RDP à partir d’un ordinateur Windows. En fonction des stratégies de votre entreprise sur l’ajout d’interfaces utilisateur graphiques aux systèmes Linux de production et de non-production, vous souhaiterez peut-être installer GNOME sur votre serveur. Suivez ces étapes pour installer le bureau GNOME sur une machine virtuelle Azure SLES 12/SLES for SAP Applications 12.

1. Installez le bureau GNOME en entrant la commande suivante, dans une fenêtre PuTTY, par exemple :

   `zypper in -t pattern gnome-basic`

2. Installez xrdp pour autoriser l’établissement d’une connexion à la machine virtuelle via RDP :

   `zypper in xrdp`

3. Modifiez /etc/sysconfig/windowmanager et définissez GNOME comme gestionnaire de fenêtrage par défaut :

   `DEFAULT_WM="gnome"`

4. Exécutez la commande **chkconfig** pour vous assurer que xrdp démarre automatiquement après un redémarrage :

   `chkconfig -level 3 xrdp on`

5. En cas de problème avec la connexion RDP, essayez de redémarrer à partir d’une fenêtre PuTTY, par exemple :

   `/etc/xrdp/xrdp.sh restart`

6. Si un redémarrage xrdp mentionné à l’étape précédente ne fonctionne pas, déterminez si un fichier .pid est présent :

   `check /var/run` 

   Recherchez `xrdp.pid`. Si vous le trouvez, supprimez-le et essayez de redémarrer à nouveau.

### <a name="start-sap-mc"></a>Démarrer la console de gestion SAP
Après avoir installé le bureau GNOME, démarrez la console de gestion SAP Java graphique à partir de Firefox. Si elle s’exécute dans une machine virtuelle Azure SLES 12/SLES for SAP Applications 12, il est possible qu’une erreur s’affiche. Cette erreur se produit en raison de l’absence du plug-in de navigateur Java.

L’URL de démarrage de la console de gestion SAP est `<server>:5<instance_number>13`.

Pour plus d’informations, consultez [Démarrage de la console de gestion web SAP](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

La capture d’écran suivante illustre le message d’erreur qui s’affiche lorsque le plug-in Java du navigateur est absent :

![Message d’erreur indiquant que le plug-in de navigateur Java est manquant](./media/hana-get-started/image013.jpg)

Pour résoudre le problème, une solution consiste à installer le plug-in manquant à l’aide de YaST, comme illustré dans la capture d’écran suivante :

![Utilisation de YaST pour installer le plug-in manquant](./media/hana-get-started/image014.jpg)

Lorsque vous entrez de nouveau l’URL de la console de gestion SAP, un message s’affiche vous demandant d’activer le plug-in :

![Boîte de dialogue demandant l’activation du plug-in](./media/hana-get-started/image015.jpg)

Un message d’erreur concernant un fichier manquant, à savoir javafx.properties, peut également s’afficher. Il est lié à l’exigence d’Oracle Java 1.8 pour la version 7.4 de l’interface utilisateur graphique de SAP. Pour plus d’informations, consultez la [note SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424).
La version IBM Java et le package openjdk fourni avec SLES/SLES for SAP Applications 12 ne comprennent pas le fichier javafx.properties nécessaire. La solution consiste à télécharger et installer Java SE 8 auprès d’Oracle.

Pour plus d’informations sur un problème similaire avec openjdk sur openSUSE, consultez le thread de discussion [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306) (SAPGui 7.4 Java pour openSUSE 42.1 Leap).

## <a name="manual-installation-of-sap-hana-swpm"></a>Installation manuelle de SAP HANA : SWPM
La série de captures d’écran de cette section illustre les principales étapes d’installation de SAP NetWeaver 7.5 et SAP HANA SP12 avec SWPM (SAPinst). Lors de l’installation de NW 7.5, SWPM permet également d’installer la base de données HANA en tant qu’instance unique.

Dans un environnement de test, nous avons installé un serveur d’application ABAP (Advanced Business Application Programming). Comme illustré dans la capture d’écran suivante, nous avons utilisé l’option **Distributed System** (Système distribué) pour installer l’instance ASCS et l’instance de serveur d’applications principal sur une machine virtuelle Azure. Nous avons utilisé SAP HANA comme système de base de données dans une autre machine virtuelle Azure.

![Installation de l’instance ASCS et de l’instance de serveur d’applications principal à l’aide de l’option Distributed System (Système distribué)](./media/hana-get-started/image012.jpg)

Une fois que l’instance ASCS est installée sur la machine virtuelle du serveur d’applications, elle est identifiée par une icône verte dans la console de gestion SAP. Le répertoire/sapmnt, qui comprend le répertoire de profil SAP, doit être partagé avec la machine virtuelle du serveur de base de données SAP HANA. L’étape d’installation de la base de données requiert l’accès à ces informations. Pour fournir cet accès, le meilleur moyen consiste à utiliser NFS, qui peut être configuré à l’aide de YaST.

![Console de gestion SAP montrant l’instance ASCS installée sur la machine virtuelle du serveur d’applications et signalée par une icône verte](./media/hana-get-started/image016.jpg)

Sur la machine virtuelle du serveur d’applications, le répertoire /sapmnt est partagé via NFS à l’aide des options **rw** et **no_root_squash**. Les options par défaut sont **ro** et **root_squash**, qui peuvent provoquer des problèmes durant l’installation de l’instance de base de données.

![Partage du répertoire /sapmnt via NFS à l’aide des options rw et no_root_squash](./media/hana-get-started/image017b.jpg)

Comme la capture d’écran suivante le montre, le partage du répertoire /sapmnt de la machine virtuelle du serveur d’applications doit être configuré sur la machine virtuelle du serveur de base de données SAP HANA par le biais du **client NFS** et YaST :

![Partage /sapmnt configuré à l’aide du client NFS](./media/hana-get-started/image018b.jpg)

Pour effectuer une installation distribuée de NetWeaver 7.5, à savoir une **instance de base de données**, connectez-vous à la machine virtuelle du serveur de base de données SAP HANA et démarrez SWPM :

![Installation d’une instance de base de données en se connectant à la machine virtuelle du serveur de base de données SAP HANA et en démarrant SWPM](./media/hana-get-started/image019.jpg)

Après avoir sélectionné l’option d’installation **par défaut** et le chemin d’accès au support d’installation, entrez un identificateur de sécurité (SID) de base de données, le nom d’hôte, le numéro d’instance et le mot de passe d’administrateur système de la base de données :

![Page de connexion de l’administrateur système de la base de données SAP HANA](./media/hana-get-started/image035b.jpg)

Entrez le mot de passe pour le schéma DBACOCKPIT :

![Zone de saisie du mot de passe pour le schéma DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Entrer une question pour le mot de passe du schéma SAPABAP1 :

![Entrer une question pour le mot de passe du schéma SAPABAP1](./media/hana-get-started/image037b.jpg)

Une fois chaque tâche terminée, une coche verte est affichée en regard de chaque phase du processus d’installation de la base de données. Le message « Execution of ... Database Instance has completed. » (L’exécution de... Instance de base de données est terminée.) s’affiche.

![Fenêtre de la tâche terminée avec un message de confirmation](./media/hana-get-started/image023.jpg)

Après installation, la console de gestion SAP montre également l’instance de base de données avec une icône verte. Elle présente la liste complète des processus SAP HANA, comme hdbindexserver et hdbcompileserver.

![Fenêtre de la console de gestion SAP montrant la liste des processus SAP HANA](./media/hana-get-started/image024.jpg)

La capture d’écran suivante montre les éléments de la structure de fichiers sous le répertoire /hana/shared que SWPM a créés durant l’installation de SAP HANA. Étant donné qu’il n’y a aucun moyen de spécifier un autre chemin d’accès, il est important de monter un espace disque supplémentaire sous le répertoire /hana avant d’installer SAP HANA à l’aide de SWPM. Cette opération empêche le système de fichiers racine de manquer d’espace libre.

![Structure de fichiers du répertoire /hana/shared créée pendant l’installation de SAP HANA](./media/hana-get-started/image025.jpg)

Cette capture d’écran illustre la structure de fichiers du répertoire /usr/sap :

![Structure de fichiers du répertoire/usr/sap](./media/hana-get-started/image026.jpg)

La dernière étape de l’installation distribuée ABAP consiste à installer l’instance de serveur d’applications principal :

![Installation ABAP montrant la dernière étape : l’instance de serveur d’applications principal](./media/hana-get-started/image027b.jpg)

Une fois installées l’instance de serveur d’applications principal et l’interface graphique utilisateur SAP, utilisez la transaction **DBA Cockpit** pour vérifier que l’installation de SAP HANA s’est terminée correctement :

![Fenêtre DBA Cockpit confirmant la réussite de l’installation](./media/hana-get-started/image028b.jpg)

Pour la dernière étape, vous pouvez d’abord installer HANA Studio sur la machine virtuelle du serveur d’applications SAP. Ensuite, connectez-vous à l’instance SAP HANA qui s’exécute sur la machine virtuelle du serveur de base de données.

![Installation de SAP HANA Studio sur la machine virtuelle du serveur d’applications SAP](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Installation manuelle de SAP HANA : HDBLCM
Outre l’installation de SAP HANA dans le cadre d’une installation distribuée avec SWPM, vous pouvez installer d’abord HANA de façon autonome, à l’aide de l’outil HDBLCM. Vous pouvez ensuite installer SAP NetWeaver 7.5, par exemple. Les captures d’écran de cette section illustrent le fonctionnement de ce processus.

Pour plus d’informations sur l’outil HANA HDBLCM, consultez :

* [Choisir l’outil SAP HANA HDBLCM adapté à votre tâche](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm).
* [Outils de gestion du cycle de vie SAP HANA](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm).
* [Guide d’installation et de mise à jour du serveur SAP HANA](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf).

L’objectif est d’éviter tout problème avec le paramètre d’ID de groupe par défaut pour le `\<HANA SID\>adm user`, qui est créé par l’outil HDBLCM. Avant d’installer SAP HANA via l’outil HDBLCM, définissez un nouveau groupe appelé `sapsys` avec l’ID de groupe `1001` :

![Nouveau groupe « sapsys » défini avec l’ID de groupe 1001](./media/hana-get-started/image030.jpg)

Quand vous démarrez HDBLCM pour la première fois, un menu de démarrage simple s’affiche. Sélectionnez l’élément 1, **Installer le nouveau système** :

![Option « Install new system » (Installer le nouveau système) de la fenêtre de démarrage de l’outil HDBLCM](./media/hana-get-started/image031.jpg)

La capture d’écran suivante montre toutes les options clés que vous avez sélectionnées précédemment.

> [!IMPORTANT]
> Les répertoires qui sont nommés pour les volumes de données et de journaux HANA, ainsi que le chemin d’installation, à savoir /hana/shared dans cet exemple, et /usr/sap, ne doivent pas être partie du système de fichiers racine. Ces répertoires appartiennent aux disques de données Azure qui ont été joints à la machine virtuelle. Pour plus d’informations, consultez la section Configuration des disques. 

Cette approche empêche le système de fichiers racine de manquer d’espace. Vous pouvez voir que l’administrateur système HANA dispose de l’identifiant utilisateur `1005` et qu’il fait partie du groupe `sapsys`, dont l’ID est `1001`, et qui a été défini avant l’installation.

![Liste de tous les composants SAP HANA clés sélectionnés précédemment](./media/hana-get-started/image032.jpg)

Vérifiez les détails `\<HANA SID\>adm user` dans le répertoire/etc/passwd. Recherchez `azdadm`, comme présenté dans la capture d’écran suivante :

![Informations de l’utilisateur \<SID HANA\>adm répertoriées dans le répertoire /etc/passwd](./media/hana-get-started/image033.jpg)

Après avoir installé SAP HANA à l’aide de l’outil HDBLCM, vous pouvez voir la structure des fichiers dans SAP HANA Studio, comme l’illustre la capture d’écran ci-dessous. Le schéma SAPABAP1, qui inclut toutes les tables SAP NetWeaver, n’est pas encore disponible.

![Structure des fichiers SAP HANA dans SAP HANA Studio](./media/hana-get-started/image034.jpg)

Après avoir installé SAP HANA, vous pouvez installer SAP NetWeaver par-dessus. Comme le montre la capture d’écran ci-dessous, l’installation a été effectuée en tant qu’installation distribuée à l’aide de SWPM. Ce processus est décrit dans la section précédente. Quand vous installez l’instance de base de données à l’aide de SWPM, vous entrez les mêmes données que pour l’outil HDBLCM. Par exemple, vous entrez le nom d’hôte, le SID HANA et le numéro d’instance. SWPM utilise ensuite l’installation HANA existante et ajoute plusieurs d’autres schémas.

![Installation distribuée effectuée à l’aide de SWPM](./media/hana-get-started/image035b.jpg)

La capture d’écran suivante illustre l’étape de l’installation avec SWPM au cours de laquelle les données relatives au schéma DBACOCKPIT sont entrées :

![Étape de l’installation avec SWPM au cours de laquelle les données du schéma DBACOCKPIT sont saisies](./media/hana-get-started/image036b.jpg)

Entrez les données relatives au schéma SAPABAP1 :

![Saisie des données relatives au schéma SAPABAP1](./media/hana-get-started/image037b.jpg)

Une fois que l’installation de l’instance de base de données avec SWPM est terminée, vous pouvez voir le schéma SAPABAP1 dans SAP HANA Studio :

![Schéma SAPABAP1 dans SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Enfin, une fois terminée l’installation du serveur d’applications SAP et de l’interface utilisateur graphique SAP, vous pouvez vérifier l’instance de base de données HANA à l’aide de la transaction **DBA Cockpit**.

![Vérification de l’instance de base de données HANA avec la transaction DBA Cockpit](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Téléchargement des logiciels SAP
Vous pouvez télécharger les logiciel à partir de SAP Service Marketplace, comme illustré dans les captures d’écran suivantes.

Télécharger NetWeaver 7.5 pour Linux/HANA :

 ![Fenêtre Installation et mise à niveau de SAP Service pour le téléchargement de NetWeaver 7.5](./media/hana-get-started/image001.jpg)

Télécharger HANA SP12 Platform Edition :

 ![Fenêtre Installation et mise à niveau de SAP Service pour le téléchargement de HANA SP12 Platform Edition](./media/hana-get-started/image002.jpg)

