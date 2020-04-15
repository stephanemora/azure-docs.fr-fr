---
title: Appliance Azure Migrate
description: Présente une vue d’ensemble de l’utilisation de l’appliance Azure Migrate pour l’évaluation et la migration de serveurs.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: bccf4738d46b65f2d149eafc8e69591141d7d073
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437586"
---
# <a name="azure-migrate-appliance"></a>Appliance Azure Migrate

Cet article récapitule les prérequis et les exigences de la prise en charge de l’appliance Azure Migrate. 

## <a name="deployment-scenarios"></a>Scénarios de déploiement

L’appliance Azure Migrate est utilisée dans les scénarios suivants.

**Scénario** | **Outil** | **Utilisé pour** 
--- | --- | ---
**Évaluation des machines virtuelles VMware** | Azure Migrate : Évaluation de serveurs | Détection des machines virtuelles VMware<br/><br/> Détection des applications et des dépendances de machine<br/><br/> Collecter les métadonnées des machines et les métadonnées de performances pour les évaluations.
**Migration sans agent des machines virtuelles VMware** | Azure Migrate : Migration de serveurs | Détection des machines virtuelles VMware <br/><br/> Répliquer des machines virtuelles VMware avec une migration sans agent.
**Évaluation des machines virtuelles Hyper-V** | Azure Migrate : Évaluation de serveurs | Détection des machines virtuelles Hyper-V<br/><br/> Collecter les métadonnées des machines et les métadonnées de performances pour les évaluations.
**Évaluation des machines physiques** |  Azure Migrate : Évaluation de serveurs |  Découvrez les serveurs physiques (ou les machines virtuelles que vous traitez comme des serveurs physiques).<br/><br/> Collecter les métadonnées des machines et les métadonnées de performances pour les évaluations.

## <a name="appliance---vmware"></a>Appliance - VMware 

Le tableau suivant récapitule les exigences de l’appliance Azure Migrate pour VMware.

