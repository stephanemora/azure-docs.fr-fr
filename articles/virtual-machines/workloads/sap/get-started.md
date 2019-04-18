---
title: Bien démarrer avec SAP sur des machines virtuelles Azure | Microsoft Docs
description: En savoir plus sur les solutions SAP qui s’exécutent sur des machines virtuelles (VM) dans Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/10/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c22715434693abd5cebdc5ffd196a0e39b227d1b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698505"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Utiliser Azure pour héberger et exécuter des scénarios de charge de travail SAP

Lorsque vous utilisez Microsoft Azure, vous pouvez exécuter en toute fiabilité vos charges de travail SAP critiques et les scénarios sur une plateforme évolutive, conforme et éprouvée en entreprise. Vous bénéficiez de l’extensibilité, flexibilité et les économies d’Azure. Avec le partenariat étendu entre Microsoft et SAP, vous pouvez exécuter les applications SAP dans des environnements de développement et de test et de production dans Azure et entièrement pris en charge. À partir de SAP NetWeaver à SAP S/4HANA, SAP BI sur Linux à Windows et SAP HANA à SQL, nous avons tout prévu.

Outre l’hébergement des scénarios de SAP NetWeaver avec différents SGBD sur Azure, vous pouvez héberger d’autres scénarios de charge de travail SAP, comme SAP BI dans Azure. 

L’unicité d’Azure pour SAP HANA est une offre qui permet à Azure les unes des autres. Pour activer l’hébergement de davantage de mémoire et processeur exigeants en ressources SAP scénarios qui impliquent SAP HANA, Azure offre l’utilisation de matériel en métal nu dédié au client. Utilisez cette solution pour exécuter des déploiements de SAP HANA nécessitant jusqu'à 24 To (120 to scale-out) de mémoire pour S/4HANA ou d’autres charges de travail SAP HANA. 

Hébergement des scénarios de charge de travail SAP dans Azure peut également créer les exigences d’intégration des identités et l’authentification unique. Cette situation peut se produire lorsque vous utilisez Azure Active Directory (Azure AD) pour connecter différents composants SAP et SAP software-as-a-service (SaaS) ou des offres platform-as-a-service (PaaS). Une liste de ces scénarios d’intégration et seul l’authentification auprès d’Azure AD et les entités SAP est décrite et documentée dans la section « Intégration des identités AAD SAP et single sign-on ».

## <a name="latest-changes"></a>Dernières modifications

- Version de [contrôler des grandes Instances Azure HANA via le portail Azure](hana-li-portal.md)

- Version de [haute disponibilité pour SAP NetWeaver sur machines virtuelles Azure sur SUSE Linux Enterprise Server avec Azure Files de NetApp pour les Applications SAP](high-availability-guide-suse-netapp-files.md)

- Clarification sur **net.ipv4.tcp_timestamps de paramètre de système d’exploitation Linux** conjointement avec Azure, les paramètres de l’équilibreur de charge

- Version de [configurations de la charge de travail SAP avec des zones de disponibilité Azure](sap-ha-availability-zones.md)

- Mise à disposition de la [liste de contrôle relative à la planification et au déploiement de la charge de travail SAP](sap-deployment-checklist.md)




## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA sur Azure (grandes instances)

Une série de documents vous guide à travers de SAP HANA sur Azure (grandes Instances), ou pour faire court, les grandes Instances HANA. Pour plus d’informations sur les domaines suivants de grandes Instances HANA, consultez :

