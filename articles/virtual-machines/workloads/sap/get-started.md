---
title: Bien démarrer avec SAP sur des machines virtuelles Azure | Microsoft Docs
description: Découvrez les solutions SAP qui s’exécutent sur des machines virtuelles Windows dans Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6fbb09d41535bf811a42b204f4e3d38b87c2846c
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51287946"
---
# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>Utilisation d’Azure pour l’hébergement et l’exécution de scénarios de charge de travail SAP

En choisissant Microsoft Azure comme partenaire de cloud computing SAP prêt, vous êtes en mesure d’exécuter en toute fiabilité vos charges de travail SAP critiques sur une plateforme évolutive, conforme et qui a fait ses preuves en entreprise.  Bénéficiez de l’extensibilité, de la flexibilité et des économies de coût fournies par Azure. Avec le partenariat étendu entre Microsoft et SAP, vous pouvez exécuter les applications SAP dans des environnements de développement/test et de production dans Azure en bénéficiant d’une prise en charge complète. De SAP NetWeaver à SAP S4/HANA ou SAP BI, de Linux à Windows, de SAP HANA à SQL, nous sommes là pour vous.

En plus de l’hébergement des scénarios de SAP NetWeaver avec différents SGBD sur Azure, vous pouvez héberger différents autres scénarios de charge de travail SAP, comme SAP BI dans Azure. Vous trouverez la documentation concernant les déploiements SAP NetWeaver sur des machines virtuelles Azure natives dans la section « SAP NetWeaver sur des Machines virtuelles Azure ».

L’unicité d’Azure pour SAP HANA est une offre unique qui permet à Azure de se démarquer de la concurrence. Afin de permettre l’hébergement de plus de mémoire et de scénarios SAP exigeants en termes de ressources processeur qui impliquent SAP HANA , Azure propose l’utilisation de matériel nu dédié au client qui permet d’exécuter des déploiements SAP HANA nécessitant jusqu'à 20 To (montée en puissance de 60 To) de mémoire pour S/4HANA ou une autre charge de travail SAP HANA. Cette solution Azure unique de SAP HANA sur Azure (grandes instances) vous autorise à exécuter SAP HANA sur le matériel nu dédié avec la couche application SAP ou une couche intermédiaire de la charge de travail hébergées dans des Machines virtuelles Azure natives. Cette solution est documentée dans plusieurs documents à la section « SAP HANA sur Azure (grandes instances) ».   

L’hébergement de scénarios de charge de travail SAP dans Azure peut aussi créer des exigences d’intégration d’identité et d’authentification unique à l’aide d’Azure Activity Directory pour différents composants SAP et SaaS SAP ou offres PaaS. Une liste de ces scénarios d’intégration et d’authentification unique avec Azure Active Directory (AAD) et des entités SAP est décrite et documentée dans la section « Intégration d’identité AAD SAP et authentification unique ».

## <a name="latest-changes"></a>Dernières modifications

Précision liée à l’utilisation et à la configuration d'[appliances virtuelles réseau Azure](https://azure.microsoft.com/solutions/network-appliances/) dans les chemins de communication critique en termes de latence des systèmes SAP dans :

- [Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Configurations et opérations de l’infrastructure SAP HANA sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)

Documentation relative à SAP HANA Dynamic Tiering pour les machines virtuelles Azure

