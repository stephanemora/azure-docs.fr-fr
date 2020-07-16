---
title: Architectures de solution avec Azure NetApp Files | Microsoft Docs
description: Cet article contient des références aux meilleures pratiques associées aux architectures de solution avec Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: b-juche
ms.openlocfilehash: 23ec482de740cc1ac8800a5de1c0e3be1f055df7
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045482"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Architectures de solution avec Azure NetApp Files
Cet article fournit des références aux meilleures pratiques pouvant vous aider à comprendre les architectures de solution pour l’utilisation d’Azure NetApp Files.  

Le diagramme suivant résume les catégories d’architectures de solution que Azure NetApp Files offre :

![Catégories d’architectures de solution](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Applications et solutions de base de données OSS Linux

Cette section fournit des références pour les solutions de bases de données et applications OSS Linux. 

### <a name="oracle"></a>Oracle

* [Oracle on Azure deployment best practice guide - Using Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf)
* [Images de machine virtuelle Oracle et leur déploiement sur Microsoft Azure : options de configuration de stockage partagé](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-vm-solutions#shared-storage-configuration-options)
* [Avantages de l’utilisation d’Azure NetApp Files avec Oracle Database](solutions-benefits-azure-netapp-files-oracle-database.md)

## <a name="windows-apps-and-sql-server-solutions"></a>Applications Windows et les solutions de SQL Server

Cette section fournit des références pour les applications Windows et les solutions de SQL Server.

### <a name="file-sharing-and-global-file-caching"></a>Partage de fichiers et mise en cache de fichiers globaux

* [Déploiement de talon/Azure NetApp Files](https://youtu.be/91LKb1qsLIM)

### <a name="sql-server"></a>SQL Server

* [Déployer des SQL Server sur SMB avec Azure NetApp Files](https://www.youtube.com/watch?v=x7udfcYbibs)
* [Déployer un cluster de basculement permanent de Microsoft SQL Server sur SMB avec les fichiers Azure NetApp Files](https://www.youtube.com/watch?v=zuNJ5E07e8Q)
* [Déployer des groupes de disponibilité permanents avec Azure NetApp Files](https://www.youtube.com/watch?v=y3VQmzzeyvc)

## <a name="sap-on-azure-solutions"></a>Solutions SAP sur Azure

Cette section fournit des références pour les Solutions SAP sur Azure. 

### <a name="generic-sap-and-sap-netweaver"></a>SAP et SAP Netweavereaver génériques 

* [SAP applications on Microsoft Azure - Using Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
* [Haute disponibilité pour SAP NetWeaver sur les machines virtuelles Azure sur SUSE Linux Enterprise Server avec Azure NetApp Files pour les applications SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
* [Haute disponibilité pour SAP NetWeaver sur des machines virtuelles Azure sur Red Hat Enterprise Linux avec Azure NetApp Files pour les applications SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* [Haute disponibilité pour SAP NetWeaver sur des machines virtuelles Azure sur Windows avec Azure NetApp Files (SMB) pour les applications SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
* [Guide multi-SID de haute disponibilité pour SAP NetWeaver sur les machines virtuelles Azure sur Red Hat Enterprise Linux for SAP Applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)

### <a name="sap-hana"></a>SAP HANA 

* [Configurations du stockage des machines virtuelles SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
* [Scale-out de SAP HANA avec le nœud de secours sur les machines virtuelles Azure avec Azure NetApp Files sur SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
* [Scale-out de SAP HANA avec le nœud de secours sur les machines virtuelles Azure avec Azure NetApp Files sur Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)

### <a name="sap-tech-community-and-blog-posts"></a>Billets de blog et communauté SAP Tech 

* [Azure NetApp Files : sauvegarde SAP HANA en secondes](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files : restaurer votre base de données HANA à partir d’une sauvegarde d’instantané](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files : déchargement de sauvegardes SAP HANA avec Cloud Sync](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Accélérez vos copies système SAP HANA à l’aide d’Azure NetApp Files](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Cloud Volumes ONTAP et Azure NetApp Files : migration du système SAP HANA facilitée](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)

## <a name="virtual-desktop-infrastructure-solutions"></a>Solutions d’infrastructure de bureau virtuel

Cette section fournit des références pour les solutions d’infrastructure de bureau virtuel.

### <a name="windows-virtual-desktop"></a>Windows Virtual Desktop

* [Options de stockage pour conteneurs de profil FSLogix dans Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/store-fslogix-profile#azure-platform-details)
* [Créer un conteneur de profils FSLogix pour un pool d’hôtes à l'aide d’Azure NetApp Files](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container)

## <a name="hpc-solutions"></a>Solutions HPC

Cette section fournit des références pour les solutions HPC (Calcul haute performance). 

### <a name="generic-hpc"></a>HPC générique

* [Azure NetApp Files : Tirer le meilleur parti de votre stockage cloud](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Exécuter des charges de travail MPI avec Azure Batch et Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Cloud Azure Cloud : Environnements HPC CycleCloud sur Azure NetApp Files](https://docs.microsoft.com/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>Hydrocarbures

* [Calcul haute performance (HPC) : Hydrocarbures dans Azure](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Exécuter le logiciel de simulation de réservoir sur Azure](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>Automatisation de la conception électronique (EDA)

* [Avantages de l’utilisation d’Azure NetApp Files pour l’automatisation de la conception électronique](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud : Laboratoire EDA HPC avec Azure NetApp Files](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)

### <a name="analytics"></a>Analytics

* [Azure NetApp Files : Nouveau système de fichiers partagés à utiliser avec la grille SAS sur Microsoft Azure](https://communities.sas.com/t5/Architecture/Azure-NetApp-Files-A-new-shared-file-system-to-use-with-SAS-Grid/m-p/606978)

## <a name="azure-platform-services-solutions"></a>Déployer des solutions pour les services de plateforme Azure

Cette section fournit des solutions pour les services de plateforme Azure. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Azure Kubernetes Serviceset Kubernetes

* [Intégrer Azure NetApp Files à Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/azure-netapp-files)
* [Performances exceptionnelles de Kubernetes sur Azure avec Azure NetApp Files](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Trident - Storage Orchestrator pour conteneurs](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)

### <a name="azure-batch"></a>Azure Batch

* [Exécuter des charges de travail MPI avec Azure Batch et Azure NetApp Files](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
 
