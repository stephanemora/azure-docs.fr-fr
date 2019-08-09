---
title: Fonctionnalités et plateformes prises en charge par Azure Security Center | Microsoft Docs
description: Ce document fournit une liste des fonctionnalités et des plateformes prises en charge par Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/03/2019
ms.author: v-mohabe
ms.openlocfilehash: 02d993d760338356fa29ee58a03215e14d6583f1
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295598"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Plateformes et fonctionnalités prises en charge par Azure Security Center

La supervision de l’état de la sécurité et des recommandations sont disponibles pour les machines virtuelles créées à l’aide des modèles de déploiement classique et Resource Manager, ainsi que des ordinateurs.

> [!NOTE]
> En savoir plus sur les [modèles de déploiement de type Classique et Resource Manager](../azure-classic-rm.md) pour les ressources Azure.
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Plateformes prenant en charge l’agent de collecte de données 

Cette section liste les plateformes sur lesquelles l’agent Azure Security Center peut s’exécuter et à partir desquelles il peut collecter des données.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Plateformes prises en charge pour les ordinateurs et machines virtuelles Windows
Les systèmes d’exploitation Windows suivants sont pris en charge :

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> L’intégration à Windows Defender ATP prend en charge seulement Windows Server 2012 R2 et Windows Server 2016.

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Plateformes prises en charge pour les ordinateurs et machines virtuelles Linux

Les systèmes d’exploitation Linux suivants sont pris en charge :

> [!NOTE]
> Étant donné que la liste des systèmes d’exploitation Linux pris en charge change constamment, si vous préférez, cliquez [ici](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) pour voir la liste actualisée des versions prises en charge, au cas où des modifications aient été apportées depuis la dernière publication de cette rubrique.

64 bits
* CentOS 6 et 7
* Amazon Linux 2017.09
* Oracle Linux 6 et 7
* Red Hat Enterprise Linux Server 6 et 7
* Debian GNU/Linux 8 et 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS et 18.04 LTS
* SUSE Linux Enterprise Server 12

32 bits
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 et 9
* Ubuntu Linux 14.04 LTS et 16.04 LTS

