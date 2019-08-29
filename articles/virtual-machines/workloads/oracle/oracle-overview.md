---
title: Solutions Oracle sur Microsoft Azure | Microsoft Docs
description: 'Découvrez les options possibles pour déployer des applications et des solutions Oracle sur Microsoft Azure : exécution totale sur l’infrastructure Azure ou connectivité cross-cloud avec Oracle Cloud Infrastructure (OCI).'
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
ms.openlocfilehash: e9e37c54668ec0343cbfd45e51e90216955b46c4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100018"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Vue d’ensemble des applications et des solutions Oracle sur Azure

Cet article présente les fonctionnalités permettant d’exécuter des solutions Oracle avec l’infrastructure Azure. Consultez aussi les introductions détaillées des [images de machine virtuelle Oracle](oracle-vm-solutions.md) disponibles sur la Place de marché Azure et la fonctionnalité en préversion [d’interconnexion Azure avec Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Bases de données Oracle sur l’infrastructure Azure

Exécutez des bases de données Oracle sur l’infrastructure Azure à l’aide d’images Linux disponibles sur la Place de marché Azure :

* Oracle Database 12.1, 12.2 et 18.3 Enterprise Edition 

* Oracle Database 12.1, 12.2 et 18.3 Standard Edition 

Vous pouvez également choisir de baser une solution sur une image personnalisée que vous créez de toutes pièces dans Azure, ou de charger des images personnalisées à partir de votre environnement local.

Si vous le souhaitez, vous pouvez effectuer la configuration avec plusieurs disques attachés et améliorer les performances de la base de données en installant Oracle Automated Storage Management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Applications sur Oracle Linux et WebLogic Server

Exécutez des applications d’entreprise dans Azure sur les systèmes d’exploitation Oracle pris en charge. Les images suivantes sont disponibles sur la Place de marché Azure :

* Oracle WebLogic Server 12.1.2

* Oracle Linux (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 et 7.6

## <a name="high-availability-and-disaster-recovery-options"></a>Options de haute disponibilité et récupération d’urgence

* Configurez Oracle Data Guard, Active Data Guard et GoldenGate sur l’infrastructure Azure en association avec les [Zones de disponibilité](../../../availability-zones/az-overview.md) pour la haute disponibilité.

* Utilisez [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) pour orchestrer et gérer la récupération d’urgence pour vos machines virtuelles Oracle Linux dans Azure et vos serveurs physiques et locaux. 

* Activez Oracle Real Application Clusters (RAC) dans Azure à l’aide de [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="integration-of-azure-with-oci-preview"></a>Intégration d’Azure avec OCI (préversion)

Exécutez des applications Oracle dans l’infrastructure Azure, connectées à des bases de données principales dans Oracle Cloud Infrastructure (OCI). Cette solution utilise les fonctionnalités suivantes : 

* **Mise en réseau cross-cloud** : utilisez l’interconnexion directe disponible entre Azure ExpressRoute et Oracle FastConnect pour établir des connexions à bande passante élevée, privées et à faible latence entre l’application et la couche de base de données.
* **Identité intégrée** : configurez l’identité fédérée entre Azure AD et Oracle IDCS pour créer une unique source d’identité unique pour les solutions. Activez l’authentification unique pour gérer les ressources entre OCI et Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Déployer des applications Oracle sur Azure

Utilisez des modèles Terraform pour configurer une infrastructure Azure et installer des applications d’Oracle validées et prises en charge en vue d’une exécution dans la configuration cross-cloud :

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Applications Oracle Retail
* Oracle Hyperion Financial Management

Déployez également des applications personnalisées dans Azure qui se connectent avec OCI et d’autres services Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Configurer des bases de données Oracle dans OCI

Utilisez les services cloud Oracle Database (Autonomous Database, RAC, Exadata, DBaaS, Single Node) en association avec les applications Oracle s’exécutant dans Azure. En savoir plus sur les [options de base de données OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Gestion des licences

Le déploiement d’applications Oracle dans Azure est basé sur un modèle « apportez votre propre licence ». Vous êtes supposé disposer des licences appropriées pour utiliser les logiciels Oracle, ainsi que d’un contrat de support avec Oracle. Oracle a garanti la mobilité des licence locale vers Azure. Consultez la [FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) Oracle-Azure.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le déploiement [d’images de machine virtuelle Oracle](oracle-vm-solutions.md) dans l’infrastructure Azure.

* En savoir plus sur [l’interconnexion d’Azure avec OCI](oracle-oci-overview.md).