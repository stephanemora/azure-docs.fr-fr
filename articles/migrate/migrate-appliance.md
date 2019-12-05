---
title: Architecture de l’appliance Azure Migrate
description: Présente une vue d’ensemble de l’utilisation de l’appliance Azure Migrate pour l’évaluation et la migration de serveurs.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 49545ca6c43c272c3fd84f8bee59b8617aae136d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232565"
---
# <a name="azure-migrate-appliance"></a>Appliance Azure Migrate

Cet article décrit l'appliance Azure Migrate. Vous déployez l'appliance lorsque vous utilisez les outils Évaluation d’Azure Migrate et Migration pour découvrir, évaluer et migrer des applications, une infrastructure et des charges de travail vers Microsoft Azure. 

[Azure Migrate](migrate-services-overview.md) offre un hub central pour suivre la découverte, l’évaluation et la migration vers Azure d’applications et de charges de travail locales, ainsi que des machines virtuelles cloud privées/publiques. Le hub fournit des outils Azure Migrate pour l’évaluation et la migration, ainsi que des offres de fournisseurs de logiciels indépendants (ISV) tiers.



## <a name="appliance-overview"></a>Vue d’ensemble de l’appliance

Voici les types d'appliance Azure Migrate et leur utilisation.

**Déployé en tant que** | **Utilisé pour** | **Détails**
--- | --- |  ---
Machine virtuelle VMware | Évaluation de machine virtuelle VMware avec l'outil Évaluation d’Azure Migrate.<br/><br/> Migration sans agent d’une machine virtuelle VMware à l’aide de l’outil Migration de serveur d’Azure Migrate | Téléchargez le modèle OVA et importez-le dans vCenter Server pour créer la machine virtuelle de l’appliance.
Machine virtuelle Hyper-V | Évaluation de machine virtuelle Hyper-V avec l'outil Évaluation d’Azure Migrate. | Téléchargez le disque dur virtuel et importez-le dans Hyper-V pour créer la machine virtuelle de l’appliance.

## <a name="appliance-access"></a>Accès à l’appliance

Après avoir configuré l'appliance, vous pouvez accéder à distance à sa machine virtuelle via le port TCP 3389. Vous pouvez également accéder à distance à l'application de gestion web de l'appliance sur le port 44368 avec URL : `https://<appliance-ip-or-name>:44368`.

## <a name="appliance-license"></a>Licence de l’appliance
L’appliance est fournie avec une licence d’évaluation Windows Server 2016 qui est valide pendant 180 jours. Si la période d’évaluation est proche de l’expiration, nous vous recommandons de télécharger et de déployer une nouvelle appliance, ou d’activer la licence du système d’exploitation de la machine virtuelle de l’appliance.

## <a name="appliance-agents"></a>Agents de l’appliance
Ces agents sont installés sur l’appliance.

**Agent** | **Détails**
--- | ---
Agent de découverte | Collecte les données de configuration des machines virtuelles locales.
Agent d’évaluation | Définit le profil de l'environnement local pour collecter les données de performance des machines virtuelles.
Adaptateur de migration | Orchestre la réplication des machines virtuelles et coordonne la communication entre les machines virtuelles et Azure.
Passerelle de migration | Envoie à Azure les données répliquées des machines virtuelles.


## <a name="appliance-deployment-requirements"></a>Exigences en matière de déploiement de l’appliance

- [Passez en revue](migrate-support-matrix-vmware.md#assessment-appliance-requirements) les exigences de déploiement d'une appliance VMware et les URL auxquelles l'appliance doit accéder.
- [Passez en revue](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) les exigences de déploiement d'une appliance  Hyper-V et les URL auxquelles l'appliance doit accéder.


## <a name="collected-performance-data-vmware"></a>Données de performances VMware collectées

Voici les données de performances des machines virtuelles VMware que l’appliance collecte et envoie à Azure.

**Données** | **Compteur** | **Évaluation de l'impact**
--- | --- | ---
Utilisation du processeur | cpu.usage.average | Taille de machine virtuelle recommandée/coût
Utilisation de la mémoire | mem.usage.average | Taille de machine virtuelle recommandée/coût
Débit de lecture du disque (Mo par seconde) | virtualDisk.read.average | Calcul de la taille du disque, du coût de stockage et de la taille de la machine virtuelle
Débit d’écriture du disque (Mo par seconde) | virtualDisk.write.average | Calcul de la taille du disque, du coût de stockage et de la taille de la machine virtuelle
Opérations de lecture du disque par seconde | virtualDisk.numberReadAveraged.average | Calcul de la taille du disque, du coût de stockage et de la taille de la machine virtuelle
Opérations d’écriture du disque par seconde | virtualDisk.numberWriteAveraged.average  | Calcul de la taille du disque, du coût de stockage et de la taille de la machine virtuelle
Débit de lecture de la carte d’interface réseau (Mo par seconde) | net.received.average | Calcul de la taille de machine virtuelle
Débit d’écriture de la carte d’interface réseau (Mo par seconde) | net.transmitted.average  |Calcul de la taille de machine virtuelle


## <a name="collected-metadata-vmware"></a>Métadonnées VMware collectées

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
Nom | container.GetType().Name
Type d’objet enfant | container.ChildType
Détails de référence | container.MoRef
Détails du parent | Container.Parent
Détails du dossier par machine virtuelle | ((Folder)container).ChildEntity.Type
Détails du centre de données par machine virtuelle | ((Datacenter)container).VmFolder
Détails du centre de données par dossier hôte | ((Datacenter)container).HostFolder
Détails du cluster par hôte | ((ClusterComputeResource)container).Host
Détails de l’hôte par machine virtuelle | ((HostSystem)container).VM



## <a name="collected-performance-data-hyper-v"></a>Données de performances Hyper-V collectées

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

## <a name="collected-metadata-hyper-v"></a>Métadonnées Hyper-V collectées

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
3. **Envoyer des données** : L’appliance envoie les données collectées aux outils Évaluation de serveur Azure Migrate et Migration de serveur Azure Migrate via le port SSL 443.
    - Pour les données de performances, l’appliance collecte les données d'utilisation en temps réel.
        - Les données de performances sont collectées toutes les 20 secondes pour VMware et toutes les 30 secondes pour Hyper-V, pour chaque mesure de performance.
        - Les données collectées sont regroupées pour créer un point de données unique pendant dix minutes.
        - La valeur d'utilisation maximale est choisie parmi tous les points de données de 20/30 secondes puis envoyée à Azure pour le calcul de l'évaluation.
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

