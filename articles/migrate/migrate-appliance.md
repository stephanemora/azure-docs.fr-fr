---
title: Appliance Azure Migrate
description: Fournit un résumé de la prise en charge de l’appliance Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: dadca1fadef9d2967f20cae13e40d01de73d39e4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778336"
---
# <a name="azure-migrate-appliance"></a>Appliance Azure Migrate

Cet article récapitule les prérequis et les exigences de la prise en charge de l’appliance Azure Migrate.

## <a name="deployment-scenarios"></a>Scénarios de déploiement

L’appliance Azure Migrate est utilisée dans les scénarios suivants.

**Scénario** | **Outil** | **Usage**
--- | --- | ---
**Découverte et évaluation des serveurs s’exécutant dans un environnement VMware** | Azure Migrate: découverte et évaluation | Découvrir les serveurs s’exécutant dans votre environnement VMware<br/><br/> Effectuez la découverte de l’inventaire logiciel installé, l’analyse des dépendances sans agent, et découvrez les instances et bases de données SQL Server.<br/><br/> Collectez les métadonnées de configuration et de performances du serveur pour les évaluations.
**Migration sans agent de serveurs s’exécutant dans un environnement VMware** | Azure Migrate : Migration de serveurs | Découvrez les serveurs s’exécutant dans votre environnement VMware. <br/><br/> Répliquez des serveurs sans installer d’agents sur ceux-ci.
**Découverte et évaluation des serveurs s’exécutant dans un environnement Hyper-V** | Azure Migrate: découverte et évaluation | Découvrez les serveurs s’exécutant dans votre environnement Hyper-V.<br/><br/> Collectez les métadonnées de configuration et de performances du serveur pour les évaluations.
**Découverte et évaluation des serveurs physiques ou virtualisés locaux** |  Azure Migrate: découverte et évaluation |  Découvrez les serveurs physiques ou virtualisés locaux.<br/><br/> Collectez les métadonnées de configuration et de performances du serveur pour les évaluations.

## <a name="deployment-methods"></a>Méthodes de déploiement

L'appliance peut être déployée à l’aide de deux méthodes :

- L’appliance peut être déployée à l’aide d’un modèle pour les serveurs s’exécutant dans un environnement VMware ou Hyper-V ([modèle OVA pour VMware](how-to-set-up-appliance-vmware.md) ou [VHD pour Hyper-V](how-to-set-up-appliance-hyper-v.md)).
- Si vous ne souhaitez pas utiliser de modèle, vous pouvez déployer l’appliance pour un environnement VMware ou Hyper-V à l’aide d’un [script d’installation PowerShell](deploy-appliance-script.md).
- Dans Azure Government, vous devez déployer l’appliance à l’aide d’un script d’installation PowerShell. Reportez-vous aux étapes de déploiement décrites [ici](deploy-appliance-script-government.md).
- Pour des serveurs physiques ou virtualisés locaux ou cloud, vous déployez toujours l’appliance à l’aide d’un script d’installation PowerShell. Découvrez les étapes de déploiement [ici](how-to-set-up-appliance-physical.md).
- Les liens de téléchargement sont disponibles dans les tableaux ci-dessous.

## <a name="appliance---vmware"></a>Appliance - VMware

Le tableau suivant récapitule les exigences de l’appliance Azure Migrate pour VMware.

