---
title: Appliance Azure Migrate
description: Présente une vue d’ensemble de l’utilisation de l’appliance Azure Migrate pour l’évaluation et la migration de serveurs.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 652fe9d379d6e2ba50e9e282f384905e154368d8
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031661"
---
# <a name="azure-migrate-appliance"></a>Appliance Azure Migrate

Cet article décrit l'appliance Azure Migrate. Vous déployez l’appliance lorsque vous utilisez l’outil [Azure Migrate : évaluation de serveur](migrate-services-overview.md#azure-migrate-server-assessment-tool) pour découvrir et évaluer les applications, l’infrastructure et les charges de travail à migrer vers Microsoft Azure. L’appliance est également utilisée lorsque vous migrez des machines virtuelles VMware vers Azure à l’aide d’[Azure Migrate : évaluation de serveur](migrate-services-overview.md#azure-migrate-server-migration-tool) avec [migration sans agent](server-migrate-overview.md).

## <a name="appliance-overview"></a>Vue d’ensemble de l’appliance

L’appliance Azure Migrate est utilisée dans les scénarios suivants.

**Scénario** | **Outil** | **Utilisé pour** 
--- | --- | ---
Machine virtuelle VMware | Azure Migrate : Server Assessment<br/><br/> Azure Migrate : Server Migration | Détection des machines virtuelles VMware<br/><br/> Détection des applications et des dépendances de machine<br/><br/> Collecter les métadonnées des machines et les métadonnées de performances pour les évaluations.<br/><br/> Répliquer des machines virtuelles VMware avec une migration sans agent.
Machine virtuelle Hyper-V | Azure Migrate : Server Assessment | Détection des machines virtuelles Hyper-V<br/><br/> Collecter les métadonnées des machines et les métadonnées de performances pour les évaluations.
Machine physique |  Azure Migrate : Server Assessment |  Détection des serveurs physiques<br/><br/> Collecter les métadonnées des machines et les métadonnées de performances pour les évaluations.

## <a name="appliance---vmware"></a>Appliance - VMware 

**Prérequis** | **VMware** 
--- | ---
**Format de téléchargement** | .OVA 
**Lien de téléchargement** | https://aka.ms/migrate/appliance/vmware 
**Taille du téléchargement** | 11,2 Go
**License** | Le modèle d’appliance téléchargé est fourni avec une licence d’évaluation Windows Server 2016 qui est valide pendant 180 jours. Si la période d’évaluation est proche de l’expiration, nous vous recommandons de télécharger et de déployer une nouvelle appliance, ou d’activer la licence du système d’exploitation de la machine virtuelle de l’appliance.
**Déploiement** | Vous déployez l’appliance en tant que machine virtuelle VMware. Vous devez disposer de suffisamment de ressources sur vCenter Server pour allouer une machine virtuelle avec 32 Go de RAM, 8 processeurs virtuels, environ 80 Go de stockage sur disque et un commutateur virtuel externe.<br/> L’appliance nécessite un accès à Internet, directement ou via un proxy.<br/> L’appliance ne peut se connecter qu'à un seul vCenter Server.
**Matériel** | Les ressources sur vCenter pour allouer une machine virtuelle avec 8 processeurs virtuels comportant 32 Go de RAM, environ 80 Go de stockage sur disque et un commutateur virtuel externe. 
**Valeur de hachage** | MD5 : c06ac2a2c0f870d3b274a0b7a73b78b1<br/><br/> SHA256 : 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c
**Serveur/hôte vCenter** | La machine virtuelle de l’appliance doit être déployée sur un hôte ESXi exécutant la version 5.5 ou ultérieure.<br/><br/> vCenter Server exécutant les versions 5,5, 6,0, 6,5 ou 6,7.
**Projet Azure Migrate** | Une appliance peut être associée à un seul projet. <br/> Un nombre quelconque d’appliances peut être associé à un même projet.<br/> 
**Découverte** | Une appliance peut découvrir jusqu’à 10 000 machines virtuelles VMware sur un serveur vCenter.<br/> Une appliance ne peut se connecter qu'à un seul vCenter Server.
**Composants de l’appliance** | Application de gestion : Application Web dans l’appliance pour les entrées d’utilisateurs pendant le déploiement.<br/> Agent de découverte : recueille les données de configuration de l’ordinateur.<br/> Agent d’évaluation : collecte les données de performances.<br/> DRA : orchestre la réplication des machines virtuelles et coordonne la communication entre les machines/Azure.<br/> Passerelle : envoie des données répliquées vers Azure.<br/> Service de mise à jour automatique : met à jour les composants (s’exécute toutes les 24 heures).
**VDDK (migration sans agent)** | Si vous exécutez une migration sans agent avec Azure Migrate Server Migration, le VDDK VMware vSphere doit être installé sur la machine virtuelle de l’appliance.


