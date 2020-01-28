---
title: Plateformes prises en charge par Azure Security Center | Microsoft Docs
description: Ce document fournit une liste des plateformes prises en charge par Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: 6ec818ff77664fcc038412f79fffc1e3e05b82f0
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294250"
---
# <a name="supported-platforms"></a>Plateformes prises en charge 

## Machines virtuelles / serveurs <a name="vm-server"></a>

Security Center prend en charge des machines virtuelles/serveurs sur différents types d’environnements hybrides :

* Azure uniquement
* Azure et en local
* Azure et autres clouds
* Azure, autres clouds et en local

Dans le cas d’un environnement Azure activé sur un abonnement Azure, Azure Security Center détecte automatiquement les ressources IaaS déployées dans l’abonnement.

> [!NOTE]
> Pour bénéficier de l’ensemble complet des fonctionnalités de sécurité, vous devez disposer du [Log Analytics Agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent), qu’Azure Security Center utilise, installé et [correctement configuré pour envoyer des données à Azure Security Center](security-center-enable-data-collection.md#manual-agent).


Les sections suivantes répertorient les systèmes d’exploitation serveur pris en charge sur lesquels peut s’exécuter le [Log Analytics Agent](../azure-monitor/platform/agents-overview.md#log-analytics-agent) qu’Azure Security Center utilise.

### Systèmes d’exploitation de serveur Windows <a name="os-windows"></a>

|Système d''exploitation|Pris en charge par Azure Security Center|Prise en charge de l’intégration avec Microsoft Defender ATP|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

Pour en savoir plus sur les fonctionnalités prises en charge pour les systèmes d’exploitation Windows, voir les [fonctionnalités de machine virtuelle/serveur prises en charge](security-center-services.md#vm-server-features).

### Systèmes d’exploitation Windows <a name="os-windows (non-server)"></a>

Azure Security Center s’intègre aux services Azure pour superviser et protéger vos machines virtuelles Windows.

### Systèmes d’exploitation Linux <a name="os-linux"></a>

64 bits

* CentOS 6 et 7
* Amazon Linux 2017.09
* Oracle Linux 6 et Oracle Linux 7
* Red Hat Enterprise Linux Server 6 et 7
* Debian GNU/Linux 8 et 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS et 18.04 LTS
* SUSE Linux Enterprise Server 12

32 bits
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 et 9
* Ubuntu Linux 14.04 LTS et 16.04 LTS

> [!NOTE]
> Étant donné que la liste des systèmes d’exploitation Linux pris en charge change constamment, si vous préférez, cliquez [ici](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) pour voir la liste actualisée des versions prises en charge, au cas où des modifications aient été apportées depuis la dernière publication de cette rubrique.

Pour en savoir plus sur les fonctionnalités prises en charge pour les systèmes d’exploitation Linux, voir les [fonctionnalités de machine virtuelle/serveur prises en charge](security-center-services.md#vm-server-features).

### Services de machine virtuelle managés <a name="virtual-machine"></a>

Des machines virtuelles sont également créées dans un abonnement client dans le cadre de certains services managés Azure, tels qu’Azure Kubernetes (AKS), Azure Databricks et bien plus encore. Ces machines virtuelles sont également découvertes par Azure Security Center, et le Log Analytics Agent peut être installé et configuré en fonction des [systèmes d’exploitation Windows/Linux](#os-windows) pris en charge répertoriés ci-dessus.

### Services cloud <a name="cloud-services"></a>

Les machines virtuelles s’exécutant dans un service cloud sont également prises en charge. Seuls les rôles de travail et web des services cloud en cours d’exécution dans des emplacements de production sont surveillés. Pour en savoir plus sur les services cloud, consultez [Vue d’ensemble d’Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).

## Services PaaS <a name="paas-services"></a>

Azure Security Center prend en charge les ressources Azure PaaS suivantes :

* SQL
* PostGreSQL
* MySQL
* CosmosDB
* Compte de stockage
* App Service
* Fonction
* Service cloud
* Réseau virtuel
* Subnet
* Carte d’interface réseau
* Groupe de sécurité réseau
* Compte Batch
* Compte Service Fabric
* Compte Automation
* Équilibrage de charge
* Recherche
* Espace de noms Service Bus
* Stream Analytics
* Espace de noms du hub d’événements
* Logic Apps
* Redis
* Data Lake Analytics
* Data Lake Store
* Coffre de clés

Pour en savoir plus sur les fonctionnalités prises en charge pour la liste ci-dessus de ressources PaaS, voir [fonctionnalités prises en charge par les services PaaS](security-center-services.md#paas-services).

La protection des machines virtuelles résidant dans Azure Stack est également prise en charge. Pour plus d’informations sur l’intégration de Security Center avec Azure Stack, consultez [Intégrer vos machines virtuelles Azure Stack à Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Security Center collecte les données et l'agent Log Analytics Agent](security-center-enable-data-collection.md).
- Découvrez comment [Security Center gère et protège les données](security-center-data-security.md).
- [Découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception](security-center-planning-and-operations-guide.md).
- Découvrez les [fonctionnalités disponibles pour les différents environnements cloud](security-center-services.md).
- Apprenez-en davantage sur la [détection des menaces pour les machines virtuelles et les serveurs dans Azure Security Center](security-center-alerts-iaas.md).
- Découvrez [les réponses aux questions le plus souvent posées sur l’utilisation d’Azure Security Center](security-center-faq.md).
- Accédez à des [billets de blog sur la sécurité et la conformité Azure](https://blogs.msdn.com/b/azuresecurity/).
