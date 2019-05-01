---
title: Configurer IBM Db2 HADR sur les machines virtuelles (VM) Azure | Microsoft Docs
description: Établir une haute disponibilité d’IBM Db2 LUW sur les machines virtuelles (VM) Azure.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/10/2019
ms.author: juergent
ms.openlocfilehash: a74dd1a932cac41081786f76938a5b35de62d878
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689709"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Haute disponibilité d’IBM Db2 LUW sur des machines virtuelles Azure sur SUSE Linux Enterprise Server avec Pacemaker

IBM Db2 pour Linux, UNIX et Windows (LUW) dans [configuration de récupération (HADR) haute disponibilité et de reprise après sinistre](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) se compose d’un nœud qui exécute une instance de base de données primaire et au moins un nœud qui exécute une instance de base de données secondaire. Modifications à l’instance de base de données primaire sont répliquées vers une instance de base de données secondaire synchrone ou asynchrone, selon votre configuration. 

Cet article décrit comment déployer et configurer les machines virtuelles (VM) Azure, installez l’infrastructure de cluster et installer le LUW de Db2 d’IBM avec une configuration HADR. 

L’article n’aborde pas comment installer et configurer IBM Db2 LUW HADR ou SAP installation du logiciel. Pour vous aider à accomplir ces tâches, nous fournissons des références à des manuels d’installation SAP et IBM. Cet article se concentre sur les types qui sont spécifiques à l’environnement Azure. 

Les versions IBM Db2 pris en charge sont 10.5 ou ultérieure, comme décrit dans la note SAP [1928533].

Avant de commencer une installation, consultez les notes SAP suivantes et la documentation :

| Note de SAP | Description |
| --- | --- |
| [1928533] | Applications SAP sur Azure : Produits et types de machines virtuelles pris en charge |
| [2015553] | SAP sur Azure : Prérequis pour la prise en charge |
| [2178632] | Clé de mesures de surveillance pour SAP sur Azure |
| [2191498] | SAP sur Linux avec Azure : Surveillance améliorée |
| [2243692] | Linux sur Azure (IaaS) de machine virtuelle : Problèmes de licence SAP |
| [1984787] | SUSE LINUX Enterprise Server 12 Notes d'installation |
| [1999351] | Résolution des problèmes de la surveillance Azure améliorée pour SAP |
| [2233094] | DB6 : Applications SAP sur Azure qui utilisent IBM Db2 pour Linux, UNIX et Windows - informations supplémentaires |
| [1612105] | DB6 : Forum aux questions sur Db2 avec HADR |