- [Présentation de SAP HANA sur Azure (grandes instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Architecture de SAP HANA sur Azure (grandes instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastructure et connectivité à SAP HANA sur Azure (grandes Instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Installer SAP HANA sur Azure (grandes Instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Haute disponibilité et récupération d’urgence de SAP HANA sur Azure (grandes Instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Résoudre les problèmes et surveillance de SAP HANA sur Azure (grandes Instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Étapes suivantes :

- Lecture [présentation et architecture de SAP HANA sur Azure (grandes Instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA sur machines virtuelles Azure
Cette partie de la documentation couvre différents aspects de SAP HANA. Comme condition préalable, vous devez connaître avec les services principaux d’Azure qui fournissent des services élémentaires d’Azure IaaS. Par conséquent, vous devez connaître de calcul Azure, stockage et le réseau. La plupart de ces sujets sont gérées dans le SAP NetWeaver liés [guide de planification Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

Pour plus d’informations sur HANA sur Azure, consultez les articles suivants et leurs subarticles :

- [Démarrage rapide : Installation manuelle d’un système SAP HANA à instance unique sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Déploiement de SAP S/4HANA ou BW/4HANA sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Configurations et opérations de l’infrastructure SAP HANA sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Haute disponibilité de SAP HANA pour les machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Disponibilité de SAP HANA au sein d’une région Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Disponibilité de SAP HANA dans l’ensemble des régions Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Haute disponibilité de SAP HANA sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Guide de sauvegarde pour SAP HANA sur machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Sauvegarde SAP HANA sur Azure au niveau fichier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Sauvegarde SAP HANA à partir de captures instantanées de stockage](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver déployé sur les machines virtuelles Azure
Cette section répertorie la documentation de planification et de déploiement pour SAP NetWeaver et Business One sur Azure. La documentation se concentre sur les principes de base et l’utilisation de bases de données non HANA avec une charge de travail SAP sur Azure. Les documents et les articles pour la haute disponibilité sont également la base pour la haute disponibilité HANA dans Azure, par exemple :

- [SAP Business One sur des machines virtuelles](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Déploiement de SAP IDES EHP7 SP3 pour SAP ERP 6.0 sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Exécution de SAP NetWeaver sur machines virtuelles Microsoft Azure SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Planification et implémentation de machines virtuelles Azure pour SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Déploiement de machines virtuelles Azure pour SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Protéger un déploiement d’application SAP NetWeaver multiniveau à l’aide de Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Connecteur SAP LaMa pour Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Pour plus d’informations sur les bases de données non-HANA sous une charge de travail SAP sur Azure, consultez :

- [Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Déploiement SGBD de machines virtuelles SQL Server Azure pour SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Déploiement SGBD de machines virtuelles Oracle Azure pour charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Déploiement SGBD de machines virtuelles IBM DB2 Azure pour charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Déploiement SGBD de machines virtuelles SAP ASE Azure pour charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Déploiement de SAP MaxDB, le Cache en direct et serveur de contenu sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Pour plus d’informations sur les bases de données SAP HANA sur Azure, consultez la section « SAP HANA sur machines virtuelles Azure ».

Pour plus d’informations sur la haute disponibilité d’une charge de travail SAP sur Azure, consultez :

- [Haute disponibilité des machines virtuelles Azure pour SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Ce document pointe vers divers autres documents d’architecture et le scénario. Dans les documents de scénario plus loin, des liens vers des documents techniques détaillés qui expliquent le déploiement et la configuration des différentes méthodes de haute disponibilité sont fournies. Les différents documents qui montrent comment établir et configurer la haute disponibilité pour une charge de travail SAP NetWeaver couvrent les systèmes d’exploitation Linux et Windows.


Pour plus d’informations sur l’intégration entre Azure Active Directory (Azure AD) et les services SAP et l’authentification unique, consultez :

- [Tutoriel : Intégration d’Azure Active Directory avec SAP Cloud for Customer](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutoriel : Intégration d’Azure Active Directory avec SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutoriel : Intégration d’Azure Active Directory avec SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutoriel : Intégration d’Azure Active Directory avec SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutoriel : Intégration d’Azure Active Directory avec SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutoriel : Intégration d’Azure Active Directory avec SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Votre environnement de S/4HANA : Fiori Launchpad SAML single sign-on avec Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Pour plus d’informations sur l’intégration des services Azure dans les composants SAP, consultez :

- [Utiliser SAP HANA dans Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery et SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Utiliser le connecteur SAP BW dans Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory offre l’intégration des données SAP HANA et Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




