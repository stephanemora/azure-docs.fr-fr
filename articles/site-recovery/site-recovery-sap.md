---
title: Configurer la récupération d’urgence de SAP NetWeaver à l’aide d’Azure Site Recovery
description: Découvrez comment configurer la récupération d’urgence de SAP NetWeaver à l’aide d’Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 0cef6332a169b71d7812efdc41247443fbc194f2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982363"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Configurer la reprise d’activité pour un déploiement d’application SAP NetWeaver multiniveau

La plupart des déploiements SAP, de moyenne et grande taille, utilisent une forme de solution de reprise d’activité après sinistre. Disposer de solutions de reprise d’activité fiables et testables est de plus en plus important, d’autant qu’un nombre croissant de processus métier essentiels sont déplacés vers des applications telles que SAP. Azure Site Recovery a été testé et intégré aux applications SAP. Site Recovery dépasse les capacités de la plupart des solutions de reprise d’activité après sinistre sur site, et à un coût total de possession (TCO) moins élevé que les solutions concurrentes.

Avec Site Recovery, vous pouvez effectuer les actions décrites ici.
* **Activer la protection des applications de production SAP NetWeaver et non-NetWeaver qui s’exécutent localement** par la réplication des composants vers Azure.
* **Activer la protection des applications de production SAP NetWeaver et non-NetWeaver qui s’exécutent sur Azure** par la réplication des composants sur un autre centre de données Azure.
* **Simplifier la migration vers le cloud** en utilisant Site Recovery pour migrer votre déploiement SAP vers Azure.
* **Simplifier les mises à niveau, les tests et la création de prototypes de projet SAP** par la création d’un clone de production à la demande pour tester les applications SAP.

Cet article explique comment protéger les déploiements d’applications SAP NetWeaver à l’aide d’[Azure Site Recovery](site-recovery-overview.md). L’article traite des bonnes pratiques permettant de protéger un déploiement SAP NetWeaver à trois niveaux sur Azure en répliquant vers un autre centre de données Azure à l’aide de Site Recovery. Il décrit les configurations et scénarios pris en charge ainsi que la façon de réaliser des tests de basculement (tests de récupération d’urgence) et des basculements réels.

## <a name="prerequisites"></a>Conditions préalables requises
Avant de commencer, assurez-vous que vous savez accomplir les tâches suivantes :

