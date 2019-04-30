---
title: Connecteur SAP LaMa pour Azure | Microsoft Docs
description: Gestion de systèmes SAP sur Azure à l’aide de SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/17/2018
ms.author: sedusch
ms.openlocfilehash: f09f66e81ec4878aedebfee9be4c0c67b75c8ad6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463002"
---
# <a name="sap-lama-connector-for-azure"></a>Connecteur SAP LaMa pour Azure

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Instruction générale de support : si vous avez besoin d’aide concernant SAP LaMa ou le connecteur Azure, ouvrez toujours un incident avec SAP sur le composant BC-VCM-LVM-HYPERV.

De nombreux clients utilisent SAP LaMa pour exploiter et surveiller leur environnement SAP. Depuis la version 3.0 SP05, SAP LaMa est livré par défaut avec un connecteur pour Azure. Vous pouvez utiliser ce connecteur pour libérer et démarrer des machines virtuelles, ainsi que pour copier, déplacer et supprimer des disques managés. Ces opérations de base vous permettent de déplacer, copier, cloner et actualiser des systèmes SAP à l’aide de SAP LaMa.

Ce guide vous explique comment configurer le connecteur Azure pour SAP LaMa, comment créer des machines virtuelles utilisables pour l’installation de systèmes SAP adaptatifs et comment les configurer.

> [!NOTE]
> Le connecteur est uniquement disponible dans l’édition Enterprise de SAP LaMa.

## <a name="resources"></a>Ressources

Les notes SAP associées à la question de SAP LaMa sur Azure sont les suivantes :

| Numéro de la note | Intitulé |
| --- | --- |
| [2343511] |Connecteur Microsoft Azure pour SAP Landscape Management (LaMa) |
| [2350235] |SAP Landscape Management 3.0 - Édition Enterprise |

