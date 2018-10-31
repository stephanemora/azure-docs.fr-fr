---
title: Fonctionnalités et plateformes prises en charge par Azure Security Center | Microsoft Docs
description: Ce document fournit une liste des fonctionnalités et des plateformes prises en charge par Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rkarlin
ms.openlocfilehash: 279818e6b43e53206deb9e33591f75ef381a8962
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319980"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Plateformes et fonctionnalités prises en charge par Azure Security Center

La surveillance de l’état de la sécurité et des recommandations sont disponibles pour les machines virtuelles créées à l’aide des modèles de déploiement classique et Resource Manager, ainsi que des ordinateurs.

> [!NOTE]
> En savoir plus sur les [modèles de déploiement de type Classique et Resource Manager](../azure-classic-rm.md) pour les ressources Azure.
>
>

## <a name="supported-platforms"></a>Plateformes prises en charge 

Cette section liste les plateformes sur lesquelles l’agent Azure Security Center peut s’exécuter et à partir desquelles il peut collecter des données.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Plateformes prises en charge pour les ordinateurs et machines virtuelles Windows
Systèmes d’exploitation Windows pris en charge :

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


### <a name="supported-platforms-for-linux-computers-and-vms"></a>Plateformes prises en charge pour les ordinateurs et machines virtuelles Linux
Systèmes d’exploitation Linux pris en charge :

* Ubuntu versions 12.04 LTS, 14.04 LTS et 16.04 LTS
* Debian versions 6, 7, 8 et 9
* CentOS versions 5, 6 et 7
* Red Hat Enterprise Linux (RHEL) versions 5, 6 et 7
* SUSE Linux Enterprise Server (SLES) versions 11 et 12
* Oracle Linux versions 5, 6 et 7
* Amazon Linux entre 2012.09 et 2017
* OpenSSL 1.1.0 n’est pris en charge que sur les plateformes x86_64 (64 bits)

> [!NOTE]
> L’analytique comportementale de machine virtuelle n’est pas encore disponible pour les systèmes d’exploitation Linux.
>
>

## <a name="vms-and-cloud-services"></a>Machines virtuelles et services cloud
Les machines virtuelles en cours d’exécution dans un service cloud sont également prises en charge. Seuls les rôles de travail et web des services cloud en cours d’exécution dans des emplacements de production sont surveillés. Pour en savoir plus sur le service cloud, consultez [Vue d’ensemble de Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Fonctionnalités IaaS prises en charge

> [!div class="mx-tableFixed"]
> 

|Serveur|Windows||Linux||
|----|----|----|----|----|
|Environnement|Azure|Non-Azure|Azure|Non-Azure|
|Alertes de détection des menaces VMBA|✔|✔|✔ (sur les versions prises en charge)|✔|
|Alertes de détection des menaces réseau|✔|X|✔|X|
|Intégration de Windows Defender ATP*|✔ (sur les versions prises en charge)|✔|X|X|
|Correctifs manquants|✔|✔|✔|✔|
|Configurations de sécurité|✔|✔|✔|✔|
|Anti-programme malveillant|✔|✔|X|X|
|Accès JIT à la machine virtuelle|✔|X|✔|X|
|Contrôles d’application adaptative|✔ (Azure uniquement)|X|X|X|
|FIM|✔|✔|✔|✔|
|Chiffrement de disque|✔|X|✔|X|
|Déploiement tiers|✔|X|✔|X|
|Groupes de sécurité réseau|✔|X|✔|X|
|Fichiers V1|✔|✔|X|X|
|Mappage réseau|✔|X|✔|X|
|Sécurisation adaptative du réseau|✔|X|✔|X|

* Ces fonctionnalités sont actuellement prises en charge en préversion publique.


## <a name="supported-paas-features"></a>Fonctionnalités PaaS prises en charge


|Service|Recommandations|Détection de menaces|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Comptes de stockage d’objets blob*|✔| ✔|
|App Services|✔| ✔|
|Services cloud|✔| X|
|Le cache Redis|✔| X|
|Service Fabric|✔| X|
|Azure Automation|✔| X|
|Data Lake |✔| X|
|Coffre de clés|✔| X|
|Service Bus|✔| X|
|Stream Analytics|✔| X|
|Batch|✔| X|
|Logic Apps|✔| X|
|Réseaux virtuels|✔| N/D|
|Sous-réseaux|✔| N/D|
|Cartes réseau|✔| ✔|
|Groupes de sécurité réseau|✔| N/D|
|Abonnement|✔| ✔|

* Ces fonctionnalités sont actuellement prises en charge en préversion publique.

## <a name="next-steps"></a>Étapes suivantes

- [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md) : découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception
- [Alertes de sécurité par type dans Azure Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis) : en savoir plus sur l’analytique comportementale de machine virtuelle et l’analyse de mémoire de vidage sur incident dans Security Center
- [FAQ d’Azure Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure
