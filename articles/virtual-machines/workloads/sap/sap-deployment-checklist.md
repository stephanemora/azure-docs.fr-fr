---
title: Check-list relative à la planification et au déploiement de la charge de travail SAP | Microsoft Docs
description: check-list relative à la planification et au déploiement de la charge de travail SAP sur Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 67083a8214724659765922047c1f0ccd6da87b9d
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54884926"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>Check-list relative à la planification et au déploiement de la charge de travail SAP sur Azure 

Cette check-list s'adresse aux clients qui migrent leurs applications SAP NetWeaver, S/4HANA et Hybris vers l'IaaS (infrastructure as a service) Azure.  Cette check-list doit être examinée par un client et/ou un partenaire SAP tout au long du projet. La plupart des vérifications doivent être effectuées en début de projet et pendant la phase de planification. À l'issue du déploiement, toute modification apportée à l'infrastructure Azure déployée ou aux versions des logiciels SAP, même mineure, peut s'avérer complexe. Examinez cette check-list lors des étapes clés d'un projet.  Les problèmes mineurs peuvent ainsi être détectés avant qu'ils ne s'aggravent en ayant le temps de remanier et de tester les modifications nécessaires. Cette check-list n'est pas exhaustive. Selon la situation, le nombre de vérifications peut être bien supérieur. 

La check-list proposée n'inclut pas les tâches réalisées indépendamment d'Azure.  Exemple : les interfaces des applications SAP changent lors d'un transfert vers le cloud public Azure ou vers un fournisseur d'hébergement.    

Cette check-list peut également être utilisée pour des systèmes déjà déployés. De nouvelles fonctionnalités telles que l'Accélérateur d'écriture, les Zones de disponibilité et de nouveaux types de machines virtuelles ont peut-être été ajoutées depuis le déploiement.  Nous vous conseillons donc d'examiner régulièrement la check-list pour vous tenir informé des nouvelles fonctionnalités ajoutées à la plateforme Azure. 

## <a name="project-preparation-and-planning-phase"></a>Phase de préparation et de planification du projet
Au cours de cette phase, une migration de la charge de travail SAP vers le cloud public Azure est planifiée. L'ensemble minimum d'entités et d'éléments discutés et définis peut être le suivant :

