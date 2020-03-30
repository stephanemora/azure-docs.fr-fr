---
title: Solutions Oracle sur Microsoft Azure | Microsoft Docs
description: Découvrez les options possibles pour déployer des applications et des solutions Oracle sur Microsoft Azure, notamment l’exécution complète sur l’infrastructure Azure ou l’utilisation de la connectivité entre clouds avec OCI (Oracle Cloud Infrastructure).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: 6ab41182d59f8c96edfca4854aad5f9a13f53436
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806607"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Vue d’ensemble des applications et des solutions Oracle sur Azure

Cet article présente les fonctionnalités permettant d’exécuter des solutions Oracle avec l’infrastructure Azure. Consultez aussi les introductions détaillées des [images de machine virtuelle Oracle](oracle-vm-solutions.md) disponibles sur la Place de marché Azure et la fonctionnalité en préversion [d’interconnexion Azure avec Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Bases de données Oracle sur l’infrastructure Azure

Exécutez des bases de données Oracle sur l’infrastructure Azure à l’aide d’Oracle Database sur des images Oracle Linux disponibles sur la Place de marché Azure :

* Oracle Database 12.1, 12.2 et 18.3 Enterprise Edition 

* Oracle Database 12.1, 12.2 et 18.3 Standard Edition 

Vous pouvez également configurer Oracle Database sur une image non-Oracle Linux disponible dans Azure, baser une solution sur une image personnalisée que vous créez de toutes pièces dans Azure ou de charger une image personnalisée à partir de votre environnement local.

Si vous le souhaitez, vous pouvez effectuer la configuration avec plusieurs disques attachés et améliorer les performances de la base de données en installant Oracle Automated Storage Management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Applications sur Oracle Linux et WebLogic Server

Exécutez des applications d’entreprise dans Azure sur les systèmes d’exploitation Oracle pris en charge. Les images suivantes sont disponibles sur la Place de marché Azure :

* Oracle WebLogic Server 12.1.2

* Oracle Linux avec noyau UEK (Unbreakable Enterprise Kernel) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 et 7.6 

## <a name="high-availability-and-disaster-recovery-options"></a>Options de haute disponibilité et récupération d’urgence

* Configurez [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), [Active Data Guard avec FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) ou [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) sur l’infrastructure Azure en association avec les [zones de disponibilité](../../../availability-zones/az-overview.md) à des fins de haute disponibilité au sein d’une région. Vous pouvez également effectuer ces configurations sur plusieurs régions Azure pour bénéficier d’une disponibilité supérieure et améliorer la reprise d’activité.

* Utilisez [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) pour orchestrer et gérer la récupération d’urgence pour vos machines virtuelles Oracle Linux dans Azure et vos serveurs physiques et locaux. 

* Activez Oracle Real Application Clusters (RAC) dans Azure à l’aide de la [solution Azure VMware](https://docs.azure.cloudsimple.com/oracle-rac/) ou de [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="backup-oracle-workloads"></a>Sauvegarde des charges de travail Oracle

* Sauvegardez vos machines virtuelles Oracle à l’aide du service [Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-overview).

* Sauvegardez votre base de données Oracle à l’aide d’Oracle RMAN et, si vous le souhaitez, utilisez [blobfuse pour Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux) pour monter un [compte de stockage Blob Azure hautement redondant](https://docs.microsoft.com/azure/storage/common/storage-redundancy) et y stocker vos sauvegardes RMAN en bénéficiant d’une résilience accrue.

## <a name="integration-of-azure-with-oci-preview"></a>Intégration d’Azure avec OCI (préversion)

Exécutez des applications Oracle dans l’infrastructure Azure, connectées à des bases de données back-end dans OCI (Oracle Cloud Infrastructure). Cette solution utilise les fonctionnalités suivantes : 

* **Mise en réseau cross-cloud** : utilisez l’interconnexion directe disponible entre Azure ExpressRoute et Oracle FastConnect pour établir des connexions à bande passante élevée, privées et à faible latence entre l’application et la couche de base de données.
* **Identité intégrée** : configurez l’identité fédérée entre Azure AD et Oracle IDCS pour créer une unique source d’identité unique pour les solutions. Activez l’authentification unique pour gérer les ressources entre OCI et Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Déployer des applications Oracle sur Azure

Utilisez des modèles Terraform pour configurer une infrastructure Azure et installer des applications Oracle validées et prises en charge en vue d’une exécution dans la configuration entre clouds :

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Applications Oracle Retail
* Oracle Hyperion Financial Management

Déployez également des applications personnalisées dans Azure qui se connectent avec OCI et d’autres services Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Configurer des bases de données Oracle dans OCI

Utilisez les services cloud Oracle Database (Autonomous Database, RAC, Exadata, DBaaS, Single Node) en association avec les applications Oracle s’exécutant dans Azure. En savoir plus sur les [options de base de données OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licence

Le déploiement d’applications Oracle dans Azure est basé sur un modèle « apportez votre propre licence ». Vous êtes supposé disposer des licences appropriées pour utiliser les logiciels Oracle, ainsi que d’un contrat de support avec Oracle. Oracle a garanti la mobilité des licence locale vers Azure. Consultez la [FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) Oracle-Azure.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le déploiement [d’images de machine virtuelle Oracle](oracle-vm-solutions.md) dans l’infrastructure Azure.

* En savoir plus sur [l’interconnexion d’Azure avec OCI](oracle-oci-overview.md).

* Consultez la [session Oracle on Azure overview](https://myignite.techcommunity.microsoft.com/sessions/82915) de la conférence Ignite 2019. 