* [Répliquer une machine virtuelle vers Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Constituer un réseau de récupération](site-recovery-azure-to-azure-networking-guidance.md)
* [Effectuer un test de basculement vers Azure](azure-to-azure-walkthrough-test-failover.md)
* [Procéder à un basculement vers Azure](site-recovery-failover.md)
* [Répliquer un contrôleur de domaine](site-recovery-active-directory.md)
* [Répliquer SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Scénarios pris en charge
Vous pouvez utiliser Site Recovery pour implémenter une solution de reprise d’activité dans les scénarios décrits ici.
* Systèmes SAP s’exécutant dans un centre de données Azure et qui répliquent dans un autre centre de données Azure (Azure vers la reprise d’activité Azure). Pour plus d’informations, consultez [Architecture de réplication Azure vers Azure](https://aka.ms/asr-a2a-architecture).
* Systèmes SAP s’exécutant sur des serveurs VMware (ou physiques) locaux et qui répliquent sur un site de reprise d’activité dans un centre de données Azure (reprise d’activité après sinistre VMware vers Azure). Ce scénario nécessite quelques composants supplémentaires. Pour plus d’informations, consultez [Architecture de réplication VMware vers Azure](https://aka.ms/asr-v2a-architecture).
* Systèmes SAP s’exécutant sur Hyper-V localement et qui répliquent sur un site de reprise d’activité dans un centre de données Azure (reprise d’activité après sinistre Hyper-V vers Azure). Ce scénario nécessite quelques composants supplémentaires. Pour plus d’informations, consultez [Architecture de réplication Hyper-V vers Azure](https://aka.ms/asr-h2a-architecture).

Dans cet article, nous utilisons un scénario de reprise d’activité **Azure vers Azure** pour illustrer les fonctionnalités de reprise d’activité après sinistre SAP de Site Recovery. La réplication Site Recovery n’étant pas propre à l’application, le processus qui est décrit est censé s’appliquer également à d’autres scénarios.

### <a name="required-foundation-services"></a>Services de base nécessaires
Dans le scénario que cet article développe, les services de base suivants sont déployés :
* Azure ExpressRoute ou Passerelle VPN Azure
* Au moins un contrôleur de domaine Active Directory et un serveur DNS, s’exécutant dans Azure

Nous vous recommandons de mettre en place cette infrastructure avant de déployer Site Recovery.

## <a name="reference-sap-application-deployment"></a>Déploiement des applications SAP de référence

Cette architecture de référence présente une application SAP NetWeaver en cours d’exécution dans un environnement Windows sur Azure avec une haute disponibilité.  Cette architecture est déployée avec des tailles de machine virtuelle spécifiques qui peuvent être modifiées en fonction des besoins de votre organisation.

![Schéma d’un modèle de déploiement SAP classique](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Considérations relatives à la récupération d’urgence

Pour la récupération d’urgence, vous devez être en mesure de basculer vers une région secondaire. Chaque couche utilise une stratégie différente pour assurer une protection par récupération d’urgence.

#### <a name="vms-running-sap-web-dispatcher-pool"></a>Machines virtuelles exécutant un pool SAP Web Dispatcher 
Le composant Web Dispatcher sert d’équilibreur de charge pour le trafic SAP entre les serveurs d’applications SAP. Pour assurer la haute disponibilité du composant Web Dispatcher, Azure Load Balancer est utilisé pour implémenter l’installation Web Dispatcher parallèle dans une configuration de tourniquet pour la distribution du trafic HTTP(S) parmi les Web Dispatchers disponibles dans le pool des équilibreurs. Il sera répliqué à l’aide de Site Recovery, et des scripts d’automatisation serviront à configurer l’équilibrage de charge dans la région de récupération d’urgence. 

#### <a name="vms-running-application-servers-pool"></a>Machines virtuelles exécutant un pool de serveurs d’applications
Pour gérer les groupes de connexion des serveurs d’applications ABAP, la transaction SMLG est utilisée. Elle s’appuie sur la fonction d’équilibrage de charge au sein du serveur de messages des services centraux pour répartir la charge de travail entre le pool de serveurs d’applications pour le trafic des clients SAP GUI et RFC. Elle sera répliquée à l’aide de Site Recovery.

#### <a name="vms-running-sap-central-services-cluster"></a>Machines virtuelles exécutant un cluster des services centraux SAP
Cette architecture de référence exécute les services centraux sur les machines virtuelles de la couche application. Les services centraux représentent un point de défaillance unique (SPOF) potentiel lors du déploiement sur une seule machine virtuelle — un déploiement classique quand la haute disponibilité ne constitue pas une exigence.<br>

Pour implémenter une solution à haute disponibilité, il est possible d’utiliser soit un cluster de disque partagé, soit un cluster de partage de fichiers. Afin de configurer des machines virtuelles pour un cluster de disque partagé, utilisez Cluster de basculement Windows Server. Le témoin de cloud est recommandé en tant que témoin de quorum.
 > [!NOTE]
 > Site Recovery ne réplique pas le témoin cloud. Par conséquent, il est recommandé de déployer le témoin cloud dans la région de récupération d’urgence.

Pour prendre en charge l’environnement de cluster de basculement, [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) exécute la fonction de volume partagé de cluster en répliquant des disques indépendants détenus par les nœuds de cluster. Azure ne prend pas en charge les disques partagés de manière native et nécessite donc les solutions fournies par SIOS.

Une autre solution pour gérer le clustering consiste à implémenter un cluster de partage de fichiers. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) a récemment modifié le modèle de déploiement des services centraux pour accéder aux répertoires globaux /sapmnt via un chemin d’accès UNC. Il est toutefois recommandé de s’assurer que le partage UNC /sapmnt est hautement disponible. Cela peut être effectué sur l’instance des services centraux en utilisant le cluster de basculement Windows Server avec le serveur de fichiers Scale Out (SOFS) et la fonctionnalité d’espaces de stockage direct (S2D) de Windows Server 2016.
 > [!NOTE]
 > Actuellement, Site Recovery prend uniquement en charge la réplication de points cohérents en cas d’incident de machines virtuelles à l’aide d’espaces de stockage direct et du nœud passif de SIOS Datakeeper


## <a name="disaster-recovery-considerations"></a>Considérations relatives à la récupération d’urgence

Vous pouvez utiliser Site Recovery pour orchestrer le basculement d’un déploiement SAP complet entre des régions Azure.
Vous trouverez ci-dessous les étapes de configuration de la récupération d’urgence 

1. Répliquer des machines virtuelles
2. Constituer un réseau de récupération
3.  Répliquer un contrôleur de domaine
4.  Répliquer le niveau de base de données
5.  Exécuter un test de basculement
6.  Effectuer un basculement

Voici la recommandation pour la récupération d’urgence de chaque niveau utilisé dans cet exemple.

 **Niveaux SAP** | **Recommandation**
 --- | ---
**Pool SAP Web Dispatcher** |  Répliquer à l’aide de Site Recovery 
**Pool de serveurs d’applications SAP** |  Répliquer à l’aide de Site Recovery 
**Cluster des services centraux SAP** |  Répliquer à l’aide de Site Recovery 
**Machines virtuelles Active directory** |  Réplication Active Directory 
**Serveurs SQL Database** |  Réplication SQL Always On

## <a name="replicate-virtual-machines"></a>Répliquer des machines virtuelles

Pour commencer la réplication de toutes les machines virtuelles d’application SAP vers le centre de données de reprise d’activité Azure, suivez les instructions dans [Répliquer une machine virtuelle vers Azure](azure-to-azure-walkthrough-enable-replication.md).


* Pour obtenir des conseils sur la protection d’Active Directory et de DNS, consultez le document [Protéger Active Directory et DNS](site-recovery-active-directory.md).

* Pour obtenir des conseils sur la protection d’un niveau de base de données en cours d’exécution sur SQL Server, consultez le document [Protéger SQL Server](site-recovery-sql.md).

## <a name="networking-configuration"></a>Configuration de la mise en réseau

Si vous utilisez une adresse IP statique, vous pouvez spécifier l’adresse IP que vous souhaitez attribuer à la machine virtuelle. Pour définir l’adresse IP, accédez à **Paramètres Calcul et réseau** > **Carte d’interface réseau**.

![Capture d’écran qui illustre la définition d’une adresse IP privée dans le volet de carte d’interface réseau de Site Recovery](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Création d’un plan de récupération
Lors d’un basculement, un plan de récupération prend en charge le séquencement des différents niveaux d’une application multiniveau. La mise en séquence permet d’assurer la cohérence de l’application. Lorsque vous créez un plan de récupération pour une application web multiniveau, suivez les étapes décrites dans [Créer un plan de récupération à l’aide de Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Ajout de machines virtuelles à des groupes de basculement

1.  Créez un plan de récupération en ajoutant le serveur d’applications, Web Dispatcher et des machines virtuelles de services centraux SAP.
2.  Cliquez sur « Personnaliser » pour regrouper les machines virtuelles. Par défaut, toutes les machines virtuelles font partie du « Groupe 1 ».



### <a name="add-scripts-to-the-recovery-plan"></a>Ajouter des scripts au plan de récupération
Afin de vous assurer du bon fonctionnement de vos applications, vous pouvez être amené à effectuer certaines opérations sur les machines virtuelles Azure après le basculement ou pendant un test de basculement. Vous pouvez automatiser certaines opérations après le basculement. Par exemple, mettre à jour l’entrée DNS et modifier les liaisons et les connexions en ajoutant au plan de récupération les scripts correspondants.


Vous pouvez déployer les scripts Site Recovery les plus couramment utilisés dans votre compte Automation en cliquant sur le bouton « Déployer dans Azure » ci-dessous. Lorsque vous utilisez n’importe quel script publié, assurez-vous de suivre les instructions dans le script.

[![Déployer sur Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Ajoutez un script d’action préalable au « Groupe 1 » pour basculer le groupe de disponibilité SQL. Utilisez le script « ASR-SQL-FailoverAG » publié dans les exemples de script. Assurez-vous de suivre les instructions dans le script et apportez les modifications requises dans le script en conséquence.
2. Ajoutez un script d’action postérieure pour attribuer un équilibrage de charge sur les machines virtuelles de niveau web basculées (Groupe 1). Utilisez le script « ASR-AddSingleLoadBalancer » publié dans les exemples de script. Assurez-vous de suivre les instructions dans le script et apportez les modifications requises dans le script en conséquence.

![Plan de récupération SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Exécuter un test de basculement

1.  Dans le portail Azure, sélectionnez votre coffre Recovery Services.
2.  Sélectionnez le plan de récupération que vous avez créé pour les applications SAP.
3.  Sélectionnez **Test de basculement**.
4.  Pour démarrer le test de basculement, sélectionnez le point de récupération et le réseau virtuel Azure.
5.  Lorsque l’environnement secondaire est opérationnel, procédez aux validations.
6.  Lorsque les validations sont terminées, pour nettoyer l’environnement de basculement, sélectionnez **Nettoyer le test de basculement**.

Pour plus d’informations, consultez [Tester le basculement vers Azure dans Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Exécuter un basculement

1.  Dans le portail Azure, sélectionnez votre coffre Recovery Services.
2.  Sélectionnez le plan de récupération que vous avez créé pour les applications SAP.
3.  Sélectionnez **Basculement**.
4.  Pour démarrer le processus de basculement, sélectionnez le point de récupération.

Pour plus d’informations, consultez [Basculement dans Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la création d’une solution de reprise d’activité après sinistre pour les déploiements SAP NetWeaver à l’aide de Site Recovery, consultez le livre blanc téléchargeable [SAP NetWeaver: Création d’une solution de reprise d’activité après sinistre avec Site Recovery](https://aka.ms/asr_sap). Ce livre blanc, qui présente les recommandations émises pour les diverses architectures SAP, répertorie les applications et les types de machines virtuelles pris en charge pour SAP sur Azure, et décrit les options de plan de test pour votre solution de reprise d’activité après sinistre.
* Approfondissez vos connaissances sur la [réplication d’autres charges de travail](site-recovery-workload.md) à l’aide de Site Recovery.