1. Document de conception de haut niveau - ce document doit contenir ce qui suit :
    1. L'inventaire actuel des composants et applications SAP et l'inventaire des applications cibles sur Azure.
    2. Créez et travaillez dans une matrice d’attribution des responsabilités (RACI) qui définit les responsabilités et les attributions des différentes parties impliquées. Démarrez au plus haut niveau et évoluez vers des niveaux de plus en plus granulaires pour la planification et les premiers déploiements
    2. Une architecture de solution de haut niveau.
    3. La décision relative aux régions Azure dans lesquelles le déploiement doit avoir lieu. Pour obtenir la liste des régions Azure, voir [Régions Azure](https://azure.microsoft.com/global-infrastructure/regions/). Pour connaître les services disponibles dans chacune des régions Azure, consultez l'article [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/).
    4. L'architecture de mise en réseau pour la connexion à Azure à partir d'une instance locale. Familiarisez-vous avec le [blueprint Centre de données virtuel pour Azure](https://docs.microsoft.com/azure/architecture/vdc/).
    5. Les principes de sécurité pour l'exécution des données à impact commercial élevé dans Azure. Commencez par lire la [Documentation relative à la sécurité Azure](https://docs.microsoft.com/azure/security/).
2.  Document de conception technique – qui contient ce qui suit :
    1.  Un schéma de la solution. 
    2.  Le dimensionnement des composants de calcul, de stockage et de mise en réseau dans Azure. Pour le dimensionnement SAP des machines virtuelles Azure, consultez la note de support SAP [n° 1928533](https://launchpad.support.sap.com/#/notes/1928533). 
    3.  L'architecture de continuité d'activité et de reprise d'activité.
    4.  Les versions détaillées des packs de support SE, DB, Kernel et SAP. Une version de système d’exploitation prise en charge par SAP NetWeaver ou S/4HANA ne l’est pas nécessairement sur les machines virtuelles Azure. Il en va de même pour les versions des SGBD. Les sources suivantes doivent impérativement être vérifiées pour aligner et, si nécessaire, mettre à niveau les versions des SAP, SGBD ou SE afin de figurer dans une fenêtre prise en charge par SAP et Azure. Vous devez obligatoirement disposer de combinaisons de versions prises en charge par SAP et Azure pour bénéficier d'une prise en charge complète par SAP et Microsoft. Si nécessaire, planifiez la mise à niveau de certains composants logiciels. Vous trouverez plus de détails sur les logiciels SAP, SE et SGBD pris en charge aux emplacements suivants :
        1.  Note de support SAP [n° 1928533](https://launchpad.support.sap.com/#/notes/1928533). Cette note définit les versions minimum des SE prises en charge dans les machines virtuelles Azure. Elle définit également les versions minimum requises pour la plupart des bases de données non-HANA. La note présente également le dimensionnement SAP des différents types de machines virtuelles Azure pris en charge par SAP.
        2.  Note de support SAP [n° 2039619](https://launchpad.support.sap.com/#/notes/2039619). Cette note définit la matrice de prise en charge Oracle sur Azure. Sachez que Windows et Oracle Linux sont les seuls systèmes d'exploitation pris en charge par Oracle en tant que SE invités dans la charge de travail Azure pour SAP. Cette instruction s'applique également à la couche Application SAP qui exécute les instances SAP. Toutefois, Oracle ne prend pas en charge la haute disponibilité pour les services SAP Central Services dans Oracle Linux par le biais de Pacemaker. Si vous avez besoin de la haute disponibilité pour ASCS sur Oracle Linux, vous devez exploiter SIOS Protection Suite pour Linux. Pour obtenir des données de certification SAP détaillées, consultez la note de prise en charge SAP [1662610 - Détails de prise en charge pour SIOS Protection Suite pour Linux](https://launchpad.support.sap.com/#/notes/1662610). Pour Windows, la solution de cluster de basculement Windows prise en charge par SAP pour SAP Central Services est prise en charge conjointement avec Oracle comme couche SGBD. 
        3.  Note de support SAP [n° 2235581](https://launchpad.support.sap.com/#/notes/2235581) pour obtenir la matrice de prise en charge de SAP HANA sur les différentes versions de SE.
        4.  Les machines virtuelles Azure prises en charge par SAP HANA et les [Grandes instances HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) sont répertoriées [ici](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
        5.  [Tableau de disponibilité des produits SAP](https://support.sap.com/en/)
        6.  Autres notes SAP pour d'autres produits spécifiques  
    5.  Nous recommandons des conceptions strictes à 3 niveaux pour les systèmes de production SAP. Il est déconseillé de combiner des serveurs ASCS et APP sur la même machine virtuelle.  Azure prend en charge l'utilisation de configurations de cluster multi-SID pour SAP Central Services avec Windows en tant que système d'exploitation invité. En revanche, Azure ne prend pas en charge les configurations de cluster multi-SID pour SAP Central Services sur les systèmes d'exploitation Linux. La documentation relative au SE invité Windows est disponible aux emplacements suivants :
        1.  [Haute disponibilité multi-SID pour une instance SAP ASCS/SCS avec le clustering de basculement Windows Server et un disque partagé sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [Haute disponibilité multi-SID pour une instance SAP ASCS/SCS avec le clustering de basculement Windows Server et le partage de fichiers sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  Architecture de haute disponibilité et de récupération d'urgence
        1.  Définissez l'architecture de haute disponibilité et de récupération d'urgence en fonction des objectifs RTO et RPO.
        2.  Pour une haute disponibilité dans la même zone, vérifiez ce que le SGBD souhaité peut offrir dans Azure. La plupart des SGBD offrent les méthodes synchrones d'un serveur de secours synchrone, ce que nous recommandons pour les systèmes de production. Consultez aussi la documentation SAP pour les différentes bases de données en commençant par [Facteurs à prendre en compte pour le déploiement SGBD de machines virtuelles Azure pour la charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) et les documents connexes
            1.  L’utilisation du service de cluster de basculement Windows avec une configuration de disque partagé pour la couche SGBD comme décrit pour SQL Server [ici](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017) n’est **PAS** prise en charge. Utilisez à la place des solutions comme :
                1.  [SQL Server Always On](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [Réplication de système HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  Pour la récupération d'urgence dans différentes régions Azure, vérifiez les possibilités offertes par les différents fournisseurs de SGBD. La plupart d'entre eux prennent en charge la réplication asynchrone ou la copie des journaux de transaction.
        4.  Pour la couche Application SAP, déterminez si vous souhaitez exécuter vos systèmes de test de régression métier, qui sont idéalement des réplicas de vos déploiements de production, dans la même région Azure ou dans la région de votre récupération d'urgence. Dans ce dernier cas, vous pouvez désigner ce système de régression métier comme cible de la récupération d'urgence pour votre production.
        5.  Si vous décidez de ne pas placer de systèmes hors production sur le site de reprise d’activité, envisagez Azure Site Recovery comme méthode viable de réplication de la couche Application SAP dans la région Azure de reprise d’activité. Voir aussi [Configurer la reprise d'activité pour un déploiement d'application SAP NetWeaver multiniveau](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap). 
        6.  Si vous décidez d’utiliser une configuration combinée haute disponibilité/reprise d’activité en exploitant les [zones de disponibilité Azure](https://docs.microsoft.com/azure/availability-zones/az-overview), familiarisez-vous avec les régions Azure où des zones de disponibilité sont disponibles et où des restrictions peuvent être introduites par de fortes latences réseau entre deux zones de disponibilité  
3.  Le client/partenaire doit dresser l'inventaire de toutes les interfaces SAP (SAP et non SAP). 
4.  Conception des services de base - cette conception pourrait comprendre les éléments suivants :
    1.  Conception Active Directory et DNS
    2.  Topologie de réseau dans Azure et affectation de différents systèmes SAP.
    3.  Structure d'[accès basée sur les rôles](https://docs.microsoft.com/azure/role-based-access-control/overview) pour vos différentes équipes afin d'assurer la gestion de l'infrastructure et des applications SAP dans Azure.
    3.  Topologie des groupes de ressources. 
    4.  [Stratégie de balisage](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing).
    5.  Convention d'affectation de noms pour les machines virtuelles, et autres composants d'infrastructure et/ou noms logiques.
5.  Contrat Support Premier Microsoft - Identifier le Gestionnaire de compte technique MS. Pour connaître les exigences de support SAP, lisez la note de support SAP [n° 2015553](https://launchpad.support.sap.com/#/notes/2015553). 
6.  Définissez le nombre d'abonnements Azure et le quota principal pour les différents abonnements. Si nécessaire, [ouvrez des demandes de support pour augmenter les quotas d'abonnements Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). 
7.  Plan de réduction et de migration des données pour la migration des données SAP vers Azure. Pour les systèmes SAP NetWeaver, SAP propose des directives afin de limiter les gros volumes de données. SAP a publié [ce guide détaillé](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) consacré à la gestion des données dans les systèmes SAP ERP. Toutefois, certains contenus s'appliquent aux systèmes NetWeaver et S/4HANA en général.
8.  Définissez une approche de déploiement automatisé. L'objectif de l'automatisation derrière les déploiements d'infrastructures sur Azure est de déployer de manière déterministe et d'obtenir des résultats déterministes. Beaucoup de clients utilisent des scripts basés sur Power Shell ou sur l'interface CLI. Mais différentes technologies open source sont disponibles et celles-ci peuvent être utilisées pour déployer une infrastructure Azure pour SAP, et même pour installer les logiciels SAP. Vous trouverez des exemples dans GitHub :
    1.  [Déploiements SAP automatisés dans le cloud Azure](https://github.com/Azure/sap-hana)
    2.  [Installation de SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Définissez une cadence régulière de révision de la conception et du déploiement entre vous, en tant que client, l'intégrateur système, Microsoft et les autres parties concernées.

 
## <a name="pilot-phase-strongly-recommended"></a>Phase pilote (fortement recommandée)
 
La phase pilote peut se dérouler avant la planification et la préparation du projet, ou parallèlement à celles-ci. Cette phase peut également être utilisée pour tester les approches et la conception proposées lors de la phase de planification et de préparation. La phase pilote peut se traduire par une véritable preuve de concepts. Il est recommandé de configurer et de valider une solution complète de haute disponibilité/récupération d'urgence ainsi qu'une conception de sécurité lors d'un déploiement pilote. Pour certains clients, des tests d'extensibilité peuvent également être réalisés au cours de cette phase. D'autres clients ont recours au déploiement de systèmes « bac à sable » SAP pour la phase pilote. Nous supposons donc que vous avez identifié un système que vous souhaitez migrer vers Azure afin d'exécuter un projet pilote.

1.  Optimisez le transfert de données vers Azure. Pour certains clients, le transfert via [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) depuis un emplacement local s'est révélé plus rapide, à condition qu'Express Circuit dispose de suffisamment de bande passante. Pour d'autres clients, il s'est avéré plus rapide de passer par Internet.
2.  Dans le cas d'une migration de plateforme hétérogène SAP, cela implique l'exportation et l'importation des données de la base de données, un test et l'optimisation des phases d'exportation et d'importation. Pour les migrations de grande envergure avec SQL Server comme plateforme de destination, des recommandations sont disponibles [ici](https://blogs.msdn.microsoft.com/saponsqlserver/2017/05/08/sap-osdb-migration-to-sql-server-faq-v6-2-april-2017/). Vous pouvez adopter l'approche Moniteur de migration/SWPM si vous n'avez pas besoin d'une mise à niveau combinée de versions ou un processus [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) si vous combinez la migration avec une mise à niveau de versions SAP et effectuez certaines combinaisons de plateformes SGBD sources et cibles, comme indiqué dans [Outil Database Migration Option (DMO) de SUM 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152), par exemple. 
    1.  Exporter vers la source, Exporter le fichier chargé vers Azure et Performances d'importation.  Optimisez le chevauchement entre l'exportation et l'importation.
    2.  Évaluez le volume de base de données entre la plateforme cible et la plateforme de destination afin de refléter le dimensionnement de l'infrastructure.    
    3.  Validez et optimisez le timing. 
3.  Validation technique 
    1.  Types de machines virtuelles
        1.  Validez à nouveau les ressources sur les notes de support SAP, le répertoire matériel SAP HANA et SAP PAM pour vous assurer qu'aucun changement n'est intervenu au niveau des machines virtuelles prises en charge pour Azure, des versions de SE prises en charge pour ces types de machines virtuelles et des versions de SAP et SGBD.
        2.  Validez à nouveau le dimensionnement de votre application et de l'infrastructure que vous déployez sur Azure. En cas de déplacement d'applications existantes, vous pouvez souvent extraire le SAPS nécessaire de l'infrastructure utilisée et de la [page web du benchmark SAP](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) et le comparer avec les numéros de SAPS répertoriés sur la note de support SAP [n° 1928533](https://launchpad.support.sap.com/#/notes/1928533). Gardez également [cet article](https://blogs.msdn.microsoft.com/saponsqlserver/2018/11/04/saps-ratings-on-azure-vms-where-to-look-and-where-you-can-get-confused/) à l'esprit.
        3.  Évaluez et testez le dimensionnement de vos machines virtuelles Azure en fonction du débit de stockage et du débit réseau maximum des différents types de machines virtuelles que vous avez choisis lors de la phase de planification. Vous trouverez les données aux emplacements suivants :
            1.  [Tailles des machines virtuelles Windows dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Il est important de tenir compte du **débit maximum du disque non mis en cache**  pour le dimensionnement.
            2.  [Tailles des machines virtuelles Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Il est important de tenir compte du **débit maximum du disque non mis en cache** pour le dimensionnement.
    2.  Stockage
        1.  Utilisez le Stockage Premium Azure pour les machines virtuelles des bases de données.
        2.  Utilisez des [disques managés Azure](https://azure.microsoft.com/services/managed-disks/).
        3.  Utilisez l'Accélérateur d'écriture Azure pour les lecteurs de journaux SGBD avec M-Series. Tenez compte des limites et de l'utilisation de l'Accélérateur d'écriture décrites dans [Accélérateur d'écriture](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).
        4.  Pour les différents types de SGBD, consultez la [documentation générique relative au SGBD lié à SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) et les documentations spécifiques aux SGBD auxquelles le document générique vous renvoie.
        5.  Pour SAP HANA, des informations plus détaillées sont disponibles dans [Configurations et opérations de l'infrastructure SAP HANA sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
        6.  Ne montez jamais de disques de données Azure dans une machine virtuelle Azure Linux en utilisant l’ID d’appareil. À la place, utilisez l’identificateur unique universel (UUID). Soyez prudent, par exemple lorsque vous utilisez des outils graphiques pour monter des disques de données Azure. Examinez les entrées dans /etc/fstab pour vérifier que les disques sont montés à l’aide de l’UUID
            1.  Pour plus d’informations, cliquez [ici](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
    3.  Mise en réseau
        1.  Testez et évaluez votre infrastructure VNet et la distribution de vos applications SAP sur les différents réseaux virtuels Azure.
            1.  Évaluez l'approche de l'architecture de réseau virtuel en étoile ou de la microsegmentation au sein d'un réseau virtuel Azure unique en fonction de ce qui suit :
                1.  Coûts liés à l'échange de données entre des [réseaux virtuels Azure appairés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Pour en savoir plus sur les coûts, consultez les [Tarifs des réseaux virtuels](https://azure.microsoft.com/pricing/details/virtual-network/).
                2.  Avantage de la déconnexion rapide de l'appairage entre des réseaux virtuels Azure par rapport à un changement de NSG pour isoler un sous-réseau au sein d'un réseau virtuel au cas où les applications ou les machines virtuelles hébergées sur un sous-réseau du réseau virtuel représenteraient un risque pour la sécurité.
                3.  Journalisation et audit centralisés du trafic réseau entre le site local, le monde extérieur et le centre de données virtuel que vous avez créé dans Azure.
            2.  Évaluez et testez le chemin des données entre la couche Application SAP et la couche SGBD SAP. 
                1.  Aucune des [appliances virtuelles réseau Azure](https://azure.microsoft.com/solutions/network-appliances/) présentes sur le chemin de communication entre l'application SAP et la couche SGBD d'un système SAP NetWeaver, Hybris ou S/4HANA basé sur SAP n'est prise en charge.
                2.  Le placement des couches Application SAP et SGBD SAP sur différents réseaux virtuels Azure non appairés n'est pas pris en charge.
                3.  Les [règles Azure ASG et NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) sont prises en charge pour la définition des itinéraires entre la couche Application SAP et la couche SGBD SAP.
            3.  Assurez-vous que la [mise en réseau accélérée Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) est activée sur les machines virtuelles utilisées sur la couche Application SAP et la couche SGBD SAP. N'oubliez pas que différents niveaux de SE sont nécessaires pour prendre en charge la mise en réseau accélérée dans Azure :
                1.  Windows Server 2012 R2 ou versions ultérieures.
                2.  SUSE Linux 12 SP3 ou versions ultérieures.
                3.  RHEL 7.4 ou versions ultérieures.
                4.  Oracle Linux 7.5. Pour le noyau RHCKL, la version 3.10.0-862.13.1.el7 est requise. Pour le noyau Oracle UEK, la version 5 doit être utilisée.
            4.   Testez et évaluez la latence du réseau entre la machine virtuelle de la couche Application SAP et celle de la couche SGBD, conformément aux notes de support SAP [n° 500235](https://launchpad.support.sap.com/#/notes/500235) et [n° 1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Évaluez les résultats par rapport aux indications de latence du réseau de la note de support SAP [n° 1100926](https://launchpad.support.sap.com/#/notes/1100926/E). La latence du réseau doit être modérée ou bonne. Des exceptions s'appliquent au trafic entre les machines virtuelles et les grandes instances HANA, comme indiqué dans la documentation disponible [ici](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance).
            5.   Assurez-vous que les déploiements ILB sont configurés pour utiliser le retour direct du serveur. Ce paramètre réduira la latence lorsque des ILB Azure sont utilisés pour les configurations à haute disponibilité sur la couche SGBD.
    4.   Déploiements de haute disponibilité et de récupération d'urgence. 
        1.   Si vous déployez la couche Application SAP sans définir de zone de disponibilité Azure spécifique, assurez-vous que toutes les machines virtuelles qui exécutent une instance de dialogue SAP ou des instances intergiciel d'un système SAP unique sont déployées dans un [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). 
            1.   Si vous n'avez pas besoin de la haute disponibilité pour les services SAP Central Services et le SGBD, ces machines virtuelles peuvent être déployées dans le même groupe à haute disponibilité que la couche Application SAP.
        2.   Si vous protégez les services SAP Central Services et la couche SGBD avec des réplicas passifs à des fins de haute disponibilité, définissez les deux nœuds SAP Central Services dans des groupes à haute disponibilité distincts et les deux nœuds SGBD dans un autre groupe à haute disponibilité.
        3.   Si vous procédez à des déploiements dans des zones de disponibilité Azure, vous ne pouvez pas utiliser de groupes à haute disponibilité. Toutefois, vous devez veiller à déployer les nœuds Central Services actifs et passifs dans deux zones de disponibilité différentes pour obtenir la latence la plus faible entre les zones.
            1.   N'oubliez pas que vous devez utiliser [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) afin d'établir des clusters de basculement Windows ou Pacemaker pour la couche SGBD et SAP Central Services dans les zones de disponibilité. L'[équilibreur de charge de base](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) ne peut pas être utilisé pour les déploiements zonaux. 
    5.   Paramètres liés au délai d'expiration
        1.   Consultez les rapports des développeurs de SAP NetWeaver pour les différentes instances SAP et assurez-vous qu'aucune rupture de connexion n'a été signalée entre le serveur de mise en file d'attente et les processus de travail SAP. Ces ruptures de connexion peuvent être évitées en définissant les deux paramètres de registre suivants :
            1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000 - Voir aussi [cet article](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))
            2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000 - Voir aussi [cet article](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) 
        2.   Pour éviter les expirations de délai d'attente entre les interfaces GUI et les couches Application SAP déployées dans Azure, vérifiez que les paramètres suivants sont définis dans le fichier default.pfl ou dans le profil de l'instance :
            1.   rdisp/keepalive_timeout = 3600
            2.   rdisp/keepalive = 20
        3.   Si vous utilisez une configuration de cluster de basculement Windows, assurez-vous que le temps de réaction relatifs aux nœuds non réactifs est correctement défini pour Azure. L'article Microsoft [Réglage des seuils réseau des clusters de basculement](https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/) répertorie les paramètres et leur impact sur la sensibilité de basculement. Parmi les paramètres répertoriés, ceux-ci doivent être définis sur les valeurs suivantes :
            1.   SameSubNetDelay = 2
            2.   SameSubNetThreshold = 15
4.   Testez vos procédures de haute disponibilité et de récupération d'urgence.
    1.   Simulez des situations de basculement en arrêtant les machines virtuelles (OS invité Windows) ou en mettant les systèmes d’exploitation en mode panique (OS invité Linux) pour déterminer si vos configurations de basculement fonctionnent comme prévu. 
    2.   Mesurez le temps nécessaire à l'exécution d'un basculement. Si les délais sont trop longs :
        1.   Pour SUSE Linux, utilisez des périphériques SBD plutôt que l'agent de délimitation Azure pour accélérer le basculement.
        2.   Pour SAP HANA, si le rechargement des données prend trop de temps, songez à augmenter la bande passante de stockage.
    3.   Testez la séquence et les temps de sauvegarde et de restauration, et améliorez-les si nécessaire. Ne vérifiez pas seulement les temps de sauvegarde. Testez aussi la restauration et chronométrez les activités de restauration. Vérifiez que les temps de restauration sont dans les objectifs RTO de vos contrats SLA, que votre RTO dépende d’une base de données ou d’un processus de restauration de machine virtuelle
    4.   Testez l'architecture et la fonctionnalité de récupération d'urgence dans la région.
5.  Vérifications de sécurité
    1.  Testez la validité de l'architecture d'accès basé sur les rôles Azure que vous avez implémentée. L'objectif est de séparer et de limiter l'accès et les autorisations des différentes équipes. Par exemple, les membres de l'équipe SAP Basis doivent pouvoir déployer des machines virtuelles et assigner des disques du stockage Azure à un réseau virtuel Azure donné. Toutefois, l'équipe SAP Basis ne doit pas être en mesure de créer ses propres réseaux virtuels ou de modifier les paramètres des réseaux virtuels existants. D'un autre côté, les membres de l'équipe réseau ne doivent pas être en mesure de déployer des machines virtuelles sur des réseaux virtuels où des machines virtuelles sont exécutées sur les couches Application SAP et SGBD. Les membres de l'équipe réseau ne doivent pas non plus être en mesure de modifier les attributs des machines virtuelles, ni même de supprimer des machines virtuelles ou des disques.  
    2.  Vérifiez que les règles [NSG et ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) fonctionnent comme prévu et protègent les ressources protégées.
    3.  Assurez-vous que toutes les ressources qui doivent être chiffrées le sont. Définissez et exécutez des processus pour sauvegarder les certificats, les stocker, y accéder et restaurer les entités chiffrées. 
    4.  Utilisez [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) et/ou pour les disques de système d'exploitation, si possible du point de vue de la prise en charge du système d'exploitation.
    5.  Vérifiez que le nombre de couches de chiffrement utilisé n'est pas trop important. Il est relativement logique d'utiliser Azure Disk Encryption, puis l'une des méthodes TDE (Transparent Data Encryption) SGBD.
6.  Tests de performances
    1.  Dans SAP, sur la base du suivi et des mesures SAP, comparez les 10 principaux rapports en ligne avec l'implémentation actuelle, le cas échéant. 
    2.  Dans SAP, sur la base du suivi et des mesures SAP, comparez les 10 principaux programmes de traitement par lots avec l'implémentation actuelle, le cas échéant. 
    3.  Dans SAP, sur la base du suivi et des mesures SAP, comparez les transferts de données réalisés via des interfaces vers le système SAP. Concentrez-vous sur les interfaces pour lesquelles vous savez que le transfert s'effectue désormais entre différents emplacements, par exemple d'un site local vers Azure. 


## <a name="non-production-phase"></a>Phase hors production 
Lors de cette phase, nous supposons qu'après une preuve de concept ou une phase pilote réussie, vous commencez à déployer des systèmes SAP non destinés à la production dans Azure. L'ensemble des expériences et apprentissages tirés de la preuve de concept doivent être adaptés à ce déploiement. Les critères et étapes répertoriés dans la preuve de concept s'appliquent également à ce type de déploiements. Au cours de cette phase, vous déployez généralement des systèmes de développement, des systèmes de test unitaire et des systèmes de test de régression métier dans Azure. Il est recommandé qu'au moins un des systèmes hors production d'une ligne d'applications SAP dispose de la configuration haute disponibilité complète, comme le futur système de production. Les étapes supplémentaires à prendre en compte au cours de cette phase sont les suivantes :  

1.  Avant de déplacer les systèmes de l'ancienne plateforme vers Azure, recueillez des données sur la consommation des ressources, comme l'utilisation de l'UC, le débit de stockage et les données IOPS. En particulier à partir des unités de la couche SGBD, mais aussi à partir des unités de la couche Application. Mesurez également la latence du réseau et du stockage.
2.  Enregistrez les modèles de temps d'utilisation de la disponibilité des systèmes. L'objectif est de déterminer si les systèmes hors production doivent être disponibles 7 jours sur 7 et 24 heures sur 24 ou si certains d'entre eux peuvent être arrêtés à certains moments de la semaine ou du mois.
3.  Procédez aux tests et déterminez si vous souhaitez créer des images du système d'exploitation pour vos machines virtuelles dans Azure ou utiliser une image de la bibliothèque d'images Azure. Si vous utilisez une image de la bibliothèque Azure, veillez à choisir celle qui reflète le mieux le contrat de support souscrit auprès de votre fournisseur de SE. Avec certains fournisseurs de SE, les bibliothèques Azure permettent d'apporter ses propres images sous licence. Pour les autres images de SE, le support est compris dans le prix indiqué par Azure. Si vous décidez de créer vos propres images de SE, vous trouverez la documentation dans les articles suivants :
    1.  Vous pouvez générer une image généralisée d'une machine virtuelle Windows déployée dans Azure à partir de [cette documentation](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).
    2.  Vous pouvez générer une image généralisée d'une machine virtuelle Linux déployée dans Azure à partir de [cette documentation](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image).
3.  Si vous utilisez SUSE et des images Red Hat Linux de la galerie de machines virtuelles Azure, vous devez utiliser les images pour SAP fournies par les fournisseurs de Linux dans la galerie de machines virtuelles Azure
4.  Vérifiez que vous remplissez les conditions de support requises par SAP en matière de contrats de support Microsoft. Des informations sont disponibles dans la note de support SAP [n° 2015553](https://launchpad.support.sap.com/#/notes/2015553). Pour les Grandes instances HANA, reportez-vous au document [Conditions d'intégration](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)
4.  Veillez à ce que les personnes adéquates reçoivent les [notifications de maintenance planifiée](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) afin de pouvoir choisir le temps d'arrêt et l'heure de redémarrage des machines virtuelles.
5.  Consultez régulièrement la documentation Azure des présentations Microsoft sur les canaux tels que [Channel9](https://channel9.msdn.com/) pour connaître les nouvelles fonctionnalités susceptibles de s'appliquer à vos déploiements.
6.  Consultez les notes SAP relatives à Azure, comme la note de support [n° 1928533](https://launchpad.support.sap.com/#/notes/1928533) pour connaître les nouvelles références SKU de machines virtuelles ou les nouvelles versions de SE et de SGBD prises en charge. Comparez les tarifs des anciens et nouveaux types de machines virtuelles afin de pouvoir déployer les machines virtuelles qui présentent le meilleur rapport qualité-prix.
7.  Validez à nouveau les ressources sur les notes de support SAP, le répertoire matériel SAP HANA et SAP PAM pour vous assurer qu'aucun changement n'est intervenu au niveau des machines virtuelles prises en charge pour Azure, des versions de SE prises en charge sur ces machines virtuelles et des versions de SAP et SGBD.
8.  Cliquez [ici](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) pour connaître les nouvelles références SKU certifiées HANA disponibles dans Azure et comparer les prix avec ce que vous avez planifié, puis apportez les modifications nécessaires afin de bénéficier des unités qui présentent le meilleur rapport qualité-prix. 
9.  Adaptez vos scripts de déploiement pour tirer parti des nouveaux types de machines virtuelles et incorporer les nouvelles fonctionnalités Azure que vous souhaitez utiliser.
10. À l'issue du déploiement de l'infrastructure, testez et évaluez la latence du réseau entre la machine virtuelle de la couche Application SAP et celle de la couche SGBD, conformément aux notes de support SAP [n° 500235](https://launchpad.support.sap.com/#/notes/500235) et [n° 1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Évaluez les résultats par rapport aux indications de latence du réseau de la note de support SAP [n° 1100926](https://launchpad.support.sap.com/#/notes/1100926/E). La latence du réseau doit être modérée ou bonne. Des exceptions s'appliquent au trafic entre les machines virtuelles et les grandes instances HANA, comme indiqué dans la documentation disponible [ici](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Assurez-vous qu'aucune des restrictions mentionnées dans [Facteurs à prendre en compte dans le cadre du déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) et dans [Configurations et opérations de l'infrastructure SAP HANA sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) ne s'applique à votre déploiement.
11. Effectuez toutes les autres vérifications de la phase Preuve de concept avant d'appliquer la charge de travail.
12. Au fil de l'application de la charge de travail, enregistrez la consommation de ressources de ces systèmes dans Azure et comparez-la avec les enregistrements de votre ancienne plateforme. Ajustez le dimensionnement des machines virtuelles des futurs déploiements si vous constatez des différences plus importantes. Gardez à l'esprit qu'en cas de redimensionnement, le stockage et la bande passante réseau d'une machine virtuelle seront également réduits :
    1.  [Tailles des machines virtuelles Windows dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    2.  [Tailles des machines virtuelles Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Travaillez sur la fonctionnalité et les processus de copie du système. L'objectif est de faciliter la copie d'un système de développement ou d'un système de test afin que les équipes de projet bénéficient rapidement de nouveaux systèmes. Voyez [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) comme un outil qui exécute ce type de tâches.
14. D'une part, vous pouvez optimiser et affiner les accès, les autorisations et les processus basés sur les rôles Azure de votre équipe afin d'être sûr de bénéficier d'une séparation des tâches. D'autre part, vous pouvez veiller à ce que toutes les équipes puissent accomplir leurs tâches dans l'infrastructure Azure.
15. Testez les procédures de haute disponibilité et de récupération d'urgence pour permettre à votre personnel d'exécuter de telles tâches. Identifiez les défauts et adaptez les nouvelles fonctionnalités Azure que vous intégrez à vos déploiements.

 
## <a name="production-preparation-phase"></a>Phase de préparation de la production 
Dans cette phase, vous pouvez collecter l’ensemble des expériences et enseignements tirés de vos déploiements hors production et les appliquer aux futurs déploiements de production. En plus des phases précédentes, vous devez également préparer le travail de transfert de données entre votre emplacement d'hébergement actuel et Azure. 

1.  Effectuez les mises à niveau nécessaires des versions SAP de vos systèmes de production avant de passer à Azure.
2.  Contactez les membres de la direction pour planifier les tests de fonctionnement et les tests commerciaux à réaliser après la migration du système de production.
    1.  Veillez à ce que tous ces tests soient effectués à l'emplacement d'hébergement actuel, avec les systèmes source. Mieux vaut éviter qu'ils soient effectués pour la première fois à l'issue du déplacement vers Azure du système.
2.  Testez le processus de migration des systèmes de production vers Azure. Si tous les systèmes de production ne sont pas déplacés vers Azure dans le même laps de temps, créez des groupes de systèmes de production qui doivent se trouver au même emplacement d'hébergement. Testez la migration des données. Les méthodes les plus courantes sont les suivantes :
    1.  Utilisez des méthodes SGBD telles que la sauvegarde/restauration en combinaison avec SQL Server AlwaysOn, HANA System Replication ou la copie des journaux de transaction pour alimenter et synchroniser le contenu de la base de données dans Azure.
    2.  Utilisez la sauvegarde/restauration pour les bases de données de plus petite taille.
    3.  Utilisez le Moniteur de migration SAP implémenté dans l'outil SAP SWPM pour effectuer des migrations hétérogènes.
    4.  Utilisez le processus [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) si vous avez besoin de combiner le processus avec une mise à niveau de version SAP. N'oubliez pas que certaines combinaisons entre les SGBD source et cible ne sont pas prises en charge. Pour plus d'informations, reportez-vous aux notes de support SAP spécifiques aux différentes versions de DMO. Par exemple, [Outil Database Migration Option (DMO) de SUM 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    5.  Testez le transfert de données via Internet ou ExpressRoute pour déterminer si le débit est meilleur au cas où vous auriez besoin de déplacer des sauvegardes ou des fichiers d'exportation SAP. Dans le cas du transfert de données via Internet, vous devrez peut-être changer certaines des règles de sécurité NSG/ASG à mettre en place pour les futurs systèmes de production.
3.  Avant de déplacer les systèmes de l'ancienne plateforme vers Azure, recueillez des données sur la consommation des ressources, comme l'utilisation de l'UC, le débit de stockage et les données IOPS. En particulier à partir des unités de la couche SGBD, mais aussi à partir des unités de la couche Application. Mesurez également la latence du réseau et du stockage.
4.  Validez à nouveau les ressources sur les notes de support SAP, le répertoire matériel SAP HANA et SAP PAM pour vous assurer qu'aucun changement n'est intervenu au niveau des machines virtuelles prises en charge pour Azure, des versions de SE prises en charge sur ces machines virtuelles et des versions de SAP et SGBD. 
4.  Adaptez les scripts de déploiement en fonction des dernières modifications que vous avez décidé d'apporter aux types de machines virtuelles et aux fonctionnalités Azure.
5.  Après le déploiement de l'infrastructure et de l'application, procédez à une série de vérifications afin de valider ce qui suit :
    1.  Les types de machines virtuelles appropriés ont été déployés avec les attributs et les tailles de stockage qui conviennent.
    2.  Vérifiez que les machines virtuelles utilisent les versions et correctifs appropriés et souhaités du système d'exploitation et assurez-vous qu'ils sont uniformes.
    3.  Vérifiez que le niveau de sécurisation renforcée des machines virtuelles est conforme aux exigences et uniforme.
    4.  Vérifiez que les versions et correctifs appropriés ont été installés et déployés pour l'application.
    5.  Les machines virtuelles ont été déployées comme prévu dans les groupes à haute disponibilité Azure.
    6.  Le Stockage Premium Azure a été utilisé pour les disques sensibles à la latence ou lorsque le [Contrat de niveau de service garantissant une disponibilité de 99,99 % des machines virtuelles](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) est requis.
    7.  Vérifiez que l'Accélérateur d'écriture Azure a été correctement déployé.
        1.  Assurez-vous que, dans la machine virtuelle, les espaces de stockage ou les jeux d'agrégats par bandes ont été correctement générés sur les disques nécessitant la prise en charge de l'Accélérateur d'écriture Azure.
            1.  Consultez [Configuration d’un RAID logiciel sur Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
            2.  Consultez [Configurer LVM sur une machine virtuelle Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
    8.  Seuls des [disques managés Azure](https://azure.microsoft.com/services/managed-disks/) ont été utilisés.
    9.  Les machines virtuelles ont été déployées dans les groupes à haute disponibilité et dans les zones de disponibilité appropriés.
    10. Assurez-vous que la [mise en réseau accélérée Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) est activée sur les machines virtuelles utilisées sur la couche Application SAP et la couche SGBD SAP.
    11. Aucun placement d'[appliances virtuelles réseau Azure](https://azure.microsoft.com/solutions/network-appliances/) sur le chemin de communication entre l'application SAP et la couche SGBD d'un système SAP NetWeaver, Hybris ou S/4HANA basé sur SAP.
    12. Les règles ASG et NSG permettent la communication souhaitée et planifiée, et bloquent celle-ci en cas de besoin.
    13. Les paramètres liés au délai d'expiration décrits précédemment ont été correctement définis.
    14. Testez et évaluez la latence du réseau entre la machine virtuelle de la couche Application SAP et celle de la couche SGBD, conformément aux notes de support SAP [n° 500235](https://launchpad.support.sap.com/#/notes/500235) et [n° 1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Évaluez les résultats par rapport aux indications de latence du réseau de la note de support SAP [n° 1100926](https://launchpad.support.sap.com/#/notes/1100926/E). La latence du réseau doit être modérée ou bonne. Des exceptions s'appliquent au trafic entre les machines virtuelles et les grandes instances HANA, comme indiqué dans la documentation disponible [ici](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance).
    15. Vérifiez que le chiffrement a été déployé aux emplacements nécessaires et avec la méthode de chiffrement requise.
    16. Vérifiez que les interfaces et autres applications sont en mesure de connecter l'infrastructure nouvellement déployée.
6.  Créez un playbook pour réagir à la maintenance planifiée d'Azure. Définissez l'ordre des systèmes et machines virtuelles à redémarrer en cas de maintenance planifiée.
    

## <a name="go-live-phase"></a>Phase de mise en service
Lors de la phase de mise en service, vous devez suivre les playbooks que vous avez développés au cours des phases précédentes. Exécutez les étapes que vous avez testées. N'acceptez pas les changements de dernière minute pour les configurations et processus. Par ailleurs, appliquez les mesures suivantes :

1. Vérifiez que la supervision du portail Azure et les autres outils de supervision fonctionnent.  Les outils recommandés sont Perfmon (Windows) ou SAR (Linux) : 
    1.  Compteurs UC 
        1.  Utilisation moyenne de l'UC - Total (toutes les UC)
        2.  Utilisation moyenne de l'UC - chaque processeur individuel (soit 128 processeurs sur m128 machines virtuelles)
        3.  Noyau de temps processeur - chaque processeur individuel
        4.  Utilisateur du temps processeur - chaque processeur individuel
    5.  Mémoire 
        1.  Mémoire disponible
        2.  Page mémoire entrante/s
        3.  Page mémoire sortante/s
    4.  Disque 
        1.  Lectures disque en Ko/s - par disque individuel 
        2.  Lectures disque/s - par disque individuel
        3.  Lectures disque en ms/lecture - par disque individuel
        4.  Écritures disque en Ko/s - par disque individuel 
        5.  Écritures disque/s – par disque individuel
        6.  Écritures disque en ms/lecture - par disque individuel
    5.  Réseau 
        1.  Paquets réseau entrants/s
        2.  Paquets réseau sortants/s
        3.  Ko réseau entrants/s
        4.  Ko réseau sortants/s 
2.  Après la migration des données, effectuez tous les tests de validation, comme convenu avec les membres de la direction. N'acceptez les résultats des tests de validation que lorsque vous disposez des résultats des systèmes source d'origine.
3.  Vérifiez que les interfaces fonctionnent et que d'autres applications peuvent communiquer avec les systèmes de production nouvellement déployés.
4.  Vérifiez le système de transport et de correction via la transaction SAP STMS.
5.  Effectuez des sauvegardes des bases de données une fois le système mis en production.
6.  Effectuez des sauvegardes des machines virtuelles de la couche Application SAP une fois le système mis en production.
7.  Pour les systèmes SAP ne faisant pas partie de la phase de mise en service actuelle, mais qui communiquent avec les systèmes SAP que vous avez transférés vers Azure au cours de cette phase, vous devez réinitialiser le tampon de noms d'hôte dans SM51. Cette étape supprimera les anciennes adresses IP mises en cache associées aux noms des instances de l'application que vous avez déplacées vers Azure.  


## <a name="post-production"></a>Post-production
Cette phase consiste à surveiller, exploiter et administrer le système. D'un point de vue SAP, les tâches que vous deviez habituellement exécuter avec votre ancien emplacement d'hébergement s'appliquent. Les tâches spécifiques à Azure sont les suivantes :

1. Analyser les factures Azure pour les systèmes à facturation élevée
2. Optimiser le rapport qualité-prix côté machines virtuelles et stockage
3. Optimiser le temps lorsque les systèmes peuvent être arrêtés  


## <a name="next-steps"></a>Étapes suivantes
Consultez la documentation suivante :

- [Planification et implémentation de machines virtuelles Azure pour SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Déploiement de machines virtuelles Azure pour SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Facteurs à prendre en compte pour le déploiement SGBD des machines virtuelles Azure pour la charge de travail SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