- [Configurations et opérations de l’infrastructure SAP HANA sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#sap-hana-dynamic-tiering-20-for-azure-virtual-machines)

La documentation autour de SAP HANA Scale-out sur la machine virtuelle Azure M128s a été ajoutée à :

- [Configurations et opérations de l’infrastructure SAP HANA sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out)
- [Disponibilité de SAP HANA au sein d’une région Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)


## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>SAP HANA sur SAP HANA sur Azure (grandes instances)

Une série de documents vous guide dans l’utilisation de SAP HANA sur Azure (grandes instances). Les documents couvrent les domaines répertoriés relatifs aux grandes instances HANA :

- [Présentation de SAP HANA sur Azure (grandes instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Architecture de SAP HANA sur Azure (grandes instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastructure et connectivité à SAP HANA sur Azure (grandes instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Installation de SAP HANA sur SAP HANA sur Azure (grandes instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Haute disponibilité et récupération d’urgence de SAP HANA sur Azure (grandes instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Résolution des problèmes et surveillance de SAP HANA sur Azure (grandes instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Étapes suivantes :

- Consultez le document [Présentation et architecture de SAP HANA sur Azure (grandes instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA sur des machines virtuelles Azure
Cette partie de la documentation couvre différents aspects de SAP HANA. Vous devez avoir une bonne connaissance des principaux services d’Azure qui fournissent les services de base de la plateforme IaaS Azure, à savoir les ressources de calcul, de stockage et de mise en réseau Azure. Bon nombre de ces aspects sont abordés dans le [Guide de planification Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) associé à SAP NetWeaver. 

La documentation spécifique à la solution HANA sur Azure se compose de cette liste d’articles et de leurs sous-articles :

- [Démarrage rapide : installation manuelle d’un système SAP HANA à instance unique sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Déploiement de SAP S/4HANA ou BW/4HANA sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Configurations et opérations de l’infrastructure SAP HANA sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Haute disponibilité de SAP HANA pour les machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Disponibilité de SAP HANA au sein d’une région Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Disponibilité de SAP HANA dans l’ensemble des régions Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Haute disponibilité de SAP HANA sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Guide de sauvegarde pour SAP HANA sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Sauvegarde SAP HANA sur Azure au niveau fichier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Sauvegarde SAP HANA à partir de captures instantanées de stockage](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver déployé sur Machines virtuelles Azure
Vous trouverez dans cette section de la documentation sur la planification et le déploiement de SAP NetWeaver et Business One sur Azure. La documentation disponible dans ce chapitre se concentre principalement sur les notions de base et l’utilisation de bases de données non-HANA avec les charges de travail SAP sur Azure. Notez que les documents et les articles relatifs à la haute disponibilité s’appliquent également à la haute disponibilité HANA dans Azure. Les articles disponibles sont les suivants :

- [SAP Business One sur les machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Déploiement de SAP IDES EHP7 SP3 pour SAP ERP 6.0 sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Exécution de SAP NetWeaver sur des machines virtuelles Microsoft Azure SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Planification et implémentation de machines virtuelles Azure pour SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Déploiement de machines virtuelles Azure pour SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Protéger un déploiement d’applications SAP NetWeaver multiniveau à l’aide de Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Connecteur SAP LaMa pour Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

En ce qui concerne les bases de données non-HANA déployées dans le cadre d’une charge de travail SAP sur Azure, les documents disponibles sont les suivants :

- [Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Déploiement SGBD de machines virtuelles SQL Server Azure pour SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Déploiement SGBD de machines virtuelles Oracle Azure pour charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Déploiement SGBD de machines virtuelles IBM DB2 Azure pour charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Déploiement SGBD de machines virtuelles SAP ASE Azure pour charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Déploiement Content Server, liveCache et SAP MaxDB sur les machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Pour les bases de données SAP HANA sur Azure, consultez la section SAP HANA sur des machines virtuelles Azure.

Pour la haute disponibilité de la charge de travail SAP sur Azure, le document d’entrée est le suivant :

- [Haute disponibilité des machines virtuelles Azure pour SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Le document d’entrée pointe vers divers autres documents relatifs à l’architecture et aux scénarios. Les documents de scénario suivants incluent des liens vers des documents techniques détaillés qui décrivent le déploiement et la configuration des différentes méthodes de haute disponibilité. Les différents documents relatifs à l’établissement et à la configuration de la haute disponibilité pour les charges de travail SAP NetWeaver s’appliquent à la fois aux systèmes d’exploitation Linux et Windows.


Concernant l’intégration entre Azure Active Directory (AAD), les services SAP et l’authentification unique, les documents disponibles sont les suivants :

- [Didacticiel : Intégration d’Azure Active Directory avec SAP Cloud pour le client](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Didacticiel : Intégration d’Azure Active Directory à SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Didacticiel : Intégration d’Azure Active Directory avec SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Didacticiel : Intégration d’Azure Active Directory à SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Didacticiel : Intégration d’Azure Active Directory à SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Didacticiel : Intégration d’Azure Active Directory à SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Votre environnement S/4HANA - Authentification unique Fiori Launchpad SAML avec Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Concernant l’intégration des services Azure avec les composants SAP, les documents disponibles sont les suivants :

- [Utiliser SAP HANA dans Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery et SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Utiliser le connecteur SAP BW dans Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory offre l’intégration des données SAP HANA et Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