## <a name="vms-and-cloud-services"></a>Machines virtuelles et services cloud
Les machines virtuelles en cours d’exécution dans un service cloud sont également prises en charge. Seuls les rôles de travail et web des services cloud en cours d’exécution dans des emplacements de production sont surveillés. Pour en savoir plus sur les services cloud, consultez [Vue d’ensemble d’Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Fonctionnalités IaaS prises en charge

> [!div class="mx-tableFixed"]
> 

|Serveur|Windows|||Linux|||Tarifs|
|----|----|----|----|----|----|----|----|
|**Environment**|**Microsoft Azure**||**Non-Azure**|**Microsoft Azure**||**Non-Azure**||
||**Machine virtuelle**|**Groupe de machines virtuelles identiques**||**Machine virtuelle**|**Groupe de machines virtuelles identiques**|
|Alertes de détection des menaces VMBA|✔|✔|✔|✔ (sur les versions prises en charge)|✔ (sur les versions prises en charge)|✔|Recommandations (Gratuit) Détection des menaces (Standard)|
|Alertes de détection des menaces réseau|✔|✔|X|✔|✔|X|standard|
|Intégration de Windows Defender ATP|✔ (sur les versions prises en charge)|✔ (sur les versions prises en charge)|✔|X|X|X|standard|
|Correctifs manquants|✔|✔|✔|✔|✔|✔|Gratuit|
|Configurations de sécurité|✔|✔|✔|✔|✔|✔|Gratuit|
|Évaluation de la protection des points de terminaison|✔|✔|✔|X|X|X|Gratuit|
|Accès JIT à la machine virtuelle|✔|X|X|✔|X|X|standard|
|Contrôles d’application adaptative|✔|X|✔|✔|X|✔|standard|
|FIM|✔|✔|✔|✔|✔|✔|standard|
|Évaluation du chiffrement des disques|✔|✔|X|✔|✔|X|Gratuit|
|Déploiement tiers|✔|X|X|✔|X|X|Gratuit|
|Évaluation du groupe de sécurité réseau|✔|✔|X|✔|✔|X|Gratuit|
|Détection des menaces sans fichier|✔|✔|✔|X|X|X|standard|
|Mappage réseau|✔|✔|X|✔|✔|X|standard|
|Contrôles réseau adaptatifs|✔|✔|X|✔|✔|X|standard|
|Tableau de bord et rapports de conformité à la réglementation|✔|✔|✔|✔|✔|✔|standard|
|Recommandations et détection des menaces sur les conteneurs IaaS hébergés dans Docker|X|X|X|✔|✔|✔|standard|

### <a name="supported-endpoint-protection-solutions"></a>Solutions de protection du point de terminaison prises en charge

Le tableau suivant fournit une matrice de ce qui suit :
 - Si vous pouvez utiliser Azure Security Center pour installer chaque solution.
 - Les solutions de protection du point de terminaison que Security Center peut détecter. Si une de ces solutions de protection du point de terminaison est détectée, Security Center vous déconseillera d'en installer une autre.

Pour plus d’informations sur le moment où les recommandations sont générées pour chacune de ces protections, consultez [Évaluation de la protection de point de terminaison et recommandations](security-center-endpoint-protection.md).

| Protection du point de terminaison| Plateformes | Installation du centre de sécurité | Détection du centre de sécurité |
|------|------|-----|-----|
| Windows Defender (logiciel anti-programme malveillant de Microsoft)| Windows Server 2016| Non, intégré au système d’exploitation| OUI |
| System Center Endpoint Protection (logiciel anti-programme malveillant de Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 (voir la remarque ci-dessous) | Via l’extension | OUI |
| Trend Micro : toutes les versions | Gamme Windows Server  | Non | OUI |
| Symantec v12.1.1100+| Gamme Windows Server  | Non | OUI |
| McAfee v10+ | Gamme Windows Server  | Non | OUI |
| Kaspersky| Gamme Windows Server  | Non | Non  |
| Sophos| Gamme Windows Server  | Non | Non  |

> [!NOTE]
> - La détection de System Center Endpoint Protection (SCEP) sur une machine virtuelle Windows Server 2008 R2 requiert l’installation de SCEP après celle de PowerShell 3.0 (ou d’une version ultérieure).

## <a name="supported-paas-features"></a>Fonctionnalités PaaS prises en charge


|de diffusion en continu|Recommandations (Gratuit)|Détection des menaces (Standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Comptes de stockage Blob Azure|✔| ✔|
|App Services|✔| ✔|
|Cloud Services|✔| X|
|Réseaux virtuels|✔| N/D|
|Sous-réseaux|✔| N/D|
|Cartes réseau|✔| N/D|
|Groupes de sécurité réseau|✔| N/D|
|Subscription|✔ **| ✔|
|Batch|✔| N/D|
|Service Fabric|✔| N/D|
|Compte Automation|✔| N/D|
|Équilibrage de charge|✔| N/D|
|Recherche|✔| N/D|
|Service Bus|✔| N/D|
|Stream Analytics|✔| N/D|
|Event Hub|✔| N/D|
|Logic Apps|✔| N/D|
|Compte de stockage|✔| N/D|
|Redis|✔| N/D|
|Data Lake Analytics|✔| N/D|
|Coffre de clés|✔| N/D|




\* Ces fonctionnalités sont actuellement prises en charge en préversion publique.

\*\* Les recommandations AAD sont disponibles seulement pour les abonnements Standard



## <a name="next-steps"></a>Étapes suivantes

- [Découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception](security-center-planning-and-operations-guide.md).
- En savoir plus sur [l’analyse comportementale de machine virtuelle et l’analyse de mémoire de vidage sur incident dans Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Découvrez [les réponses aux questions le plus souvent posées sur l’utilisation d’Azure Security Center](security-center-faq.md).
- Accédez à des [billets de blog sur la sécurité et la conformité Azure](https://blogs.msdn.com/b/azuresecurity/).
