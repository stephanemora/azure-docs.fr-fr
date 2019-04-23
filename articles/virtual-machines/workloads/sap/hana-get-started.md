---
title: 'Démarrage rapide : Installation manuelle d’un système SAP HANA à une instance sur des machines virtuelles Azure | Microsoft Docs'
description: Guide de démarrage rapide pour l’installation manuelle d’un système SAP HANA à instance unique sur des machines virtuelles Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 5091932989849943f00cb71f72378dd17af23a4a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001369"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Démarrage rapide : Installation manuelle d’une seule instance SAP HANA sur des Machines virtuelles Azure
## <a name="introduction"></a>Présentation
Ce guide vous aide à configurer une seule instance SAP HANA sur des Machines virtuelles Azure lorsque vous installez SAP NetWeaver 7.5 et SAP HANA 1.0 SP12 manuellement. Ce guide porte sur la façon de déployer SAP HANA sur Azure. Il ne remplace la documentation SAP. 

> [!NOTE]
> Ce guide décrit des déploiements de SAP HANA sur des machines virtuelles Azure. Pour plus d’informations sur la façon de déployer SAP HANA dans les grandes instances HANA, consultez [utiliser SAP sur des Machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Conditions préalables
Ce guide part du principe que vous êtes familiarisé avec cette infrastructure en tant que certaines bases d’un service (IaaS) :
 * Comment déployer des machines virtuelles (VM) ou des réseaux virtuels via le portail Azure ou PowerShell.
 * L’inter-plateformes interface Azure (CLI), qui inclut la possibilité d’utiliser les modèles JavaScript Objet Notation (JSON).

Ce guide suppose également que vous êtes familiarisé avec :
* Les systèmes SAP HANA et SAP NetWeaver et comment les installer localement.
* Comment installer et utiliser SAP HANA et les instances d’application SAP sur Azure.
* Les concepts et les procédures suivants :
   * Planification du déploiement de SAP sur Azure, qui comprend la planification du réseau virtuel Azure et l’utilisation du stockage Azure. Consultez [SAP NetWeaver sur des Machines virtuelles Azure - guide de planification et implémentation](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Principes de déploiement et modalités de déploiement sur des machines virtuelles dans Azure. Consultez [Déploiement de machines virtuelles Azure pour SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Haute disponibilité pour SAP NetWeaver ABAP SAP Central Services (ASCS) /SAP Central Services (SCS) et serveur ERS (Enqueue Replication) sur Azure. Consultez [Haute disponibilité pour SAP NetWeaver sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Détails sur la façon d’améliorer l’efficacité dans une installation multi-SID d’ASCS/SCS sur Azure. Consultez [Créer une configuration SAP NetWeaver multi-SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Principes de l’exécution de SAP NetWeaver basée sur des machines virtuelles pilotées par Linux dans Azure. Consultez [exécuter SAP NetWeaver sur machines virtuelles Microsoft Azure SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Ce guide fournit des paramètres spécifiques à Linux dans machines virtuelles Azure. Il fournit également des informations sur comment attacher des disques de stockage Azure aux machines virtuelles Linux.

Les types de machines virtuelles Azure qui peuvent être utilisés pour les scénarios de production sont répertoriés dans la [documentation SAP pour IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Pour les scénarios de production, une plus grande variété de types natifs de machine virtuelle Azure est disponible.
Pour plus d’informations sur la configuration de machine virtuelle et les opérations, consultez [opérations sur Azure et les configurations d’infrastructure SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
Pour la haute disponibilité de SAP HANA, consultez [disponibilité de SAP HANA pour les Machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Si vous souhaitez obtenir une instance SAP HANA ou un système S/4HANA ou BW/4HANA déployés rapidement, envisagez d’utiliser [SAP Cloud Appliance Library](https://cal.sap.com). Vous trouverez la documentation sur la façon de déployer un système S/4HANA via SAP Cloud Appliance Library sur Azure, par exemple, [ce guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Il vous suffit d’un abonnement Azure et un utilisateur SAP qui peut être enregistré avec SAP Cloud Appliance Library.

## <a name="additional-resources"></a>Ressources supplémentaires
### <a name="sap-hana-backup"></a>Sauvegarde SAP HANA
Pour plus d’informations sur la façon de sauvegarder les bases de données SAP HANA sur des machines virtuelles Azure, consultez :
* [Guide de sauvegarde pour SAP HANA sur des Machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide).
* [Sauvegarde SAP HANA Azure au niveau fichier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level).
* [Sauvegarde de SAP HANA à partir de captures instantanées de stockage](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots).

### <a name="sap-cloud-appliance-library"></a>Bibliothèque d’appliances cloud SAP
Pour plus d’informations sur l’utilisation de SAP Cloud Appliance Library pour déployer S/4HANA ou BW/4HANA, consultez [déploiement de SAP S/4HANA ou BW/4HANA sur Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>Systèmes d’exploitation pris en charge par SAP HANA
Pour plus d’informations sur les systèmes d’exploitation pris en charge par SAP HANA, consultez [SAP Note 2235581 - SAP HANA : Les systèmes d’exploitation pris en charge](https://launchpad.support.sap.com/#/notes/2235581/E). Les machines virtuelles Azure ne prennent en charge qu’un sous-ensemble de ces systèmes d’exploitation. Les systèmes d’exploitation suivants sont pris en charge pour déployer SAP HANA sur Azure : 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Pour obtenir de la documentation SAP supplémentaire sur SAP HANA et les différents systèmes d’exploitation Linux, consultez :

* [Note SAP 171356 : Logiciels SAP sur Linux : Obtenir des informations générales](https://launchpad.support.sap.com/#/notes/1984787).
* [Note SAP 1944799 : Instructions SAP HANA pour l’installation du système d’exploitation SLES](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
* [Note SAP 2205917 : Base de données SAP HANA du système d’exploitation paramètres recommandés pour SLES 12 pour les applications SAP](https://launchpad.support.sap.com/#/notes/2205917/E).
* [Note SAP 1391070 relative aux : Solutions Linux UUID](https://launchpad.support.sap.com/#/notes/1391070).
* [Note SAP 2009879 relative aux : Instructions SAP HANA pour système d’exploitation de Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879).
* [Note SAP 2292690 : SAP HANA DB : Système d’exploitation aux paramètres recommandés pour RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E).

### <a name="sap-monitoring-in-azure"></a>Surveillance SAP dans Azure
Pour plus d’informations sur la surveillance de SAP dans Azure :

* [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) traite SAP enhanced monitoring avec des machines virtuelles Linux sur Azure. 
* [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) présente des informations relatives à SAPOSCOL sur Linux. 
* [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) aborde des métriques de surveillance clés pour SAP sur Microsoft Azure.

### <a name="azure-vm-types"></a>Types de machines virtuelles Azure
Les types de machines virtuelles Azure et scénarios SAP pris en charge la charge de travail utilisés par SAP HANA sont documentés dans [plateforme IaaS certifiée SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Les types de machines virtuelles Azure sont certifiées par SAP pour SAP NetWeaver ou la couche d’application S/4HANA sont documentées dans [Note SAP 1928533 : Applications SAP sur Azure : Produits et types de machines virtuelles Azure pris en charge](https://launchpad.support.sap.com/#/notes/1928533/E).

> [!NOTE]
> L’intégration SAP-Linux-Azure est prise en charge uniquement sur Azure Resource Manager ; le modèle de déploiement classique n’est pas compatible. 

## <a name="manual-installation-of-sap-hana"></a>Installation manuelle de SAP HANA

> [!IMPORTANT]
> Assurez-vous que le système d’exploitation que vous sélectionnez est certifiées pour SAP HANA sur les types de machine virtuelle spécifiques, que vous utilisez SAP. La liste de SAP HANA certified libère de types de machines virtuelles et le système d’exploitation pour ces types de machines virtuelles peuvent être recherchés [plateformes IaaS certifiées SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Veillez à cliquer sur les détails du type de machine virtuelle répertoriées pour obtenir la liste complète des versions de système d’exploitation de prise en charge par SAP HANA pour le type de machine virtuelle spécifique. Par exemple dans ce document, nous avons utilisé une version SUSE Linux Enterprise Server (SLES) du système d’exploitation qui n’est pas pris en charge par SAP pour SAP HANA sur des machines virtuelles de série M.
>

Ce guide explique comment installer manuellement SAP HANA sur des machines virtuelles Azure de deux manières :

* Utiliser SAP Software Provisioning Manager (SWPM) dans le cadre d’une installation NetWeaver distribuée à l’étape « instance de base de données d’installation ».
* Utilisez l’outil de manager (HDBLCM) de cycle de vie de base de données SAP HANA et installer NetWeaver.

Vous pouvez également utiliser SWPM pour installer tous les composants, tels que SAP HANA, serveur d’applications SAP et l’instance ASCS, dans une seule machine virtuelle. Les étapes sont décrites dans ce [annonce du blog SAP HANA](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Cette option n’est pas décrite dans ce guide de démarrage rapide, mais les problèmes que vous devez prendre en compte sont les mêmes.

Avant de commencer une installation, nous vous recommandons de lire la « préparation des machines virtuelles Azure pour l’installation manuelle de SAP HANA « section plus loin dans ce guide. Cela vous aidera à prévenir plusieurs erreurs de base qui peuvent survenir lorsque vous utilisez seulement une configuration de machine virtuelle Azure par défaut.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Étapes clés de l’installation de SAP HANA quand vous utilisez SAP SWPM
Cette section répertorie les étapes clés pour l’installation manuelle d’un système SAP HANA à instance unique lorsque vous utilisez SAP SWMP pour réaliser une installation distribuée de SAP NetWeaver 7.5. Les différentes étapes sont expliquées plus en détail dans les captures d’écran plus loin dans ce guide.

1. Créer un réseau virtuel Azure qui inclut deux machines virtuelles de test.
2. Déployer les deux machines virtuelles Azure avec les systèmes d’exploitation en fonction du modèle Azure Resource Manager. Cet exemple utilise SUSE Linux Enterprise Server et SLES for SAP Applications 12 SP1. 
3. Attacher deux disques Azure standard ou premium stockage, par exemple, les disques 75 Go ou 500 Go, au serveur d’applications de machine virtuelle.
4. Joindre des disques de stockage premium à la machine virtuelle du serveur HANA DB. Pour plus d’informations, consultez la section « Configuration des disques » plus loin dans ce guide.
5. Selon les exigences de taille ou de débit, attacher plusieurs disques. Créez ensuite des volumes agrégés par bandes. Utilisez la gestion des volumes logiques (LVM) ou un outil d’administration (mdadm) de périphériques multiples au niveau du système d’exploitation à l’intérieur de la machine virtuelle.
6. Créer des systèmes de fichiers XFS sur les disques ou volumes logiques joints.
7. Monter les nouveaux systèmes de fichiers XFS au niveau du système d’exploitation. Utiliser un des systèmes de fichiers pour tous les logiciels SAP. Utiliser l’autre pour le répertoire /sapmnt et les sauvegardes, par exemple. Sur le serveur de base de données SAP HANA, monter les systèmes de fichiers XFS sur les disques de stockage Premium en tant que /hana et /usr/sap. Ce processus est nécessaire pour empêcher la saturation du système de fichiers racine. Le système de fichiers racine peu volumineux sur des machines virtuelles Azure Linux. 
8. Entrer les adresses IP locales des machines virtuelles de test dans le fichier /etc/hosts.
9. Entrer le paramètre **nofail** dans le fichier /etc/fstab.
10. Définissez les paramètres de noyau Linux en fonction de la version de système d’exploitation Linux que vous utilisez. Pour plus d’informations, consultez les Notes SAP qui abordent HANA et la section « Paramètres de noyau » dans ce guide.
11. Ajouter un espace d’échange.
12. Installer éventuellement un bureau graphique sur les machines virtuelles de test. Sinon, utiliser une installation SAPinst distante.
13. Télécharger les logiciels SAP à partir de SAP Service Marketplace.
14. Installer l’instance SAP ASCS sur la machine virtuelle du serveur d’applications.
15. Partager le répertoire /sapmnt entre les machines virtuelles de test à l’aide de NFS. La machine virtuelle du serveur d’applications est le serveur NFS.
16. Installer l’instance de base de données incluant HANA, sur la machine virtuelle du serveur de base de données à l’aide de SWPM.
17. Installer le serveur d’applications principal (PAS) sur la machine virtuelle du serveur d’applications.
18. Démarrer la console de gestion SAP. Se connecter avec l’interface graphique utilisateur SAP ou HANA Studio, par exemple.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Étapes clés de l’installation de SAP HANA quand vous utilisez HDBLCM
Cette section répertorie les étapes clés pour l’installation manuelle d’un système SAP HANA à instance unique quand vous utilisez SAP HDBLCM pour réaliser une installation distribuée de SAP NetWeaver 7.5. Les différentes étapes sont expliquées plus en détail dans les captures d’écran tout au long de ce guide.

1. Créer un réseau virtuel Azure qui inclut deux machines virtuelles de test.
2. Déployer deux machines virtuelles Azure avec les systèmes d’exploitation en fonction du modèle Azure Resource Manager. Cet exemple utilise SLES et SLES for SAP Applications 12 SP1.
3. Attacher deux disques Azure standard ou premium stockage, par exemple, les disques 75 Go ou 500 Go, à la machine virtuelle du serveur d’applications.
4. Joindre des disques de stockage premium à la machine virtuelle du serveur HANA DB. Pour plus d’informations, consultez la section « Configuration des disques » plus loin dans ce guide.
5. Selon les exigences de taille ou de débit, attacher plusieurs disques. Créer des volumes agrégés par bandes à l’aide de la gestion des volumes logiques ou un outil de mdadm au niveau du système d’exploitation à l’intérieur de la machine virtuelle.
6. Créer des systèmes de fichiers XFS sur les disques ou volumes logiques joints.
7. Monter les nouveaux systèmes de fichiers XFS au niveau du système d’exploitation. Utiliser un des systèmes de fichiers pour tous les logiciels SAP. Utiliser l’autre pour le répertoire /sapmnt et les sauvegardes, par exemple. Sur le serveur de base de données SAP HANA, monter les systèmes de fichiers XFS sur les disques de stockage Premium en tant que /hana et /usr/sap. Ce processus est nécessaire pour aider à empêcher la saturation du système de fichiers racine. Le système de fichiers racine peu volumineux sur des machines virtuelles Azure Linux.
8. Entrer les adresses IP locales des machines virtuelles de test dans le fichier /etc/hosts.
9. Entrer le paramètre **nofail** dans le fichier /etc/fstab.
10. Définissez les paramètres de noyau en fonction de la version de système d’exploitation Linux que vous utilisez. Pour plus d’informations, consultez les Notes SAP qui abordent HANA et la section « Paramètres de noyau » dans ce guide.
11. Ajouter un espace d’échange.
12. Installer éventuellement un bureau graphique sur les machines virtuelles de test. Sinon, utiliser une installation SAPinst distante.
13. Télécharger les logiciels SAP à partir de SAP Service Marketplace.
14. Créer un groupe, sapsys, avec l’ID de groupe 1001 sur la machine virtuelle du serveur de base de données HANA.
15. Installer SAP HANA sur la machine virtuelle du serveur de base de données à l’aide du Gestionnaire de cycle de vie de base de données HANA.
16. Installer l’instance SAP ASCS sur la machine virtuelle du serveur d’applications.
17. Partager le répertoire /sapmnt entre les machines virtuelles de test à l’aide de NFS. La machine virtuelle du serveur d’applications est le serveur NFS.
18. Installer l’instance de base de données incluant HANA, sur la machine virtuelle du serveur de base de données HANA à l’aide de SWPM.
19. Installer le serveur d’applications principal sur le serveur d’applications machine virtuelle.
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
Vérifier les mises à jour du système d’exploitation Linux et de correctifs avant d’installer des logiciels supplémentaires. En installant un correctif, vous pouvez éviter un appel au support technique.

Assurez-vous que vous utilisez :
* SUSE Linux Enterprise Server pour applications SAP ;
* Red Hat Enterprise Linux pour applications SAP ou Red Hat Enterprise Linux pour SAP HANA. 

Si vous n’avez pas encore fait, inscrivez le déploiement du système d’exploitation dans votre abonnement Linux auprès du fournisseur de Linux. SUSE a des images de système d’exploitation pour les applications SAP qui incluent déjà des services, et qui sont enregistrées automatiquement.

Voici un exemple montrant comment rechercher les correctifs disponibles pour SUSE Linux à l’aide de la **zypper** commande :

 `sudo zypper list-patches`

Suivant le genre de problème, les correctifs sont classés par catégorie et par gravité. Les valeurs couramment utilisées pour la catégorie sont : 
- Sécurité
- Recommandé
- Facultatif
- Fonctionnalité
- Document
- YaST

Les valeurs couramment utilisées pour la gravité sont :

- Critique
- Important
- Modéré
- Faible
- Non spécifié

La commande **zypper** recherche uniquement les mises à jour nécessaires aux packages installés. Par exemple, vous pouvez utiliser cette commande :

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Vous pouvez ajouter le paramètre `--dry-run` pour tester la mise à jour sans réellement mettre à jour le système.


### <a name="disk-setup"></a>Configuration des disques
La taille du système de fichiers racine d’une machine virtuelle Linux sur Azure est limitée. Par conséquent, vous devez attacher un espace disque supplémentaire à une machine virtuelle Azure à l’exécution de SAP. Pour le serveur d’applications SAP machines virtuelles Azure, l’utilisation de disques de stockage Azure standard peut suffire. Pour les machines virtuelles Azure SGBD HANA SAP, l’utilisation de disques de stockage Azure premium pour les implémentations de production et hors production est obligatoire.

Selon le [les besoins de stockage SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), la configuration de stockage Azure premium suivante est suggérée : 

| Référence de la machine virtuelle | RAM |  /hana/data et /hana/log <br /> agrégés avec LVM ou mdadm | /hana/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 Go | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

Dans la configuration de disque suggérée, le volume de données HANA et le volume de journal sont placés sur le même ensemble de disques de stockage Azure premium qui sont agrégés avec LVM ou mdadm. Il n’est pas nécessaire de définir un niveau de redondance RAID, car le stockage Azure premium conserve trois images des disques pour la redondance. 

Pour vous assurer que vous configurez suffisamment de stockage, consultez [les besoins de stockage SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) et [guide d’installation et mise à jour serveur SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Envisagez également les volumes de débit de disque dur virtuel (VHD) différents des disques de stockage premium Azure différents, comme décrit dans [le stockage premium hautes performances et des disques gérés pour les machines virtuelles](../../windows/disks-types.md). 

Vous pouvez ajouter plusieurs disques de stockage premium aux machines virtuelles HANA SGBD pour stocker les sauvegardes des journaux de transaction ou de la base de données.

Pour plus d’informations sur les deux principaux outils utilisés pour configurer l’agrégation par bandes, consultez :

* [Configuration logicielle de RAID sur Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Configurer LVM sur une machine virtuelle Linux dans Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pour plus d’informations sur comment attacher des disques aux machines virtuelles Azure exécutant Linux en tant qu’invité du système d’exploitation, consultez [ajouter un disque à une VM Linux](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Avec les disques SSD Azure premium, vous pouvez définir les modes de mise en cache. Pour le jeu de bandes contenant/Hana/Data et/Hana/log, désactivez la mise en cache du disque. Pour les autres volumes, autrement dit, les disques, la valeur est le mode de mise en cache **ReadOnly**.

Pour rechercher des exemples de modèles JSON à utiliser pour créer des machines virtuelles, consultez le [modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates).
Le modèle vm-simple-sles est un modèle de base. Il inclut une section de stockage, avec un disque de données de 100 Go supplémentaires. Utilisez ce modèle comme base. Vous pouvez adapter le modèle à votre propre configuration.

> [!NOTE]
> Il est important attacher le disque de stockage Azure à l’aide d’un UUID comme documenté dans [exécuter SAP NetWeaver sur machines virtuelles Microsoft Azure SUSE Linux](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Dans l’environnement de test, les disques de deux le stockage Azure standard sont attachés au serveur d’application SAP machine virtuelle, comme illustré dans la capture d’écran suivante. Un seul disque stocke tous les logiciels SAP, telles que NetWeaver 7.5, GUI SAP et SAP HANA, pour l’installation. Le deuxième disque permet de s’assurer que suffisamment d’espace libre est disponible pour les besoins supplémentaires. Par exemple, les données de sauvegarde et de test et le répertoire/sapmnt, autrement dit, les profils SAP, doivent être partagés entre toutes les machines virtuelles qui appartiennent au même paysage SAP.

![Fenêtre des disques du serveur d’applications SAP HANA montrant deux disques de données et leur taille](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Paramètres de noyau
SAP HANA nécessite des paramètres de noyau Linux spécifiques. Ces paramètres de noyau ne font pas partie des images de galerie Azure standard et doivent être définies manuellement. Selon que vous utilisez SUSE ou Red Hat, les paramètres peuvent différer. Les Notes SAP mentionnées précédemment donner que d’informations sur ces paramètres. Dans les captures d’écran présentées, SUSE Linux 12 SP1 a été utilisé. 

SLES pour SAP Applications 12 générale et SLES for SAP Applications 12 SP1 offrent un nouvel outil, **tuned-adm**, qui remplace l’ancien **sapconf** outil. Un profil SAP HANA spécial est disponible pour **tuned-adm**. Pour paramétrer le système pour SAP HANA, entrez le profil suivant en tant qu’utilisateur racine :

   `tuned-adm profile sap-hana`

Pour plus d’informations sur **tuned-adm**, consultez la [documentation de SUSE sur tuned-adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Dans la capture d’écran suivante, vous pouvez voir comment **tuned-adm** modifié le `transparent_hugepage` et `numa_balancing` valeurs, en fonction des paramètres SAP HANA requis :

![L’outil tuned-adm modifie les valeurs en fonction des paramètres SAP HANA requis](./media/hana-get-started/image005.jpg)

Pour conserver les paramètres de noyau SAP HANA, utilisez **grub2** sur SLES 12. Pour plus d’informations sur **grub2**, consultez le [structure de fichier de Configuration](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) section de la documentation SUSE.

La capture d’écran suivante montre comment les paramètres de noyau ont été modifiés dans le fichier config, puis compilés à l’aide de **grub2-mkconfig** :

![Paramètres de noyau modifiés dans le fichier config et compilés à l’aide de grub2-mkconfig](./media/hana-get-started/image006.jpg)

Vous pouvez également modifier les paramètres à l’aide de YaST et des réglages de **Chargeur de démarrage** > **Paramètres du kernel** :

![Onglet Paramètres du kernel dans le chargeur de démarrage YaST](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Systèmes de fichiers
La capture d’écran suivante montre les deux systèmes de fichiers qui ont été créés sur la machine virtuelle du serveur d’applications SAP, sur les deux disques de stockage standard Azure. Les deux systèmes de fichiers sont du type XFS et sont montés sur /sapdata et /sapsoftware.

Il n’est pas obligé de structurer vos systèmes de fichiers de cette façon. Vous avez d’autres options pour la structure de l’espace disque. Le plus important est d’empêcher le système de fichiers racine de manquer d’espace libre.

![Deux systèmes de fichiers créés sur la machine virtuelle du serveur d’applications SAP](./media/hana-get-started/image008.jpg)

Pour la base de données machine virtuelle SAP HANA, lors d’une installation de base de données, lorsque vous utilisez SAPinst avec SWPM et **typique** option d’installation, tout est installé sous /hana et/usr/SAP. L’emplacement par défaut de la sauvegarde des fichiers journaux SAP HANA se trouve sous /usr/sap. Là encore, il est important d’empêcher le système de fichiers racine de manquer d’espace de stockage. Assurez-vous qu’il est suffisamment d’espace libre sous /hana et/usr/SAP avant d’installer SAP HANA à l’aide de SWPM.

Pour obtenir une description de la disposition du système de fichiers standard de SAP HANA, consultez le [guide d’installation et mise à jour serveur SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Systèmes de fichiers supplémentaires créés sur la machine virtuelle du serveur d’applications SAP](./media/hana-get-started/image009.jpg)

Lorsque vous installez SAP NetWeaver sur SLES/SLES standard pour l’image de galerie SAP Applications 12 Azure, un message s’affiche indiquant qu’aucun espace d’échange, comme illustré dans la capture d’écran suivante. Pour faire disparaître ce message, vous pouvez ajouter manuellement un fichier d’échange à l’aide des options **dd**, **mkswap** et **swapon**. Pour en savoir plus, recherchez « Ajout d’un fichier d’échange manuellement » dans le [à l’aide du partitionneur YaST](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) section de la documentation SUSE.

Vous pouvez également configurer l’espace d’échange à l’aide de l’agent de machine virtuelle Linux. Pour plus d’informations, consultez le [Guide d’utilisateur de l’agent Linux Azure](../../extensions/agent-linux.md).

![Message contextuel indiquant qu’il existe d’espace d’échange insuffisants](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>Fichier /etc/hosts
Avant de commencer l’installation de SAP, pensez à inclure les noms d’hôtes et les adresses IP des machines virtuelles SAP dans le fichier /etc/hosts. Déployer toutes les machines virtuelles SAP dans un même réseau virtuel Azure. Utilisez ensuite les adresses IP internes, comme illustré ici :

![Noms d’hôte et adresses IP des machines virtuelles SAP répertoriés dans le fichier/etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Fichier /etc/fstab

Il est utile d’ajouter le **nofail** paramètre au fichier fstab. De cette façon, si une erreur survient avec les disques, la machine virtuelle ne cessent de répondre dans le processus de démarrage. Cependant, gardez à l’esprit que l’espace disque supplémentaire n’est peut-être pas disponible et les processus peuvent saturer le système de fichiers racine. Si /hana est manquant, SAP HANA ne démarrera pas.

![Ajouter le paramètre nofail au fichier fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Bureau graphique GNOME sur SLES 12/SLES for SAP Applications 12
Cette section explique comment :

* Installez le bureau GNOME et xrdp sur SLES 12/SLES for SAP Applications 12.
* Exécutez la console de gestion SAP basée sur Java à l’aide de Firefox sur SLES 12/SLES for SAP Applications 12.

Vous pouvez également utiliser des alternatives telles que Xterminal ou VNC, qui ne sont pas décrites dans ce guide.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Installer le bureau GNOME et xrdp sur SLES 12/SLES for SAP Applications 12
Si vous avez un arrière-plan de Windows, vous pouvez facilement utiliser un bureau graphique directement dans les machines virtuelles Linux de SAP pour exécuter Firefox, SAPinst, l’interface graphique utilisateur SAP, console de gestion SAP ou HANA Studio. Puis vous pouvez vous connecter à la machine virtuelle via le protocole RDP (Remote Desktop) à partir d’un ordinateur Windows. En fonction de vos stratégies d’entreprise sur l’ajout d’interfaces utilisateur graphiques aux systèmes de production et de non-production basés sur Linux, vous souhaiterez peut-être installer GNOME sur votre serveur. Suivez ces étapes pour installer le bureau GNOME sur un Azure SLES 12/SLES pour SAP Applications 12 VM.

1. Installez le bureau GNOME en entrant la commande suivante, par exemple, dans une fenêtre PuTTY :

   `zypper in -t pattern gnome-basic`

2. Installez xrdp pour autoriser l’établissement d’une connexion à la machine virtuelle via RDP :

   `zypper in xrdp`

3. Modifiez /etc/sysconfig/windowmanager et définissez GNOME comme gestionnaire de fenêtrage par défaut :

   `DEFAULT_WM="gnome"`

4. Exécutez la commande **chkconfig** pour vous assurer que xrdp démarre automatiquement après un redémarrage :

   `chkconfig -level 3 xrdp on`

5. Si vous rencontrez un problème avec la connexion RDP, essayez de redémarrer, par exemple, à partir d’une fenêtre PuTTY :

   `/etc/xrdp/xrdp.sh restart`

6. Si un redémarrage xrdp mentionné à l’étape précédente ne fonctionne pas, déterminez si un fichier .pid est présent :

   `check /var/run` 

   Recherchez `xrdp.pid`. Si vous le trouvez, supprimez-le et essayez de redémarrer à nouveau.

### <a name="start-sap-mc"></a>Démarrer la console de gestion SAP
Après avoir installé le bureau GNOME installé, démarrez le graphique basée sur Java console de gestion SAP à partir de Firefox. Si elle s’exécute dans un Azure SLES 12/SLES pour SAP Applications 12 VM, il peut afficher une erreur. L’erreur se produit en raison de l’absence du plug-in de navigateur Java.

L’URL de démarrage de la console de gestion SAP est `<server>:5<instance_number>13`.

Pour plus d’informations, consultez [à partir de la Console de gestion SAP basée sur le web](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

La capture d’écran suivante montre le message d’erreur qui s’affiche lorsque le plug-in Java-navigateur est manquant :

![Message d’erreur indiquant que le plug-in de navigateur Java est manquant](./media/hana-get-started/image013.jpg)

Pour résoudre le problème, une solution consiste à installer le plug-in manquant à l’aide de YaST, comme illustré dans la capture d’écran suivante :

![Utilisation de YaST pour installer le plug-in manquant](./media/hana-get-started/image014.jpg)

Lorsque vous entrez à nouveau l’URL de Console de gestion SAP, vous êtes invité à activer le plug-in :

![Boîte de dialogue demandant l’activation du plug-in](./media/hana-get-started/image015.jpg)

Un message d’erreur concernant un fichier manquant, à savoir javafx.properties, peut également s’afficher. Il se rapporte à l’exigence d’Oracle Java 1.8 pour la version 7.4 de l’interface graphique utilisateur SAP. Pour plus d’informations, consultez [Note SAP 2059429](https://launchpad.support.sap.com/#/notes/2059424).
La version IBM Java et le package openjdk fourni avec SLES/SLES for SAP Applications 12 n’incluent pas le fichier javafx.properties nécessaire. La solution consiste à télécharger et installer Java SE 8 auprès d’Oracle.

Pour plus d’informations sur un problème similaire avec openjdk sur openSUSE, consultez le thread de discussion [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306) (SAPGui 7.4 Java pour openSUSE 42.1 Leap).

## <a name="manual-installation-of-sap-hana-swpm"></a>Installation manuelle de SAP HANA : SWPM
La série de captures d’écran dans cette section illustre les principales étapes pour l’installation de SAP NetWeaver 7.5 et SAP HANA SP12 lorsque vous utilisez SWPM avec SAPinst. Dans le cadre d’une installation de NetWeaver 7.5, SWPM pouvez également installer la base de données HANA en tant qu’une seule instance.

Dans un environnement de test, nous avons installé un serveur d’applications Advanced Business Application Programming (ABAP). Comme indiqué dans la capture d’écran suivante, nous avons utilisé le **Distributed System** option d’installation de l’instance ASCS et les instances de serveur principal d’application dans une machine virtuelle Azure. Nous avons utilisé SAP HANA en tant que le système de base de données dans une autre machine virtuelle de Azure.

![Installation de l’instance ASCS et de l’instance de serveur d’applications principal à l’aide de l’option Distributed System (Système distribué)](./media/hana-get-started/image012.jpg)

Une fois que l’instance ASCS est installée sur la machine virtuelle du serveur d’applications, il est identifié par une icône verte dans la Console de gestion SAP. Le répertoire/sapmnt, qui inclut le répertoire de profil SAP, doit être partagé avec la machine virtuelle du serveur de base de données SAP HANA. L’étape d’installation de la base de données requiert l’accès à ces informations. Pour fournir cet accès, le meilleur moyen consiste à utiliser NFS, qui peut être configuré à l’aide de YaST.

![Console de gestion SAP montrant l’instance ASCS installée sur le serveur d’application virtuelle à l’aide d’une icône verte](./media/hana-get-started/image016.jpg)

Sur le serveur d’application machine virtuelle, le répertoire /sapmnt est partagé via NFS à l’aide de la **rw** et **no_root_squash** options. Les options par défaut sont **ro** et **root_squash**, qui peuvent provoquer des problèmes durant l’installation de l’instance de base de données.

![Partage du répertoire /sapmnt via NFS à l’aide des options rw et no_root_squash](./media/hana-get-started/image017b.jpg)

Comme le montre la capture d’écran suivante, le partage /sapmnt à partir de la machine virtuelle du serveur d’applications doit être configuré sur la machine virtuelle du serveur de base de données SAP HANA à l’aide de **Client NFS** et YaST :

![Partage /sapmnt configuré à l’aide du client NFS](./media/hana-get-started/image018b.jpg)

Pour effectuer une installation distribuée de NetWeaver 7.5, autrement dit, un **Instance de base de données**, connectez-vous à la machine virtuelle du serveur de base de données SAP HANA et démarrez SWPM :

![Installation d’une instance de base de données en se connectant à la machine virtuelle du serveur de base de données SAP HANA et en démarrant SWPM](./media/hana-get-started/image019.jpg)

Une fois que vous sélectionnez **typique** installation et le chemin d’accès au support d’installation, entrez un SID de la base de données, le nom d’hôte, le numéro d’instance et le mot de passe administrateur du système DB :

![Page de connexion de l’administrateur système de la base de données SAP HANA](./media/hana-get-started/image035b.jpg)

Entrez le mot de passe pour le schéma DBACOCKPIT :

![Zone de saisie du mot de passe pour le schéma DBACOCKPIT](./media/hana-get-started/image036b.jpg)

Entrer une question pour le mot de passe du schéma SAPABAP1 :

![Entrer une question pour le mot de passe du schéma SAPABAP1](./media/hana-get-started/image037b.jpg)

Une fois chaque tâche terminée, une coche verte est affichée en regard de chaque phase du processus d’installation de la base de données. Le message « Execution of ... Database Instance has completed. » (L’exécution de... Instance de base de données est terminée.) s’affiche.

![Fenêtre de la tâche terminée avec un message de confirmation](./media/hana-get-started/image023.jpg)

Après l’installation, la Console de gestion SAP montre également l’instance de base de données avec une icône verte. Il affiche la liste complète des processus SAP HANA, telles que hdbindexserver et hdbcompileserver.

![Fenêtre de la console de gestion SAP montrant la liste des processus SAP HANA](./media/hana-get-started/image024.jpg)

La capture d’écran suivante montre les éléments de la structure de fichiers sous le répertoire /hana/shared que SWPM a créés durant l’installation de SAP HANA. Comme il n’existe aucune option pour spécifier un autre chemin d’accès, il est important de monter un espace disque supplémentaire sous le répertoire /hana avant l’installation de SAP HANA à l’aide de SWPM. Cette étape empêche le système de fichiers racine de manquer d’espace libre.

![Structure de fichiers du répertoire /hana/shared créée pendant l’installation de SAP HANA](./media/hana-get-started/image025.jpg)

Cette capture d’écran illustre la structure de fichiers du répertoire /usr/sap :

![Structure de fichiers du répertoire/usr/sap](./media/hana-get-started/image026.jpg)

La dernière étape de l’installation distribuée ABAP consiste à installer l’instance de serveur d’applications principal :

![Installation ABAP montrant la dernière étape : l’instance de serveur d’applications principal](./media/hana-get-started/image027b.jpg)

Une fois que l’instance de serveur principal d’application et l’interface GUI SAP sont installés, utilisez la **DBA Cockpit** transaction pour confirmer que l’installation de SAP HANA a été terminée correctement :

![Fenêtre DBA Cockpit confirmant la réussite de l’installation](./media/hana-get-started/image028b.jpg)

En tant que dernière étape, vous pouvez vouloir installer d’abord HANA Studio sur le serveur d’applications SAP machine virtuelle. Puis connectez-vous à l’instance de SAP HANA qui s’exécute sur la machine virtuelle du serveur de base de données.

![Installation de SAP HANA Studio sur la machine virtuelle du serveur d’applications SAP](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Installation manuelle de SAP HANA : HDBLCM
Outre l’installation de SAP HANA dans le cadre d’une installation distribuée avec SWPM, vous pouvez installer d’abord HANA de façon autonome, à l’aide de l’outil HDBLCM. Vous pouvez ensuite installer SAP NetWeaver 7.5, par exemple. Les captures d’écran de cette section illustrent le fonctionnement de ce processus.

Pour plus d’informations sur l’outil HANA HDBLCM, consultez :

* [Choisissez le correct SAP HANA HDBLCM pour votre tâche](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm).
* [Outils de gestion du cycle de vie de SAP HANA](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm).
* [Guide d’installation et mise à jour serveur SAP HANA](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf).

Vous souhaitez éviter tout problème avec un paramètre d’ID de groupe par défaut pour le `\<HANA SID\>adm user`, qui est créé par l’outil HDBLCM. Avant d’installer SAP HANA via l’outil HDBLCM, définir un nouveau groupe appelé `sapsys` à l’aide des ID de groupe `1001`:

![Nouveau groupe « sapsys » défini avec l’ID de groupe 1001](./media/hana-get-started/image030.jpg)

Lorsque vous démarrez HDBLCM pour la première fois, une simple Démarrer affiche de menu. Sélectionner un élément 1, **installer le nouveau système**:

![Option « Install new system » (Installer le nouveau système) de la fenêtre de démarrage de l’outil HDBLCM](./media/hana-get-started/image031.jpg)

La capture d’écran suivante montre toutes les options clés que vous avez sélectionnées précédemment.

> [!IMPORTANT]
> Répertoires qui sont nommés journal HANA et les volumes de données et le chemin d’installation, c'est-à-dire/Hana/Shared dans cet exemple et/usr/SAP ne doivent pas faire partie du système de fichiers racine. Ces répertoires appartiennent aux disques de données Azure qui étaient attachées à la machine virtuelle. Pour plus d’informations, consultez la section « Configuration des disques ». 

Cette approche empêche le système de fichiers racine de manquer d’espace. Notez que l’administrateur de système HANA a pseudo `1005` et fait partie de la `sapsys` groupe avec l’ID `1001`, qui a été défini avant l’installation.

![Liste de tous les composants SAP HANA clés sélectionnés précédemment](./media/hana-get-started/image032.jpg)

Vérifier le `\<HANA SID\>adm user` détails dans le répertoire/etc/passwd. Recherchez `azdadm`, comme illustré dans la capture d’écran suivante :

![Informations de l’utilisateur \<SID HANA\>adm répertoriées dans le répertoire /etc/passwd](./media/hana-get-started/image033.jpg)

Après avoir installé SAP HANA à l’aide de l’outil HDBLCM, vous pouvez voir la structure des fichiers dans SAP HANA Studio, comme l’illustre la capture d’écran ci-dessous. Le schéma SAPABAP1, qui inclut toutes les tables SAP NetWeaver, n’est pas encore disponible.

![Structure des fichiers SAP HANA dans SAP HANA Studio](./media/hana-get-started/image034.jpg)

Après avoir installé SAP HANA, vous pouvez installer SAP NetWeaver par-dessus. Comme indiqué dans la capture d’écran suivante, l’installation a été effectuée en tant qu’une installation distribuée à l’aide de SWPM. Ce processus est décrit dans la section précédente. Lorsque vous installez l’instance de base de données à l’aide de SWPM, vous entrez les mêmes données à l’aide de HDBLCM. Par exemple, vous entrez le nom d’hôte, le SID HANA et le numéro d’instance. SWPM utilise ensuite l’installation HANA existante et ajoute plusieurs d’autres schémas.

![Installation distribuée effectuée à l’aide de SWPM](./media/hana-get-started/image035b.jpg)

La capture d’écran suivante illustre l’étape de l’installation avec SWPM au cours de laquelle les données relatives au schéma DBACOCKPIT sont entrées :

![Étape de l’installation avec SWPM au cours de laquelle les données du schéma DBACOCKPIT sont saisies](./media/hana-get-started/image036b.jpg)

Entrez les données relatives au schéma SAPABAP1 :

![Saisie des données relatives au schéma SAPABAP1](./media/hana-get-started/image037b.jpg)

Une fois l’installation d’instance de base de données avec SWPM est terminée, vous pouvez voir le schéma SAPABAP1 dans SAP HANA Studio :

![Schéma SAPABAP1 dans SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Enfin, une fois que le serveur d’applications SAP et les installations de l’interface utilisateur graphique SAP sont terminées, vérifiez que l’instance de base de données HANA à l’aide de la **DBA Cockpit** transaction :

![Vérification de l’instance de base de données HANA avec la transaction DBA Cockpit](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Téléchargement des logiciels SAP
Vous pouvez télécharger les logiciel à partir de SAP Service Marketplace, comme illustré dans les captures d’écran suivantes.

Télécharger NetWeaver 7.5 pour Linux/HANA :

 ![Fenêtre de mise à niveau et installation de service pour le téléchargement de NetWeaver 7.5 SAP](./media/hana-get-started/image001.jpg)

Télécharger HANA SP12 Platform Edition :

 ![Fenêtre de mise à niveau et installation de service pour le téléchargement de HANA SP12 Platform Edition SAP](./media/hana-get-started/image002.jpg)

