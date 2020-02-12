---
title: Bien démarrer avec SAP sur des machines virtuelles Azure | Microsoft Docs
description: Découvrir les solutions SAP qui s’exécutent sur des machines virtuelles dans Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42df2d24aad1465a200203e648feb97d4e96fd18
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989822"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Utiliser Azure pour héberger et exécuter des scénarios de charge de travail SAP

Quand vous utilisez Microsoft Azure, vous pouvez exécuter de façon fiable vos charges de travail et scénarios SAP critiques sur une plateforme scalable, conforme et qui a fait ses preuves en entreprise. Vous bénéficiez de la scalabilité, de la flexibilité et des économies de coût fournies par Azure. Avec le partenariat étendu entre Microsoft et SAP, vous pouvez exécuter les applications SAP dans des scénarios de développement, de test et de production dans Azure en bénéficiant d’une prise en charge complète. De SAP NetWeaver à SAP S/4HANA ou SAP BI, de Linux à Windows et de SAP HANA à SQL, nous sommes là pour vous.

En plus de l’hébergement des scénarios SAP NetWeaver avec différents SGBD sur Azure, vous pouvez héberger d’autres scénarios de charge de travail SAP, comme SAP BI sur Azure. 

Le caractère unique d’Azure pour SAP HANA est une offre qui place Azure à part. Pour permettre l’hébergement de scénarios SAP demandant davantage de ressources mémoire et processeur impliquant SAP HANA, Azure offre l’utilisation de matériel physique dédié au client. Utilisez cette solution pour exécuter des déploiements de SAP HANA nécessitant jusqu’à 24 To de mémoire (avec scale-out jusqu’à 120 To) pour S/4HANA ou d’autres charges de travail SAP HANA. 

L’hébergement de scénarios de charge de travail SAP dans Azure peut également induire des exigences en matière d’intégration des identités et d’authentification unique. Cette situation peut se produire quand vous utilisez Azure Active Directory (Azure AD) pour connecter différents composants SAP et des offres SAP SaaS ou PaaS. Une liste de ces scénarios d’intégration et d’authentification unique avec Azure AD et des entités SAP est décrite et documentée dans la section « Intégration des identités et de l’authentification unique AAD SAP ».

## <a name="changes-to-the-sap-workload-section"></a>Modifications apportées à la section charge de travail SAP
Les modifications apportées aux documents dans la section charge de travail de SAP sur Azure sont répertoriées à la fin de cet article.


## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA sur Azure (grandes instances)

Une série de documents vous guident dans l’utilisation de SAP HANA sur Azure (grandes instances), qui est abrégé en « HANA - Grandes instances ». Pour plus d’informations sur les aspects suivants d’HANA - Grandes instances, consultez :