Consultez également le [portail d’aide SAP pour SAP LaMa](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Remarques générales

* Dans Setup (Configuration) -> Settings (Paramètres) -> Engine (Moteur), veillez à activer *Automatic Mountpoint Creation* (Création de point de montage automatique).  
  Si SAP LaMa monte des volumes à l’aide de SAP Adaptive Extensions sur une machine virtuelle, le point de montage doit exister si ce paramètre est désactivé.

* Ayez recours à un sous-réseau distinct et n’utilisez pas d’adresses IP dynamiques afin d’empêcher le « vol » d’adresses IP lorsque vous déployez de nouvelles machines virtuelles et que la préparation d’instances SAP est annulée.  
  Si vous utilisez dans le sous-réseau l’allocation d’adresses IP dynamiques, qui est également appliquée par SAP LaMa, la préparation d’un système SAP avec SAP LaMa risque d’échouer. Si la préparation d’un système SAP est annulée, les adresses IP ne sont pas réservées et peuvent donc être allouées à d’autres machines virtuelles.

* Si vous ouvrez une session sur des ordinateurs hôtes gérés, veillez à ne pas bloquer le démontage des systèmes de fichiers.  
  Si vous ouvrez une session sur une machine virtuelle Linux et que vous remplacez le répertoire de travail par un répertoire dans un point de montage, par exemple /usr/sap/AH1/ASCS00/exe, le volume ne peut pas être démonté, et un déplacement ou une annulation de préparation échouent.

## <a name="set-up-azure-connector-for-sap-lama"></a>Configurer le connecteur Azure pour SAP LaMa

Le connecteur Azure est fourni à partir de la version SAP LaMa 3.0 SP05. Nous vous recommandons d’installer systématiquement le dernier package de support et le dernier correctif pour SAP LaMa 3.0. Le connecteur Azure utilise un principal de service pour l’autorisation sur Microsoft Azure. Pour créer un principal de service pour SAP Landscape Management (LaMa), procédez comme suit.

1. Accédez à https://portal.azure.com
1. Ouvrez le panneau Azure Active Directory
1. Cliquez sur Inscriptions des applications.
1. Cliquez sur Ajouter.
1. Entrez un nom, sélectionnez le Type d’Application « Application/API Web », entrez une URL de connexion (par exemple http :\//localhost), puis cliquez sur Créer
1. L’URL de connexion n’est pas utilisée et peut être une URL valide
1. Sélectionnez la nouvelle application, puis cliquez sur Clés dans l’onglet Paramètres.
1. Entrez une description pour la nouvelle clé, sélectionnez « N’expire jamais », puis cliquez sur Enregistrer.
1. Notez la valeur. Cette valeur est utilisée comme mot de passe pour le principal de service.
1. Notez l’ID de l’application. Cette valeur est utilisée comme nom d’utilisateur du principal de service.

Par défaut, le principal de service ne possède pas les autorisations d’accéder à vos ressources Azure. Vous devez accorder au principal de service les autorisations d’accès à ces ressources.

1. Accédez à https://portal.azure.com
1. Ouvrez le panneau Groupes de ressources.
1. Sélectionnez le groupe de ressources à utiliser.
1. Cliquez sur Contrôle d’accès (IAM)
1. Cliquez sur Ajouter une attribution de rôle.
1. Sélectionnez le rôle Contributeur.
1. Entrez le nom de l’application que vous avez créée ci-dessus
1. Cliquez sur Enregistrer.
1. Répétez les étapes 3 à 8 pour tous les groupes de ressources que vous souhaitez utiliser dans SAP LaMa.

Ouvrez le site web SAP LaMa et accédez à Infrastructure (Infrastructure). Accédez à l’onglet Cloud Managers (Gestionnaires de cloud) et cliquez sur Add (Ajouter). Sélectionnez l’adaptateur cloud Microsoft Azure, puis cliquez sur Next (Suivant). Entrez les informations suivantes :

* Étiquette : choisissez un nom pour l’instance de connecteur.
* Nom d’utilisateur : ID d'application du principal de service
* Mot de passe : clé/mot de passe du principal de service.
* URL : conservez l’URL par défaut https://management.azure.com/.
* Intervalle de supervision (secondes) : doit être au moins égal à 300.
* ID d’abonnement : ID d’abonnement Azure
* ID de locataire Azure Active Directory : ID du locataire Azure Active Directory.
* Hôte proxy : nom d’hôte du proxy si SAP LaMa a besoin d’un proxy pour se connecter à Internet.
* Port du proxy : port TCP du proxy.

Cliquez sur Test Configuration (Tester la configuration) pour valider vos entrées. Vous devriez voir s’afficher le message suivant :

Connexion établie : la connexion à Microsoft Cloud a réussi. 7 resource groups found (only 10 groups requested) (7 groupes de ressources ont été trouvés [seuls 10 groupes ont été demandés]).

Ce message doit apparaître au bas du site web.

## <a name="provision-a-new-adaptive-sap-system"></a>Approvisionner un nouveau système SAP adaptatif

Vous pouvez déployer une machine virtuelle manuellement ou utiliser l’un des modèles Azure accessibles à partir du [référentiel de démarrage rapide](https://github.com/Azure/azure-quickstart-templates). Ce référentiel contient des modèles pour [SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), les [serveurs d’applications SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps) et la [base de données](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). Vous pouvez également utiliser ces modèles pour approvisionner de nouveaux hôtes dans le cadre d’une opération de copie/clonage d’un système, etc.

Nous vous recommandons de recourir à un sous-réseau distinct pour toutes les machines virtuelles que vous souhaitez gérer avec SAP LaMa, et de ne pas utiliser d’adresses IP dynamiques afin d’empêcher le « vol » d’adresses IP lorsque vous déployez de nouvelles machines virtuelles et que la préparation d’instances SAP est annulée.

> [!NOTE]
> Dans la mesure du possible, supprimez toutes les extensions de machine virtuelle, car elles sont susceptibles d’entraîner de longues durées d’exécution pour le détachement des disques d’une machine virtuelle.

Assurez-vous que l’utilisateur \<hanasid>adm, \<sapsid>adm et le groupe sapsys existent sur la machine cible avec le même ID et identificateur de groupe (GID) ou utilisent LDAP. Activez et démarrez le serveur NFS sur les machines virtuelles qui doivent être utilisées pour l’exécution de SAP NetWeaver (A)SCS.

### <a name="manual-deployment"></a>Déploiement manuel

SAP LaMa communique avec la machine virtuelle à l’aide de l’agent hôte SAP. Si vous déployez les machines virtuelles manuellement ou que vous n’utilisez pas le modèle Azure Resource Manager du référentiel de démarrage rapide, veillez à installer le dernier agent hôte SAP et SAP Adaptive Extensions. Pour plus d’informations sur les niveaux de correctif requis pour Azure, consultez la note SAP [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Déploiement manuel d’une machine virtuelle Linux

Créez une machine virtuelle avec l’un des systèmes d’exploitation pris en charge répertoriés dans la note SAP [2343511]. Ajoutez des configurations IP supplémentaires pour les instances SAP. Chaque instance doit disposer d’au moins une adresse IP et doit être installée à l’aide d’un nom d’hôte virtuel.

L’instance SAP NetWeaver ASCS a besoin de disques pour /sapmnt/\<SAPSID>, /usr/sap/\<SAPSID>, /usr/sap/trans et /usr/sap/\<sapsid>adm. Les serveurs d’applications SAP NetWeaver ne nécessitent pas de disques supplémentaires. Tous les éléments associés à l’instance SAP doivent être stockés sur l’instance ASCS et exportés par le biais de NFS. Dans le cas contraire, SAP LaMa ne vous permet pas pour l’instant d’ajouter des serveurs d’applications supplémentaires.

![SAP NetWeaver ASCS sur Linux](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Déploiement manuel pour SAP HANA

Créez une machine virtuelle avec l’un des systèmes d’exploitation pris en charge pour SAP HANA qui sont répertoriés dans la note SAP [2343511]. Ajoutez une configuration IP supplémentaire pour SAP HANA, ainsi qu’une configuration pour chaque locataire HANA.

SAP HANA doit disposer de disques pour /hana/shared, /hana/backup, /hana/data et /hana/log.

![SAP HANA sur Linux](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Déploiement manuel pour Oracle Database sur Linux

Créez une machine virtuelle avec l’un des systèmes d’exploitation pris en charge pour les bases de données Oracle qui sont répertoriés dans la note SAP [2343511]. Ajoutez une configuration IP supplémentaire pour la base de données Oracle.

La base de données Oracle doit disposer de disques pour /oracle, /home/oraod1 et /home/oracle.

![Base de données Oracle sur Linux](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Déploiement manuel pour Microsoft SQL Server

Créez une machine virtuelle avec l’un des systèmes d’exploitation pris en charge pour Microsoft SQL Server qui sont répertoriés dans la note SAP [2343511]. Ajoutez une configuration IP supplémentaire pour l’instance SQL Server.

Le serveur de base de données SQL Server doit disposer de disques pour les données de base de données et les fichiers journaux, ainsi que de disques pour c:\usr\sap.

![Base de données Oracle sur Linux](media/lama/sap-lama-db-sql.png)

Veillez à installer un pilote Microsoft ODBC Driver for SQL Server pris en charge sur la machine virtuelle vers laquelle vous souhaitez déplacer un serveur d’applications SAP NetWeaver ou que vous souhaitez utiliser comme cible de copie/clonage d’un système.

SAP LaMa ne peut pas déplacer SQL Server proprement dit ; SQL Server doit donc être préinstallé sur la machine virtuelle vers laquelle vous souhaitez déplacer une instance de base de données ou que vous voulez utiliser comme cible de copie/clonage d’un système.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Déployer une machine virtuelle à l’aide d’un modèle Azure

Téléchargez les dernières archives disponibles à partir de la [place de marché des logiciels SAP](https://support.sap.com/swdc) pour le système d’exploitation des machines virtuelles :

1. SAPCAR 7.21
1. SAP HOST AGENT 7.21
1. SAP ADAPTIVE EXTENSION 1.0 EXT

Téléchargez également les composants ci-après à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download).

1. Package redistribuable Microsoft Visual C++ 2010 (x64) (Windows uniquement)
1. Pilote Microsoft ODBC Driver for SQL Server (SQL Server uniquement)

Ces composants sont requis pour le déploiement du modèle. Le moyen le plus simple de les mettre à la disposition du modèle consiste à les charger dans un compte de stockage Azure et à créer une signature d’accès partagé (SAP).

Les modèles présentent les paramètres suivants :

* sapSystemId : ID du système SAP. Cet ID est utilisé pour la création de la disposition du disque (par exemple, /usr/sap/\<sapsid>).

* computerName : nom d’ordinateur de la nouvelle machine virtuelle. Ce paramètre est également utilisé par SAP LaMa. Lorsque vous utilisez ce modèle pour approvisionner une nouvelle machine virtuelle dans le cadre de la copie d’un système, SAP LaMa attend que l’hôte portant ce nom d’ordinateur soit accessible.

* osType : type du système d’exploitation que vous souhaitez déployer.

* dbtype : type de la base de données. Ce paramètre permet de déterminer le nombre de configurations IP supplémentaires à ajouter, ainsi que la disposition du disque souhaitée.

* sapSystemSize : taille du système SAP que vous souhaitez déployer. Ce paramètre est utilisé pour déterminer le type et la taille d’instance de machine virtuelle.

* adminUsername : nom d’utilisateur pour la machine virtuelle.

* adminPassword : mot de passe pour la machine virtuelle. Vous pouvez également fournir une clé publique pour le protocole SSH.

* sshKeyData : clé SSH publique pour les machines virtuelles. Ce paramètre est uniquement pris en charge pour les systèmes d’exploitation Linux.

* subnetId : ID du sous-réseau que vous souhaitez utiliser.

* deployEmptyTarget : vous pouvez déployer une cible vide si vous souhaitez utiliser la machine virtuelle en tant que cible pour un déplacement d’instance ou une opération similaire. Dans ce cas, aucun disque ni configuration IP supplémentaires ne sont attachés.

* sapcarLocation : emplacement de l’application sapcar qui correspond au système d’exploitation que vous déployez. L’application sapcar permet d’extraire les archives que vous fournissez dans d’autres paramètres.

* sapHostAgentArchiveLocation : emplacement de l’archive de l’agent hôte SAP. L’agent hôte SAP est déployé dans le cadre du déploiement de ce modèle.

* sapacExtLocation : emplacement de SAP Adaptive Extensions. La note SAP [2343511] répertorie le niveau de correctif minimal requis pour Azure.

* vcRedistLocation : emplacement du runtime VC requis pour l’installation de SAP Adaptive Extensions. Ce paramètre est uniquement requis pour Windows.

* odbcDriverLocation : emplacement du pilote ODBC que vous souhaitez installer. Microsoft ODBC Driver for SQL Server est le seul pilote pris en charge.

* sapadmPassword : mot de passe de l’utilisateur sapadm.

* sapadmId : identificateur Linux de l’utilisateur sapadm. Ce paramètre n’est pas requis pour Windows.

* sapsysGid : identificateur de groupe Linux du groupe sapsys. Ce paramètre n’est pas requis pour Windows.

* _artifactsLocation : URI de base où se situent les artefacts requis par ce modèle. Lorsque le modèle est déployé à l’aide des scripts fournis, un emplacement privé de l’abonnement est utilisé, et cette valeur est générée automatiquement. Ce paramètre n’est nécessaire que si vous ne déployez pas le modèle à partir de GitHub.

* _artifactsLocationSasToken : jeton sasToken nécessaire pour accéder à l’emplacement _artifactsLocation. Lorsque le modèle est déployé à l’aide des scripts fournis, un jeton sasToken est généré automatiquement. Ce paramètre n’est nécessaire que si vous ne déployez pas le modèle à partir de GitHub.

### <a name="sap-hana"></a>SAP HANA

Dans les exemples ci-après, nous supposons que vous installez SAP HANA présentant l’ID système HN1, ainsi que le système SAP NetWeaver présentant l’ID système AH1. Les noms d’hôtes virtuels sont hn1-db pour l’instance HANA, ah1-db pour le locataire HANA utilisé par le système SAP NetWeaver, ah1-ascs pour SAP NetWeaver ASCS et ah1-di-0 pour le premier serveur d’applications SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sap-hana"></a>Installer SAP NetWeaver ASCS pour SAP HANA

Avant de démarrer SAP Software Provisioning Manager (SWPM), vous devez monter l’adresse IP du nom d’hôte virtuel de l’instance ASCS. La méthode recommandée consiste à utiliser sapacext. Si vous montez l’adresse IP à l’aide de sapacext, veillez à remonter l’adresse IP après un redémarrage.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

Exécutez SWPM et utilisez *ah1-ascs* pour le champ *ASCS Instance Host Name* (Nom d’hôte d’instance ASCS).

![Linux][Logo_Linux] Linux  
Ajoutez le paramètre de profil ci-après au profil d’agent hôte SAP, situé à l’emplacement /usr/sap/hostctrl/exe/host_profile. Pour plus d’informations, consultez la note SAP [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-hana"></a>Installer SAP HANA

Si vous installez SAP HANA à l’aide de l’outil en ligne de commande hdblcm, utilisez le paramètre --hostname pour fournir un nom d’hôte virtuel. Vous devez ajouter l’adresse IP du nom d’hôte virtuel de la base de données à une interface réseau. La méthode recommandée consiste à utiliser sapacext. Si vous montez l’adresse IP à l’aide de sapacext, veillez à remonter l’adresse IP après un redémarrage.

Ajoutez un autre nom d’hôte virtuel et une autre adresse IP pour le nom utilisé par les serveurs d’applications pour la connexion au locataire HANA.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Exécutez l’installation de l’instance de base de données de SWPM sur la machine virtuelle du serveur d’applications, et non sur la machine virtuelle HANA. Dans la boîte de dialogue *Database for SAP System* (Base de données du système SAP), utilisez *ah1-db* comme valeur du champ *Database Host* (Hôte de base de données).

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Installer le serveur d’applications SAP NetWeaver pour SAP HANA

Avant de démarrer SAP Software Provisioning Manager (SWPM), vous devez monter l’adresse IP du nom d’hôte virtuel du serveur d’applications. La méthode recommandée consiste à utiliser sapacext. Si vous montez l’adresse IP à l’aide de sapacext, veillez à remonter l’adresse IP après un redémarrage.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

Il est recommandé d’utiliser le paramètre de profil SAP NetWeaver dbs/hdb/hdb_use_ident afin de définir l’identité utilisée pour rechercher la clé dans le magasin d’utilisateurs HDB. Vous pouvez ajouter ce paramètre manuellement après l’installation de l’instance de base de données avec SWPM ou exécuter SWPM avec.

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Si vous définissez ce paramètre manuellement, vous devez également créer des entrées de magasin d’utilisateurs HDB.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Dans la boîte de dialogue *Primary Application Server Instance* (Instance du serveur d'applications principal), utilisez *ah1-di-0* pour la valeur du champ *PAS Instance Host Name* (Nom d'hôte d'instance PAS).

#### <a name="post-installation-steps-for-sap-hana"></a>Étapes post-installation pour SAP HANA

Veillez à sauvegarder la base de données SYSTEMDB et toutes les bases de données de locataire avant d’essayer de copier un locataire, de déplacer un locataire ou de créer une réplication de système.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

Dans les exemples ci-après, nous supposons que vous installez le système SAP NetWeaver présentant l’ID système AS1. Les noms d’hôtes virtuels sont as1-db pour l’instance SQL Server utilisée par le système SAP NetWeaver, as1-ascs pour SAP NetWeaver ASCS et as1-di-0 pour le premier serveur d’applications SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Installer SAP NetWeaver ASCS pour SQL Server

Avant de démarrer SAP Software Provisioning Manager (SWPM), vous devez monter l’adresse IP du nom d’hôte virtuel de l’instance ASCS. La méthode recommandée consiste à utiliser sapacext. Si vous montez l’adresse IP à l’aide de sapacext, veillez à remonter l’adresse IP après un redémarrage.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Exécutez SWPM et utilisez *as1-ascs* pour le champ *ASCS Instance Host Name* (Nom d’hôte d’instance ASCS).

#### <a name="install-sql-server"></a>Installer SQL Server

Vous devez ajouter l’adresse IP du nom d’hôte virtuel de la base de données à une interface réseau. La méthode recommandée consiste à utiliser sapacext. Si vous montez l’adresse IP à l’aide de sapacext, veillez à remonter l’adresse IP après un redémarrage.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Exécutez l’installation de l’instance de base de données de SWPM sur la machine virtuelle SQL Server. Utilisez SAPINST_USE_HOSTNAME=*as1-db* afin de remplacer le nom d’hôte utilisé pour la connexion à SQL Server. Si vous avez déployé la machine virtuelle à l’aide du modèle Azure Resource Manager, veillez à définir le répertoire utilisé pour les fichiers de données de base de données sur *C:\sql\data*, et le répertoire du fichier journal de base de données sur *C:\sql\log*.

Assurez-vous que l’utilisateur *NT AUTHORITY\SYSTEM* a accès à SQL Server et dispose du rôle serveur *sysadmin*. Pour plus d’informations, consultez les notes SAP [1877727] et [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Installer le serveur d’applications SAP NetWeaver

Avant de démarrer SAP Software Provisioning Manager (SWPM), vous devez monter l’adresse IP du nom d’hôte virtuel du serveur d’applications. La méthode recommandée consiste à utiliser sapacext. Si vous montez l’adresse IP à l’aide de sapacext, veillez à remonter l’adresse IP après un redémarrage.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Dans la boîte de dialogue *Primary Application Server Instance* (Instance du serveur d'applications principal), utilisez *as1-di-0* pour la valeur du champ *PAS Instance Host Name* (Nom d'hôte d'instance PAS).

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="errors-and-warnings-during-discover"></a>Erreurs et avertissements pendant la découverte

* L’autorisation SELECT a été refusée
  * « [Microsoft][ODBC SQL Server Driver][SQL Server]The SELECT permission was denied on the object 'log_shipping_primary_databases', database 'msdb', schema 'dbo'. [SOAPFaultException]  
  The SELECT permission was denied on the object 'log_shipping_primary_databases', database 'msdb', schema 'dbo'. »
  * Solution  
    Assurez-vous que *NT AUTHORITY\SYSTEM* peut accéder à SQL Server. Consultez la note SAP [2562184].


### <a name="errors-and-warnings-for-instance-validation"></a>Erreurs et avertissements pour la validation d’instance

* Une exception a été levée lors de la validation du magasin d’utilisateurs HDB  
  * « see Log Viewer  
    com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException : Exception in validator with ID 'RuntimeHDBConnectionValidator' (Validation: 'VALIDATION_HDB_USERSTORE'): Could not retrieve the hdbuserstore  
    HANA userstore is not in the correct location »
  * Solution  
    Assurez-vous que /usr/sap/AH1/hdbclient/install/installation.ini est correct.

### <a name="errors-and-warnings-during-a-system-copy"></a>Erreurs et avertissements lors d’une copie de système

* Une erreur s’est produite lors de la validation de l’étape d’approvisionnement du système
  * « Caused by: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r »
  * Solution  
    Sauvegardez toutes les bases de données du système HANA source.

* Étape *Start* (Démarrer) de copie du système d’instance de base de données
  * « Host Agent Operation '000D3A282BC91EE8A1D76CF1F92E2944' failed (OperationException. FaultCode: '127', Message: 'Command execution failed. : [Microsoft][ODBC SQL Server Driver][SQL Server]User does not have permission to alter database 'AS2', the database does not exist, or the database is not in a state that allows access checks.') »
  * Solution  
    Assurez-vous que *NT AUTHORITY\SYSTEM* peut accéder à SQL Server. Consultez la note SAP [2562184].

### <a name="errors-and-warnings-during-a-system-clone"></a>Erreurs et avertissements lors d’un clonage de système

* Une erreur s’est produite lors de la tentative d’inscription de l’agent d’instance à l’étape *Forced Register and Start Instance Agent* (Forcer l’inscription et démarrer l’agent d’instance) du serveur d’applications ou de l’instance ASCS
  * « Error occurred when trying to register instance agent. (RemoteException: 'Failed to load instance data from profile '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0':  Cannot access profile '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': No such file or directory.')
  * Solution  
   Assurez-vous que le partage sapmnt sur ASCS/SCS dispose d’un accès total à SAP_AS1_GlobalAdmin.

* Une erreur s’est produite à l’étape *Enable Startup Protection for Clone* (Activer la protection du clone au démarrage)
  * Failed to open file '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0' Cause: No such file or directory
  * Solution  
    Le compte d’ordinateur du serveur d’applications doit disposer d’un accès en écriture au profil.

### <a name="errors-and-warnings-during-create-system-replication"></a>Erreurs et avertissements lors de la création d’une réplication de système

* Exception lors de la sélection de l’opération de création d’une réplication de système
  * « Caused by: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException   Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r »
  * Solution  
    Vérifiez si sapacext peut être exécuté en tant que `<hanasid`>adm.

* Erreur lorsque la copie complète n’est pas activée à l’étape de stockage
  * « An error occurred when reporting a context attribute message for path IStorageCopyData.storageVolumeCopyList:1 and field targetStorageSystemId »
  * Solution  
    Ignorez les avertissements de l’étape et réessayez. Ce problème sera résolu dans un nouveau package de support/correctif de SAP LaMa.

### <a name="errors-and-warnings-during-relocate"></a>Erreurs et avertissements pendant le déplacement

* Le chemin d’accès « /usr/sap/AH1 » n’est pas autorisé pour les réexportations nfs
  * « Check SAP Note [2628497] for details. »
  * Solution  
    Ajoutez des exportations ASCS au profil d’agent hôte ASCS. Consultez la note SAP [2628497].

* Fonction non implémentée lors du déplacement d’ASCS
  * Sortie de la commande : exportfs: host:/usr/sap/AX1: Function not implemented
  * Solution  
    Assurez-vous que le service de serveur NFS est activé sur la machine virtuelle cible du déplacement.

### <a name="errors-and-warnings-during-application-server-installation"></a>Erreurs et avertissements lors de l’installation du serveur d’applications

* Erreur lors de l’exécution de l’étape SAPinst : getProfileDir
  * ERROR: (Last error reported by the step: Caught ESAPinstException in module call: Validator of step '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_readProfileDir|ind|ind|ind|ind|readProfile|0|getProfileDir' reported an error: Node \\\as1-ascs\sapmnt\AS1\SYS\profile does not exist. Start SAPinst in interactive mode to solve this problem) »
  * Solution  
    Assurez-vous que SWPM s’exécute avec un utilisateur ayant accès au profil. Cet utilisateur peut être configuré dans l’Assistant d’installation du serveur d’applications.

* Erreur lors de l’exécution de l’étape SAPinst : askUnicode
  * ERROR: (Last error reported by the step: Caught ESAPinstException in module call: Validator of step '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_getUnicode|ind|ind|ind|ind|unicode|0|askUnicode' reported an error: Start SAPinst in interactive mode to solve this problem) »
  * Solution  
    Si vous utilisez un noyau SAP récent, SWPM ne peut pas déterminer si le système est un système unicode à l’aide du serveur de messages d’ASCS. Pour plus d’informations, consultez la note SAP [2445033].  
    Ce problème sera résolu dans un nouveau package de support/correctif de SAP LaMa.  
    Pour contourner ce problème, définissez le paramètre de profil OS_UNICODE=uc dans le profil par défaut de votre système SAP.

* Erreur lors de l’exécution de l’étape SAPinst : dCheckGivenServer
  * Error executing SAPinst step: dCheckGivenServer" version="1.0" ERROR: (Last error reported by the step: \<p> Installation was canceled by user. \</p> »
  * Solution  
    Assurez-vous que SWPM s’exécute avec un utilisateur ayant accès au profil. Cet utilisateur peut être configuré dans l’Assistant d’installation du serveur d’applications.

* Erreur lors de l’exécution de l’étape SAPinst : checkClient
  * Error executing SAPinst step: checkClient" version="1.0" ERROR: (Last error reported by the step: \<p> Installation was canceled by user. \</p>) »
  * Solution  
    Assurez que Microsoft ODBC Driver for SQL Server est installé sur la machine virtuelle sur laquelle vous souhaitez installer le serveur d’applications.

* Erreur lors de l’exécution de l’étape SAPinst : copyScripts
  * Last error reported by the step: System call failed. DETAILS: Error 13 (0x0000000d) (Permission denied) in execution of system call 'fopenU' with parameter (\\\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), line (494) in file (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp), stack trace:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream(args_t const& _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl::CSyFileStream2Impl(const CSyPath & \\\aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * Solution  
    Assurez-vous que SWPM s’exécute avec un utilisateur ayant accès au profil. Cet utilisateur peut être configuré dans l’Assistant d’installation du serveur d’applications.

* Erreur lors de l’exécution de l’étape SAPinst : askPasswords
  * Last error reported by the step: System call failed. DETAILS: Error 5 (0x00000005) (Access is denied.) in execution of system call 'NetValidatePasswordPolicy' with parameter (...), line (359) in file (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), stack trace:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  CSiStepExecute.cpp: 764: CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp: 56: DarkModeGuiEngine::showDialogCalledByJs()  
  DarkModeDialog.cpp: 85: DarkModeDialog::submit()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validatePasswordPolicy(args_t const& _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,*****) const )
  * Solution  
    Veillez à ajouter une règle d’hôte à l’étape *Isolation* (Isolation) pour autoriser la communication entre la machine virtuelle et le contrôleur de domaine.

## <a name="next-steps"></a>Étapes suivantes
* [Guide des opérations SAP HANA sur Azure][hana-ops-guide]
* [Planification et implémentation de machines virtuelles Azure pour SAP][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP][dbms-guide]