> [!Note]
> La découverte et l’évaluation d’instances et de bases de données SQL Server s’exécutant dans votre environnement VMware sont actuellement en préversion. Pour tester cette fonctionnalité, utilisez [**ce lien**](https://aka.ms/AzureMigrate/SQL) afin de créer un projet dans la région **Australie Est**. Si vous avez déjà un projet en Australie Est et si vous souhaitez tester cette fonctionnalité, vérifiez que vous avez effectué ces [**prérequis**](how-to-discover-sql-existing-project.md) dans le portail.

**Prérequis** | **VMware**
--- | ---
**autorisations** | Pour accéder au gestionnaire de configuration de l’appliance localement ou à distance, vous devez disposer d’un compte d’utilisateur local ou de domaine doté de privilèges administratifs sur le serveur de l’appliance.
**Services de l’appliance** | L’appliance offre les services suivants :<br/><br/> - **Gestionnaire de configuration de l’appliance** : application web configurable avec les détails de la source pour démarrer la découverte et l’évaluation des serveurs.<br/> - **Agent de découverte VMware** : collecte les métadonnées de configuration du serveur utilisables pour créer des évaluations locales.<br/>- **Agent d’évaluation VMware** : collecte les métadonnées de performances du serveur utilisables pour créer des évaluations basées sur les performances.<br/>- **Service de mise à jour automatique** : tient à jour tous les agents en cours d’exécution sur l’appliance. S’exécute automatiquement une fois toutes les 24 heures.<br/>- **Agent DRA** : orchestre la réplication des serveurs et coordonne la communication entre les serveurs répliquées et Azure. Utilisé seulement lors de la réplication de serveurs sur Azure avec une migration sans agent.<br/>- **Passerelle** : envoie des données répliquées vers Azure. Utilisée seulement lors de la réplication de serveurs sur Azure avec une migration sans agent.<br/>- **Agent de découverte et d’évaluation SQL** : envoie les métadonnées de configuration et de performances des instances et bases de données SQL Server à Azure.
**Limites de projet** |  Une appliance ne peut être inscrite qu’auprès d’un seul projet.<br/> Un projet unique peut avoir plusieurs appliances inscrites.
**Limites de la découverte** | Une appliance peut découvrir jusqu’à 10 000 serveurs s’exécutant sur un serveur vCenter.<br/> Une appliance ne peut se connecter qu'à un seul vCenter Server.
**Déploiement pris en charge** | Déployez en tant que nouveau serveur s’exécutant sur vCenter Server à l’aide d’un modèle OVA.<br/><br/> Déployez sur un serveur existant exécutant Windows Server 2016 à l’aide d’un script d’installation PowerShell.
**Modèle OVA** | Téléchargez à partir du projet ou d’[ici](https://go.microsoft.com/fwlink/?linkid=2140333).<br/><br/> La taille du téléchargement est de 11,9 Go.<br/><br/> Le modèle d’appliance téléchargé est fourni avec une licence d’évaluation Windows Server 2016 qui est valide pendant 180 jours.<br/>Si la période d’évaluation est proche de l’expiration, nous vous recommandons de télécharger et déployer une nouvelle appliance à l’aide d’un modèle OVA, ou d’activer la licence du système d’exploitation du serveur de l’appliance.
**Vérification d’OVA** | [Vérifiez](tutorial-discover-vmware.md#verify-security) le modèle OVA téléchargé à partir du projet en contrôlant les valeurs de hachage.
**Script PowerShell** | Consultez cet [article](./deploy-appliance-script.md#set-up-the-appliance-for-vmware) sur le déploiement d’une appliance à l’aide du script d’installation PowerShell.<br/><br/> 
**Configuration requise pour le matériel et le réseau** |  L’appliance doit s’exécuter sur un serveur avec Windows Server 2016, 32 Go de RAM, 8 processeurs virtuels, environ 80 Go de stockage sur disque et un commutateur virtuel externe.<br/> L’appliance nécessite un accès à Internet, directement ou via un proxy.<br/><br/> Si vous déployez l’appliance à l’aide d’une modèle OVA, vous devez disposer de suffisamment de ressources sur le vCenter Server pour créer un serveur présentant la configuration requise pour le matériel.<br/><br/> Si vous exécutez l’appliance sur un serveur existant, vérifiez qu’elle exécute Windows Server 2016 et présente la configuration requise pour le matériel.<br/>_(Actuellement, le déploiement de l’appliance est pris en charge uniquement sur Windows Server 2016.)_
**Conditions requises VMware** | Si vous déployez l’appliance en tant que serveur sur vCenter Server, vous devez la déployer sur un vCenter Server exécutant la version 5.5, 6.0, 6.5 ou 6.7 et un hôte ESXi exécutant la version 5.5 ou une version ultérieure.<br/><br/> 
**VDDK (migration sans agent)** | Pour tirer parti de l’appliance pour la migration sans agent de serveurs, le VDDK VMware vSphere doit être installé sur le serveur de l’appliance.

## <a name="appliance---hyper-v"></a>Appliance - Hyper-V

**Prérequis** | **Hyper-V**
--- | ---
**autorisations** | Pour accéder au gestionnaire de configuration de l’appliance localement ou à distance, vous devez disposer d’un compte d’utilisateur local ou de domaine doté de privilèges administratifs sur le serveur de l’appliance.
**Services de l’appliance** | L’appliance offre les services suivants :<br/><br/> - **Gestionnaire de configuration de l’appliance** : application web configurable avec les détails de la source pour démarrer la découverte et l’évaluation des serveurs.<br/> - **Agent de découverte** : collecte les métadonnées de configuration du serveur utilisables pour créer des évaluations locales.<br/>- **Agent d’évaluation** : collecte les métadonnées de performances du serveur utilisables pour créer des évaluations basées sur les performances.<br/>- **Service de mise à jour automatique** : tient à jour tous les agents en cours d’exécution sur l’appliance. S’exécute automatiquement une fois toutes les 24 heures.
**Limites de projet** |  Une appliance ne peut être inscrite qu’auprès d’un seul projet.<br/> Un projet unique peut avoir plusieurs appliances inscrites.
**Limites de la découverte** | Une appliance peut détecter jusqu’à 5 000 serveurs s’exécutant dans un environnement Hyper-V.<br/> Une appliance peut connecter à jusqu’à 300 hôtes Hyper-V.
**Déploiement pris en charge** | Déployez en tant que serveur s’exécutant sur un hôte Hyper-V à l’aide d’un modèle de disque dur virtuel.<br/><br/> Déployez sur un serveur existant exécutant Windows Server 2016 à l’aide d’un script d’installation PowerShell.
**Modèle de disque dur virtuel** | Fichier zip incluant un disque dur virtuel. Téléchargez à partir du projet ou d’[ici](https://go.microsoft.com/fwlink/?linkid=2140422).<br/><br/> La taille du téléchargement est de 8,91 Go.<br/><br/> Le modèle d’appliance téléchargé est fourni avec une licence d’évaluation Windows Server 2016 qui est valide pendant 180 jours.<br/> Si la période d’évaluation est proche de l’expiration, nous vous recommandons de télécharger et de déployer une nouvelle appliance, ou d’activer la licence du système d’exploitation du serveur de l’appliance.
**Vérification de disque dur virtuel** | [Vérifiez](tutorial-discover-hyper-v.md#verify-security) le modèle de disque dur virtuel téléchargé à partir du projet en contrôlant les valeurs de hachage.
**Script PowerShell** | Consultez cet [article](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v) sur le déploiement d’une appliance à l’aide du script d’installation PowerShell.<br/>
**Configuration requise pour le matériel et le réseau**  |  L’appliance doit s’exécuter sur un serveur avec Windows Server 2016, 16 Go de RAM, 8 processeurs virtuels, environ 80 Go de stockage sur disque et un commutateur virtuel externe.<br/> L’appliance nécessite une adresse IP statique ou dynamique et un accès à Internet, directement ou via un proxy.<br/><br/> Si vous exécutez l’appliance en tant que serveur s’exécutant sur un hôte Hyper-V, vous devez disposer de suffisamment de ressources sur l’hôte pour créer un serveur présentant la configuration requise pour le matériel.<br/><br/> Si vous exécutez l’appliance sur un serveur existant, vérifiez qu’elle exécute Windows Server 2016 et présente la configuration requise pour le matériel.<br/>_(Actuellement, le déploiement de l’appliance est pris en charge uniquement sur Windows Server 2016.)_
**Configuration requise pour Hyper-V** | Si vous déployez l’appliance avec le modèle de disque dur virtuel, l’appliance fournie par Azure Migrate est une machine virtuelle Hyper-V version 5.0.<br/><br/> L’hôte Hyper-V doit exécuter Windows Server 2012 R2 ou une version ultérieure.

## <a name="appliance---physical"></a>Appliance - Physique

**Prérequis** | **Physique**
--- | ---
**autorisations** | Pour accéder au gestionnaire de configuration de l’appliance localement ou à distance, vous devez disposer d’un compte d’utilisateur local ou de domaine doté de privilèges administratifs sur le serveur de l’appliance.
**Services de l’appliance** | L’appliance offre les services suivants :<br/><br/> - **Gestionnaire de configuration de l’appliance** : application web configurable avec les détails de la source pour démarrer la découverte et l’évaluation des serveurs.<br/> - **Agent de découverte** : collecte les métadonnées de configuration du serveur utilisables pour créer des évaluations locales.<br/>- **Agent d’évaluation** : collecte les métadonnées de performances du serveur utilisables pour créer des évaluations basées sur les performances.<br/>- **Service de mise à jour automatique** : tient à jour tous les agents en cours d’exécution sur l’appliance. S’exécute automatiquement une fois toutes les 24 heures.
**Limites de projet** |  Une appliance ne peut être inscrite qu’auprès d’un seul projet.<br/> Un projet unique peut avoir plusieurs appliances inscrites.<br/>
**Limites de la découverte** | Une appliance peut découvrir jusqu’à 1 000 serveurs physiques.
**Déploiement pris en charge** | Déployez sur un serveur existant exécutant Windows Server 2016 à l’aide d’un script d’installation PowerShell.
**Script PowerShell** | Téléchargez le script (AzureMigrateInstaller.ps1) dans un dossier zip à partir du portail ou à partir d’[ici](https://go.microsoft.com/fwlink/?linkid=2140334). [Plus d’informations](tutorial-discover-physical.md)<br/><br/> La taille du téléchargement est de 85,8 Mo.
**Vérification du script** | [Vérifiez](tutorial-discover-physical.md#verify-security) le script d’installation PowerShell téléchargé à partir du projet en contrôlant les valeurs de hachage.
**Configuration requise pour le matériel et le réseau** |  L’appliance doit s’exécuter sur un serveur avec Windows Server 2016, 16 Go de RAM, 8 processeurs virtuels et environ 80 Go de stockage sur disque.<br/> L’appliance nécessite une adresse IP statique ou dynamique et un accès à Internet, directement ou via un proxy.<br/><br/> Si vous exécutez l’appliance sur un serveur existant, vérifiez qu’elle exécute Windows Server 2016 et présente la configuration requise pour le matériel.<br/>_(Actuellement, le déploiement de l’appliance est pris en charge uniquement sur Windows Server 2016.)_

## <a name="url-access"></a>accès URL

L’appliance Azure Migrate a besoin d’une connectivité Internet.

- Lorsque vous déployez l’appliance, Azure Migrate effectue une vérification de la connectivité aux URL requises.
- Vous devez autoriser l’accès à toutes les URL de la liste. Si vous effectuez uniquement une évaluation, vous pouvez ignorer les URL marquées comme requises pour la migration sans agent VMware.
- Si vous utilisez un proxy basé sur les URL pour vous connecter à Internet, vérifiez que le proxy résout tous les enregistrements CNAME reçus lors de la recherche des URL.

### <a name="public-cloud-urls"></a>URL de cloud public

**URL** | **Détails**  
--- | --- |
*. portal.azure.com  | Accédez au portail Azure.
\* .windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com | Connectez-vous à votre abonnement Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Créez des applications Azure Active Directory (AD) pour que l’appliance communique avec Azure Migrate.
management.azure.com | Créez des applications Azure AD pour que l’appliance communique avec le service Azure Migrate.
*.services.visualstudio.com | Chargez les journaux d’appliance utilisés pour la surveillance interne.
*.vault.azure.net | Gérez les secrets dans Azure Key Vault.<br/> Remarque : vérifiez que les serveurs à répliquer y ont accès.
aka.ms/* | Autorisez l’accès aux aka links utilisés pour télécharger et installer les dernières mises à jour pour les services de l’appliance.
download.microsoft.com/download | Autorisez les téléchargements à partir du centre de téléchargement Microsoft.
*.servicebus.windows.net | Communication entre l’appliance et le service Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Connectez-vous aux URL du service Azure Migrate.
*.hypervrecoverymanager.windowsazure.com | **Utilisé pour la migration sans agent VMware**<br/><br/> Connectez-vous aux URL du service Azure Migrate.
*.blob.core.windows.net |  **Utilisé pour la migration sans agent VMware**<br/><br/>Chargez les données vers le stockage pour la migration.

### <a name="government-cloud-urls"></a>URL cloud Government

**URL** | **Détails**  
--- | --- |
*.portal.azure.us  | Accédez au portail Azure.
graph.windows.net | Connectez-vous à votre abonnement Azure.
login.microsoftonline.us  | Créez des applications Azure Active Directory (AD) pour que l’appliance communique avec Azure Migrate.
management.usgovcloudapi.net | Créez des applications Azure AD pour que l’appliance communique avec le service Azure Migrate.
*.services.visualstudio.com | Chargez les journaux d’appliance utilisés pour la surveillance interne.
*.vault.usgovcloudapi.net | Gérez les secrets dans Azure Key Vault.
aka.ms/* | Autorisez l’accès aux aka links utilisés pour télécharger et installer les dernières mises à jour pour les services de l’appliance.
download.microsoft.com/download | Autorisez les téléchargements à partir du centre de téléchargement Microsoft.
*.servicebus.usgovcloudapi.net  | Communication entre l’appliance et le service Azure Migrate.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Connectez-vous aux URL du service Azure Migrate.
*.hypervrecoverymanager.windowsazure.us | **Utilisé pour la migration sans agent VMware**<br/><br/> Connectez-vous aux URL du service Azure Migrate.
*.blob.core.usgovcloudapi.net  |  **Utilisé pour la migration sans agent VMware**<br/><br/>Chargez les données vers le stockage pour la migration.
*.applicationinsights.us | Chargez les journaux de l’appliance utilisés pour la surveillance interne.

## <a name="collected-data---vmware"></a>Données collectées - VMware

L’appliance collecte les métadonnées de configuration, les métadonnées de performances et les données relatives aux dépendances du serveur (si l’[analyse des dépendances](concepts-dependency-visualization.md) sans agent est utilisée).

### <a name="metadata"></a>Métadonnées

Les métadonnées découvertes par l’appliance Azure Migrate vous aident à déterminer si les serveurs sont prêts pour la migration vers Azure : dimensionnement approprié des serveurs, coûts des plans et analyse des dépendances des applications. Microsoft n’utilise pas ces données dans le cadre des audits de conformité des licences.

Voici la liste complète des métadonnées des serveurs que l’appliance collecte et envoie à Azure.

**DONNÉES** | **COMPTEUR**
--- | ---
**Détails du serveur** |
ID du serveur | vm.Config.InstanceUuid
Nom du serveur | vm.Config.Name
ID du vCenter Server | VMwareClient.Instance.Uuid
Description du serveur | vm.Summary.Config.Annotation
Nom de produit de la licence | vm.Client.ServiceContent.About.LicenseProductName
Type de système d'exploitation | vm.SummaryConfig.GuestFullName
Type de démarrage | vm.Config.Firmware
Nombre de mémoires à tores magnétiques | vm.Config.Hardware.NumCPU
Mémoire (Mo) | vm.Config.Hardware.MemoryMB
Nombre de disques | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
Liste des tailles de disque | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
Liste des adaptateurs réseau | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
Utilisation du processeur | cpu.usage.average
Utilisation de la mémoire |mem.usage.average
**Détails par disque** |
Valeur de la clé du disque | disk.Key
Numéro du disque | disk.UnitNumber
Valeur de clé de contrôleur de disque | disk.ControllerKey.Value
Gigaoctets approvisionnés | virtualDisk.DeviceInfo.Summary
Nom du disque | Valeur générée à l’aide de disk.UnitNumber, disk.Key, disk.ControllerKey.VAlue
Opérations de lecture par seconde | virtualDisk.numberReadAveraged.average
Opérations d’écriture par seconde | virtualDisk.numberWriteAveraged.average
Débit de lecture (Mo par seconde) | virtualDisk.read.average
Débit d’écriture (Mo par seconde) | virtualDisk.write.average
**Détails par carte d’interface réseau** |
Nom de la carte réseau | nic.Key
Adresse MAC | ((VirtualEthernetCard)nic).MacAddress
Adresses IPv4 | vm.Guest.Net
Adresses IPv6 | vm.Guest.Net
Débit de lecture (Mo par seconde) | net.received.average
Débit d’écriture (Mo par seconde) | net.transmitted.average
**Détails de chemin d’accès d’inventaire** |
Nom | container.GetType().Name
Type d’objet enfant | container.ChildType
Détails de référence | container.MoRef
Détails du parent | Container.Parent
Détails du dossier par serveur | ((Folder)container).ChildEntity.Type
Détails du centre de données par serveur | ((Datacenter)container).VmFolder
Détails du centre de données par dossier hôte | ((Datacenter)container).HostFolder
Détails du cluster par hôte | ((ClusterComputeResource)container).Host
Détails de l’hôte par serveur | ((HostSystem)container).VM

### <a name="performance-data"></a>Données de performances

Voici les données de performances qu’une appliance collecte pour un serveur s’exécutant sur VMware, et envoie à Azure.

**Données** | **Compteur** | **Évaluation de l'impact**
--- | --- | ---
Utilisation du processeur | cpu.usage.average | Taille/coût du serveur recommandés
Utilisation de la mémoire | mem.usage.average | Taille/coût du serveur recommandés
Débit de lecture du disque (Mo par seconde) | virtualDisk.read.average | Calcul de la taille du disque, du coût de stockage et de la taille du serveur
Débits d’écriture du disque (Mo par seconde) | virtualDisk.write.average | Calcul de la taille du disque, du coût de stockage et de la taille du serveur
Opérations de lecture du disque par seconde | virtualDisk.numberReadAveraged.average | Calcul de la taille du disque, du coût de stockage et de la taille du serveur
Opérations d’écriture du disque par seconde | virtualDisk.numberWriteAveraged.average  | Calcul de la taille du disque, du coût de stockage et de la taille du serveur
Débit de lecture de la carte d’interface réseau (Mo par seconde) | net.received.average | Calcul de la taille de serveur
Débit des écritures de la carte réseau (Mo par seconde) | net.transmitted.average  |Calcul de la taille de serveur

### <a name="installed-software-inventory"></a>Inventaire logiciel installé

L’appliance collecte des données concernant l’inventaire logiciel installé sur les serveurs.

#### <a name="windows-server-software-inventory-data"></a>Données d’inventaire logiciel de serveur Windows

Voici les données d’inventaire logiciel que l’appliance collecte à partir de chaque serveur Windows découvert dans votre environnement VMware.

**Données** | **Emplacement du registre** | **Clé**
--- | --- | ---
Nom de l’application  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Version  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion
Fournisseur  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Serveur de publication

#### <a name="windows-server-features-data"></a>Données des fonctionnalités de serveur Windows

Voici les données de fonctionnalités que l’appliance collecte à partir de chaque serveur Windows découvert dans votre environnement VMware.

**Données**  | **Applet de commande PowerShell** | **Propriété**
--- | --- | ---
Nom  | Get-WindowsFeature  | Nom
Type de fonctionnalité | Get-WindowsFeature  | FeatureType
Parent  | Get-WindowsFeature  | Parent

#### <a name="sql-server-metadata"></a>Métadonnées SQL Server

Voici les données de SQL Server que l’appliance collecte à partir de chaque serveur Windows découvert dans votre environnement VMware.

**Données**  | **Emplacement du registre**  | **Clé**
--- | --- | ---
Nom  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
Édition  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | Édition
Service Pack  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | SP
Version  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | Version

#### <a name="windows-server-operating-system-data"></a>Données du système d’exploitation du serveur Windows

Voici les données du système d’exploitation que l’appliance collecte à partir de chaque serveur Windows découvert dans votre environnement VMware.

**Données**  | **Classe WMI**  | **Propriété de classe WMI**
--- | --- | ---
Nom  | Win32_operatingsystem  | Caption
Version  | Win32_operatingsystem  | Version
Architecture  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-software-inventory-data"></a>Données d’inventaire logiciel de serveur Linux

Voici les données d’inventaire logiciel que l’appliance collecte à partir de chaque serveur Linux découvert dans votre environnement VMware. En fonction du système d’exploitation du serveur, une ou plusieurs des commandes sont exécutées.

**Données**  | **Commandes**
--- | ---
Nom | rpm, dpkg-query, snap
Version | rpm, dpkg-query, snap
Fournisseur | rpm, dpkg-query, snap

#### <a name="linux-server-operating-system-data"></a>Données du système d’exploitation du serveur Linux

Voici les données du système d’exploitation que l’appliance collecte à partir de chaque serveur Linux découvert dans votre environnement VMware.

**Données**  | **Commandes**
--- | ---
Nom <br/> version | Collectées dans un ou plusieurs des fichiers suivants :<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version
Architecture | uname

### <a name="sql-server-instances-and-databases-data"></a>Données des instances et bases de données SQL Server

L’appliance collecte des données sur les instances et bases de données SQL Server.

> [!Note]
> La découverte et l’évaluation d’instances et de bases de données SQL Server s’exécutant dans votre environnement VMware sont actuellement en préversion. Pour tester cette fonctionnalité, utilisez [**ce lien**](https://aka.ms/AzureMigrate/SQL) afin de créer un projet dans la région **Australie Est**. Si vous avez déjà un projet en Australie Est et si vous souhaitez tester cette fonctionnalité, vérifiez que vous avez effectué ces [**prérequis**](how-to-discover-sql-existing-project.md) dans le portail.

#### <a name="sql-database-metadata"></a>Métadonnées de base de données SQL

**Métadonnées de base de données** | **Affichages/ Propriétés de SQL Server**
--- | ---
Identificateur unique de la base de données | sys.databases
ID de base de données défini par le serveur | sys.databases
Nom de la base de données | sys.databases
Niveau de compatibilité de la base de données | sys.databases
Nom de classement de la base de données | sys.databases
État de la base de données | sys.databases
Taille de la base de données (en Mo) | sys.master_files
Lettre de lecteur de l’emplacement contenant les fichiers de données | SERVERPROPERTY et Software\Microsoft\MSSQLServer\MSSQLServer
Liste des fichiers de base de données | sys.databases, sys.master_files
Service Broker est activé ou non | sys.databases
La base de données est activée pour la capture des changements de données ou non | sys.databases

#### <a name="sql-server-metadata"></a>Métadonnées SQL Server

**Métadonnées du serveur** | **Affichages/ Propriétés de serveur SQL Server**
--- | ---
Nom du serveur |SERVERPROPERTY
FQDN | Chaîne de connexion dérivée de la découverte des applications installées
ID d’installation | sys.dm_server_registry
Version du serveur | SERVERPROPERTY
Édition du serveur | SERVERPROPERTY
Plateforme hôte du serveur (Windows/Linux) | SERVERPROPERTY
Niveau de produit du serveur (RTM SP CTP) | SERVERPROPERTY
Chemin de sauvegarde par défaut | SERVERPROPERTY
Chemin par défaut des fichiers de données | SERVERPROPERTY et Software\Microsoft\MSSQLServer\MSSQLServer
Chemin par défaut des fichiers journaux | SERVERPROPERTY et Software\Microsoft\MSSQLServer\MSSQLServer
Non. de cœurs sur le serveur | sys.dm_os_schedulers, sys.dm_os_sys_info
Nom du classement du serveur | SERVERPROPERTY
Non. de cœurs sur le serveur avec l’état VISIBLE ONLINE | sys.dm_os_schedulers
ID de serveur unique | sys.dm_server_registry
Haute disponibilité activée ou non | SERVERPROPERTY
Extension du pool de mémoires tampons activée ou non | sys.dm_os_buffer_pool_extension_configuration
Cluster de basculement configuré ou non | SERVERPROPERTY
Serveur utilisant le mode d’authentification Windows uniquement | SERVERPROPERTY
Le serveur installe Polybase | SERVERPROPERTY
Non. d’UC logiques sur le système | sys.dm_server_registry, sys.dm_os_sys_info
Rapport entre le nombre de cœurs logiques et le nombre de cœurs physiques exposés par un package de processeurs physiques | sys.dm_os_schedulers, sys.dm_os_sys_info
Nombre d’UC physiques sur le système | sys.dm_os_schedulers, sys.dm_os_sys_info
Date et heure du dernier démarrage du serveur | sys.dm_server_registry
Utilisation maximale de la mémoire du serveur (en Mo) | sys.dm_os_process_memory
Nombre total d’utilisateurs sur toutes les bases de données | sys.databases, sys.logins
Taille totale de toutes les bases de données utilisateur | sys.databases
Taille de la base de données temporaire | sys.master_files, sys.configurations, sys.dm_os_sys_info
Non. de connexions | sys.logins
Liste des serveurs liés | sys.servers
Liste des travaux de l’agent | [msdb].[dbo].[sysjobs], [sys].[syslogins], [msdb].[dbo].[syscategories]

### <a name="performance-metadata"></a>Métadonnées de performances

**Performances** | **Affichages/ Propriétés de serveur SQL Server** | **Évaluation de l’impact**
--- | --- | ---
Utilisation de l’UC SQL Server| sys.dm_os_ring_buffers| Taille de référence SKU recommandée (dimension de l’UC)
Nombre d’UC logiques SQL| sys.dm_os_sys_info| Taille de référence SKU recommandée (dimension de l’UC)
Mémoire physique SQL utilisée| sys.dm_os_process_memory| Inutilisé
Pourcentage d’utilisation de la mémoire SQL| sys.dm_os_process_memory | Inutilisé
Utilisation de l’UC de la base de données| sys.dm_exec_query_stats, sys.dm_exec_plan_attributes| Taille de référence SKU recommandée (dimension de l’UC)
Mémoire de la base de données en cours d’utilisation (pool de mémoires tampons)| sys.dm_os_buffer_descriptors| Taille de référence SKU recommandée (dimension de la mémoire)
E/S de lecture/écriture de fichier| sys.dm_io_virtual_file_stats, sys.master_files| Taille de référence SKU recommandée (dimension des E/S)
Nombre de lectures/écritures dans le fichier| sys.dm_io_virtual_file_stats, sys.master_files| Taille de référence SKU recommandée (dimension du débit)
Lecture/écriture bloquée en l’E/S dans le fichier (ms)| sys.dm_io_virtual_file_stats, sys.master_files| Taille de référence SKU recommandée (dimension de la latence des E/S)
Taille du fichier| sys.master_files| Taille de référence SKU recommandée (dimension du stockage)


### <a name="application-dependency-data"></a>Données de dépendance de l’application

L’analyse des dépendances sans agent collecte les données de connexion et de traitement.

#### <a name="windows-server-dependencies-data"></a>Données des dépendances du serveur Windows

Voici les données de connexion que l’appliance collecte auprès de chaque serveur Windows activé pour l’analyse des dépendances sans agent.

**Données** | **Commandes**
--- | ---
Port local | netstat
Adresse IP locale | netstat
Port distant | netstat
Adresse IP distante | netstat
État de la connexion TCP | netstat
ID du processus | netstat
Nombre de connexions actives | netstat

Voici les données de connexion que l’appliance collecte auprès de chaque serveur Windows activé pour l’analyse des dépendances sans agent.

**Données** | **Classe WMI** | **Propriété de classe WMI**
--- | --- | ---
Nom du processus | Win32_Process | ExecutablePath
Arguments de processus | Win32_Process | CommandLine
Nom de l'application | Win32_Process | Paramètre VersionInfo.ProductName de la propriété ExecutablePath

#### <a name="linux-server-dependencies-data"></a>Données des dépendances du serveur Linux

Voici les données de connexion que l’appliance collecte auprès de chaque serveur Linux activé pour l’analyse des dépendances sans agent.

**Données** | **Commandes**
--- | ---
Port local | netstat
Adresse IP locale | netstat
Port distant | netstat
Adresse IP distante | netstat
État de la connexion TCP | netstat
Nombre de connexions actives | netstat
ID du processus  | netstat
Nom du processus | ps
Arguments de processus | ps
Nom de l'application | dpkg ou rpm

## <a name="collected-data---hyper-v"></a>Données collectées - Hyper-V

L’appliance collecte les métadonnées de configuration et de performances des serveurs s’exécutant dans un environnement Hyper-V.

### <a name="metadata"></a>Métadonnées
Les métadonnées découvertes par l’appliance Azure Migrate vous aident à déterminer si les serveurs sont prêts pour la migration vers Azure : dimensionnement approprié des serveurs et coûts des plans. Microsoft n’utilise pas ces données dans le cadre des audits de conformité des licences.

Voici la liste complète des métadonnées des serveurs que l’appliance collecte et envoie à Azure.

**Données** | **Classe WMI** | **Propriété de classe WMI**
--- | --- | ---
**Détails du serveur** | 
Numéro de série du BIOS | Msvm_BIOSElement | BIOSSerialNumber
Type de serveur (Gen1 ou 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nom d’affichage du serveur | Msvm_VirtualSystemSettingData | ElementName
Version du serveur | Msvm_ProcessorSettingData | VirtualQuantity
Mémoire (octets) | Msvm_MemorySettingData | VirtualQuantity
Mémoire maximale que le serveur peut consommer | Msvm_MemorySettingData | Limite
Mémoire dynamique activée | Msvm_MemorySettingData | DynamicMemoryEnabled
Nom/version/nom de domaine complet du système d'exploitation | Msvm_KvpExchangeComponent | Données de nom GuestIntrinsicExchangeItems
État d’alimentation du serveur | Msvm_ComputerSystem | EnabledState
**Détails par disque** |
Identificateur de disque | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Type de disque dur virtuel | Msvm_VirtualHardDiskSettingData | Type
Taille de disque dur virtuel | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Parent de disque dur virtuel | Msvm_VirtualHardDiskSettingData | ParentPath
**Détails par carte d’interface réseau** |
Adresses IP (cartes réseau synthétiques) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP activé (cartes réseau synthétiques) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID de carte réseau (cartes réseau synthétiques) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Adresse MAC de la carte réseau (cartes réseau synthétiques) | Msvm_SyntheticEthernetPortSettingData | Adresse
ID de la carte réseau (cartes réseau héritées) | MsvmEmulatedEthernetPortSetting Data | InstanceID
ID MAC de la carte réseau (cartes réseau héritées) | MsvmEmulatedEthernetPortSetting Data | Adresse

### <a name="performance-data"></a>Données de performances

Voici les données de performances des serveurs que l’appliance collecte et envoie à Azure.

**Classe du compteur de performances** | **Compteur** | **Évaluation de l'impact**
--- | --- | ---
Processeur virtuel de l’hyperviseur Hyper-V | % de temps d'exécution de l’invité | Taille/coût du serveur recommandés
Serveur de la mémoire dynamique Hyper-V | Pression actuelle (%)<br/> Mémoire physique visible de l’invité (Mo) | Taille/coût du serveur recommandés
Dispositif de stockage virtuel Hyper-V | Octets lus/seconde | Calcul de la taille du disque, du coût de stockage et de la taille du serveur
Dispositif de stockage virtuel Hyper-V | Octets écrits/seconde | Calcul de la taille du disque, du coût de stockage et de la taille du serveur
Carte réseau virtuelle Hyper-V | Octets reçus/seconde | Calcul de la taille de serveur
Carte réseau virtuelle Hyper-V | Octets envoyés/seconde | Calcul de la taille de serveur

- L'utilisation du processeur représente la somme de l’utilisation de tous les processeurs virtuels connectés à un serveur.
- L'utilisation de la mémoire correspond à (Pression actuelle * Mémoire physique visible de l'invité) / 100.
- Les valeurs d'utilisation du disque et du réseau sont collectées à partir des compteurs de performances Hyper-V répertoriés.

## <a name="collected-data---physical"></a>Données collectées – Physique

L’appliance collecte des métadonnées de configuration et de performances à partir de serveurs physiques ou virtuels s’exécutant localement.

### <a name="metadata"></a>Métadonnées

Les métadonnées découvertes par l’appliance Azure Migrate vous aident à déterminer si les serveurs sont prêts pour la migration vers Azure : dimensionnement approprié des serveurs et coûts des plans. Microsoft n’utilise pas ces données dans le cadre des audits de conformité des licences.

### <a name="windows-server-metadata"></a>Métadonnées du serveur Windows

Voici la liste complète des métadonnées des serveurs Windows que l’appliance collecte et envoie à Azure.

**Données** | **Classe WMI** | **Propriété de classe WMI**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domain, Name, PartOfDomain
Nombre de cœurs de processeur | Win32_PRocessor | NumberOfCores
Mémoire allouée | Win32_ComputerSystem | TotalPhysicalMemory
Numéro de série du BIOS | Win32_ComputerSystemProduct | IdentifyingNumber
GUID du BIOS | Win32_ComputerSystemProduct | UUID
Type de démarrage | Win32_DiskPartition | Recherchez une partition dont Type = **GPT:System** pour EFI/BIOS
Nom du système d’exploitation | Win32_OperatingSystem | Caption
Version du SE |Win32_OperatingSystem | Version
Architecture du système d’exploitation | Win32_OperatingSystem | OSArchitecture
Nombre de disques | Win32_DiskDrive | Model, Size, DeviceID, MediaType, Name
Taille du disque | Win32_DiskDrive | Taille
Liste de cartes réseau | Win32_NetworkAdapterConfiguration | Description, Index
Adresse IP de la carte réseau | Win32_NetworkAdapterConfiguration | IPAddress
Adresse MAC de la carte réseau | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-server-metadata"></a>Métadonnées du serveur Linux

Voici la liste complète des métadonnées des serveurs Linux que l’appliance collecte et envoie à Azure.

**Données** | **Commandes**
--- | ---
FQDN | cat /proc/sys/kernel/hostname, hostname -f
Nombre de cœurs de processeur |  /proc/cpuinfo \| awk '/^processor/{print $3}' \| wc -l
Mémoire allouée | cat /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
Numéro de série du BIOS | lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2=""; print}'
GUID du BIOS | cat /sys/class/dmi/id/product_uuid
Type de démarrage | [ -d /sys/firmware/efi ] && echo EFI \|\| echo BIOS
Nom/version du système d’exploitation | Nous accédons à ces fichiers pour déterminer la version et le nom du système d’exploitation :<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Architecture du système d’exploitation | Uname -m
Nombre de disques | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Disque de démarrage | df /boot \| sed -n 2p \| awk '{print $1}'
Taille du disque | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
Liste de cartes réseau | ip -o -4 addr show \| awk '{print $2}'
Adresse IP de la carte réseau | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1 -d "/" 
Adresse MAC de la carte réseau | ip addr show $nic \| grep ether \| awk '{print $2}'

### <a name="windows-performance-data"></a>Données de performances Windows

Voici les données de performances des serveurs Windows que l’appliance collecte et envoie à Azure.

**Données** | **Classe WMI** | **Propriété de classe WMI**
--- | --- | ---
Utilisation de l’UC | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Utilisation de la mémoire | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
Nombre de cartes réseau | Win32_PerfFormattedData_Tcpip_NetworkInterface | Obtient le nombre de périphériques réseau.
Données reçues par la carte réseau | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Données transmises par la carte réseau | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Nombre de disques | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Nombre de disques
Détails du disque | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Données de performances Linux

Voici les données de performances des serveurs Linux que l’appliance collecte et envoie à Azure.

**Données** | **Commandes**
--- | ---
Utilisation de l’UC | cat /proc/stat/| grep 'cpu' /proc/stat
Utilisation de la mémoire | free \| grep Mem \| awk '{print $3/$2 * 100.0}'
Nombre de cartes réseau | lshw -class network \| grep eth[0-60] \| wc -l
Données reçues par la carte réseau | cat /sys/class/net/eth$nic/statistics/rx_bytes
Données transmises par la carte réseau | cat /sys/class/net/eth$nic/statistics/tx_bytes
Nombre de disques | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Détails du disque | cat /proc/diskstats


## <a name="appliance-upgrades"></a>Mises à niveau d’appliance

L’appliance est mise à niveau à mesure que les services Azure Migrate fonctionnant sur l’appliance sont mis à jour. Ceci se produit automatiquement, car la mise à jour automatique est activée par défaut sur l’appliance. Vous pouvez modifier ce paramètre par défaut pour mettre à jour les services de l’appliance manuellement.

### <a name="turn-off-auto-update"></a>Désactiver la mise à jour automatique

1. Sur le serveur exécutant l’appliance, ouvrez l’Éditeur du Registre.
2. Accédez à **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Pour désactiver la mise à jour automatique, créez une clé de Registre **AutoUpdate** avec la valeur DWORD 0.

    ![Définir la clé de Registre](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Activer la mise à jour automatique

Vous pouvez activer la mise à jour automatique à l’aide de l’une des méthodes suivantes :

- En supprimant la clé de registre AutoUpdate de HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
- Cliquez sur **Afficher les services de l’appliance** résultant de la dernière vérification des mises à jour dans le panneau **Configurer les prérequis** pour activer la mise à jour automatique.

Pour supprimer la clé de Registre :

1. Sur le serveur exécutant l’appliance, ouvrez l’Éditeur du Registre.
2. Accédez à **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Supprimez la clé de Registre **AutoUpdate** précédemment créée pour désactiver la mise à jour automatique.

Pour activer à partir d’Appliance Configuration Manager, une fois la découverte terminée :

1. Dans le gestionnaire de configuration de l’appliance, accédez au panneau **Configurer les prérequis**.
2. Dans la dernière vérification des mises à jour, cliquez sur **Afficher les services de l’appliance**, puis cliquez sur le lien pour activer la mise à jour automatique.

    ![Activer les mises à jour automatiques](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Vérifier la version des services de l’appliance

Vous pouvez vérifier la version des services de l’appliance à l’aide de l’une des méthodes suivantes :

- Dans le gestionnaire de configuration de l’appliance, accédez au panneau **Configurer les prérequis**.
- Sur l’appliance, dans le **Panneau de configuration** > **Programmes et fonctionnalités**.

Pour vérifier dans le gestionnaire de configuration de l’appliance :

1. Dans le gestionnaire de configuration de l’appliance, accédez au panneau **Configurer les prérequis**.
2. Dans la dernière vérification des mises à jour, cliquez sur **Afficher les services de l’appliance**.

    ![Vérifier la version](./media/migrate-appliance/versions.png)

Pour vérifier dans le Panneau de configuration :

1. Sur l’appliance, cliquez sur **Démarrer** > **Panneau de configuration** > **Programmes et fonctionnalités**
2. Vérifiez les versions des services de l’appliance dans la liste.

    ![Vérifier la version dans le Panneau de configuration](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Mise à jour manuelle d'une ancienne version

Si vous utilisez une ancienne version pour l’un des services, vous devez désinstaller le service puis effectuer une mise à jour manuelle avec la dernière version.

1. Pour vérifier les dernières versions d’un service d’appliance, [téléchargez](https://aka.ms/latestapplianceservices) le fichier LatestComponents.json.
2. Après le téléchargement, ouvrez le fichier LatestComponents.json dans le Bloc-notes.
3. Vous trouverez dans ce fichier la dernière version du service ainsi que le lien de téléchargement correspondant. Par exemple :

    "Name": "ASRMigrationWebApp", "DownloadLink": "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4. Téléchargez la dernière version d'un service obsolète en utilisant le lien de téléchargement figurant dans le fichier.
5. Après le téléchargement, exécutez la commande suivante dans une fenêtre de commande d'administrateur pour vérifier l'intégrité du fichier msi téléchargé.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Par exemple :  C:\>CertUtil -HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Vérifiez que la sortie de la commande correspond à l'entrée de la valeur de hachage pour le service dans le fichier (par exemple, la valeur de hachage MD5 ci-dessus).
6. Exécutez maintenant le fichier msi pour installer le service. L’installation est silencieuse et la fenêtre d'installation se ferme une fois l’opération terminée.
7. Une fois l'installation terminée, vérifiez la version du service dans **Panneau de configuration** > **Programmes et fonctionnalités**. La version du service devrait maintenant être mise à jour avec la dernière version indiquée dans le fichier json.

## <a name="next-steps"></a>Étapes suivantes

- [Découvrez comment](how-to-set-up-appliance-vmware.md) configurer l’appliance pour VMware.
- [Découvrez comment](how-to-set-up-appliance-hyper-v.md) configurer l’appliance pour Hyper-V.
- [Découvrez comment](how-to-set-up-appliance-physical.md) configurer l’appliance pour des serveurs physiques.