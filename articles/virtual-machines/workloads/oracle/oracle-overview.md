---
title: Solutions Oracle sur Microsoft Azure | Microsoft Docs
description: Découvrez les options pour déployer des applications Oracle et les solutions sur Microsoft Azure, y compris en cours d’exécution entièrement sur l’infrastructure Azure ou à l’aide de la connectivité entre-cloud avec Cloud Infrastructure OCI (Oracle).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: b5e40975fca491f289d949ee273d13053897fc6d
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743634"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Vue d’ensemble des applications Oracle et des solutions sur Azure

Cet article présente les fonctionnalités pour exécuter des solutions Oracle à l’aide d’infrastructure Azure. Voir aussi introductions détaillées disponibles [images de machine virtuelle Oracle](oracle-vm-solutions.md) dans la place de marché Azure et la fonction de la version préliminaire pour [interconnexion Azure avec Cloud Infrastructure OCI (Oracle)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Bases de données Oracle sur l’infrastructure Azure

Exécutez des bases de données Oracle sur l’infrastructure Azure à l’aide d’images Linux disponibles dans la place de marché Azure :

* Oracle Database 12.1 12.2 et 18,3 Enterprise Edition 

* Oracle Database 12.1 12.2 et 18,3 Standard Edition 

Vous pouvez également choisir de baser une solution sur une image personnalisée, vous créez à partir de toutes pièces dans Azure ou chargez une image personnalisée à partir de votre environnement local.

Si vous le souhaitez configurer avec plusieurs disques attachés et améliorer les performances de la base de données en installant Oracle Automated Storage Management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Applications sur Oracle Linux et WebLogic Server

Exécuter des applications d’entreprise dans Azure sur les systèmes d’exploitation pris en charge Oracle. Les images suivantes sont disponibles dans la place de marché Azure :

* Oracle WebLogic Server 12.1.2

* Oracle Linux (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 et 7.6

## <a name="high-availability-and-disaster-recovery-options"></a>Options de récupération d’urgence et disponibilité élevées

* Configurer l’infrastructure Azure conjointement avec Oracle Data Guard, Active Data Guard et GoldenGate [Zones de disponibilité](../../../availability-zones/az-overview.md) pour la haute disponibilité.

* Utilisez [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) pour orchestrer et gérer la récupération d’urgence pour vos machines virtuelles Oracle Linux dans Azure et votre local ou sur des serveurs physiques. 

* Activer Oracle Real Application Clusters (RAC) dans Azure à l’aide [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="integration-of-azure-with-oci-preview"></a>Intégration d’Azure avec l’interface OCI (version préliminaire)

Exécuter des applications Oracle dans l’infrastructure Azure, connecté à des bases de données principales dans Cloud Infrastructure OCI (Oracle). Cette solution utilise les fonctionnalités suivantes : 

* **Mise en réseau entre clouds** -utilisez la méthode directe d’interconnexion disponibles entre Azure ExpressRoute et Oracle FastConnect pour établir des connexions à bande passante élevée, privées et à faible latence entre l’application et la couche de base de données.
* **Intégré identity** -configurer une identité fédérée entre Azure AD et IDCS Oracle pour créer une source d’identité unique pour les solutions. Activer l’authentification unique gérer des ressources entre OCI et Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Déployer des applications Oracle sur Azure

Utiliser des modèles Terraform pour configurer une infrastructure Azure et installer des applications d’Oracle validé et pris en charge à exécuter dans la configuration entre clouds :

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Applications de vente au détail d’Oracle
* Oracle Hyperion de gestion financière

Également déployer des applications personnalisées dans Azure qui se connectent avec OCI et d’autres services Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Configurer les bases de données Oracle OCI

Utiliser les Services de Cloud de base de données Oracle (base de données autonome, RAC, Exadata, DBaaS, nœud unique) en association avec les applications Oracle s’exécutant dans Azure. En savoir plus sur [options de base de données OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Gestion des licences

Déploiement d’applications Oracle dans Azure est basé sur un modèle « apportez votre propre licence ». Il est supposé que vous sont concédés sous licence correctement pour utiliser le logiciel Oracle et que vous disposez d’un contrat de support avec Oracle. Oracle a garanti la mobilité des licence locale vers Azure. Consultez l’Azure Oracle [FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le déploiement [images de machine virtuelle Oracle](oracle-vm-solutions.md) dans l’infrastructure Azure.

* En savoir plus sur comment [Azure avec OCI d’interconnexion](oracle-oci-overview.md).