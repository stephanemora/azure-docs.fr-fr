---
title: Tableau de prise en charge Azure Migrate pour l’évaluation et la migration Hyper-V
description: Résume les paramètres et les limites de l'évaluation et de la migration Hyper-V à l'aide du service Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: 507ca6daa30a19b73848d6d3cf253390baf496af
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372462"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Tableau de prise en charge pour l’évaluation et la migration Hyper-V

Vous pouvez utiliser le [service Azure Migrate](migrate-overview.md) pour évaluer et migrer des machines vers le cloud Microsoft Azure. Cet article résume les paramètres et les limites de la prise en charge pour l'évaluation et la migration de machines virtuelles Hyper-V en local.



## <a name="hyper-v-scenarios"></a>Scénarios Hyper-V

Le tableau récapitule les scénarios pris en charge pour les machines virtuelles Hyper-V.

**Déploiement** | **Détails***
--- | ---
**Évaluer des machines virtuelles Hyper-V en local** | [Configurez](tutorial-prepare-hyper-v.md) votre première évaluation.<br/><br/> [Exécutez](scale-hyper-v-assessment.md) une évaluation à grande échelle.
**Migrer des machines virtuelles Hyper-V vers Azure** | [Testez](tutorial-migrate-hyper-v.md) une migration vers Azure.



## <a name="azure-migrate-projects"></a>Projets Azure Migrate

**Support** | **Détails**
--- | ---
Autorisations Azure | Vous avez besoin d’autorisations Contributeur ou Propriétaire dans l’abonnement pour créer un projet Azure Migrate.
Machines virtuelles Hyper-V | Évaluez jusqu’à 10 000 machines virtuelles Hyper-V au sein d’un même projet.

Un projet peut inclure à la fois des machines virtuelles VMware et des machines virtuelles Hyper-V, jusqu’aux limites de l’évaluation.

**Zone géographique :** Un projet Azure Migrate peut être créé dans un certain nombre de zones géographiques. Même si vous ne pouvez créer des projets que dans ces zones, vous pouvez néanmoins toujours évaluer ou migrer vos machines pour d’autres emplacements cibles. La zone géographique du projet est uniquement utilisée pour stocker les métadonnées détectées.


 **Zone géographique** | **Emplacement de stockage des métadonnées**
 --- | ---
 Azure Government | Gouvernement américain - Virginie
 Asie-Pacifique | Asie Sud-Est ou Asie Est
 Europe | Europe Sud ou Europe Ouest
 Royaume-Uni | Royaume-Uni Sud ou Royaume-Uni Ouest
 États-Unis | USA Centre ou USA Ouest 2


 > [!NOTE]
 > La prise en charge d’Azure Government est actuellement disponible pour l’[ancienne version](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) d’Azure Migrate uniquement.


## <a name="assessment-hyper-v-host-requirements"></a>Évaluation - Exigences de l'hôte Hyper-V

| **Support**                | **Détails**               
| :-------------------       | :------------------- |
| **Déploiement de l’hôte**       | L'hôte Hyper-V peut être autonome ou déployé dans un cluster. |
| **autorisations**           | Vous avez besoin des droits d'administrateur sur l'hôte Hyper-V. |
| **Système d'exploitation hôte** | Windows Server 2016 ou Windows Server 2012 R2.<br/> Vous ne pouvez pas évaluer des machines virtuelles situées sur des hôtes Hyper-V exécutant Windows Server 2019. |
| **Communication à distance PowerShell**   | Doit être activée sur chaque hôte. |
| **Réplica Hyper-V**       | Si vous utilisez Hyper-V Replica (ou si vous avez plusieurs machines virtuelles avec les mêmes identificateurs de machines virtuelles) et que vous découvrez les machines virtuelles originales et répliquées à l'aide d'Azure Migrate, l'évaluation générée par Azure Migrate pourrait ne pas être exacte. |


## <a name="assessment-hyper-v-vm-requirements"></a>Évaluation - Exigences de la machine virtuelle Hyper-V

