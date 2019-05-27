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
ms.date: 5/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 910e9acaea5c8f95dd344bc8e5454cd2bd3b7a25
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968332"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Plateformes et fonctionnalités prises en charge par Azure Security Center

La supervision de l’état de la sécurité et des recommandations sont disponibles pour les machines virtuelles créées à l’aide des modèles de déploiement classique et Resource Manager, ainsi que des ordinateurs.

> [!NOTE]
> En savoir plus sur les [modèles de déploiement de type Classique et Resource Manager](../azure-classic-rm.md) pour les ressources Azure.
>
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
>
>

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Plateformes prises en charge pour les ordinateurs et machines virtuelles Linux
Les systèmes d’exploitation Linux suivants sont pris en charge :

* Ubuntu versions 12.04 LTS, 14.04 LTS et 16.04 LTS.
* Debian versions 6, 7, 8 et 9.
* CentOS versions 5, 6 et 7.
* Red Hat Enterprise Linux (RHEL) versions 5, 6 et 7.
* SUSE Linux Enterprise Server (SLES) versions 11 et 12.
* Oracle Linux versions 5, 6 et 7.
* Amazon Linux, des versions 2012.09 à 2017.
* OpenSSL 1.1.0 n’est pris en charge que sur les plateformes x86_64 (64 bits).

## <a name="vms-and-cloud-services"></a>Machines virtuelles et services cloud
Les machines virtuelles en cours d’exécution dans un service cloud sont également prises en charge. Seuls les rôles de travail et web des services cloud en cours d’exécution dans des emplacements de production sont surveillés. Pour en savoir plus sur les services cloud, consultez [Vue d’ensemble d’Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Fonctionnalités IaaS prises en charge

> [!div class="mx-tableFixed"]
> 

|Serveur|Windows||Linux||
|----|----|----|----|----|
|Environnement|Azure|Non Azure|Azure|Non Azure|
|Alertes de détection des menaces VMBA|✔|✔|✔ (sur les versions prises en charge)|✔|
|Alertes de détection des menaces réseau|✔|X|✔|X|
|Intégration de Windows Defender ATP|✔ (sur les versions prises en charge)|✔|X|X|
|Correctifs manquants|✔|✔|✔|✔|
|Configurations de sécurité|✔|✔|✔|✔|
|Endpoint protection|✔|✔|X|X|
|Accès JIT à la MV|✔|X|✔|X|
|Contrôles d’applications adaptatifs|✔|✔|✔|✔|
|FIM|✔|✔|✔|✔|
|Chiffrement de disque|✔|X|✔|X|
|Déploiement tiers|✔|X|✔|X|
|Groupes de sécurité réseau|✔|X|✔|X|
|Détection des menaces sans fichier|✔|✔|X|X|
|Carte réseau|✔|X|✔|X|
|Contrôles réseau adaptatifs|✔|X|✔|X|


### <a name="supported-endpoint-protection-solutions"></a>Solutions de protection du point de terminaison prises en charge

Le tableau suivant fournit une matrice de ce qui suit :
 - Si vous pouvez utiliser Azure Security Center pour installer chaque solution.
 - Les solutions de protection du point de terminaison que Security Center peut détecter. Si une de ces solutions de protection du point de terminaison est détectée, Security Center vous déconseillera d'en installer une autre.

| Endpoint Protection| Plateformes | Installation du centre de sécurité | Détection du centre de sécurité |
|------|------|-----|-----|
| Windows Defender (logiciel anti-programme malveillant de Microsoft)| Windows Server 2016| Non, intégré au système d’exploitation| Oui |
| System Center Endpoint Protection (logiciel anti-programme malveillant de Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 (voir la remarque ci-dessous) | Via l’extension | Oui |
| Trend Micro : toutes les versions | Gamme Windows Server  | Non | Oui |
| Symantec v12.1.1100+| Gamme Windows Server  | Non | Oui |
| McAfee v10+ | Gamme Windows Server  | Non | Oui |
| Kaspersky| Gamme Windows Server  | Non | Non  |
| Sophos| Gamme Windows Server  | Non | Non  |

> [!NOTE]
> - La détection de System Center Endpoint Protection (SCEP) sur une machine virtuelle Windows Server 2008 R2 requiert l’installation de SCEP après celle de PowerShell 3.0 (ou d’une version ultérieure).
>
>

## <a name="supported-paas-features"></a>Fonctionnalités PaaS prises en charge 


|de diffusion en continu|Recommandations|Détection de menaces|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Comptes de stockage Azure blob*|✔| ✔|
|App Services|✔| ✔|
|Cloud Services|✔| X|
|Réseaux virtuels|✔| N/D|
|Sous-réseaux|✔| N/D|
|Cartes réseau|✔| ✔|
|Groupes de sécurité réseau|✔| N/D|
|Abonnement|✔| ✔|

\* Ces fonctionnalités sont actuellement prises en charge en préversion publique. 



## <a name="next-steps"></a>Étapes suivantes

- [Découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception](security-center-planning-and-operations-guide.md).
- En savoir plus sur [l’analyse comportementale de machine virtuelle et l’analyse de mémoire de vidage sur incident dans Security Center](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Découvrez [les réponses aux questions le plus souvent posées sur l’utilisation d’Azure Security Center](security-center-faq.md).
- Accédez à des [billets de blog sur la sécurité et la conformité Azure](https://blogs.msdn.com/b/azuresecurity/).