**Prérequis** | **VMware** 
--- | ---
**Composants de l’appliance** | L’appliance a les composants suivants :<br/><br/> - **Application de gestion** : Il s’agit d’une application web pour l’entrée des utilisateurs pendant le déploiement de l’appareil. Utilisé lors de l’évaluation des machines pour la migration vers Azure.<br/> - **Agent de découverte** : L’agent collecte les données de configuration de la machine. Utilisé lors de l’évaluation des machines pour la migration vers Azure.<br/>- **Agent d’évaluation** : L’agent collecte les données de performance. Utilisé lors de l’évaluation des machines pour la migration vers Azure.<br/>- **Service de mise à jour automatique** : Met à jour les composants de l’appliance (s’exécute toutes les 24 heures).<br/>- **Agent DRA** : Orchestre la réplication des machines virtuelles et coordonne la communication entre les machines répliquées et Azure. Utilisé seulement lors de la réplication de machines virtuelles VMware sur Azure avec la migration sans agent.<br/>- **Passerelle** : envoie des données répliquées vers Azure. Utilisé seulement lors de la réplication de machines virtuelles VMware sur Azure avec la migration sans agent.
**Déploiement pris en charge** | Déployez en tant que machine virtuelle VMware à en utilisant un modèle OVA.<br/><br/> Déployez en tant que machine virtuelle VMware ou que machine physique avec un script d’installation PowerShell.
**Prise en charge de projet** |  Une appliance peut être associée à un seul projet. <br/> Un nombre quelconque d’appliances peut être associé à un même projet.<br/> 
**Limites de la découverte** | Une appliance peut découvrir jusqu’à 10 000 machines virtuelles VMware sur un serveur vCenter.<br/> Une appliance ne peut se connecter qu'à un seul vCenter Server.
**Modèle OVA** | Téléchargez depuis le portail ou depuis https://aka.ms/migrate/appliance/vmware.<br/><br/> La taille du téléchargement est de 11,2 Go.<br/><br/> Le modèle d’appliance téléchargé est fourni avec une licence d’évaluation Windows Server 2016 qui est valide pendant 180 jours. Si la période d’évaluation est proche de l’expiration, nous vous recommandons de télécharger et de déployer une nouvelle appliance, ou d’activer la licence du système d’exploitation de la machine virtuelle de l’appliance.
**Script PowerShell** | [Téléchargez](https://go.microsoft.com/fwlink/?linkid=2105112) le script.<br/><br/> 
**Logiciel/matériel** |  L’appliance doit s’exécuter sur une machine avec Windows Server 2016, 32 Go de RAM, 8 processeurs virtuels, environ 80 Go de stockage sur disque et un commutateur virtuel externe.<br/> L’appliance nécessite un accès à Internet, directement ou via un proxy.<br/><br/> Si vous exécutez l’appliance sur une machine virtuelle VMware, vous devez disposer de suffisamment de ressources sur le serveur vCenter pour allouer une machine virtuelle qui répond à la configuration requise.<br/><br/> Si vous exécutez l’appliance sur une machine physique, vérifiez qu’elle exécute Windows Server 2016 et qu’elle est conforme à la configuration matérielle requise. 
**Conditions requises VMware** | Si vous déployez l’appliance en tant que machine virtuelle VMware, elle doit être déployée sur un hôte ESXi exécutant la version 5.5 ou ultérieure.<br/><br/> vCenter Server exécutant les versions 5,5, 6,0, 6,5 ou 6,7.
**VDDK (migration sans agent)** | Si vous déployez l’appliance en tant que machine virtuelle VMware et que vous effectuez une migration sans agent, le VDDK VMware vSphere doit être installé sur la machine virtuelle de l’appliance.
**Valeur de hachage - OVA** | [Vérifiez](tutorial-assess-vmware.md#verify-security) les valeurs de hachage du modèle OVA.
**Valeur de hachage - Script PowerShell** | [Vérifiez](deploy-appliance-script.md#verify-file-security) les valeurs de hachage du script PowerShell.




## <a name="appliance---hyper-v"></a>Appliance - Hyper-V

**Prérequis** | **Hyper-V** 
--- | ---
**Composants de l’appliance** | L’appliance a les composants suivants :<br/><br/>- **Application de gestion** : Il s’agit d’une application web pour l’entrée des utilisateurs pendant le déploiement de l’appareil. Utilisé lors de l’évaluation des machines pour la migration vers Azure.<br/> - **Agent de découverte** : L’agent collecte les données de configuration de la machine. Utilisé lors de l’évaluation des machines pour la migration vers Azure.<br/>- **Agent d’évaluation** : L’agent collecte les données de performance. Utilisé lors de l’évaluation des machines pour la migration vers Azure.<br/>- **Service de mise à jour automatique** : Met à jour les composants de l’appliance (s’exécute toutes les 24 heures).
**Déploiement pris en charge** | Déployez en tant que machine virtuelle Hyper-V en utilisant un modèle de disque dur virtuel.<br/><br/> Déployez en tant que machine virtuelle Hyper-V ou que machine physique avec un script d’installation PowerShell.
**Prise en charge de projet** |  Une appliance peut être associée à un seul projet. <br/> Un nombre quelconque d’appliances peut être associé à un même projet.<br/> 
**Limites de la découverte** | Une appliance peut détecter jusqu’à 5 000 machines virtuelles Hyper-V.<br/> Une appliance peut connecter à jusqu’à 300 hôtes Hyper-V.
**Modèle de disque dur virtuel** | Dossier compressé incluant le disque dur virtuel. Téléchargez depuis le portail ou depuis https://aka.ms/migrate/appliance/hyperv.<br/><br/> La taille du téléchargement est de 10 Go.<br/><br/> Le modèle d’appliance téléchargé est fourni avec une licence d’évaluation Windows Server 2016 qui est valide pendant 180 jours. Si la période d’évaluation est proche de l’expiration, nous vous recommandons de télécharger et de déployer une nouvelle appliance, ou d’activer la licence du système d’exploitation de la machine virtuelle de l’appliance.
**Script PowerShell** | [Téléchargez](https://go.microsoft.com/fwlink/?linkid=2105112) le script.<br/><br/> 
**Logiciel/matériel***   |  L’appliance doit s’exécuter sur une machine avec Windows Server 2016, 32 Go de RAM, 8 processeurs virtuels, environ 80 Go de stockage sur disque et un commutateur virtuel externe.<br/> L’appliance nécessite une adresse IP statique ou dynamique et un accès à Internet, directement ou via un proxy.<br/><br/> Si vous déployez l’appliance en tant que machine virtuelle Hyper-V, vous devez disposer de suffisamment de ressources sur l’hôte Hyper-V pour allouer 16 Go de RAM, 8 processeurs virtuels, environ 80 Go d’espace de stockage et un commutateur externe pour la machine virtuelle de l’appliance.<br/><br/> Si vous exécutez l’appliance sur une machine physique, vérifiez qu’elle exécute Windows Server 2016 et qu’elle est conforme à la configuration matérielle requise. 
**Configuration requise pour Hyper-V** | Si vous déployez l’appliance avec le modèle de disque dur virtuel, la machine virtuelle de l’appliance fournie par Azure Migrate est une machine virtuelle Hyper-V version 5.0.<br/><br/> L’hôte Hyper-V doit exécuter Windows Server 2012 R2 ou une version ultérieure. 
**Valeur de hachage - Disque dur virtuel** | [Vérifiez](tutorial-assess-hyper-v.md#verify-security) les valeurs de hachage du modèle de disque dur virtuel.
**Valeur de hachage - Script PowerShell** | [Vérifiez](deploy-appliance-script.md#verify-file-security) les valeurs de hachage du script PowerShell.


## <a name="appliance---physical"></a>Appliance - Physique

**Prérequis** | **Physique** 
--- | ---
**Composants de l’appliance** | L’appliance a les composants suivants : <br/><br/> - **Application de gestion** : Il s’agit d’une application web pour l’entrée des utilisateurs pendant le déploiement de l’appareil. Utilisé lors de l’évaluation des machines pour la migration vers Azure.<br/> - **Agent de découverte** : L’agent collecte les données de configuration de la machine. Utilisé lors de l’évaluation des machines pour la migration vers Azure.<br/>- **Agent d’évaluation** : L’agent collecte les données de performance. Utilisé lors de l’évaluation des machines pour la migration vers Azure.<br/>- **Service de mise à jour automatique** : Met à jour les composants de l’appliance (s’exécute toutes les 24 heures).
**Déploiement pris en charge** | Déployez en tant que machine dédiée ou en tant que machine virtuelle avec un script d’installation PowerShell.
**Prise en charge de projet** |  Une appliance peut être associée à un seul projet. <br/> Un nombre quelconque d’appliances peut être associé à un même projet.<br/> 
**Limites de la découverte** | Une appliance peut découvrir jusqu’à 250 serveurs physiques.
**Script PowerShell** | Téléchargez le script (AzureMigrateInstaller. ps1) dans un dossier compressé depuis le portail. [Plus d’informations](tutorial-assess-physical.md#set-up-the-appliance) Vous pouvez aussi [télécharger directement](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> La taille du téléchargement est de 59.7 Go.
**Logiciel/matériel** |  L’appliance doit s’exécuter sur une machine avec Windows Server 2016, 32 Go de RAM, 8 processeurs virtuels, environ 80 Go de stockage sur disque et un commutateur virtuel externe.<br/> L’appliance nécessite une adresse IP statique ou dynamique et un accès à Internet, directement ou via un proxy.<br/><br/> Si vous exécutez l’appliance sur une machine physique, vérifiez qu’elle exécute Windows Server 2016 et qu’elle est conforme à la configuration matérielle requise. 
**Valeur de hachage** | [Vérifiez](deploy-appliance-script.md#verify-file-security) les valeurs de hachage du script PowerShell.

## <a name="url-access"></a>accès URL

L’appliance Azure Migrate a besoin d’une connectivité Internet.

- Quand vous déployez l’appliance, Azure Migrate effectue un contrôle de connectivité aux URL récapitulées dans le tableau ci-dessous.
- Si vous utilisez un proxy basé sur les URL pour vous connecter à Internet, vous devez autoriser l’accès à ces URL, en vérifiant que le proxy résout tous les enregistrements CNAME reçus lors de la recherche des URL.

**URL** | **Détails**  
--- | --- |
*. portal.azure.com  | Accédez au portail Azure.
\* .windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Connectez-vous à votre abonnement Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Créez des applications Azure Active Directory (AD) pour que l’appliance communique avec Azure Migrate.
management.azure.com | Créez des applications Azure AD pour que l’appliance communique avec le service Azure Migrate.
dc.services.visualstudio.com | Chargez les journaux d’applications utilisés pour la supervision interne.
*.vault.azure.net | Gérez les secrets dans Azure Key Vault.
aka.ms/* | Autorisez l’accès à des liens aka. Utilisé pour les mises à jour de l’appliance Azure Migrate.
download.microsoft.com/download | Autoriser les téléchargements à partir du téléchargement Microsoft.
*.servicebus.windows.net | Communication entre l’appliance et le service Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Connectez-vous aux URL du service Azure Migrate.
*.hypervrecoverymanager.windowsazure.com | **Utilisé pour la migration sans agent VMware**<br/><br/> Connectez-vous aux URL du service Azure Migrate.
*.blob.core.windows.net |  **Utilisé pour la migration sans agent VMware**<br/><br/>Chargez les données vers le stockage pour la migration.




## <a name="collected-data---vmware"></a>Données collectées - VMware

L’appliance collecte les métadonnées, les données de performance et les données d’analyse des dépendances (si l’[analyse des dépendances](concepts-dependency-visualization.md) sans agent est utilisée).

### <a name="metadata"></a>Métadonnées

Les métadonnées découvertes par l’appliance Azure Migrate vous aident à déterminer si les machines et les applications sont prêtes pour la migration vers Azure : dimensionnement approprié des machines et des applications, coûts des plans et analyse des dépendances des applications. Microsoft n’utilise pas ces données dans le cadre des audits de conformité des licences.

Voici la liste complète des métadonnées des machines virtuelles VMware que l’appliance collecte et envoie à Azure.

**DONNÉES** | **COMPTEUR**
--- | --- 
**Détails de la machine** | 
ID de l'ordinateur virtuel | vm.Config.InstanceUuid 
nom de la machine virtuelle | vm.Config.Name
ID du vCenter Server | VMwareClient.Instance.Uuid
Description de la machine virtuelle | vm.Summary.Config.Annotation
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
Détails du dossier par machine virtuelle | ((Folder)container).ChildEntity.Type
Détails du centre de données par machine virtuelle | ((Datacenter)container).VmFolder
Détails du centre de données par dossier hôte | ((Datacenter)container).HostFolder
Détails du cluster par hôte | ((ClusterComputeResource)container).Host
Détails de l’hôte par machine virtuelle | ((HostSystem)container).VM

### <a name="performance-data"></a>Données de performances


Voici les données de performances des machines virtuelles VMware que l’appliance collecte et envoie à Azure.

**Données** | **Compteur** | **Évaluation de l'impact**
--- | --- | ---
Utilisation du processeur | cpu.usage.average | Taille de machine virtuelle recommandée/coût
Utilisation de la mémoire | mem.usage.average | Taille de machine virtuelle recommandée/coût
Débit de lecture du disque (Mo par seconde) | virtualDisk.read.average | Calcul de la taille du disque, du coût de stockage et de la taille de la machine virtuelle
Débits d’écriture du disque (Mo par seconde) | virtualDisk.write.average | Calcul de la taille du disque, du coût de stockage et de la taille de la machine virtuelle
Opérations de lecture du disque par seconde | virtualDisk.numberReadAveraged.average | Calcul de la taille du disque, du coût de stockage et de la taille de la machine virtuelle
Opérations d’écriture du disque par seconde | virtualDisk.numberWriteAveraged.average  | Calcul de la taille du disque, du coût de stockage et de la taille de la machine virtuelle
Débit de lecture de la carte d’interface réseau (Mo par seconde) | net.received.average | Calcul de la taille de machine virtuelle
Débit des écritures de la carte réseau (Mo par seconde) | net.transmitted.average  |Calcul de la taille de machine virtuelle

### <a name="app-dependencies-metadata"></a>Métadonnées des dépendances d’application

L’analyse des dépendances sans agent collecte les données de connexion et de processus.

#### <a name="connection-data"></a>Données de connexion

Voici les données de connexion que l’appliance collecte auprès de chaque machine virtuelle activée pour l’analyse des dépendances sans agent. Ces données sont envoyées à Azure.

**Données** | **Commande utilisée** 
--- | --- 
Port local | netstat
Adresse IP locale | netstat
Port distant | netstat
Adresse IP distante | netstat
État de la connexion TCP | netstat
ID du processus | netstat
Non. de connexions actives | netstat

#### <a name="process-data"></a>Traitement des données
Voici les données de processus que l’appliance collecte auprès de chaque machine virtuelle activée pour l’analyse des dépendances sans agent. Ces données sont envoyées à Azure.

**Données** | **Classe WMI** | **Propriété de classe WMI**
--- | --- | ---
Nom du processus | Win32_Process | ExecutablePath
Arguments de processus | Win32_Process | CommandLine
Nom de l'application | Win32_Process | Paramètre VersionInfo.ProductName de la propriété ExecutablePath

#### <a name="linux-vm-data"></a>Données des machines virtuelles Linux

Voici les données de connexion et de processus que l’appliance collecte auprès de chaque machine virtuelle Linux activée pour l’analyse des dépendances sans agent. Ces données sont envoyées à Azure.

**Données** | **Commande utilisée** 
--- | ---
Port local | netstat 
Adresse IP locale | netstat 
Port distant | netstat 
Adresse IP distante | netstat 
État de la connexion TCP | netstat 
Non. de connexions actives | netstat
ID du processus  | netstat 
Nom du processus | ps
Arguments de processus | ps
Nom de l'application | dpkg ou rpm



## <a name="collected-data---hyper-v"></a>Données collectées - Hyper-V

L’appliance collecte les métadonnées, les données de performance et les données d’analyse des dépendances (si l’[analyse des dépendances](concepts-dependency-visualization.md) sans agent est utilisée).

### <a name="metadata"></a>Métadonnées
Les métadonnées découvertes par l’appliance Azure Migrate vous aident à déterminer si les machines et les applications sont prêtes pour la migration vers Azure : dimensionnement approprié des machines et des applications, coûts des plans et analyse des dépendances des applications. Microsoft n’utilise pas ces données dans le cadre des audits de conformité des licences.

Voici la liste complète des métadonnées des machines virtuelles Hyper-V que l’appliance collecte et envoie à Azure.

**DONNÉES* | **CLASSE WMI** | **PROPRIÉTÉ DE LA CLASSE WMI**
--- | --- | ---
**Détails de la machine** | 
Numéro de série de BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Type de machine virtuelle (Gen1 ou 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nom d'affichage de la machine virtuelle | Msvm_VirtualSystemSettingData | ElementName
Version de la machine virtuelle | Msvm_ProcessorSettingData | VirtualQuantity
Mémoire (octets) | Msvm_MemorySettingData | VirtualQuantity
Mémoire maximale pouvant être consommée par machine virtuelle | Msvm_MemorySettingData | Limite
Mémoire dynamique activée | Msvm_MemorySettingData | DynamicMemoryEnabled
Nom/version/nom de domaine complet du système d'exploitation | Msvm_KvpExchangeComponent | Données de nom GuestIntrinsicExchangeItems
États d’alimentation de la machine virtuelle | Msvm_ComputerSystem | EnabledState
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

Voici les données de performances des machines virtuelles Hyper-V que l’appliance collecte et envoie à Azure.

**Classe du compteur de performances** | **Compteur** | **Évaluation de l'impact**
--- | --- | ---
Processeur virtuel de l’hyperviseur Hyper-V | % de temps d'exécution de l’invité | Taille de machine virtuelle recommandée/coût
Machine virtuelle de la mémoire dynamique Hyper-V | Pression actuelle (%)<br/> Mémoire physique visible de l’invité (Mo) | Taille de machine virtuelle recommandée/coût
Dispositif de stockage virtuel Hyper-V | Octets lus/seconde | Calcul de la taille du disque, du coût de stockage et de la taille de la machine virtuelle
Dispositif de stockage virtuel Hyper-V | Octets écrits/seconde | Calcul de la taille du disque, du coût de stockage et de la taille de la machine virtuelle
Carte réseau virtuelle Hyper-V | Octets reçus/seconde | Calcul de la taille de machine virtuelle
Carte réseau virtuelle Hyper-V | Octets envoyés/seconde | Calcul de la taille de machine virtuelle

- L'utilisation du processeur représente la somme de toutes les utilisations pour tous les processeurs virtuels connectés à une machine virtuelle.
- L'utilisation de la mémoire correspond à (Pression actuelle * Mémoire physique visible de l'invité) / 100.
- Les valeurs d'utilisation du disque et du réseau sont collectées à partir des compteurs de performances Hyper-V répertoriés.

## <a name="appliance-upgrades"></a>Mises à niveau d’appliance

L'appliance est mise à niveau à mesure que les agents Azure Migrate fonctionnant sur l'appliance sont mis à jour. Ceci se produit automatiquement, car la mise à jour automatique est activée par défaut sur l’appliance. Vous pouvez modifier ce paramètre par défaut pour mettre à jour les agents manuellement.

- **Désactiver la mise à jour automatique** : Vous désactivez la mise à jour automatique dans le registre en définissant la clé HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "AutoUpdate" sur 0 (DWORD). Si vous décidez d’utiliser des mises à jour manuelles, il est important de mettre à jour tous les agents de l’appliance en même temps en utilisant le bouton **Mettre à jour** pour chaque agent obsolète de l’appliance.
- **Mettre à jour manuellement** : Pour les mises à jour manuelles, veillez à mettre à jour tous les agents de l’appliance en utilisant le bouton **Mettre à jour** pour chaque agent obsolète de l’appliance. Vous pouvez à tout moment repasser en mode de mise à jour automatique.

![Mettre à jour l’appliance automatiquement](./media/migrate-appliance/autoupdate.png)

## <a name="next-steps"></a>Étapes suivantes

- [Découvrez comment](how-to-set-up-appliance-vmware.md) configurer l’appliance pour VMware.
- [Découvrez comment](how-to-set-up-appliance-hyper-v.md) configurer l’appliance pour Hyper-V.
- [Découvrez comment](how-to-set-up-appliance-physical.md) configurer l’appliance pour des serveurs physiques.