| **Support**                  | **Détails**               
| :----------------------------- | :------------------- |
| **Système d’exploitation** | Tous les systèmes d'exploitation [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) et [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) pris en charge par Azure. |
| **autorisations**           | Vous avez besoin des droits d'administrateur sur chaque machine virtuelle Hyper-V à évaluer. |
| **Services d’intégration**       | Les [services d'intégration Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) doivent fonctionner sur les machines virtuelles que vous évaluez afin de capturer les informations du système d'exploitation. |
| **Modifications requises pour Azure** | Certaines machines virtuelles peuvent nécessiter des modifications pour fonctionner dans Azure. Azure Migrate effectue automatiquement ces modifications pour les systèmes d’exploitation suivants :<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7,8<br/><br/> Pour les autres systèmes d’exploitation, vous devez effectuer les ajustements manuellement avant la migration. Les articles pertinents contiennent des instructions sur la façon de procéder. |
| **Démarrage Linux**                 | Si /boot se trouve sur une partition dédiée, il doit résider sur le disque du système d’exploitation et ne pas être réparti sur plusieurs disques.<br/> Si /boot fait partie de la partition racine (/), la partition « / » doit se trouver sur le disque du système d’exploitation et ne pas couvrir d’autres disques. |
| **Démarrage UEFI**                  | Les machines virtuelles avec démarrage UEFI ne sont pas prises en charge pour la migration. |
| **Disques/volumes chiffrés**    | Les machines virtuelles avec des disques/volumes chiffrés ne sont pas prises en charge pour la migration. |
| **RDM/disques directs**      | Si les machines virtuelles ont des disques RDM ou directs, ces disques ne seront pas répliqués dans Azure. |
| **NFS**                        | Les volumes NFS montés en tant que volumes sur les machines virtuelles ne sont pas répliqués. |
| **Disque cible**                | Les évaluations Azure Migrate recommandent de migrer vers des machines virtuelles Azure avec disques managés uniquement. |


## <a name="assessment-appliance-requirements"></a>Évaluation - exigences de l’appliance

Pour l’évaluation, Azure Migrate exécute une appliance légère pour découvrir les machines virtuelles Hyper-V et envoyer les métadonnées et les données de performances de la machine virtuelle à Azure Migrate. L'appliance fonctionne sur une machine virtuelle Hyper-V que vous configurez en utilisant un disque dur virtuel (VHD) Hyper-V compressé téléchargé depuis le portail Azure. Le tableau suivant récapitule les exigences de l’appliance.

| **Support**                | **Détails**               
| :-------------------       | :------------------- |
| **Projet Azure Migrate**  |  Une appliance peut être associée à un seul projet.<br/> Vous pouvez découvrir jusqu'à 5 000 machines virtuelles Hyper-V avec une seule appliance.
| **Limitations Hyper-V**    |  Vous déployez l’appliance en tant que machine virtuelle Hyper-V.<br/> La machine virtuelle de l’appliance fournie est la version 5.0 de la machine virtuelle Hyper-V.<br/> L’hôte de la machine virtuelle doit exécuter Windows Server 2012 R2 ou une version ultérieure.<br/> Il a besoin de suffisamment d'espace pour allouer 16 Go de RAM, 4 processeurs virtuels et 1 commutateur externe pour la machine virtuelle de l'appliance.<br/> L'appliance nécessite une adresse IP statique ou dynamique et un accès Internet.
| **Appliance Hyper-V**      |  L’appliance est configurée en tant que machine virtuelle Hyper-V.<br/> Le disque dur virtuel (VHD) fourni en téléchargement est une machine virtuelle Hyper-V version 5.0.
| **Hôte**                   | L'hôte de la machine virtuelle exécutant la machine virtuelle de l’appliance doit exécuter Windows Server 2012 R2 ou une version ultérieure.<br/> Il a besoin de suffisamment d'espace pour allouer 16 Go de RAM, 4 processeurs virtuels et un commutateur externe pour la machine virtuelle de l'appliance.<br/> L'appliance nécessite une adresse IP statique ou dynamique et un accès Internet. |
| **Prise en charge de la migration**      | Pour commencer à répliquer des machines, la version du service Migration Gateway sur l'appliance doit être 1.18.7141.12919 ou ultérieure. Connectez-vous à l'application web de l'appliance pour vérifier la version. |

## <a name="assessment-appliance-url-access"></a>Évaluation - accès à l’URL de l’appliance

Pour évaluer des machines virtuelles, l'appliance Azure Migrate a besoin d'une connectivité Internet.

- Lorsque vous déployez l'appliance, Azure Migrate effectue un contrôle de connectivité vers les URL résumées dans le tableau ci-dessous.
- Si vous utilisez un firewall.proxy basé sur URL, autorisez l'accès aux URL du tableau, en vérifiant que le proxy résout tous les enregistrements CNAME reçus lors de la recherche des URL.
- Si vous disposez d'un proxy d'interception, vous devrez peut-être importer le certificat de serveur depuis le serveur proxy vers l'appliance.


**URL** | **Détails**  
--- | ---
*. portal.azure.com | Accès au portail Azure
\* .windows.net | Connectez-vous à votre abonnement Azure :
*.microsoftonline.com | Création d'applications Azure Active Directory pour les communications de l’appliance au service.
management.azure.com | Création d'applications Azure Active Directory pour les communications de l’appliance au service.
dc.services.visualstudio.com | Enregistrement et surveillance
*.vault.azure.net | Gérez les secrets dans Azure Key Vault lorsque vous communiquez entre l'appliance et le service.


## <a name="assessment-port-requirements"></a>Évaluation - exigences du port

Le tableau suivant résume les exigences du port pour l’évaluation.

**Appareil** | **Connection**
--- | ---
**Appliance** | Connexions entrantes sur le port TCP 3389 pour permettre des connexions à distance vers l'appliance.<br/> Connexions entrantes sur le port 44368 pour accéder à distance à l'application de gestion de l'appliance via l'URL : https://<appliance-ip-or-name>:44368<br/> Connexions sortantes sur le port 443 pour envoyer des métadonnées de découverte et de performance à Azure Migrate.
**Hôte/cluster Hyper-V** | Connexions entrantes sur les ports WinRM 5985 (HTTP) et 5986 (HTTPS) pour extraire les métadonnées de configuration et de performance des machines virtuelles Hyper-V en utilisant une session Common Information Model (CIM).

## <a name="migration-hyper-v-host-requirements"></a>Migration - exigences de l’hôte Hyper-V

| **Support**                | **Détails**               
| :-------------------       | :------------------- |
| **Déploiement de l’hôte**       | L'hôte Hyper-V peut être autonome ou déployé dans un cluster. |
| **autorisations**           | Vous avez besoin des droits d'administrateur sur l'hôte Hyper-V. |
| **Système d'exploitation hôte** | Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Migration - exigences de la machine virtuelle Hyper-V

| **Support**                  | **Détails**               
| :----------------------------- | :------------------- |
| **Système d’exploitation** | Tous les systèmes d'exploitation [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) et [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) pris en charge par Azure. |
| **autorisations**           | Vous avez besoin des droits d'administrateur sur chaque machine virtuelle Hyper-V à évaluer. |
| **Services d’intégration**       | Les [services d'intégration Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) doivent fonctionner sur les machines virtuelles que vous évaluez afin de capturer les informations du système d'exploitation. |
| **Modifications requises pour Azure** | Certaines machines virtuelles peuvent nécessiter des modifications pour fonctionner dans Azure. Azure Migrate effectue automatiquement ces modifications pour les systèmes d’exploitation suivants :<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7,8<br/><br/> Pour les autres systèmes d’exploitation, vous devez effectuer les ajustements manuellement avant la migration. Les articles pertinents contiennent des instructions sur la façon de procéder. |
| **Démarrage Linux**                 | Si /boot se trouve sur une partition dédiée, il doit résider sur le disque du système d’exploitation et ne pas être réparti sur plusieurs disques.<br/> Si /boot fait partie de la partition racine (/), la partition « / » doit se trouver sur le disque du système d’exploitation et ne pas couvrir d’autres disques. |
| **Démarrage UEFI**                  | Les machines virtuelles avec démarrage UEFI ne sont pas prises en charge pour la migration. |
| **Disques/volumes chiffrés**    | Les machines virtuelles avec des disques/volumes chiffrés ne sont pas prises en charge pour la migration. |
| **RDM/disques directs**      | Si les machines virtuelles ont des disques RDM ou directs, ces disques ne seront pas répliqués dans Azure. |
| **NFS**                        | Les volumes NFS montés en tant que volumes sur les machines virtuelles ne sont pas répliqués. |
| **Disque cible**                | Vous pouvez uniquement migrer des machines virtuelles Azure avec des disques managés. |




## <a name="migration-hyper-v-host-url-access"></a>Migration - Accès à l’URL de l’hôte Hyper-V

Le tableau suivant résume les conditions d'accès aux URL pour les hôtes Hyper-V.

**URL** | **Détails**  
--- | ---
login.microsoftonline.com | Contrôle d’accès et gestion des identités avec Active Directory.
*.backup.windowsazure.com | Transfert des données de réplication et coordination.
*.hypervrecoverymanager.windowsazure.com | Connectez-vous aux URL du service Azure Migrate.
*.blob.core.windows.net | Chargez des données dans les comptes de stockage.
dc.services.visualstudio.com | Chargez les journaux d’applications utilisés pour la surveillance interne.
time.windows.com | Vérifie la synchronisation horaire entre l’horloge système et l’heure globale.

## <a name="migration-port-access"></a>Migration - accès au port

Le tableau suivant résume les exigences de port sur les hôtes et les machines virtuelles Hyper-V pour la migration des machines virtuelles.

**Appareil** | **Connection**
--- | ---
Hôtes/machines virtuelles Hyper-V | Connexions sortantes sur le port HTTPS 443 pour envoyer les données de réplication des machines virtuelles à Azure Migrate.


## <a name="migration-vm-disk-support"></a>Migration - prise en charge des disques de machines virtuelles

**Support** | **Détails**
--- | ---
Disques migrés | Les machines virtuelles peuvent uniquement être migrées vers des disques managés (disques durs standard, disques SSD Premium) dans Azure.


## <a name="next-steps"></a>Étapes suivantes

[Se préparer à l'évaluation d’une machine virtuelle Hyper-V](tutorial-prepare-hyper-v.md) pour la migration.
