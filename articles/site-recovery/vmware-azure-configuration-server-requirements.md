---
title: Exigences du serveur de configuration pour la récupération d’urgence de VMware sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article décrit le support et les exigences lors du déploiement du serveur de configuration pour la récupération d’urgence de VMware sur Azure avec Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: raynew
ms.openlocfilehash: 506f5102d38191e20e18e395f3a59ac12d951ab7
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850616"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Exigences du serveur de configuration pour la récupération d’urgence de VMware sur Azure

Vous devez déployer un serveur de configuration local quand vous utilisez [Azure Site Recovery](site-recovery-overview.md) pour la récupération d’urgence de machines virtuelles VMware et de serveurs physiques sur Azure.

- Le serveur de configuration coordonne la communication entre les machines locales VMware et Azure. Il gère également la réplication des données.
- Le serveur de configuration coordonne la communication entre les machines locales VMware et Azure. Il gère également la réplication des données.
- [Découvrez-en plus](vmware-azure-architecture.md) sur les composants et les processus du serveur de configuration.

## <a name="configuration-server-deployment"></a>Déploiement du serveur de configuration

Pour la reprise d’activité de machines virtuelles VMware sur Azure, vous devez déployer un serveur de configuration en tant que machine VMware.

- Site Recovery fournit un modèle OVA que vous téléchargez à partir du portail Azure et importez dans vCenter Server pour configurer la machine virtuelle du serveur de configuration.
- Lorsque vous déployez le serveur de configuration à l’aide du modèle OVA, la machine virtuelle répond automatiquement aux exigences énumérées dans cet article.
- Nous vous recommandons fortement de configurer le serveur de configuration à l’aide du modèle OVA. Toutefois, si vous configurez une reprise d’activité pour les machines virtuelles VMware mais que vous ne pouvez pas utiliser le modèle OVA, vous pouvez déployer le serveur de configuration en utilisant [ces instructions](physical-azure-set-up-source.md).
- Si vous déployez le serveur de configuration pour la reprise d’activité de machines physiques locales sur Azure, suivez les instructions de [cet article](physical-azure-set-up-source.md). 


## <a name="hardware-requirements"></a>Configuration matérielle requise

**Composant** | **Prérequis** 
--- | ---
Cœurs d’unité centrale | 8 
RAM | 16 Go
Nombre de disques | 3, y compris le disque du système d’exploitation, le disque de cache du serveur de processus et le lecteur de conservation pour la restauration automatique 
Espace disque disponible (cache du serveur de traitement) | 600 Go
Espace disque disponible (disque de rétention) | 600 Go

## <a name="software-requirements"></a>Configuration logicielle requise

**Composant** | **Prérequis** 
--- | ---
Système d’exploitation | Windows Server 2012 R2 <br> Windows Server 2016
Paramètres régionaux du système d’exploitation | Anglais (en-us)
Rôles Windows Server | N’activez pas ces rôles : <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Stratégies de groupe | N’activez pas ces stratégies de groupe : <br> - Empêcher l’accès à l’invite de commandes <br> - Empêcher l’accès aux outils de modification du Registre <br> - Logique de confiance pour les pièces jointes <br> - Activer l’exécution des scripts <br> [En savoir plus](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Aucun site web par défaut préexistant <br> - Aucune application/aucun site web préexistants ne doivent écouter le port 443 <br>- Activer [l’authentification anonyme](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Activer le paramètre [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 

## <a name="network-requirements"></a>Configuration requise pour le réseau

**Composant** | **Prérequis** 
--- | --- 
Type d’adresse IP | statique 
Accès à Internet | Le serveur doit également accéder à ces URL (directement ou par le biais d’un proxy) : <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> - https:\//management.azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF doit également accéder aux URL suivantes : <br> - https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> - https:\//login.live.com  <br> - https:\//auth.gfx.ms <br> - https:\//graph.windows.net <br> - https:\//login.windows.net <br> - https:\//www.live.com <br> - https:\//www.microsoft.com <br> - https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Ports | 443 (Orchestration du canal de contrôle)<br>9443 (Transport de données) 
Type de carte réseau | VMXNET3 (si le serveur de configuration est une machine virtuelle VMware)

## <a name="required-software"></a>Logiciels requis

**Composant** | **Prérequis** 
--- | ---
VMware vSphere PowerCLI | [PowerCLI version 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) doit être installé si le serveur de configuration est en cours d’exécution sur une machine virtuelle VMware.
MYSQL | MySQL doit être installé. Vous pouvez l’installer manuellement ou laisser Site Recovery le faire.

## <a name="sizing-and-capacity-requirements"></a>Besoins en capacité et dimensionnement

Le tableau suivant récapitule les besoins en capacité du serveur de configuration. Si vous répliquez plusieurs machines virtuelles VMware, lisez les [considérations sur la planification de la capacité](site-recovery-plan-capacity-vmware.md) et exécutez l’outil [Planificateur de déploiement Azure Site Recovery](site-recovery-deployment-planner.md) pour la réplication VMware.read 

**Composant** | **Prérequis** 
--- | ---
**UC** | **Mémoire** | **Disque cache** | **Taux de modification des données** | **Machines répliquées**
--- | --- | --- | --- | ---
8 processeurs virtuels<br/><br/> 2 sockets * 4 cœurs \@ 2,5 GHz | 16 Go | 300 Go | 500 Go ou moins | Moins de 100 machines
12 processeurs virtuels<br/><br/> 2 sockets * 6 cœurs \@ 2,5 GHz | 18 Go | 600 Go | 500 Go à 1 To | 100 à 150 machines
16 processeurs virtuels<br/><br/> 2 sockets * 8 cœurs \@ 2,5 GHz | 32 Go | 1 To | 1 à 2 To | Entre 150 et 200 machines



## <a name="next-steps"></a>Étapes suivantes
Configurez la récupération d’urgence des [machines virtuelles VMware](vmware-azure-tutorial.md) vers Azure.
