---
title: Prise en charge de l’évaluation et de la migration VMware dans Azure Migrate
description: Découvrez la prise en charge de l’évaluation ou de la migration VMware dans Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 135680a9b0b6c8b5520958c884d99a83f1f87c88
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196278"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>Tableau de prise en charge pour l’évaluation et la migration VMware

Vous pouvez utiliser [Azure Migrate](migrate-overview.md) pour évaluer et migrer des machines vers le cloud Microsoft Azure. Cet article récapitule les paramètres et les limites de la prise en charge de l’évaluation et la migration de machines virtuelles VMware locales.


## <a name="vmware-scenarios"></a>Scénarios VMware

Le tableau récapitule les scénarios pris en charge pour les machines virtuelles VMware.

**Déploiement** | **Détails**
--- | ---
**Évaluer des machines virtuelles VMware locales** | [Configurez](tutorial-prepare-vmware.md) votre première évaluation.<br/><br/> [Exécutez](scale-vmware-assessment.md) une évaluation à grande échelle.
**Migrer des machines virtuelles VMware** | Vous pouvez migrer avec une migration sans agent, ou bien utiliser une migration basée sur un agent. [En savoir plus](server-migrate-overview.md)


## <a name="azure-migrate-projects"></a>Projets Azure Migrate

**Support** | **Détails**
--- | ---
**Autorisations Azure** | Vous avez besoin d’autorisations Contributeur ou Propriétaire dans l’abonnement pour créer un projet Azure Migrate.
**Limitations VMware**  | Évaluez jusqu’à 35 000 machines virtuelles VMware dans un même projet. Vous pouvez créer plusieurs projets dans un abonnement Azure. Un projet peut inclure à la fois des machines virtuelles VMware et des machines virtuelles Hyper-V, jusqu’aux limites d’évaluation.
**Zone géographique** | [Passez en revue](migrate-support-matrix.md#supported-geographies) les zones géographiques prises en charge.

**Zone géographique** | **Emplacement de stockage des métadonnées**
--- | ---
Azure Government | Gouvernement américain - Virginie
Asie-Pacifique | Asie Est ou Asie Sud-Est
Australie | Australie Est ou Australie Sud-Est
Brésil | Brésil Sud
Canada | Canada Centre ou Canada Est
Europe | Europe Nord ou Europe Ouest
France | France Centre
Inde | Inde Centre ou Inde Sud
Japon |  Japon Est ou Japon Ouest
Corée du Sud | Corée Centre ou Corée Sud
Royaume-Uni | Royaume-Uni Sud ou Royaume-Uni Ouest
États-Unis | USA Centre ou USA Ouest 2


 > [!NOTE]
 > La prise en charge d’Azure Government est actuellement disponible pour l’[ancienne version](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) d’Azure Migrate uniquement.


## <a name="application-discovery"></a>Découverte des applications

Azure Migrate : L’évaluation de serveur peut découvrir des applications, des rôles et des fonctionnalités. La découverte de votre inventaire d’applications vous permet d’identifier et de planifier un chemin de migration adapté à vos charges de travail locales. Azure Migrate : L’évaluation de serveur propose la découverte sans agent, en utilisant les informations d’identification de l’invité de la machine, en accédant à distance à des machines à l’aide des appels WMI et SSH.

**Support** | **Détails**
--- | ---
Machines prises en charge | Machines virtuelles VMware locales
Système d’exploitation des machines | Toutes les versions de Windows et Linux
Informations d'identification | Prend actuellement en charge l’utilisation d’une information d’identification pour tous les serveurs Windows et d’une information d’identification pour tous les serveurs Linux. Vous créez un compte d’utilisateur invité pour les machines virtuelles Windows et un compte d’utilisateur standard/normal (accès non-sudo) pour toutes les machines virtuelles Linux.
Limites de machine pour la découverte d’applications | 10 000 par appliance. 35 000 par projet

## <a name="assessment-vcenter-server-requirements"></a>Évaluation - Exigences relatives vCenter Server

Ce tableau récapitule la prise en charge et les limitations de l’évaluation pour les serveurs de virtualisation VMware.

**Support** | **Détails**
--- | ---
**Serveur vCenter** | Les machines virtuelles VMware que vous voulez évaluer doivent être gérées par un ou plusieurs serveurs vCenter exécutant la version 5.5, 6.0, 6.5 ou 6.7.

## <a name="assessment-vcenter-server-permissions"></a>Évaluation - Autorisations de vCenter Server

Azure Migrate doit accéder à vCenter Server pour découvrir les machines virtuelles à des fins d’évaluation et de migration sans agent.

- Si vous envisagez de découvrir des applications ou de visualiser la dépendance sans agent, créez un compte vCenter Server avec un accès en lecture seule, ainsi que des privilèges activés pour **Machines virtuelles** > **Opérations invité**.

  ![Privilèges du compte vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Si vous n’envisagez pas d’effectuer une détection d’applications et une visualisation de la dépendance sans agent, configurez un compte en lecture seule pour vCenter Server.

## <a name="assessment-appliance-requirements"></a>Évaluation - Exigences relatives à l’appliance

Azure Migrate exécute une appliance légère pour découvrir les machines virtuelles VMware et envoyer les métadonnées et les données de performances de la machine virtuelle à Azure Migrate. L’appliance pour VMware est déployée à l’aide d’un modèle OVA importé dans vCenter Server. Le tableau suivant récapitule les exigences de l’appliance.

**Support** | **Détails**
--- | ---
**Étapes de déploiement d’appliance** | Vous déployez l’appliance en tant que machine virtuelle VMware. Vous devez disposer de suffisamment de ressources sur vCenter Server pour allouer une machine virtuelle avec 32 Go de RAM, 8 processeurs virtuels, environ 80 Go de stockage sur disque et un commutateur virtuel externe.<br/><br/> L’appliance nécessite un accès à Internet, directement ou via un proxy.<br/> La machine virtuelle de l’appliance doit être déployée sur un hôte ESXi exécutant la version 5.5 ou ultérieure.
**Projet Azure Migrate** | Une appliance peut être associée à un seul projet. <br/> Un nombre quelconque d’appliances peut être associé à un même projet.<br/> Vous pouvez évaluer jusqu’à 35 000 machines virtuelles par projet.
**Découverte** | Une appliance peut découvrir jusqu’à 10 000 machines virtuelles VMware sur un serveur vCenter.<br/> Une appliance ne peut se connecter qu'à un seul vCenter Server.
**Groupe d’évaluation** | Vous pouvez ajouter jusqu’à 35 000 machines dans un groupe unique.
**Évaluation** | Vous pouvez évaluer jusqu’à 35,000 machines virtuelles par évaluation.


## <a name="assessment-url-access-requirements"></a>Évaluation - Exigences relatives à l’accès aux URL

L’appliance Azure Migrate a besoin d’une connectivité Internet.

- Quand vous déployez l’appliance, Azure Migrate effectue un contrôle de connectivité aux URL récapitulées dans le tableau ci-dessous.
- Si vous utilisez un proxy basé sur les URL pour vous connecter à Internet, autorisez l’accès à ces URL, en vérifiant que le proxy résout tous les enregistrements CNAME reçus lors de la recherche des URL.

**URL** | **Détails**  
--- | --- |
*. portal.azure.com  | Accédez à Azure Migrate dans le portail Azure.
\* .windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Connectez-vous à votre abonnement Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Créez des applications Active Directory pour que l’appliance communique avec le service Azure Migrate.
management.azure.com | Créez des applications Active Directory pour que l’appliance communique avec le service Azure Migrate.
dc.services.visualstudio.com | Chargez les journaux d’applications utilisés pour la supervision interne.
*.vault.azure.net | Gérez les secrets dans Azure Key Vault.
*.servicebus.windows.net | Communication entre l’appliance et le service Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Connectez-vous aux URL du service Azure Migrate.
*.blob.core.windows.net | Chargez des données dans des comptes de stockage.
https://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Utilisé pour les mises à jour de l’appliance Azure Migrate.

## <a name="assessment-port-requirements"></a>Évaluation - Exigences relatives aux ports

**Appareil** | **Connection**
--- | ---
Appliance | Connexions entrantes sur le port TCP 3389 pour permettre des connexions Bureau à distance avec l’appliance.<br/><br/> Connexions entrantes sur le port 44368 pour accéder à distance à l’application de gestion de l’appliance via l’URL : ```https://<appliance-ip-or-name>:44368``` <br/><br/>Connexions sortantes sur les ports 443, 5671 et 5672 pour envoyer les métadonnées de découverte et de performances à Azure Migrate.
Serveur vCenter | Connexions entrantes sur le port TCP 443 pour permettre à l’appliance de collecter les métadonnées de configuration et de performances pour les évaluations. <br/><br/> L’appliance se connecte à vCenter sur le port 443 par défaut. Si le serveur vCenter écoute sur un autre port, vous pouvez modifier le port lors de la configuration de la découverte.

## <a name="assessment-dependency-visualization"></a>Visualisation des dépendances d’évaluation

La visualisation des dépendances vous permet de visualiser les dépendances entre les machines que vous voulez utiliser et migrer. En général, vous utilisez le mappage des dépendances lorsque vous souhaitez évaluer les machines avec des niveaux de confiance élevés. Pour les machines virtuelles VMware, la visualisation des dépendances est prise en charge comme suit :

- **Visualisation des dépendances sans agent** : Cette option est actuellement en préversion. Il n’est pas nécessaire d’installer des agents sur les machines.
    - Une capture des données de connexion TCP des machines pour lesquelles elle est activée suffit. Une fois la découverte des dépendances lancée, l’appliance recueille les données des machines selon un intervalle d’interrogation de cinq minutes.
    - Les données suivantes sont collectées :
        - Connexions TCP
        - Noms des processus ayant des connexions actives
        - Noms des applications installées exécutant les processus ci-dessus
        - Non. de connexions détectées à chaque intervalle d’interrogation
- **Visualisation des dépendances basée sur les agents** : Pour utiliser la visualisation des dépendances basée sur les agents, vous devez télécharger et installer les agents suivants sur chaque machine locale que vous souhaitez analyser.
    - Microsoft Monitoring Agent (MMA) doit être installé sur chaque machine. [En savoir plus](how-to-create-group-machine-dependencies.md#install-the-mma) sur l’installation de l’agent MMA.
    - Le programme Dependency Agent doit être installé sur chaque machine. [En savoir plus](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) sur l’installation de l’agent de dépendances.
    - En outre, si certaines de vos machines sont dépourvues de connexion Internet, vous devez télécharger et installer la passerelle Log Analytics sur ces machines.

## <a name="migration---limitations"></a>Migration - Limitations
Vous pouvez sélectionner jusqu’à 10 machines virtuelles à la fois pour la réplication. Si vous souhaitez migrer davantage de machines, répliquez-les dans des groupes de 10. Pour la migration sans agent VMware, vous pouvez exécuter jusqu’à 100 réplications simultanément.

## <a name="agentless-migration-vmware-server-requirements"></a>Migration sans agent - Exigences relatives au serveur VMware

Ce tableau récapitule la prise en charge et les limitations de l’évaluation pour les serveurs de virtualisation VMware.

**Support** | **Détails**
--- | ---
Serveur vCenter | Version 5.5, 6.0, 6.5 ou 6.7.
VMWare vSphere | Version 5.5, 6.0, 6.5 ou 6.7,

## <a name="agentless-migration-vcenter-server-permissions"></a>Migration sans agent - Autorisations de vCenter Server

**autorisations** | **Détails**
--- | ---
Datastore.Browse | Autoriser l’exploration des fichiers journaux des machines virtuelles pour résoudre les problèmes de création et de suppression des instantanés.
Datastore.LowLevelFileOperations | Autoriser les opérations de lecture/écriture/suppression/renommage dans l’explorateur de magasin de données pour résoudre les problèmes de création et de suppression des instantanés.
VirtualMachine.Configuration.DiskChangeTracking | Autoriser l’activation ou la désactivation du suivi des modifications des disques de machine virtuelle pour extraire les blocs de données modifiés entre les instantanés
VirtualMachine.Configuration.DiskLease | Autoriser les opérations de bail de disque pour une machine virtuelle, à lire le disque en utilisant VDDK (vSphere Virtual Disk Development Kit) VMware vSphere.
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | Autoriser l’ouverture d’un disque sur une machine virtuelle pour lire le disque avec VDDK.
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | Autorise les opérations de lecture sur les fichiers associés à une machine virtuelle, à télécharger les journaux et à résoudre les problèmes en cas d’échec.
VirtualMachine.SnapshotManagement.* | Autoriser la création et la gestion des instantanés de machines virtuelles pour la réplication.
Virtual Machine.Interaction.Power Off | Autoriser la mise hors tension de la machine virtuelle pendant la migration vers Azure.


## <a name="agentless-migration-vmware-vm-requirements"></a>Migration sans agent - Exigences relatives aux machines virtuelles VMware

**Support** | **Détails**
--- | ---
**Systèmes d’exploitation pris en charge** | Les systèmes d’ exploitation [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) et [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) pris en charge par Azure peuvent être migrés avec une migration sans agent.
**Modifications nécessaires pour Azure** | Certaines machines virtuelles peuvent nécessiter des modifications pour fonctionner dans Azure. Azure Migrate effectue automatiquement ces modifications pour les systèmes d’exploitation suivants :<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8<br/><br/> Pour les autres systèmes d’exploitation, vous devez effectuer les ajustements manuellement avant la migration. Les articles pertinents contiennent des instructions sur la façon de procéder.
**Démarrage Linux** | Si /boot se trouve sur une partition dédiée, il doit être le disque du système d’exploitation et ne pas être réparti sur plusieurs disques.<br/> Si /boot fait partie de la partition racine (/), la partition « / » doit se trouver sur le disque du système d’exploitation et ne pas s’étendre sur d’autres disques.
**Démarrage UEFI** | Les machines virtuelles avec démarrage UEFI ne sont pas prises en charge pour la migration.
**Taille du disque** | Disque de système d’exploitation de 2 To ; 4 To pour des disques de données.
**Limites du disque** |  Jusqu'à 60 disques par machine virtuelle.
**Disques/volumes chiffrés** | Les machines virtuelles avec des disques/volumes chiffrés ne sont pas prises en charge pour la migration.
**Cluster de disque partagé** | Non pris en charge.
**Disques indépendants** | Non pris en charge.
**RDM/disques directs** | Si les machines virtuelles ont des disques RDM ou directs, ces disques ne seront pas répliqués dans Azure.
**NFS** | Les volumes NFS montés en tant que volumes sur les machines virtuelles ne sont pas répliqués.
**Cibles iSCSI** | Les machines virtuelles avec des cibles iSCSI ne sont pas prises en charge pour la migration sans agent.
**Multipath I/O** | Non pris en charge.
**Storage vMotion** | Non pris en charge. La réplication ne fonctionne pas si une machine virtuelle utilise Storage vMotion.
**Cartes réseau associées** | Non pris en charge.
**IPv6** | Non pris en charge.
**Disque cible** | Les machines virtuelles peuvent être migrées seulement vers des disques managés (disques durs standard, disques SSD Premium) dans Azure.
**Réplication simultanée** | 100 machines virtuelles par serveur VCenter Server. Si vous en avez plus, migrez-les par lots de 100.


## <a name="agentless-migration-appliance-requirements"></a>Migration sans agent - Exigences relatives à l’appliance


**Support** | **Détails**
--- | ---
**ESXi** | La machine virtuelle de l’appliance doit être déployée sur un hôte ESXi exécutant la version 5.5 ou ultérieure.
**Projet Azure Migrate** | Une appliance peut être associée à un seul projet.
**vCenter Server** | Une appliance peut découvrir jusqu’à 10 000 machines virtuelles VMware sur un serveur vCenter.<br/> Une appliance ne peut se connecter qu’à un seul serveur vCenter.
**VDDK** | Si vous exécutez une migration sans agent avec Azure Migrate Server Migration, le VDDK VMware vSphere doit être installé sur la machine virtuelle de l’appliance.

## <a name="agentless-migration-url-access-requirements"></a>Migration sans agent - Exigences relatives à l’accès aux URL

L’appliance Azure Migrate a besoin d’une connectivité Internet.

- Quand vous déployez l’appliance, Azure Migrate effectue un contrôle de connectivité aux URL récapitulées dans le tableau ci-dessous.
- Si vous utilisez un proxy basé sur les URL, autorisez l’accès à ces URL, en vérifiant que le proxy résout tous les enregistrements CNAME reçus lors de la recherche des URL.

**URL** | **Détails**  
--- | ---
*. portal.azure.com | Accédez à Azure Migrate dans le portail Azure.
\* .windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com  | Connectez-vous à votre abonnement Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Créez des applications Active Directory pour que l’appliance communique avec le service Azure Migrate.
management.azure.com | Créez des applications Active Directory pour que l’appliance communique avec le service Azure Migrate.
dc.services.visualstudio.com | Chargez les journaux d’applications utilisés pour la supervision interne.
*.vault.azure.net | Gérez les secrets dans Azure Key Vault.
*.servicebus.windows.net | Communication entre l’appliance et le service Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Connectez-vous aux URL du service Azure Migrate.
*.blob.core.windows.net | Chargez des données dans des comptes de stockage.
https://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Utilisé pour les mises à jour de l’appliance Azure Migrate.


## <a name="agentless-migration-port-requirements"></a>Migration sans agent - Exigences relatives aux ports

**Appareil** | **Connection**
--- | ---
Appliance | Connexions sortantes sur le port 443 pour charger les données répliquées sur Azure et pour communiquer avec les services Azure Migrate effectuant la réplication et la migration.
Serveur vCenter | Connexions entrantes sur le port 443 pour permettre à l’appliance d’orchestrer la réplication : créer des instantanés, copier des données, publier des instantanés
Hôte vSphere/EXSI | Trafic entrant sur le port TCP 902 pour que l’appliance réplique des données à partir d’instantanés.


## <a name="agent-based-migration-vmware-server-requirements"></a>Migration basée sur un agent - Exigences relatives au serveur VMware

Ce tableau récapitule la prise en charge et les limitations de l’évaluation pour les serveurs de virtualisation VMware.

**Support** | **Détails**
--- | ---
Serveur vCenter | Version 5.5, 6.0, 6.5 ou 6.7.
VMWare vSphere | Version 5.5, 6.0, 6.5 ou 6.7.

### <a name="agent-based-migration-vcenter-server-permissions"></a>Migration basée sur un agent - Autorisations de vCenter Server

Un compte en lecture seule pour vCenter Server.

## <a name="agent-based-migration-replication-appliance-requirements"></a>Migration basée sur un agent - Exigences relatives à l’appliance de réplication

Les exigences pour [l’appliance de réplication](migrate-replication-appliance.md) utilisée pour la migration basée sur un agent des machines virtuelles VMware et des serveurs physiques avec Azure Migrate Server Migration sont récapitulées dans le tableau.

> [!NOTE]
> Quand vous configurez l’appliance de réplication en utilisant le modèle OVA fourni dans le hub Azure Migrate, l’appliance exécute Windows Server 2016 et est conforme aux exigences de la prise en charge. Si vous configurez manuellement l’appliance de réplication sur un serveur physique, vérifiez qu’elle est conforme aux exigences.



**Composant** | **Prérequis**
--- | ---
 | **Paramètres VMware** (Appliance de machine virtuelle VMware)
PowerCLI | [PowerCLI version 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) doit être installé si l’appliance de réplication s’exécute sur une machine virtuelle VMware.
Type de carte réseau | VMXNET3 (si l’appliance est une machine virtuelle VMware)
 | **Paramètres matériels**
Cœurs d’unité centrale | 8
RAM | 16 Go
Nombre de disques | Trois : Le disque du système d’exploitation, le disque de cache du serveur de processus et le lecteur de conservation.
Espace disque disponible (cache) | 600 Go
Espace disque disponible (disque de rétention) | 600 Go
**Paramètres logiciels** |
Système d’exploitation | Windows Server 2016 ou Windows Server 2012 R2
Paramètres régionaux du système d’exploitation | Anglais (en-us)
TLS | TLS 1.2 doit être activé.
.NET Framework | .NET Framework 4.6 ou ultérieur doit être installé sur la machine (avec un chiffrement fort activé).
MySQL | MySQL doit être installé sur l’appliance.<br/> MySQL doit être installé. Vous pouvez l’installer manuellement ou laisser Site Recovery le faire lors du déploiement de l’appliance.
Autres applications | N’exécutez pas d’autres applications sur l’appliance de réplication.
Rôles Windows Server | N’activez pas ces rôles : <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Stratégies de groupe | N’activez pas ces stratégies de groupe : <br> - Empêcher l’accès à l’invite de commandes <br> - Empêcher l’accès aux outils de modification du Registre <br> - Logique de confiance pour les pièces jointes <br> - Activer l’exécution des scripts <br> [En savoir plus](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Aucun site web par défaut préexistant <br> - Aucune application/aucun site web préexistants ne doivent écouter le port 443 <br>- Activer [l’authentification anonyme](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Activer le paramètre [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
**Paramètres réseau** |
Type d’adresse IP | statique
Ports | 443 (Orchestration du canal de contrôle)<br>9443 (Transport de données)
Type de carte réseau | VMXNET3

### <a name="replication-appliance-url-access"></a>Accès aux URL par l’appliance de réplication

L’appliance de réplication doit accéder à ces URL.

**URL** | **Détails**
--- | ---
\*.backup.windowsazure.com | Élément utilisé pour la coordination et le transfert des données répliquées
\*.store.core.windows.net | Élément utilisé pour la coordination et le transfert des données répliquées
\*.blob.core.windows.net | Utilisé pour l’accès au compte de stockage qui stocke les données répliquées
\*.hypervrecoverymanager.windowsazure.com | Élément utilisé pour la coordination et l’administration des opérations de gestion de la réplication
https:\//management.azure.com | Élément utilisé pour la coordination et l’administration des opérations de gestion de la réplication
*.services.visualstudio.com | Utilisé dans le cadre de la télémétrie (facultatif)
time.nist.gov | Éléments utilisés pour vérifier la synchronisation horaire entre l’horloge système et l’heure globale.
time.windows.com | Éléments utilisés pour vérifier la synchronisation horaire entre l’horloge système et l’heure globale.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | L’installation OVF nécessite l’accès à ces URL. Azure Active Directory utilise ces adresses pour le contrôle d’accès et la gestion des identités
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Pour terminer le téléchargement de MySQL


#### <a name="mysql-installation-options"></a>Options d’installation de MySQL

MySQL peut être installé sur l’appliance de réplication avec une de ces méthodes.

**Méthode** | **Détails**
--- | ---
Télécharger et installer manuellement | Téléchargez l’application MySQL et placez-la dans le dossier C:\Temp\ASRSetup, puis installez-la manuellement.<br/> Quand vous configurez l’appliance, le MySQL apparaît comme déjà installé.
Sans téléchargement en ligne | Placez votre application d’installation de MySQL dans le dossier C:\Temp\ASRSetup. Quand vous installez l’appliance, et que vous cliquez pour télécharger et installer MySQL, le programme d’installation utilise le programme d’installation que vous avez ajouté.
Télécharger et installer dans Azure Migrate | Lorsque vous installez l’appliance et que vous êtes invité à installer MySQL, sélectionnez **Télécharger et installer**.



## <a name="agent-based-migration-vmware-vm-requirements"></a>Migration basée sur un agent - Exigences relatives aux machines virtuelles VMware

**Support** | **Détails**
--- | ---
**Charge de travail des machines** | Azure Migrate prend en charge la migration de toutes les charges de travail (par exemple Active Directory, SQL Server, etc.) exécutées sur une machine prise en charge.
**Systèmes d’exploitation** | Pour obtenir les informations les plus récentes, consultez [Prise en charge des systèmes d’exploitation](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) pour Site Recovery. Azure Migrate fournit une prise en charge identique des systèmes d’exploitation des machines virtuelles.
**Stockage invité/système de fichiers Linux** | Pour obtenir les informations les plus récentes, consultez [Prise en charge des systèmes de fichiers Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) pour Site Recovery. Azure Migrate a une prise en charge identique des systèmes de fichiers Linux.
**Réseau/Stockage** | Pour obtenir les informations les plus récentes, consultez les prérequis en matière de [réseau](../site-recovery/vmware-physical-azure-support-matrix.md#network) et de [stockage](../site-recovery/vmware-physical-azure-support-matrix.md#storage) pour Site Recovery. Azure Migrate fournit des exigences identiques pour le réseau/stockage.
**Conditions requises pour Azure** | Pour obtenir les informations les plus récentes, consultez les exigences d’Azure en matière de [réseau](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), de [stockage](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage) et de [calcul](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) pour Site Recovery. Azure Migrate a des exigences identiques pour la migration de VMware.
**Service de mobilité** | L’agent du service Mobilité doit être installé sur chaque machine virtuelle que vous voulez migrer.
**Démarrage UEFI** | La machine virtuelle migrée dans Azure est automatiquement convertie en machine virtuelle de démarrage du BIOS.<br/><br/> Le disque du système d’exploitation doit avoir jusqu’à quatre partitions et les volumes doivent être formatés avec NTFS.
**Disque cible** | Les machines virtuelles peuvent être migrées seulement vers des disques managés (disques durs standard, disques SSD Premium) dans Azure.
**Taille du disque** | Disque de système d’exploitation de 2 To ; 8 To pour des disques de données.
**Limites du disque** |  Jusqu'à 63 disques par machine virtuelle.
**Disques/volumes chiffrés** | Les machines virtuelles avec des disques/volumes chiffrés ne sont pas prises en charge pour la migration.
**Cluster de disque partagé** | Non pris en charge.
**Disques indépendants** | Pris en charge.
**Disques directs** | Pris en charge.
**NFS** | Les volumes NFS montés en tant que volumes sur les machines virtuelles ne sont pas répliqués.
**Cibles iSCSI** | Les machines virtuelles avec des cibles iSCSI ne sont pas prises en charge pour la migration sans agent.
**Multipath I/O** | Non pris en charge.
**Storage vMotion** | Pris en charge
**Cartes réseau associées** | Non pris en charge.
**IPv6** | Non pris en charge.




## <a name="agent-based-migration-url-access-requirements"></a>Migration basée sur un agent - Exigences relatives à l’accès aux URL

Le service Mobilité s’exécutant sur des machines virtuelles VMware a besoin d’une connexion Internet.

Quand vous déployez le service Mobilité, Azure Migrate effectue un contrôle de connectivité vers les URL récapitulées dans le tableau ci-dessous.


**URL** | **Détails**  
--- | ---
*. portal.azure.com | Accédez à Azure Migrate dans le portail Azure.
\* .windows.net | Connectez-vous à votre abonnement Azure.
*.microsoftonline.com | Créez des applications Active Directory pour que l’appliance communique avec le service Azure Migrate.
management.azure.com | Créez des applications Active Directory pour que l’appliance communique avec le service Azure Migrate.
dc.services.visualstudio.com | Chargez les journaux d’applications utilisés pour la supervision interne.
*.vault.azure.net | Gérez les secrets dans Azure Key Vault.
*.servicebus.windows.net | Communication entre l’appliance et le service Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Connectez-vous aux URL du service Azure Migrate.
*.blob.core.windows.net | Chargez des données dans des comptes de stockage.

## <a name="agent-based-migration-port-requirements"></a>Migration basée sur un agent - Exigences relatives aux ports

**Appareil** | **Connection**
--- | ---
Machines virtuelles | Le service Mobility en cours d’exécution sur des machines virtuelles communique avec l'appliance de réplication locale (serveur de configuration) via le port HTTPS 443 entrant, pour la gestion de la réplication.<br/><br/> Les machines virtuelles envoient des données de réplication au serveur de traitement (s’exécutant sur l’ordinateur du serveur de configuration) sur le port HTTPS 9443 entrant. Ce port peut être modifié.
Appliance de réplication | L’appliance de réplication orchestre la réplication avec Azure sur le port HTTPS 443 sortant.
Serveur de traitement | Le serveur de traitement reçoit les données de réplication, les optimise et les chiffre, puis les envoie au stockage Azure via le port 443 sortant.<br/> Le serveur de processus s’exécute par défaut sur l’appliance de réplication.

## <a name="azure-vm-requirements"></a>Exigences des machines virtuelles Azure

Toutes les machines virtuelles locales répliquées sur Azure doivent respecter les exigences des machines virtuelles Azure récapitulées dans ce tableau. Quand Site Recovery vérifie les prérequis pour la réplication, la vérification échoue si certaines exigences ne sont pas satisfaites.

**Composant** | **Configuration requise** | **Détails**
--- | --- | ---
Système d’exploitation invité | Vérifiez les systèmes d’exploitation pris en charge pour les [machines virtuelles VMware utilisant la réplication sans agent](#agentless-migration-vmware-vm-requirements) et pour les [machines virtuelles VMware utilisant la réplication basée sur un agent](#agent-based-migration-vmware-vm-requirements).<br/> Vous pouvez migrer les charges de travail s’exécutant sur un système d’exploitation pris en charge. | La vérification est mise en échec en cas de défaut de prise en charge.
Architecture du système d’exploitation invité | 64 bits. | La vérification est mise en échec en cas de défaut de prise en charge.
Taille du disque du système d’exploitation | Jusqu’à 2 048 Go. | La vérification est mise en échec en cas de défaut de prise en charge.
Nombre de disques du système d’exploitation | 1 | La vérification est mise en échec en cas de défaut de prise en charge.
Nombre de disques de données | 64 ou moins. | La vérification est mise en échec en cas de défaut de prise en charge.
Taille de disque de données | Jusqu’à 4 095 Go | La vérification est mise en échec en cas de défaut de prise en charge.
Adaptateurs réseau | Prise en charge de plusieurs adaptateurs réseau. |
Disque dur virtuel partagé | Non pris en charge. | La vérification est mise en échec en cas de défaut de prise en charge.
Disque FC | Non pris en charge. | La vérification est mise en échec en cas de défaut de prise en charge.
BitLocker | Non pris en charge. | Vous devez désactiver BitLocker avant d’activer la réplication pour une machine.
nom de la machine virtuelle | De 1 et 63 caractères.<br/> Uniquement des lettres, des chiffres et des traits d’union.<br/><br/> Le nom de la machine doit commencer et se terminer par une lettre ou un chiffre. |  Mettez à jour la valeur dans les propriétés de machine de Site Recovery.
Se connecter après la migration - Windows | Pour vous connecter à des machines virtuelles Azure exécutant Windows après la migration :<br/> - Avant la migration, active le protocole RDP sur la machine virtuelle locale. Vérifiez que des règles TCP et UDP sont ajoutées pour le profil **Public** et que RDP est autorisé dans **Pare-feu Windows** > **Applications autorisées** pour tous les profils.<br/> Pour l’accès VPN site à site, activez RDP et autorisez RDP dans **Pare-feu Windows** -> **Applications et fonctionnalités autorisées** pour les réseaux **Domaine et Privé**. Vérifiez aussi que la stratégie SAN du système d’exploitation est définie sur **OnlineAll**. [Plus d’informations](prepare-for-migration.md) |
Se connecter après la migration - Linux | Pour vous connecter à des machines virtuelles Azure après la migration avec SSH :<br/> Avant la migration, sur la machine locale, vérifiez que le service Secure Shell est défini sur Démarrer et que les règles de pare-feu autorisent une connexion SSH.<br/> Après le basculement, sur la machine virtuelle Azure, autorisez les connexions entrantes au port SSH pour les règles du groupe de sécurité réseau sur la machine virtuelle basculée, et pour le sous-réseau Azure auquel elle est connectée. Ajoutez aussi une adresse IP publique pour la machine virtuelle. |  


## <a name="next-steps"></a>Étapes suivantes

[Préparer VMware](tutorial-prepare-vmware.md) à l’évaluation et à la migration.
