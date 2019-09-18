---
title: Prendre en charge la matrice pour effectuer une récupération d’urgence de machines virtuelles Hyper-V locales vers Azure
description: Résume les composants pris en charge et les exigences pour la récupération d’urgence de machines virtuelles Hyper-V vers Azure avec Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: raynew
ms.openlocfilehash: 5d7ac7cf00ee59a06a914d312fd58de00515a0b4
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873342"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Prendre en charge la matrice pour effectuer une récupération d’urgence de machines virtuelles Hyper-V locales vers Azure


Cet article résume les composants pris en charge ainsi que les paramètres concernant la récupération d’urgence de machines virtuelles Hyper-V locales vers Azure, à l’aide de [Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Scénarios pris en charge

**Scénario** | **Détails**
--- | ---
Hyper-V avec Virtual Machine Manager | Vous pouvez effectuer la récupération d’urgence vers Azure pour les machines virtuelles s’exécutant sur des hôtes Hyper-V managés dans l’infrastructure System Center Virtual Machine Manager.<br/><br/> Vous pouvez déployer ce scénario dans le portail Azure ou à l’aide de PowerShell.<br/><br/> Lorsque les hôtes Hyper-V sont managés par Virtual Machine Manager, vous pouvez également effectuer la récupération d’urgence vers un site secondaire local. Pour plus d’informations sur ce scénario, consultez [ce didacticiel](hyper-v-vmm-disaster-recovery.md).
Hyper-V sans Virtual Machine Manager | Vous pouvez effectuer la récupération d’urgence vers Azure pour les machines virtuelles s’exécutant sur les hôtes Hyper-V qui ne sont pas gérés par Virtual Machine Manager.<br/><br/> Vous pouvez déployer ce scénario dans le portail Azure ou à l’aide de PowerShell.


## <a name="on-premises-servers"></a>Serveurs locaux

**Serveur** | **Configuration requise** | **Détails**
--- | --- | ---
Hyper-V (exécuté sans Virtual Machine Manager) |  Windows Server 2019, Windows Server 2016 (installation de Server Core incluse), Windows Server 2012 R2 avec les dernières mises à jour | Si vous avez déjà configuré Windows Server 2012 R2 avec/ou SCVMM 2012 R2 avec Azure Site Recovery et que vous prévoyez de mettre à niveau le système d’exploitation, suivez les instructions de la [documentation.](upgrade-2012R2-to-2016.md) 
Hyper-V (exécuté avec Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Si Virtual Machine Manager est utilisé, les hôtes Windows Server 2019 doivent être managés dans Virtual Machine Manager 2019. De même, les hôtes Windows Server 2016 doivent être gérés dans Virtual Machine Manager 2016.<br/><br/>


## <a name="replicated-vms"></a>Machines virtuelles répliquées


Le tableau suivant récapitule la prise en charge des machines virtuelles. Site Recovery prend en charge les charges de travail s’exécutant sur un système d’exploitation pris en charge.

 **Composant** | **Détails**
--- | ---
Configuration des machines virtuelles | Les machines virtuelles qui répliquent vers Azure doivent répondre aux [conditions requises par Azure](#azure-vm-requirements).
Système d’exploitation invité | N’importe quel système d’exploitation invité [pris en charge par Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases).<br/><br/> Windows Server 2016 Nano Server n’est pas pris en charge.


## <a name="vmdisk-management"></a>Gestion des machines virtuelles/disques

**Action** | **Détails**
--- | ---
Redimensionner le disque sur la machine virtuelle Hyper-V répliquée | Non pris en charge. Désactivez la réplication, effectuez la modification, puis réactivez la réplication pour la machine virtuelle.
Ajouter un disque sur la machine virtuelle Hyper-V répliquée | Non pris en charge. Désactivez la réplication, effectuez la modification, puis réactivez la réplication pour la machine virtuelle.

## <a name="hyper-v-network-configuration"></a>Configuration réseau Hyper-V

**Composant** | **Hyper-V avec Virtual Machine Manager** | **Hyper-V sans Virtual Machine Manager**
--- | --- | ---
Réseau hôte : Association de cartes réseau | OUI | OUI
Réseau hôte : VLAN | OUI | OUI
Réseau hôte : IPv4 | OUI | OUI
Réseau hôte : IPv6 | Non | Non
Réseau machines virtuelles invitées : Association de cartes réseau | Non | Non
Réseau machines virtuelles invitées : IPv4 | OUI | OUI
Réseau machines virtuelles invitées : IPv6 | Non | OUI
Réseau machines virtuelles invitées : Adresse IP statique (Windows) | OUI | OUI
Réseau machines virtuelles invitées : Adresse IP statique (Linux) | Non | Non
Réseau machines virtuelles invitées : Plusieurs cartes réseau | OUI | OUI



## <a name="azure-vm-network-configuration-after-failover"></a>Configuration de réseau des machines virtuelles Azure (après basculement)

**Composant** | **Hyper-V avec Virtual Machine Manager** | **Hyper-V sans Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | OUI | OUI
ILB | OUI | OUI
ELB | OUI | OUI
Azure Traffic Manager | OUI | OUI
Plusieurs cartes réseau | OUI | OUI
Adresse IP réservée | OUI | OUI
IPv4 | OUI | OUI
Conserver l’adresse IP source | OUI | OUI
Points de terminaison de service de réseau virtuel Azure<br/> (sans pare-feu de stockage Azure) | OUI | OUI
Mise en réseau accélérée | Non | Non


## <a name="hyper-v-host-storage"></a>Stockage hôte Hyper-V

**Stockage** | **Hyper-V avec Virtual Machine Manager** | **Hyper-V sans Virtual Machine Manager**
--- | --- | --- 
NFS | N/D | N/D
SMB 3.0 | OUI | OUI
SAN (ISCSI) | OUI | OUI
Chemins d’accès multiples (MPIO). Testé avec :<br></br> Module DSM Microsoft, EMC PowerPath 5.7 SP4<br/><br/> Module DSM EMC PowerPath pour CLARiiON | OUI | OUI

## <a name="hyper-v-vm-guest-storage"></a>Stockage invité de machines virtuelles Hyper-V

**Stockage** | **Hyper-V avec Virtual Machine Manager** | **Hyper-V sans Virtual Machine Manager**
--- | --- | ---
VMDK | N/D | N/D
VHD/VHDX | OUI | OUI
Machine virtuelle de 2e génération | OUI | OUI
EFI/UEFI| OUI | OUI
Disque de cluster partagé | Non | Non
Disque chiffré | Non | Non
NFS | N/D | N/D
SMB 3.0 | Non | Non
RDM | N/D | N/D
Disque > 1 To | Oui, jusqu’à 4,095 Go | Oui, jusqu’à 4,095 Go
Disque : secteur logique et physique de 4 K | Non pris en charge : Gen 1/Gen 2 | Non pris en charge : Gen 1/Gen 2
Disque : secteur logique de 4 K et physique de 512 octets | OUI |  OUI
Gestion des volumes logiques (LVM). LVM est pris en charge uniquement sur des disques de données. Azure ne fournit qu’un seul disque de système d’exploitation. | OUI | OUI
Volume avec disque à bandes > 1 To | OUI | OUI
Espaces de stockage | Non | Non
Ajout/suppression de disque à chaud | Non | Non
Exclure le disque | OUI | OUI
Chemins d’accès multiples (MPIO) | OUI | OUI

## <a name="azure-storage"></a>Stockage Azure

**Composant** | **Hyper-V avec Virtual Machine Manager** | **Hyper-V sans Virtual Machine Manager**
--- | --- | ---
Stockage localement redondant | OUI | OUI
Stockage géo-redondant | OUI | OUI
Stockage géo-redondant avec accès en lecture | OUI | OUI
Stockage froid | Non | Non
Stockage chaud| Non | Non
Objets blob de blocs | Non | Non
Chiffrement au repos (SSE)| OUI | OUI
Stockage Premium | OUI | OUI
Service Import/Export | Non | Non
Pare-feu et réseaux virtuels de stockage Azure configurés dans le compte de stockage de cache/de stockage cible (utilisé pour stocker les données de réplication) | Non | Non
Modification du compte de stockage | Non. Le compte de stockage Azure cible ne peut pas être modifié une fois la réplication activée. Pour le modifier, désactivez puis réactivez la récupération d'urgence. | Non


## <a name="azure-compute-features"></a>Fonctionnalités de Calcul Azure

**Fonctionnalité** | **Hyper-V avec Virtual Machine Manager** | **Hyper-V sans Virtual Machine Manager**
--- | --- | ---
Groupes à haute disponibilité | OUI | OUI
HUB | OUI | OUI  
Disques managés | Oui, pour le basculement.<br/><br/> La restauration automatique des disques managés n’est pas prise en charge. | Oui, pour le basculement.<br/><br/> La restauration automatique des disques managés n’est pas prise en charge.

## <a name="azure-vm-requirements"></a>Exigences des machines virtuelles Azure

Les machines virtuelles locales que vous répliquez vers Azure doivent respecter les exigences des machines virtuelles Azure décrites dans ce tableau.

**Composant** | **Configuration requise** | **Détails**
--- | --- | ---
Système d’exploitation invité | Site Recovery fonctionne sur tous les systèmes d’exploitation [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Architecture du système d’exploitation invité | 32 bits (Windows Server 2008)/64 bits | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Taille du disque du système d’exploitation | Jusqu’à 2 048 Go pour les machines virtuelles de 1ère génération.<br/><br/> Jusqu’à 300 Go pour les machines virtuelles de 2e génération.  | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Nombre de disques du système d’exploitation | 1 | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Nombre de disques de données | 16 ou moins  | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Taille du disque dur virtuel de données | Jusqu’à 4 095 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Adaptateurs réseau | Prise en charge de plusieurs adaptateurs réseau. |
Disque dur virtuel partagé | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Disque FC | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Format de disque dur | Disque dur virtuel (VHD) <br/><br/> VHDX | Site Recovery convertit automatiquement VHDX en VHD quand vous effectuez un basculement vers Azure. Lorsque vous procédez à une restauration automatique vers un site local, les machines virtuelles continuent d’utiliser le format VHDX.
BitLocker | Non pris en charge | BitLocker doit être désactivé avant d’activer la réplication pour une machine virtuelle.
nom de la machine virtuelle | Entre 1 et 63 caractères. Uniquement des lettres, des chiffres et des traits d’union. Le nom de la machine virtuelle doit commencer et se terminer par une lettre ou un chiffre. | Mettez à jour la valeur dans les propriétés de machine virtuelle de Site Recovery.
Type de machine virtuelle | Génération 1<br/><br/> Génération 2--Windows | Les machines virtuelles de 2e génération avec un type de disque de système d’exploitation de base, qui inclut un ou deux volumes de données au format VHDX et un espace disque inférieur à 300 Go sont prises en charge.<br></br>Les machines virtuelles Linux de 2e génération ne sont pas prises en charge. [Plus d’informations](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="recovery-services-vault-actions"></a>Actions de coffre Recovery Services

**Action** |  **Hyper-V avec VMM** | **Hyper-V sans VMM**
--- | --- | ---
Déplacer le coffre entre plusieurs groupes de ressources<br/><br/> Au sein et entre des abonnements | Non | Non
Déplacer le stockage, les réseaux, les machines virtuelles Azure entre des groupes de ressources<br/><br/> Au sein et entre des abonnements | Non | Non

> [!NOTE]
> Dans le cadre de la réplication de machines virtuelles Hyper-V locales sur Azure, vous ne pouvez répliquer que sur un seul locataire AD à partir d’un environnement spécifique (site Hyper-V ou Hyper-V avec VMM selon le cas).


## <a name="provider-and-agent"></a>Fournisseur et agent

Pour vous assurer que votre déploiement est compatible avec les paramètres de cet article, vérifiez que vous utilisez le fournisseur et les versions d’agent les plus récents.

**Name** | **Description** | **Détails**
--- | --- | --- 
Fournisseur Azure Site Recovery | Coordonne les communications entre les serveurs locaux et Azure <br/><br/> Hyper-V avec Virtual Machine Manager : Installé sur les serveurs Virtual Machine Manager<br/><br/> Hyper-V sans Virtual Machine Manager : Installé sur les hôtes Hyper-V| Version la plus récente : 5.1.2700.1 (disponible dans le portail Azure)<br/><br/> [Fonctionnalités et correctifs récents](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Agent Microsoft Azure Recovery Services | Coordonne la réplication entre les machines virtuelles Hyper-V et Azure<br/><br/> Installé sur des serveurs Hyper-V locaux (avec ou sans serveur Virtual Machine Manager) | Dernier agent disponible sur le portail






## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [préparer Azure](tutorial-prepare-azure.md) à la récupération d’urgence de machines virtuelles Hyper-V locales.
