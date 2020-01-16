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
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 7ffab200b50a7c4d1af60a230f237b18e8afc621
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754228"
---
# <a name="supported-features-available-in-azure-security-center"></a>Fonctionnalités prises en charge disponibles dans Azure Security Center

> [!NOTE]
>Certaines fonctionnalités ne sont disponibles qu'avec le niveau Standard. Si vous ne vous êtes pas encore inscrit au niveau Standard de Security Center, une période d'essai gratuite est disponible. Pour plus d’informations, consultez la [page de tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Les sections suivantes présentent les fonctionnalités de Security Center disponibles pour les [plateformes prises en charge](security-center-os-coverage.md).

* [Machines virtuelles / serveurs](#vm-server-features)
* [Services PaaS](#paas-services)


## Fonctionnalités prises en charge par les machines virtuelles / serveurs <a name="vm-server-features"></a>

### <a name="windowstabfeatures-windows"></a>[Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Machines virtuelles Azure**|**Groupes de machines virtuelles identiques Azure**|**Machines non-Azure**|**Tarification**
|[Intégration de Microsoft Defender ATP](security-center-wdatp.md)|✔ (sur les versions prises en charge)|✔ (sur les versions prises en charge)|✔|standard|
|[Alertes de détection des menaces – Analytique comportementale des machines virtuelles](security-center-alerts-iaas.md)|✔|✔|✔|Recommandations (Gratuit) Détection des menaces (Standard)|
|[Alertes de détection des menaces sans fichier](alerts-reference.md#alerts-windows)|✔|✔|✔|standard|
|[Alertes de détection des menaces réseau](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|standard|
|[Accès juste-à-temps aux machines virtuelles](security-center-just-in-time.md)|✔|-|-|standard|
|[Évaluation native des vulnérabilités](built-in-vulnerability-assessment.md)|✔|-|-|standard|
|[Supervision de l’intégrité des fichiers](security-center-file-integrity-monitoring.md)|✔|✔|✔|standard|
|[Contrôles d’application adaptative](security-center-adaptive-application.md)|✔|-|✔|standard|
|[Mappage réseau](security-center-network-recommendations.md#network-map)|✔|✔|-|standard|
|[Durcissement réseau adaptatif](security-center-adaptive-network-hardening.md)|✔|-|-|standard|
|Contrôles réseau adaptatifs|✔|✔|-|standard|
|[Tableau de bord et rapports de conformité à la réglementation](security-center-compliance-dashboard.md)|✔|✔|✔|standard|
|Recommandations et détection des menaces sur les conteneurs IaaS hébergés dans Docker|-|-|-|standard|
|Évaluation des correctifs de système d’exploitation manquants|✔|✔|✔|Gratuit|
|Évaluation des erreurs de configuration de la sécurité|✔|✔|✔|Gratuit|
|[Évaluation de la protection des points de terminaison](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Gratuit|
|Évaluation du chiffrement des disques|✔|✔|-|Gratuit|
|Évaluation des vulnérabilités tierces|✔|-|-|Gratuit|
|[Évaluation de la sécurité réseau](security-center-network-recommendations.md)|✔|✔|-|Gratuit|


### <a name="linuxtabfeatures-linux"></a>[Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Machines virtuelles Azure**|**Groupes de machines virtuelles identiques Azure**|**Machines non-Azure**|**Tarification**
|[Intégration de Microsoft Defender ATP](security-center-wdatp.md)|-|-|-|standard|
|[Alertes de détection des menaces – Analytique comportementale des machines virtuelles](security-center-alerts-iaas.md)|✔ (sur les versions prises en charge)|✔ (sur les versions prises en charge)|✔|Recommandations (Gratuit) Détection des menaces (Standard)|
|[Alertes de détection des menaces sans fichier](alerts-reference.md#alerts-windows)|-|-|-|standard|
|[Alertes de détection des menaces réseau](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|standard|
|[Accès juste-à-temps aux machines virtuelles](security-center-just-in-time.md)|✔|-|-|standard|
|[Évaluation native des vulnérabilités](built-in-vulnerability-assessment.md)|✔|-|-|standard|
|[Supervision de l’intégrité des fichiers](security-center-file-integrity-monitoring.md)|✔|✔|✔|standard|
|[Contrôles d’application adaptative](security-center-adaptive-application.md)|✔|-|✔|standard|
|[Mappage réseau](security-center-network-recommendations.md#network-map)|✔|✔|-|standard|
|[Durcissement réseau adaptatif](security-center-adaptive-network-hardening.md)|✔|-|-|standard|
|Contrôles réseau adaptatifs|✔|✔|-|standard|
|[Tableau de bord et rapports de conformité à la réglementation](security-center-compliance-dashboard.md)|✔|✔|✔|standard|
|Recommandations et détection des menaces sur les conteneurs IaaS hébergés dans Docker|✔|✔|✔|standard|
|Évaluation des correctifs de système d’exploitation manquants|✔|✔|✔|Gratuit|
|Évaluation des erreurs de configuration de la sécurité|✔|✔|✔|Gratuit|
|[Évaluation de la protection des points de terminaison](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Gratuit|
|Évaluation du chiffrement des disques|✔|✔|-|Gratuit|
|Évaluation des vulnérabilités tierces|✔|-|-|Gratuit|
|[Évaluation de la sécurité réseau](security-center-network-recommendations.md)|✔|✔|-|Gratuit|

--- 

## Solutions de protection du point de terminaison prises en charge <a name="endpoint-supported"></a>

Le tableau suivant fournit une matrice de ce qui suit :

 - Si vous pouvez utiliser Azure Security Center pour installer chaque solution.
 - Les solutions de protection du point de terminaison que Security Center peut détecter. Si l’une des solutions de protection du point de terminaison de cette liste est détectée, Security Center vous déconseillera d'en installer une autre.

Pour plus d’informations sur le moment où les recommandations sont générées pour chacune de ces protections, consultez [Évaluation de la protection de point de terminaison et recommandations](security-center-endpoint-protection.md).

| Protection du point de terminaison| Plateformes | Installation du centre de sécurité | Détection du centre de sécurité |
|------|------|-----|-----|
| Windows Defender (logiciel anti-programme malveillant de Microsoft)| Windows Server 2016| Non, intégré au système d’exploitation| Oui |
| System Center Endpoint Protection (logiciel anti-programme malveillant de Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 (voir la remarque ci-dessous) | Via l’extension | Oui |
| Trend Micro : toutes les versions* | Gamme Windows Server  | Non | Oui |
| Symantec v12.1.1100+| Gamme Windows Server  | Non | Oui |
| McAfee v10+ | Gamme Windows Server  | Non | Oui |
| McAfee v10+ | Famille de serveurs Linux  | Non | Oui **\*** |
| Sophos V9+| Famille de serveurs Linux  | Non | Oui  **\***  |

 **\*** L’état de couverture et les données de prise en charge sont actuellement disponibles uniquement dans l’espace de travail Log Analytics associé à vos abonnements protégés. Il n’est pas reflété dans le portail Azure Security Center.

> [!NOTE]
> - La détection de System Center Endpoint Protection (SCEP) sur une machine virtuelle Windows Server 2008 R2 requiert l’installation de SCEP après celle de PowerShell 3.0 (ou d’une version ultérieure).
> - La détection de la protection Trend Micro est prise en charge pour les agents Deep Security.  Les agents OfficeScan ne sont pas pris en charge.


## Fonctionnalités prises en charge par les services PaaS <a name="paas-services"> </a>

Azure Security Center prend en charge les ressources PaaS suivantes :

|Service|Recommandations (Gratuit)|Alertes de détection des menaces (Standard)|l'Évaluation des vulnérabilités (Standard)|
|----|:----:|:----:|:----:|
|BASES DE DONNÉES SQL|✔|✔|✔|
|Azure Container Registry|-|-|✔|
|Azure Kubernetes Service|✔|✔|-|
|Azure Database pour PostgreSQL*|✔|✔|-|
|Azure Database pour MySQL*|✔|✔|-|
|Azure CosmosDB*|-|✔|-|
|Comptes de stockage|✔|-|-|
|Stockage Blob|✔|✔|-|
|App Service|✔|✔|-|
|Conteneur de fonctions|✔|-|-|
|Cloud Services|✔|-|-|
|Réseau virtuel|✔|-|-|
|Subnet|✔|-|-|
|Carte d’interface réseau|✔|-|-|
|Network Security Group|✔|-|-|
|Subscription|✔ **|✔|-|
|Compte Batch|✔|-|-|
|Compte Service Fabric|✔|-|-|
|Compte Automation|✔|-|-|
|Load Balancer|✔|-|-|
|Recherche cognitive|✔|-|-|
|Espace de noms Service Bus|✔|-|-|
|Stream Analytics|✔|-|-|
|Espace de noms du hub d’événements|✔|-|-|
|Logic Apps|✔|-|-|
|Cache pour Redis|✔|-|-|
|Data Lake Analytics|✔|-|-|
|Azure Data Lake Storage|✔|-|-|
|Key Vault|✔|✔ *|-|

\* Ces fonctionnalités sont actuellement prises en charge en préversion.

\*\* Les recommandations Azure Active Directory (Azure AD) sont uniquement disponibles pour les abonnements Standard.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Security Center collecte les données et l'agent Log Analytics Agent](security-center-enable-data-collection.md).
- Découvrez comment [Security Center gère et protège les données](security-center-data-security.md).
- [Découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception](security-center-planning-and-operations-guide.md).
- Passez en revue les [plateformes qui prennent en charge Security Center](security-center-os-coverage.md).
- Apprenez-en davantage sur la [détection des menaces pour les machines virtuelles et les serveurs dans Azure Security Center](security-center-alerts-iaas.md).
- Découvrez [les réponses aux questions le plus souvent posées sur l’utilisation d’Azure Security Center](security-center-faq.md).
- Accédez à des [billets de blog sur la sécurité et la conformité Azure](https://blogs.msdn.com/b/azuresecurity/).