- [Présentation de SAP HANA sur Azure (grandes instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Architecture de SAP HANA sur Azure (grandes instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastructure et connectivité à SAP HANA sur Azure (grandes instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Installer SAP HANA sur Azure (grandes instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Haute disponibilité et repris d’activité de SAP HANA sur Azure (grandes instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Résoudre les problèmes et superviser SAP HANA sur Azure (grandes instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Étapes suivantes :

- Consultez [Vue d’ensemble et architecture de SAP HANA sur Azure (grandes instances)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA sur des machines virtuelles Azure
Cette partie de la documentation couvre différents aspects de SAP HANA. Vous devez avoir une bonne connaissance des principaux services d’Azure qui fournissent les services de base de la plateforme IaaS Azure. Vous devez donc connaître les ressources de calcul, de stockage et de réseau Azure. Bon nombre de ces sujets sont abordés dans le [Guide de planification Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) relatif à SAP NetWeaver. 

Pour plus d’informations sur HANA sur Azure, consultez les articles suivants et leurs sous-articles :

- [Démarrage rapide : Installation manuelle d’un système SAP HANA à instance unique sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Déploiement de SAP S/4HANA ou BW/4HANA sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Configurations et opérations de l’infrastructure SAP HANA sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Haute disponibilité de SAP HANA pour les machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Disponibilité de SAP HANA au sein d’une région Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Disponibilité de SAP HANA dans l’ensemble des régions Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Haute disponibilité de SAP HANA sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Guide de sauvegarde pour SAP HANA sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Sauvegarde SAP HANA sur Azure au niveau fichier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Sauvegarde SAP HANA à partir de captures instantanées de stockage](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver déployé sur des machines virtuelles Azure
Cette section liste la documentation sur la planification et le déploiement de SAP NetWeaver et de Business One sur Azure. La documentation se concentre sur les principes de base et l’utilisation de bases de données non-HANA avec une charge de travail SAP sur Azure. Les documents et les articles relatifs à la haute disponibilité s’appliquent également à la haute disponibilité HANA dans Azure, comme :

- [SAP Business One sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Déploiement de SAP IDES EHP7 SP3 pour SAP ERP 6.0 sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Exécution de SAP NetWeaver sur des machines virtuelles Microsoft Azure SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Planification et implémentation de machines virtuelles Azure pour SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Déploiement de machines virtuelles Azure pour SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Protéger un déploiement d’applications SAP NetWeaver multiniveau à l’aide de Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Connecteur SAP LaMa pour Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Pour plus d’informations sur les bases de données non-HANA sous une charge de travail SAP sur Azure, consultez :

- [Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Déploiement SGBD de machines virtuelles SQL Server Azure pour SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Déploiement SGBD de machines virtuelles Oracle Azure pour charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Déploiement SGBD de machines virtuelles IBM DB2 Azure pour charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Déploiement SGBD de machines virtuelles SAP ASE Azure pour charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Déploiement de Content Server, LiveCache et SAP maxDB sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Pour plus d’informations sur les bases de données SAP HANA sur Azure, consultez la section « SAP HANA sur des machines virtuelles Azure ».

Pour plus d’informations sur la haute disponibilité d’une charge de travail SAP sur Azure, consultez :

- [Haute disponibilité des machines virtuelles Azure pour SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Ce document pointe vers différents autres documents relatifs à l’architecture et aux scénarios. Des documents sur des scénarios ultérieurs incluent des liens vers des documents techniques détaillés qui décrivent le déploiement et la configuration des différentes méthodes de haute disponibilité. Les différents documents montrant comment établir et configurer la haute disponibilité pour une charge de travail SAP NetWeaver s’appliquent à la fois aux systèmes d’exploitation Linux et Windows.


Pour plus d’informations sur l’intégration entre Azure Active Directory (Azure AD), les services SAP et l’authentification unique, consultez :

- [Tutoriel : Intégration d’Azure Active Directory avec SAP Cloud for Customer](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutoriel : Intégration d’Azure Active Directory avec SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutoriel : Intégration d’Azure Active Directory avec SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutoriel : Intégration d’Azure Active Directory avec SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutoriel : Intégration d’Azure Active Directory avec SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutoriel : Intégration d’Azure Active Directory avec SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Your S/4HANA environment: Fiori Launchpad SAML single sign-on with Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Pour plus d’informations sur l’intégration de services Azure dans des composants SAP, consultez :

- [Utiliser SAP HANA dans Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery et SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Utiliser le connecteur SAP BW dans Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory offre l’intégration des données SAP HANA et Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Journal des modifications

- 03/02/2020 : Modification dans [Haute disponibilité pour SAP NetWeaver sur les machines virtuelles Azure sur SUSE Linux Enterprise Server pour les applications SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) et [Haute disponibilité pour SAP NetWeaver sur les machines virtuelles Azure sur SUSE Linux Enterprise Server avec Azure NetApp Files pour les applications SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) pour supprimer l’avertissement relatif à l’utilisation d’un tiret dans les noms d’hôtes des nœuds de cluster sur SLES
- 28/01/2020 : Modification de la [haute disponibilité de SAP HANA sur les machines virtuelles Azure sous RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) pour aligner les délais d’expiration des ressources de cluster SAP HANA sur les recommandations de Red Hat en matière de délai d’attente
- 17/01/2020 : Modifications apportées à la rubrique [Groupes de placements de proximité Azure pour une latence réseau optimale avec les applications SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) afin de modifier la section concernant le déplacement des machines virtuelles existantes vers un groupe de placements de proximité
- 17/01/2020 : Modifications apportées à la rubrique [Configurations de la charge de travail SAP avec des zones de disponibilité Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) afin de diriger le lecteur vers une procédure qui automatise les mesures de latence entre les zones de disponibilité
- 16/01/2020 : Modifications apportées à la rubrique [Guide pratique d’installation et de configuration de SAP HANA (grandes instances) sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) afin d’adapter les versions de système d’exploitation au répertoire du matériel IaaS HANA
- 16/01/2020 : Modifications apportées à la rubrique [Haute disponibilité pour SAP NetWeaver sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) afin d’ajouter des instructions concernant les systèmes SAP et l’utilisation de l’architecture ENSA2 (Enqueue Server 2)
- 10/01/2020 : Modifications apportées à [Scale-out de SAP HANA avec le nœud de secours sur les machines virtuelles Azure avec Azure NetApp Files sur SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) et à [Scale-out de SAP HANA avec le nœud de secours sur les machines virtuelles Azure avec Azure NetApp Files sur RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) pour ajouter des instructions sur la façon de rendre les modifications `nfs4_disable_idmapping` permanentes.
- 10/01/2020 : Modifications apportées à [Haute disponibilité pour SAP NetWeaver sur des machines virtuelles Azure sur SLES avec Azure NetApp Files pour les applications SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) et à [Haute disponibilité des machines virtuelles Azure pour SAP NetWeaver sur RHEL avec Azure NetApp Files pour les applications SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) pour ajouter des instructions sur le montage des volumes NFSv4 d’Azure NetApp Files.
- 23/12/2019 : Publication du [Guide de haute disponibilité pour SAP NetWeaver sur des machines virtuelles Azure sur SLES multi-SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 18/12/2019 : Publication de [Scale-out de SAP HANA avec le nœud de secours sur les machines virtuelles Azure avec Azure NetApp Files sur RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 21/11/2019 : Modifications apportées dans [Scale-out de SAP HANA avec le nœud de secours sur les machines virtuelles Azure avec Azure NetApp Files sur SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) pour simplifier la configuration du mappage d’ID NFS et changer l’interface réseau principale recommandée afin de simplifier le routage.
- 15/11/2019 : Modifications mineures apportées dans [Haute disponibilité pour SAP NetWeaver sur SUSE Linux Enterprise Server avec Azure NetApp Files pour les applications SAP](high-availability-guide-suse-netapp-files.md) et [Haute disponibilité pour SAP NetWeaver sur Red Hat Enterprise Linux avec Azure NetApp Files pour les applications SAP](high-availability-guide-rhel-netapp-files.md) pour clarifier les restrictions de taille du pool de capacité et supprimer la déclaration stipulant que seule la version NFSv3 est prise en charge.
- 12/11/2019 : Publication de [Haute disponibilité pour SAP NetWeaver sur Windows avec Azure NetApp Files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 08/11/2019 : Modifications apportées à [Haute disponibilité de SAP HANA sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](sap-hana-high-availability.md), [Configurer la réplication système SAP HANA sur les machines virtuelles Azure](sap-hana-high-availability-rhel.md), [Haute disponibilité des machines virtuelles Azure pour SAP NetWeaver sur SUSE Linux Enterprise Server pour les applications SAP](high-availability-guide-suse.md), [Haute disponibilité des machines virtuelles Azure pour SAP NetWeaver sur SUSE Linux Enterprise Server avec Azure NetApp Files](high-availability-guide-suse-netapp-files.md), [Haute disponibilité des machines virtuelles Azure pour SAP NetWeaver sur Red Hat Enterprise Linux](high-availability-guide-rhel.md), [Haute disponibilité des machines virtuelles Azure pour SAP NetWeaver sur Red Hat Enterprise Linux avec Azure NetApp Files](high-availability-guide-rhel-netapp-files.md), [Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](high-availability-guide-suse-nfs.md), [GlusterFS sur les machines virtuelles Azure sur Red Hat Enterprise Linux pour SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) pour recommander l’équilibreur de charge standard Azure  
- 08/11/2019 : Modifications apportées à [Check-list relative à la planification et au déploiement de la charge de travail SAP sur Azure](sap-deployment-checklist.md) pour clarifier la recommandation de chiffrement  
- 04/11/2019 : Modifications apportées à la [configuration de Pacemaker sur SUSE Linux Enterprise Server dans Azure](high-availability-guide-suse-pacemaker.md) pour créer le cluster directement avec la configuration de monodiffusion  
- 29/10/2019 : Publication de [Connectivité de point de terminaison public pour les machines virtuelles avec Azure Standard Load Balancer dans les scénarios de haute disponibilité SAP](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 25/10/2019 : Modifications apportées aux [configurations de stockage des machines virtuelles Azure SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) et [Scale-out de SAP HANA avec nœud de secours sur les machines virtuelles Azure avec Azure NetApp Files sur SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) pour clarifier le protocole NFS pour /hana/volume partagé
- 22/10/2019 : Modification de [Haute disponibilité pour SAP NetWeaver sur les machines virtuelles Azure sur SUSE Linux Enterprise Server pour les applications SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Haute disponibilité pour SAP NetWeaver sur les machines virtuelles Azure sur SUSE Linux Enterprise Server avec Azure NetApp Files pour les applications SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Configuration de Pacemaker sur SUSE Linux Enterprise Server dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [Haute disponibilité d’IBM Db2 LUW sur les machines virtuelles Azure sur SUSE Linux Enterprise Server avec Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) et [Haute disponibilité de SAP HANA sur les machines virtuelles Azure sur SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) pour le renforcement de la détection de l’équilibreur de charge Azure
- Modification de la section ANF et de la section d’en-tête dans [Configurations du stockage des machines virtuelles SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 21/10/2019 : Publication de [Scale-out de SAP HANA avec le nœud de secours sur les machines virtuelles Azure avec Azure NetApp Files sur SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 16/10/2019 : Correction de liens rompus dans [Sauvegarder et restaurer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore).
- 16/10/2019 : Modification du système d’exploitation minimal recommandé de SLES 12 SP3 en SLES 12 SP4 dans [Haute disponibilité d’IBM Db2 LUW sur les machines virtuelles Azure sur SUSE Linux Enterprise Server avec Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm).
- 11/10/2019 : Modifications apportées aux configurations de stockage sur disque Ultra et introduction d’ANF dans [Configurations du stockage des machines virtuelles SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage).
- 01/10/2019 : Modification des graphismes de la rubrique [Groupes de placements de proximité Azure pour une latence réseau optimale avec les applications SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) pour plus de clarté
- 01/10/2019 : Modification de la rubrique [Configurations et opérations de l’infrastructure SAP HANA sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) pour corriger des instructions relatives au partage NFS hautement disponible pour /Hana/Shared. 
- 28/09/2019 : Modification la rubrique [Configuration de Pacemaker sur Red Hat Entreprise Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) pour clarifier le fait que SBD en tant que mécanisme de délimitation n’est pas pris en charge sur des clusters RHEL  
- 17/09/2019 : Modification du Guide de planification et de déploiement NetWeaver afin d'unifier les termes liés à l’extension de machine virtuelle pour SAP  
- 22/08/2019 : Modifications apportées à la [configuration de Pacemaker sur SUSE Linux Enterprise Server dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) afin de mettre à jour les URL pour la création de rôle personnalisée  
- 16/08/2019 : Modifications apportées à [Configuration de Pacemaker sur Red Hat Enterprise Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) pour rappeler aux clients de mettre à jour les actions dans le rôle personnalisé s’ils effectuent une mise à jour vers la nouvelle version de l’agent de clôture Azure  
- 15/08/2019 : Modifications apportées à [Configurations du stockage des machines virtuelles SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) pour refléter la disponibilité générale du disque de stockage Ultra (anciennement disque SSD Ultra)
- 01/08/2019 : Modifications apportées à [Configuration de Pacemaker sur SUSE Linux Enterprise Server dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) pour intégrer les modifications spécifiquement pour SLES 15 
- 23/07/2019 : Modifications apportées à [Mettre en cluster une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide du partage de fichiers dans Azure](sap-high-availability-guide-wsfc-file-share.md) pour refléter la prise en charge de l’espace de stockage direct par Azure Site Recovery Services
- 14/07/2019 : Publication de [Groupes de placements de proximité Azure pour une latence réseau optimale avec les applications SAP | Microsoft Docs](sap-proximity-placement-scenarios.md)
- 11/07/2019 : Modifications apportées à divers documents couvrant les grandes instances HANA afin de couvrir la révision 4 des grandes instances HANA
- 09/07/2019 : Publication d’un nouveau guide pour [IBM Db2 HADR dans Red Hat Enterprise Server](high-availability-guide-rhel-ibm-db2-luw.md)
- 13/06/2019 : Publication de [Haute disponibilité pour SAP NetWeaver sur Red Hat Enterprise Linux avec Azure NetApp Files pour les applications SAP](high-availability-guide-rhel-netapp-files.md)