## <a name="appliance---hyper-v"></a>Appliance - Hyper-V

**Prérequis** | **Hyper-V** 
--- | ---
**Format de téléchargement** | Dossier zippé (avec disque dur virtuel)
**Lien de téléchargement** | https://aka.ms/migrate/appliance/hyperv 
**Taille du téléchargement** | 10 Go
**License** | Le modèle d’appliance téléchargé est fourni avec une licence d’évaluation Windows Server 2016 qui est valide pendant 180 jours. Si la période d’évaluation est proche de l’expiration, nous vous recommandons de télécharger et de déployer une nouvelle appliance, ou d’activer la licence du système d’exploitation de la machine virtuelle de l’appliance.
**Étapes de déploiement d’appliance**   |  Vous déployez l’appliance en tant que machine virtuelle Hyper-V.<br/> La machine virtuelle de l’appliance fournie par Azure Migrate est la version 5.0 de la machine virtuelle Hyper-V.<br/> L’hôte Hyper-V doit exécuter Windows Server 2012 R2 ou une version ultérieure.<br/> L’hôte a besoin de suffisamment d’espace pour allouer 16 Go de RAM, 8 processeurs virtuels, environ 80 Go d’espace de stockage et un commutateur externe à la machine virtuelle de l’appliance.<br/> L'appliance nécessite une adresse IP statique ou dynamique et un accès Internet.
**Matériel** | Ressources sur l’hôte Hyper-V pour allouer 16 Go de RAM, 8 processeurs virtuels, environ 80 Go d’espace de stockage et un commutateur externe à la machine virtuelle de l’appliance.
**Valeur de hachage** | MD5 : 29a7531f32bcf69f32d964fa5ae950bc<br/><br/> SHA256 : 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31
**Hôte Hyper-V** | Exécution de Windows Server 2012 R2 ou version ultérieure.
**Projet Azure Migrate** | Une appliance peut être associée à un seul projet. <br/> Un nombre quelconque d’appliances peut être associé à un même projet.<br/> 
**Découverte** | Une appliance peut découvrir jusqu’à 5 000 machines virtuelles VMware sur un serveur vCenter.<br/> Une appliance peut connecter à jusqu’à 300 hôtes Hyper-V.
**Composants de l’appliance** | Application de gestion : Application Web dans l’appliance pour les entrées d’utilisateurs pendant le déploiement.<br/> Agent de découverte : recueille les données de configuration de l’ordinateur.<br/> Agent d’évaluation : collecte les données de performances.<br/>  Service de mise à jour automatique : met à jour les composants (s’exécute toutes les 24 heures).


## <a name="appliance---physical"></a>Appliance - Physique

