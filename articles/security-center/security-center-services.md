---
title: Fonctionnalités prises en charge disponibles dans Azure Security Center | Microsoft Docs
description: Ce document fournit la liste des services pris en charge par Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: ad662cdeb0a87e57eb9e3e7480d120be1c6c729e
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218264"
---
# <a name="supported-features-available-in-azure-security-center"></a>Fonctionnalités prises en charge disponibles dans Azure Security Center

> [!NOTE]
>Certaines fonctionnalités ne sont disponibles qu'avec le niveau Standard. Si vous ne vous êtes pas encore inscrit au niveau Standard de Security Center, une période d'essai gratuite est disponible. Pour plus d’informations, consultez la [page de tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Les sections suivantes présentent les fonctionnalités de Security Center disponibles pour les [plateformes prises en charge](security-center-os-coverage.md).

* [Machines virtuelles / serveurs](#vm-server-features)
* [Services PaaS](#paas-services)


## Fonctionnalités prises en charge par les machines virtuelles / serveurs <a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Serveur|Windows|||Linux|||Tarifs|
|----|----|----|----|----|----|----|----|
|**Environment**|**Microsoft Azure**||**Non-Azure**|**Microsoft Azure**||**Non-Azure**||
||**Machine virtuelle**|**Groupe de machines virtuelles identiques**||**Machine virtuelle**|**Groupe de machines virtuelles identiques**|
|Alertes de détection des menaces VMBA|✔|✔|✔|✔ (sur les versions prises en charge)|✔ (sur les versions prises en charge)|✔|Recommandations (Gratuit) Détection des menaces (Standard)|
|Alertes de détection des menaces réseau|✔|✔|X|✔|✔|X|standard|
|Intégration de Microsoft Defender ATP|✔ (sur les versions prises en charge)|✔ (sur les versions prises en charge)|✔|X|X|X|standard|
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

### Solutions de protection du point de terminaison prises en charge <a name="endpoint-supported"></a>

Le tableau suivant fournit une matrice de ce qui suit :

 - Si vous pouvez utiliser Azure Security Center pour installer chaque solution.
 - Les solutions de protection du point de terminaison que Security Center peut détecter. Si une de ces solutions de protection du point de terminaison est détectée, Security Center vous déconseillera d'en installer une autre.

Pour plus d’informations sur le moment où les recommandations sont générées pour chacune de ces protections, consultez [Évaluation de la protection de point de terminaison et recommandations](security-center-endpoint-protection.md).

| Protection du point de terminaison| Plateformes | Installation du centre de sécurité | Détection du centre de sécurité |
|------|------|-----|-----|
| Windows Defender (logiciel anti-programme malveillant de Microsoft)| Windows Server 2016| Non, intégré au système d’exploitation| OUI |
| System Center Endpoint Protection (logiciel anti-programme malveillant de Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 (voir la remarque ci-dessous) | Via l’extension | OUI |
| Trend Micro : toutes les versions* | Gamme Windows Server  | Non | OUI |
| Symantec v12.1.1100+| Gamme Windows Server  | Non | OUI |
| McAfee v10+ | Gamme Windows Server  | Non | OUI |
| McAfee v10+ | Famille de serveurs Linux  | Non | Oui **\*** |
| Sophos V9+| Famille de serveurs Linux  | Non | Oui  **\***  |

 **\*** L’état de couverture et les données de prise en charge sont actuellement disponibles uniquement dans l’espace de travail Log Analytics associé à vos abonnements protégés. Ils ne sont pas reflétés dans le portail Azure Security Center.

> [!NOTE]
>
> - La détection de System Center Endpoint Protection (SCEP) sur une machine virtuelle Windows Server 2008 R2 requiert l’installation de SCEP après celle de PowerShell 3.0 (ou d’une version ultérieure).
> - La détection de la protection Trend Micro est prise en charge pour les agents Deep Security.  Les agents OfficeScan ne sont pas pris en charge.


## Fonctionnalités prises en charge par les services PaaS <a name="paas-services"> </a>

Azure Security Center prend en charge les ressources PaaS suivantes :

|de diffusion en continu|Recommandations (Gratuit)|Détection des menaces (Standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|CosmosDB*|X| ✔|
|Stockage d'objets blob|✔| ✔|
|Compte de stockage|✔| N/D|
|App Service|✔| ✔|
|Fonction|✔| X|
|Service cloud|✔| X|
|Réseau virtuel|✔| N/D|
|Subnet|✔| N/D|
|Carte d’interface réseau|✔| N/D|
|Groupe de sécurité réseau|✔| N/D|
|Subscription|✔ **| ✔|
|Compte Batch|✔| X|
|Compte Service Fabric|✔| X|
|Compte Automation|✔| X|
|Équilibrage de charge|✔| X|
|Recherche|✔| X|
|Espace de noms Service Bus|✔| X|
|Stream Analytics|✔| X|
|Espace de noms du hub d’événements|✔| X|
|Logic Apps|✔| X|
|Redis|✔| N/D|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|Coffre de clés|✔| X|

\* Ces fonctionnalités sont actuellement prises en charge en préversion publique.

\*\* Les recommandations Azure Active Directory (Azure AD) sont uniquement disponibles pour les abonnements Standard.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Security Center collecte les données et l'agent Log Analytics Agent](security-center-enable-data-collection.md).
- Découvrez comment [Security Center gère et protège les données](security-center-data-security.md).
- [Découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception](security-center-planning-and-operations-guide.md).
- Passez en revue les [plateformes qui prennent en charge Security Center](security-center-os-coverage.md).
- Apprenez-en davantage sur la [détection des menaces pour les machines virtuelles et les serveurs dans Azure Security Center](security-center-alerts-iaas.md).
- Découvrez [les réponses aux questions le plus souvent posées sur l’utilisation d’Azure Security Center](security-center-faq.md).
- Accédez à des [billets de blog sur la sécurité et la conformité Azure](https://blogs.msdn.com/b/azuresecurity/).