| Documentation | 
| --- |
| [Wiki de la Communauté SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Dispose de toutes les Notes SAP requises pour Linux |
| [Planification des Machines virtuelles et implémentation Azure pour SAP sur Linux] [ planning-guide] guide |
| [Déploiement de Machines virtuelles Azure pour SAP sur Linux] [ deployment-guide] (cet article) |
| [Déploiement de system(DBMS) de gestion pour SAP sur Linux de la base de données des Machines virtuelles] [ dbms-guide] guide |
| [Charge de travail SAP sur la liste de vérification de planification et de déploiement Azure][azr-sap-plancheck] |
| [Guides des meilleures pratiques de SUSE Linux Enterprise Server pour applications SAP 12 SP3][sles-for-sap-bp] |
| [SUSE Linux Enterprise haute disponibilité Extension 12 SP3][sles-ha-guide] |
| [Déploiement SGBD de Machines virtuelles Azure IBM Db2 pour la charge de travail SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Présentation
Pour obtenir une haute disponibilité, IBM Db2 LUW avec HADR est installé sur au moins deux machines virtuelles, qui sont déployés dans un [à haute disponibilité Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) ou à travers [Zones de disponibilité Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

Les graphiques suivants affichent une configuration du serveur de base de données de deux machines virtuelles Azure. À la fois le serveur de base de données des machines virtuelles Azure ont leur propre stockage attaché et sont en cours d’exécution. Dans HADR, une instance de base de données dans une des machines virtuelles Azure a le rôle de l’instance principale. Tous les clients sont connectés à cette instance principale. Toutes les modifications dans les transactions de base de données sont rendus persistants localement dans le journal des transactions Db2. Comme les enregistrements du journal des transactions sont rendus persistants localement, les enregistrements sont transférés via TCP/IP à l’instance de base de données sur le second serveur de base de données, le serveur de secours ou une instance de secours. L’instance de secours met à jour la base de données locale en restaurant par progression la transaction transférée des enregistrements de journal. De cette façon, le serveur de secours est synchronisé avec le serveur principal.

HADR est uniquement une fonctionnalité de réplication. Il a aucune détection des défaillances et aucune fonction de prise en charge ou le basculement automatique. Une prise en charge ou les transférer vers le serveur de secours doit être lancée manuellement par un administrateur de base de données. Pour obtenir une prise en charge automatique et la détection de défaillance, vous pouvez utiliser la fonctionnalité de clustering Linux Pacemaker. Pacemaker analyse les instances de serveur de deux bases de données. Lorsque l’instance de serveur de base de données principal tombe en panne, lance de Pacemaker un *automatique* prise en charge HADR par le serveur de secours. Pacemaker garantit également que l’adresse IP virtuelle est affectée au nouveau serveur principal.

![Vue d’ensemble de la haute disponibilité IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Pour que SAP serveurs d’applications se connectent à la base de données primaire, vous avez besoin d’un nom d’hôte virtuel et une adresse IP virtuelle. En cas de basculement, les serveurs d’applications SAP seront connecte à une nouvelle instance de base de données primaire. Dans un environnement Azure, un [équilibreur de charge Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) est nécessaire pour utiliser une adresse IP virtuelle de la façon requise pour HADR d’IBM Db2. 

Pour vous aider à mieux comprendre comment IBM Db2 LUW avec HADR et Pacemaker s’adapte à une configuration de système SAP à haute disponibilité, l’image suivante présente une vue d’ensemble d’une configuration à haute disponibilité d’un système SAP en fonction de la base de données IBM Db2. Cet article couvre uniquement IBM Db2, mais il fournit des références à d’autres articles sur la façon de configurer d’autres composants d’un système SAP.

![Vue d’ensemble complète de l’environnement de haute disponibilité IBM DB2](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Vue d’ensemble des étapes nécessaires
Pour déployer une configuration IBM Db2, vous devez procéder comme suit :

  + Planifier votre environnement.
  + Déployer les machines virtuelles.
  + Mettre à jour SUSE Linux et configurez des systèmes de fichiers.
  + Installez et configurez Pacemaker.
  + Installer [NFS hautement disponible][nfs-ha].
  + Installer [ASC/ERS sur un cluster séparé][ascs-ha].
  + Installer la base de données IBM Db2 avec l’option Distributed/haute disponibilité (SWPM).
  + Installer et créer un nœud de base de données secondaire et une instance et configurer des HADR.
  + Vérifiez que HADR fonctionne.
  + Appliquer la configuration de Pacemaker au contrôle IBM Db2.
  + Configurer l’équilibrage de charge Azure.
  + Installer principal et les serveurs d’applications de boîte de dialogue.
  + Vérifiez et adapter la configuration des serveurs d’applications SAP.
  + Effectuer un basculement et des tests de prise en charge.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planifier l’infrastructure Azure pour l’hébergement d’IBM Db2 LUW avec HADR

Terminer le processus de planification avant d’exécuter le déploiement. Planification génère la base pour le déploiement d’une configuration de Db2 avec HADR dans Azure. Les éléments clés qui doivent faire partie de la planification d’IMB Db2 LUW (partie de la base de données de l’environnement SAP) sont répertoriés dans le tableau suivant :

| Rubrique | Brève description |
| --- | --- |
| Définir des groupes de ressources Azure | Groupes de ressources où vous déployez la machine virtuelle, réseau virtuel, Azure Load Balancer et autres ressources. Peut être à nouveau ou existant. |
| Réseau virtuel / définition de sous-réseau | Où des machines virtuelles pour IBM Db2 et équilibrage de charge Azure sont déployées. Peut être existant ou nouvellement créé. |
| Machines virtuelles hébergeant IBM Db2 LUW | Taille de machine virtuelle, stockage, mise en réseau, adresse IP. |
| Nom d’hôte virtuel et l’adresse IP virtuelle pour la base de données IBM Db2| Le virtual IP ou nom d’hôte qui est utilisé pour la connexion des serveurs d’applications SAP. **db-virt-hostname**, **db-virt-ip**. |
| Délimitation Azure | Délimitation Azure ou isolation SBD (hautement recommandé). Méthode afin d’éviter des situations fractionnement cerveau est empêchée. |
| MACHINE VIRTUELLE SBD | Taille de machine virtuelle SBD, le stockage réseau. |
| Azure Load Balancer | Utilisation de Basic ou Standard (recommandé), port pour la base de données Db2 (notre recommandation 62500) de la sonde **port de sonde**. |
| Résolution de noms| Fonctionne de résolution de noms dans l’environnement. Service DNS est fortement recommandé. Fichier hosts local peut être utilisé. |
    
Pour plus d’informations sur Linux Pacemaker dans Azure, consultez [configurer Pacemaker sur SUSE Linux Enterprise Server dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker).

## <a name="deployment-on-suse-linux"></a>Déploiement sur SUSE Linux

L’agent de ressource pour IBM Db2 LUW est inclus dans SUSE Linux Enterprise Server for SAP Applications. Pour le programme d’installation qui est décrite dans ce document, vous devez utiliser SUSE Linux Server pour les Applications SAP. La place de marché Azure contient une image de SUSE Enterprise Server for SAP Applications 12 que vous pouvez utiliser pour déployer de nouvelles machines virtuelles. N’oubliez pas les différents modèles de prise en charge ou service sont proposés par SUSE via la place de marché Azure lorsque vous choisissez une image de machine virtuelle dans le Marketplace de machines virtuelles Azure. 

### <a name="hosts-dns-updates"></a>Hôtes : Mises à jour de DNS
Dressez la liste de tous les noms d’hôte, y compris les noms d’hôtes virtuels et mettre à jour de vos serveurs DNS pour activer une adresse IP appropriée pour la résolution de nom d’hôte. Si un serveur DNS n’existe pas ou vous ne pouvez pas mettre à jour et créer des entrées DNS, vous devez utiliser les fichiers d’hôte local des machines virtuelles individuelles qui font partie de ce scénario. Si vous utilisez des entrées de fichiers d’hôte, assurez-vous que les entrées sont appliquées à toutes les machines virtuelles dans l’environnement du système SAP. Toutefois, nous vous recommandons d’utiliser votre serveur DNS qui, dans l’idéal, s’étend dans Azure


### <a name="manual-deployment"></a>Déploiement manuel

Assurez-vous que le système d’exploitation sélectionné ne prend en charge/SAP compris IBM pour IBM Db2 LUW. La liste des versions de système d’exploitation pris en charge pour les machines virtuelles Azure et les versions de Db2 est disponible dans la note SAP [1928533]. La liste des versions de système d’exploitation par version de Db2 individuelle est disponible dans le tableau de disponibilité des produits de SAP. Nous vous recommandons un minimum de SLES 12 SP3 en raison d’améliorations de performances liés à Azure dans cette ou version ultérieure des versions SUSE Linux.

1. Créez ou sélectionnez un groupe de ressources.
1. Créez ou sélectionnez un réseau virtuel et un sous-réseau.
1. Créer un groupe à haute disponibilité Azure ou déployer une zone de disponibilité.
    + Pour la haute disponibilité, définissez les domaines de mise à jour maximal sur 2.
1. Créer la Machine virtuelle 1.
    + Utilisez SLES pour SAP image dans Azure Marketplace.
    + Sélectionnez le groupe à haute disponibilité Azure créé à l’étape 3, ou Zone de disponibilité.
1.  Créer la Machine virtuelle 2.
    + Utilisez SLES pour SAP image dans Azure Marketplace.
    + Sélectionnez le groupe à haute disponibilité Azure vous dans créé à l’étape 3, ou sélectionnez la Zone de disponibilité (pas la même zone comme à l’étape 3).
1. Ajoutez des disques de données pour les machines virtuelles et vérifiez ensuite la recommandation d’une installation de système de fichiers dans l’article [déploiement SGBD de Machines virtuelles Azure IBM Db2 pour la charge de travail SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Créer le cluster Pacemaker
    
Pour créer un cluster Pacemaker de base pour ce serveur IBM Db2, consultez [configurer Pacemaker sur SUSE Linux Enterprise Server dans Azure][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Installer l’environnement SAP et IBM Db2 LUW

Avant de commencer l’installation d’un environnement SAP basé sur IBM Db2 LUW, passez en revue la documentation suivante :

+ Documentation Azure
+ Documentation de SAP
+ Documentation d’IBM

Liens vers cette documentation sont fournis dans la section d’introduction de cet article.

Consultez les manuels d’installation de SAP sur l’installation des applications basées sur NetWeaver sur IBM Db2 LUW.

Vous pouvez trouver les guides sur le portail de l’aide de SAP à l’aide de la [SAP Installation Guide Finder][sap-instfind].

Vous pouvez réduire le nombre de guides affiché dans le portail en définissant les filtres suivants :

- Je veux : « Installer un nouveau système »
- Ma base de données : « IBM Db2 pour Linux, Unix et Windows »
- Filtres supplémentaires pour les versions SAP NetWeaver, la configuration de la pile ou système d’exploitation

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Indicateurs d’installation pour la configuration d’IBM Db2 LUW avec HADR

Pour configurer l’instance de base de données IBM Db2 LUW principale :

- Utiliser la haute disponibilité ou l’option distribuée.
- Installer l’instance SAP ASCS/ERS et la base de données.
- Effectuez une sauvegarde de la base de données nouvellement installé.


> [!IMPORTANT] 
> Notez que le port de Communication de base de données « » qui est défini lors de l’installation. Il doit être le même numéro de port pour les deux instances de base de données

Pour configurer le serveur de base de données de secours à l’aide de la procédure de copie de système homogène SAP, exécutez ces étapes :

1. Sélectionnez le **copie système** option > **systèmes cibles** > **Distributed** > **instance de base de données**.
1. Comme une méthode de copie, sélectionnez **système homogène** afin que vous pouvez utiliser la sauvegarde pour restaurer une sauvegarde sur l’instance de serveur de secours.
1. Lorsque vous atteignez l’étape de sortie pour restaurer la base de données pour la copie de système homogène, quittez le programme d’installation. Restaurer la base de données à partir d’une sauvegarde de l’hôte principal. Toutes les phases d’installation suivantes ont déjà été exécutés sur le serveur de base de données primaire.
1. Configurer HADR pour IBM Db2.

   > [!NOTE]
   > Pour l’installation et la configuration spécifique à Azure et Pacemaker : Au cours de la procédure d’installation via le Gestionnaire de déploiement de logiciels SAP, il y a une question explicite sur la haute disponibilité pour IBM Db2 LUW :
   >+ Ne sélectionnez pas **IBM Db2 pureScale**.
   >+ Ne sélectionnez pas **installer IBM Tivoli System Automation pour Multiplatforms**.
   >+ Ne sélectionnez pas **générer des fichiers de configuration de cluster**.

   Lorsque vous utilisez un appareil SBD pour Linux Pacemaker, définissez les paramètres de Db2 HADR suivants :
   + Durée (en secondes) (HADR_PEER_WINDOW) de la fenêtre homologue HADR = 300  
   + Valeur de délai d’attente HADR (HADR_TIMEOUT) = 60

   Lorsque vous utilisez un agent de délimitation Azure Pacemaker, définissez les paramètres suivants :
   + Durée (en secondes) (HADR_PEER_WINDOW) de la fenêtre homologue HADR = 900  
   + Valeur de délai d’attente HADR (HADR_TIMEOUT) = 60

Nous vous recommandons les paramètres précédents, selon le test de basculement/prise en charge initiale. Il est obligatoire de test pour disposer des fonctionnalités de basculement et la prise en charge avec ces paramètres. Étant donné que les configurations individuelles peuvent varier, les paramètres peuvent nécessiter d’ajustement. 

> [!IMPORTANT]
> Spécifiques à IBM Db2 avec une configuration HADR avec démarrage normal : L’instance de base de données secondaire ou de secours doit être en cours d’exécution avant de commencer l’instance de base de données primaire.

Pour des fins de démonstration et les procédures décrites dans cet article, de la base de données SID est **PTR**.

#### <a name="ibm-db2-hadr-check"></a>Vérification d’IBM Db2 HADR
Une fois que vous avez configuré HADR et l’état est homologue et connecté sur les nœuds principaux et de secours, effectuez la vérification suivante :

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Configuration de Pacemaker de DB2

Lorsque vous utilisez Pacemaker pour le basculement automatique en cas de défaillance de nœud, vous devez configurer vos instances de Db2 et le Pacemaker en conséquence. Cette section décrit ce type de configuration.

Les éléments suivants sont précédés :

- **[A]**  : S’applique à tous les nœuds
- **[1]**  : Applicable uniquement au nœud 1 
- **[2]**  : Applicable uniquement au nœud 2

**[A]**  Conditions préalables pour la configuration de Pacemaker :
1. Arrêtez les deux serveurs de base de données avec db2 de l’utilisateur\<sid > avec db2stop.
1. Modifier l’environnement de shell pour db2\<sid > utilisateur */bin/ksh*. Nous vous recommandons d’utiliser l’outil de Yast. 


### <a name="pacemaker-configuration"></a>Configuration de pacemaker

**[1]**  Configuration de Pacemaker HADR spécifique de IBM Db2 :
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  Créer IBM Db2 ressources :
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> anything \
        params binfile="/usr/bin/nc" cmdline_options="-l -k <b>62500</b>" \
        op monitor timeout="20s" interval="10" depth="0"

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]**  Démarrer IBM Db2 ressources :
* Placez Pacemaker hors du mode maintenance.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  Vous assurer que l’état du cluster est OK et que toutes les ressources sont démarrés. Il n’est pas important de savoir quel nœud les ressources sont en cours d’exécution.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 nœuds configurés
# <a name="5-resources-configured"></a>5 ressources configurées

# <a name="online--azibmdb01-azibmdb02-"></a>En ligne : [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>Liste complète des ressources :

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd    (stonith:external/sbd): Prise en main azibmdb02
#  <a name="resource-group-gipdb2ptrptr"></a>Groupe de ressources : g_ip_db2ptr_PTR
#      <a name="rscipdb2ptrptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Prise en main azibmdb02
#      <a name="rscncdb2ptrptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Prise en main azibmdb02
#  <a name="masterslave-set-msldb2db2ptrptr-rscdb2db2ptrptr"></a>Jeu de combinaison maître/esclave : msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Maîtres : [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Subordonnés : [azibmdb01]
</pre>

> [!IMPORTANT]
> Vous devez gérer le Pacemaker instance Db2 en cluster à l’aide des outils de Pacemaker. Si vous utilisez des commandes de db2, telles que db2stop, Pacemaker détecte l’action comme un échec de ressource. Si vous effectuez une maintenance, vous pouvez placer les nœuds ou les ressources en mode maintenance. Pacemaker suspend les ressources de surveillance, et vous pouvez ensuite utiliser les commandes d’administration db2 normal.


### <a name="configure-azure-load-balancer"></a>Configurer Azure Load Balancer
Pour configurer l’équilibrage de charge Azure, nous vous recommandons d’utiliser le [référence SKU équilibreur de charge Standard Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) puis procédez comme suit :

1. Créer un pool IP frontal :

   a. Dans le portail Azure, ouvrez l’équilibreur de charge Azure, sélectionnez **pool IP frontal**, puis sélectionnez **ajouter**.

   b. Entrez le nom du nouveau pool IP frontal (par exemple, **Db2 connection**).

   c. Définir le **attribution** à **statique**et entrez l’adresse IP **adresse IP virtuelle** définies au début.

   d. Sélectionnez **OK**.

   e. Une fois le pool d’adresses IP frontal créé, notez son adresse IP.

1. Créer un pool back-end :

   a. Dans le portail Azure, ouvrez l’équilibreur de charge Azure, sélectionnez **pools principaux**, puis sélectionnez **ajouter**.

   b. Entrez le nom du nouveau pool principal (par exemple, **Db2-backend**).

   c. Cliquez sur **Ajouter une machine virtuelle**.

   d. Sélectionnez le groupe à haute disponibilité ou les machines virtuelles hébergeant créé à l’étape précédente de la base de données IBM Db2.

   e. Sélectionnez les machines virtuelles du cluster IBM Db2.

   f. Sélectionnez **OK**.

1. Créer une sonde d’intégrité :

   a. Dans le portail Azure, ouvrez l’équilibreur de charge Azure, sélectionnez **sondes d’intégrité**, puis sélectionnez **ajouter**.

   b. Entrez le nom de la nouvelle sonde d’intégrité (par exemple, **Db2-hp**).

   c. Sélectionnez **TCP** en tant que le protocole et le port **62500**. Conserver le **intervalle** la valeur **5**et conserver le **seuil de défaillance** la valeur **2**.

   d. Sélectionnez **OK**.

1. Créer les règles d’équilibrage de charge :

   a. Dans le portail Azure, ouvrez l’équilibreur de charge Azure, sélectionnez **règles d’équilibrage de charge**, puis sélectionnez **ajouter**.

   b. Entrez le nom de la nouvelle règle d’équilibrage de charge (par exemple, **Db2-SID**).

   c. Sélectionnez l’adresse IP frontale, le pool back-end et la sonde d’intégrité que vous avez créé précédemment (par exemple, **Db2-frontend**).

   d. Conserver le **protocole** définie sur **TCP**et entrez le port *port de Communication de base de données*.

   e. Augmentez le **délai d’inactivité** à 30 minutes.

   f. Veillez à **activer l’IP flottante** .

   g. Sélectionnez **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Modifier les profils SAP à utiliser l’adresse IP virtuelle pour la connexion
Pour vous connecter à l’instance principale de la configuration du HADR SAP couche d’application doit utiliser l’adresse IP virtuelle qui vous définis et configurés pour l’équilibrage de charge Azure. Les modifications suivantes sont requises :

/sapmnt/\<SID >/profil/par défaut. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID > /global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Installer principal et les serveurs d’applications de boîte de dialogue

Lorsque vous installez principal et de serveurs d’applications de boîte de dialogue par rapport à une configuration de Db2 HADR, utilisez l’hôte virtuel nom que vous avez choisis pour la configuration. 

Si vous avez effectué l’installation avant que vous avez créé la configuration Db2 HADR, apportez les modifications, comme décrit dans la section précédente et comme suit pour les piles Java de SAP.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Systèmes de pile ABAP + Java ou Java URL JDBC

Utilisez l’outil de configuration de J2EE pour vérifier ou mettre à jour de l’URL JDBC. Étant donné que l’outil de configuration de J2EE est un outil graphique, vous devez disposer de X server installé :
 
1. Connectez-vous au serveur d’applications principal de l’instance de J2EE et exécutez :
     <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
1. Dans le cadre de gauche, choisissez **magasin sécurité**.
1. Dans le cadre de droite, choisissez le pool de jdbc/clé / \<SAPSID>/url.
1. Modifier le nom d’hôte dans l’URL JDBC au nom d’hôte virtuel.
     <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
1. Sélectionnez **ajouter**.
1. Pour enregistrer vos modifications, sélectionnez l’icône de disque en haut à gauche.
1. Fermez l’outil de configuration.
1. Redémarrez l’instance de Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Configurer l’archivage des journaux pour le programme d’installation HADR
Pour configurer le journal de Db2 d’archivage pour le programme d’installation HADR, nous vous recommandons de configurer le serveur principal et la base de données de secours pour que la fonctionnalité de récupération automatique des journaux à partir de tous les emplacements d’archive de journal. Les deux base de données principale et de secours doit être en mesure de récupérer les fichiers d’archive de journaux à partir de tous les emplacements d’archive de journal de la base de données soit l’instances peuvent archiver des fichiers journaux. 

Le journal d’archivage est exécutée uniquement par la base de données primaire. Si vous modifiez les rôles HADR des serveurs de base de données ou si une défaillance se produit, la nouvelle base de données principal est responsable de l’archivage des journaux. Si vous avez configuré plusieurs emplacements d’archive de journal, vos journaux peuvent être archivés à deux reprises. En cas d’une mise à jour locale ou distante, vous devrez peut-être également copier manuellement les journaux archivés à partir de l’ancien serveur principal à l’emplacement du journal actif du nouveau serveur principal.

Nous vous recommandons de configurer un partage NFS commun dans lequel les journaux sont écrits dans les deux nœuds. Le partage NFS doit être hautement disponible. 

Vous pouvez utiliser des partages NFS à haute disponibilités existants pour les transports ou un répertoire de profil. Pour plus d'informations, consultez les pages suivantes :

- [Haute disponibilité pour NFS sur les machines virtuelles Azure sur SUSE Linux Enterprise Server][nfs-ha] 
- [Haute disponibilité pour SAP NetWeaver sur machines virtuelles Azure sur SUSE Linux Enterprise Server avec Azure Files de NetApp pour les Applications SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Azure Files NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (pour créer des partages NFS)


## <a name="test-the-cluster-setup"></a>Tester la configuration du cluster

Cette section décrit comment vous pouvez tester votre configuration de Db2 HADR. *Chaque test suppose que vous êtes connecté tant qu’utilisateur racine* et IBM Db2 principal est en cours d’exécution le *azibmdb01* machine virtuelle.

L’état initial pour tous les cas de test est expliquée ici : (crm_mon - r ou crm status)

- **état de CRM** est un instantané de l’état de Pacemaker au moment de l’exécution 
- **crm_mon - r** est sortie continue d’état de Pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

L’état d’origine dans un système SAP est documentée dans Transaction DBACOCKPIT > Configuration > vue d’ensemble, comme illustré dans l’image suivante :

![DBACockpit - Migration de versions antérieures](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Prise en charge de test d’IBM Db2


> [!IMPORTANT] 
> Avant de commencer le test, assurez-vous que :
> * Pacemaker n’a pas toutes les actions ayant échouées (état crm).
> * Il n’existe aucune contrainte d’emplacement (restes de test de migration)
> * L’utilisation de la synchronisation d’IBM Db2 HADR. Vérifiez auprès d’utilisateur db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migrer le nœud qui exécute la base de données Db2 principal en exécutant la commande suivante :
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Une fois la migration est terminée, la sortie de l’état crm ressemble à :
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

L’état d’origine dans un système SAP est documentée dans Transaction DBACOCKPIT > Configuration > vue d’ensemble, comme illustré dans l’image suivante :

![DBACockpit - après la Migration](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Migration de ressources avec « migrer les ressources de crm » crée des contraintes d’emplacement. Contraintes d’emplacement doivent être supprimés. Si les contraintes d’emplacement ne sont pas supprimés, la ressource ne peut pas restaurer ou vous pouvez essayer le processus indésirables. 

Migrer la ressource vers *azibmdb01* et désactivez les contraintes d’emplacement
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **migrer des ressources de CRM \<res_name > <host>:** Crée des contraintes d’emplacement et peut entraîner des problèmes avec la prise en charge
- **ressource de CRM clair \<res_name >**: Efface les contraintes d’emplacement
- **nettoyage des ressources CRM \<res_name >**: Efface toutes les erreurs de la ressource

### <a name="test-the-fencing-agent"></a>Test de l’agent de délimitation

Dans ce cas, nous testons la délimitation SBD, nous vous recommandons d’effectuer lorsque vous utilisez SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Nœud de cluster *azibmdb01* doit être redémarré. IBM Db2 HADR son principal va être déplacé vers *azibmdb02*. Lorsque *azibmdb01* est de nouveau en ligne, le Db2 instance vont passer dans le rôle d’une instance de base de données secondaire. 

Si le service Pacemaker ne démarre pas automatiquement sur l’ancien redémarré primaire, veillez à démarrer manuellement à l’aide :

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Tester une prise en charge manuelle

Vous pouvez tester une prise de contrôle manuel en arrêtant le service Pacemaker sur *azibmdb01* nœud :
<pre><code>service pacemaker stop</code></pre>

status on *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Après le basculement, vous pouvez démarrer le service à nouveau sur *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Arrêter le processus de Db2 sur le nœud qui exécute la base de données primaire HADR

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

L’instance de Db2 va tomber en panne et Pacemaker signalera suivant l’état :

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker va redémarrer l’instance de base de données primaire Db2 sur le même nœud, ou il bascule vers le nœud qui exécute l’instance de base de données secondaire et une erreur est signalée.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Arrêter le processus de Db2 sur le nœud qui exécute l’instance de base de données secondaire

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Le nœud obtient en échec indiqué et l’erreur signalée
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

L’instance de Db2 est redémarrée dans le rôle secondaire, qu'il avait assigné avant.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Arrêter la base de données par le biais de force db2stop sur le nœud qui exécute l’instance de base de données primaire HADR

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

En tant qu’utilisateur db2\<sid > exécuter force db2stop de commande :
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Échec détecté
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

L’instance de base de données secondaire HADR de Db2 été promue dans le rôle principal
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Se bloquer de machine virtuelle avec redémarrage sur le nœud qui exécute l’instance de base de données primaire HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker promeut l’instance secondaire pour le rôle de l’instance principale. L’ancienne instance principal est déplacés dans le rôle secondaire après la machine virtuelle et tous les services sont entièrement restaurés après le redémarrage de la machine virtuelle :

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Blocage de la machine virtuelle qui exécute l’instance de base de données primaire HADR avec « halt »

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Dans ce cas, Pacemaker détecte que le nœud qui exécute l’instance de base de données primaire ne répond pas.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

l’étape suivante consiste à vérifier pour un *fractionnement cerveau* situation. Une fois que le nœud restant a déterminé que le nœud qui la dernière exécution de l’instance de base de données primaire est en panne, un basculement des ressources est exécuté.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


En cas d’un « arrêt » du nœud, le nœud défaillant doit être redémarré à l’aide des outils de gestion Azure (dans le portail Azure, PowerShell ou l’interface CLI Azure). Une fois que le nœud défaillant est en ligne, il démarre l’instance de Db2 dans le rôle secondaire.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Étapes suivantes
- [Scénarios et architecture de haute disponibilité pour SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Configurer Pacemaker sur SUSE Linux Enterprise Server dans Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