**Prérequis** | **Physique** 
--- | ---
**Format de téléchargement** | Dossier zippé (avec le script du programme d’installation PowerShell)
**Lien de téléchargement** | [Lien de téléchargement](https://go.microsoft.com/fwlink/?linkid=2105112)
**Taille du téléchargement** | 59,7 Mo
**Matériel** | Machine physique dédiée ou machine virtuelle. L’ordinateur exécutant l’appliance nécessite 16 Go de RAM, 8 processeurs virtuels, environ 80 Go d’espace de stockage et un commutateur externe.<br/> L'appliance nécessite une adresse IP statique ou dynamique et un accès Internet.
**Valeur de hachage** | MD5 : 1e92ede3e87c03bd148e56a708cdd33f<br/><br/> SHA256 : a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c
**Système d’exploitation** | La machine de l’appliance doit exécuter Windows Server 2016. 
**Étapes de déploiement d’appliance**   |  Le script du programme d'installation de l'appliance est téléchargé depuis le portail (dans un dossier compressé). <br/> Vous décompressez le dossier et exécutez le script PowerShell (AzureMigrateInstaller.ps1).
**Découverte** | Une appliance peut découvrir jusqu’à 250 serveurs physiques.
**Composants de l’appliance** | Application de gestion : Application Web dans l’appliance pour les entrées d’utilisateurs pendant le déploiement.<br/> Agent de découverte : recueille les données de configuration de l’ordinateur.<br/> Agent d’évaluation : collecte les données de performances.<br/>  Service de mise à jour automatique : met à jour les composants (s’exécute toutes les 24 heures).


## <a name="url-access"></a>accès URL

L’appliance Azure Migrate a besoin d’une connectivité Internet.

- Quand vous déployez l’appliance, Azure Migrate effectue un contrôle de connectivité aux URL récapitulées dans le tableau ci-dessous.
- Si vous utilisez un proxy basé sur les URL pour vous connecter à Internet, autorisez l’accès à ces URL, en vérifiant que le proxy résout tous les enregistrements CNAME reçus lors de la recherche des URL.

**URL** | **Détails**  
--- | --- |
*. portal.azure.com  | Accédez au portail Azure.
\* .windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Connectez-vous à votre abonnement Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Créez des applications Active Directory pour que l’appliance communique avec Azure Migrate.
management.azure.com | Créez des applications Active Directory pour que l’appliance communique avec le service Azure Migrate.
dc.services.visualstudio.com | Chargez les journaux d’applications utilisés pour la supervision interne.
*.vault.azure.net | Gérez les secrets dans Azure Key Vault.
aka.ms/* | Autorisez l’accès à des liens aka. Utilisé pour les mises à jour de l’appliance Azure Migrate.
download.microsoft.com/download | Autoriser les téléchargements à partir du téléchargement Microsoft.
*.servicebus.windows.net | Communication entre l’appliance et le service Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Connectez-vous aux URL du service Azure Migrate.
*.hypervrecoverymanager.windowsazure.com | **Utilisé pour la migration sans agent VMware**<br/><br/> Connectez-vous aux URL du service Azure Migrate.
*.blob.core.windows.net |  **Utilisé pour la migration sans agent VMware**<br/><br/>Chargez les données vers le stockage pour la migration.




## <a name="collected-data---vmware"></a>Données collectées - VMware

### <a name="collected-performance-data-vmware"></a>Données de performances VMware collectées

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


### <a name="collected-metadata-vmware"></a>Métadonnées VMware collectées

> [!NOTE]
> Les métadonnées découvertes par l'appliance Azure Migrate sont utilisées pour vous aider à adapter la taille de vos applications lors de leur migration vers Azure, ainsi qu'à effectuer les analyses d'adéquation Azure, les analyses de dépendance des applications et la planification des coûts. Microsoft n’utilise pas ces données dans le cadre d’un audit de conformité des licences.

Voici la liste complète des métadonnées des machines virtuelles VMware que l’appliance collecte et envoie à Azure.

**Données** | **Compteur**
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
Name | container.GetType().Name
Type d’objet enfant | container.ChildType
Détails de référence | container.MoRef
Détails du parent | Container.Parent
Détails du dossier par machine virtuelle | ((Folder)container).ChildEntity.Type
Détails du centre de données par machine virtuelle | ((Datacenter)container).VmFolder
Détails du centre de données par dossier hôte | ((Datacenter)container).HostFolder
Détails du cluster par hôte | ((ClusterComputeResource)container).Host
Détails de l’hôte par machine virtuelle | ((HostSystem)container).VM

## <a name="collected-data---hyper-v"></a>Données collectées - Hyper-V

### <a name="collected-performance-data-hyper-v"></a>Données de performances Hyper-V collectées

> [!NOTE]
> Les métadonnées découvertes par l'appliance Azure Migrate sont utilisées pour vous aider à adapter la taille de vos applications lors de leur migration vers Azure, ainsi qu'à effectuer les analyses d'adéquation Azure, les analyses de dépendance des applications et la planification des coûts. Microsoft n’utilise pas ces données dans le cadre d’un audit de conformité des licences.

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

### <a name="collected-metadata-hyper-v"></a>Métadonnées Hyper-V collectées

Voici la liste complète des métadonnées des machines virtuelles Hyper-V que l’appliance collecte et envoie à Azure.

**Données** | **Classe WMI** | **Propriété de classe WMI**
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




## <a name="discovery-and-collection-process"></a>Processus de découverte et de collecte

L'appliance communique avec les instances vCenter Server et les hôtes/grappes Hyper-V via le processus suivant.

1. **Démarrer la découverte** :
    - Lorsque vous lancez la découverte sur l'appliance Hyper-V, celle-ci communique avec les hôtes Hyper-V sur les ports WinRM 5985 (HTTP) et 5986 (HTTPS).
    - Lorsque vous lancez la découverte sur l'appliance VMware, elle communique par défaut avec vCenter Server sur le port TCP 443. Si vCenter Server écoute sur un port différent, vous pouvez le configurer dans l'application web de l'appliance.
2. **Recueillir des métadonnées et des données de performances** :
    - L'appliance utilise une session CIM (Common Information Model) pour collecter les données des machines virtuelles Hyper-V de l'hôte Hyper-V sur les ports 5985 et 5986.
    - Par défaut, l'appliance communique avec le port 443 pour collecter les données des machines virtuelles VMware à partir de vCenter Server.
3. **Envoyer des données** : L’appliance envoie les données collectées aux outils Évaluation de serveur Azure Migrate et Migration de serveur Azure Migrate via le port SSL 443. L’appliance peut se connecter à Azure via Internet, ou vous pouvez utiliser ExpressRoute avec le Peering public/Microsoft.
    - Pour les données de performances, l’appliance collecte les données d'utilisation en temps réel.
        - Les données de performances sont collectées toutes les 20 secondes pour VMware et toutes les 30 secondes pour Hyper-V, pour chaque mesure de performance.
        - Les données collectées sont regroupées pour créer un point de données unique pendant 10 minutes.
        - La valeur d'utilisation maximale est choisie parmi tous les points de données de 20/30 secondes puis envoyée à Azure pour le calcul de l'évaluation.
        - Selon la valeur du percentile spécifiée dans les propriétés de l’évaluation (50e/90e/95e/99e), les points de dix minutes sont triés par ordre croissant, et la valeur appropriée du percentile sert à calculer l'évaluation
    - Pour Migration du serveur, l'appliance commence à collecter les données des machines virtuelles et les réplique vers Azure.
4. **Évaluer et migrer** : Vous pouvez maintenant créer des évaluations à partir des métadonnées collectées par l'appliance en utilisant l’outil Évaluation du serveur Azure Migrate. En outre, vous pouvez également commencer à migrer des machines virtuelles VMware en utilisant l’outil Migration de serveur Azure Migrate pour orchestrer la réplication des machines virtuelles sans agent.


![Architecture](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Mises à niveau d’appliance

L'appliance est mise à niveau à mesure que les agents Azure Migrate fonctionnant sur l'appliance sont mis à jour.

- Cela se produit automatiquement car la mise à jour automatique est activée par défaut sur l'appliance.
- Vous pouvez modifier ce paramètre par défaut pour mettre à jour les agents manuellement.
- Pour désactiver la mise à jour automatique, accédez à l’éditeur du Registre > HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance et définissez la clé de Registre « AutoUpdate » sur 0 (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Définir des mises à jour manuelles de l'agent

Pour les mises à jour manuelles, assurez-vous de mettre à jour tous les agents de l'appliance en même temps en utilisant le bouton **Update** (Mettre à jour) pour chaque agent obsolète de l'appliance. Vous pouvez à tout moment repasser en mode de mise à jour automatique.

## <a name="next-steps"></a>Étapes suivantes

[Découvrez comment](tutorial-assess-vmware.md#set-up-the-appliance-vm) configurer l’appliance pour VMware.
[Découvrez comment](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) configurer l’appliance pour Hyper-V.

