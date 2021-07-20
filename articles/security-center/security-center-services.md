---
title: Fonctionnalités d’Azure Security Center en fonction du système d’exploitation, du type de machine et du cloud
description: Découvrez les fonctionnalités d’Azure Security Center disponibles en fonction du système d’exploitation, du type et du déploiement cloud.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 06/17/2021
ms.custom: references_regions
ms.author: memildin
ms.openlocfilehash: 05b535a0aabe0108c0a9abcedef166293541d407
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113005471"
---
# <a name="feature-coverage-for-machines"></a>Couverture des fonctionnalités pour les machines

Les deux tableaux ci-dessous montrent les fonctionnalités d’Azure Security Center disponibles pour machines virtuelles et les serveurs Windows et Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Fonctionnalités prises en charge pour les machines virtuelles et les serveurs <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Machines Windows**](#tab/features-windows)

|**Fonctionnalité**|**Machines virtuelles Azure**|**Groupes de machines virtuelles identiques Azure**|**Machines compatibles avec Azure Arc**|**Azure Defender requis**
|----|:----:|:----:|:----:|:----:|
|[Intégration de Microsoft Defender for Endpoint](security-center-wdatp.md)|✔</br>(sur les versions prises en charge)|✔</br>(sur les versions prises en charge)|✔|Oui|
|[Analytique comportementale des machines virtuelles (et alertes de sécurité)](alerts-reference.md)|✔|✔|✔|Oui|
|[Alertes de sécurité sans fichier](alerts-reference.md#alerts-windows)|✔|✔|✔|Oui|
|[Alertes de sécurité réseau](other-threat-protections.md#network-layer)|✔|✔|-|Oui|
|[Accès juste-à-temps aux machines virtuelles](security-center-just-in-time.md)|✔|-|-|Oui|
|[Évaluation native des vulnérabilités](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Oui|
|[Supervision de l’intégrité des fichiers](security-center-file-integrity-monitoring.md)|✔|✔|✔|Oui|
|[Contrôles d’application adaptative](security-center-adaptive-application.md)|✔|-|✔|Oui|
|[Mappage réseau](security-center-network-recommendations.md#network-map)|✔|✔|-|Oui|
|[Durcissement réseau adaptatif](security-center-adaptive-network-hardening.md)|✔|-|-|Oui|
|[Tableau de bord et rapports de conformité réglementaire](security-center-compliance-dashboard.md)|✔|✔|✔|Oui|
|Recommandations et protection contre les menaces sur les conteneurs IaaS hébergés dans Docker|-|-|-|Oui|
|Évaluation des correctifs de système d’exploitation manquants|✔|✔|✔|Azure : Non<br><br>Compatible avec Arc : Oui|
|Évaluation des erreurs de configuration de la sécurité|✔|✔|✔|Azure : Non<br><br>Compatible avec Arc : Oui|
|[Évaluation de la protection des points de terminaison](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure : Non<br><br>Compatible avec Arc : Oui|
|Évaluation du chiffrement des disques|✔</br>(pour les [scénarios pris en charge](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Non|
|Évaluation des vulnérabilités tierces|✔|-|✔|Non|
|[Évaluation de la sécurité réseau](security-center-network-recommendations.md)|✔|✔|-|Non|


### <a name="linux-machines"></a>[**Machines Linux**](#tab/features-linux)

|**Fonctionnalité**|**Machines virtuelles Azure**|**Groupes de machines virtuelles identiques Azure**|**Machines compatibles avec Azure Arc**|**Azure Defender requis**
|----|:----:|:----:|:----:|:----:|
|[Intégration de Microsoft Defender for Endpoint](security-center-wdatp.md)|-|-|-|-|
|[Analytique comportementale des machines virtuelles (et alertes de sécurité)](./azure-defender.md)|✔</br>(sur les versions prises en charge)|✔</br>(sur les versions prises en charge)|✔|Oui|
|[Alertes de sécurité sans fichier](alerts-reference.md#alerts-windows)|-|-|-|Oui|
|[Alertes de sécurité réseau](other-threat-protections.md#network-layer)|✔|✔|-|Oui|
|[Accès juste-à-temps aux machines virtuelles](security-center-just-in-time.md)|✔|-|-|Oui|
|[Évaluation native des vulnérabilités](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Oui|
|[Supervision de l’intégrité des fichiers](security-center-file-integrity-monitoring.md)|✔|✔|✔|Oui|
|[Contrôles d’application adaptative](security-center-adaptive-application.md)|✔|-|✔|Oui|
|[Mappage réseau](security-center-network-recommendations.md#network-map)|✔|✔|-|Oui|
|[Durcissement réseau adaptatif](security-center-adaptive-network-hardening.md)|✔|-|-|Oui|
|[Tableau de bord et rapports de conformité réglementaire](security-center-compliance-dashboard.md)|✔|✔|✔|Oui|
|Recommandations et protection contre les menaces sur les conteneurs IaaS hébergés dans Docker|✔|✔|✔|Oui|
|Évaluation des correctifs de système d’exploitation manquants|✔|✔|✔|Azure : Non<br><br>Compatible avec Arc : Oui|
|Évaluation des erreurs de configuration de la sécurité|✔|✔|✔|Azure : Non<br><br>Compatible avec Arc : Oui|
|[Évaluation de la protection des points de terminaison](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Non|
|Évaluation du chiffrement des disques|✔</br>(pour les [scénarios pris en charge](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Non|
|Évaluation des vulnérabilités tierces|✔|-|✔|Non|
|[Évaluation de la sécurité réseau](security-center-network-recommendations.md)|✔|✔|-|Non|

--- 


> [!TIP]
>Pour expérimenter les fonctionnalités disponibles uniquement avec Azure Defender, vous pouvez vous inscrire à une version d’évaluation de 30 jours. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Solutions de protection du point de terminaison prises en charge <a name="endpoint-supported"></a>

Le tableau suivant fournit une matrice de ce qui suit :

 - Si vous pouvez utiliser Azure Security Center pour installer chaque solution.
 - Les solutions de protection du point de terminaison que Security Center peut détecter. Si l’une des solutions de protection du point de terminaison de cette liste est détectée, Security Center vous déconseillera d'en installer une autre.

Pour plus d’informations sur le moment où les recommandations sont générées pour chacune de ces protections, consultez [Évaluation de la protection de point de terminaison et recommandations](security-center-endpoint-protection.md).

| Protection du point de terminaison| Plateformes | Installation du centre de sécurité | Détection du centre de sécurité |
|------|------|-----|-----|
| Antivirus Microsoft Defender| Windows Server 2016 ou version ultérieure| Non, intégré au système d’exploitation| Oui |
| System Center Endpoint Protection (logiciel anti-programme malveillant de Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 (voir la remarque ci-dessous) | Via l’extension | Oui |
| Trend Micro - Deep Security | Gamme Windows Server  | Non | Oui |
| Symantec v12.1.1100+| Gamme Windows Server  | Non | Oui |
| McAfee v10+ | Gamme Windows Server  | Non | Oui |
| McAfee v10+ | Famille de serveurs Linux  | Non | Oui |
| Sophos V9+| Famille de serveurs Linux  | Non | Oui |

> [!NOTE]
> La détection de System Center Endpoint Protection (SCEP) sur une machine virtuelle Windows Server 2008 R2 nécessite l’installation de SCEP après celle de PowerShell (v3.0 ou ultérieure).



## <a name="feature-support-in-government-and-sovereign-clouds"></a>Prise en charge des fonctionnalités dans les clouds du secteur public et souverains

| Fonctionnalité/Service                                                                                                                                                             | Azure          | Gouvernement américain                  | Azure Chine   |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|---------------|
| **Fonctionnalités Security Center gratuites**                                                                                                                                           |                |                                |               |
| - [Exportation continue](/azure/security-center/continuous-export)                                                                                                             | GA             | GA                             | GA            |
| - [Automatisation des workflows](/azure/security-center/continuous-export)                                                                                                           | GA             | GA                             | GA            |
| - [Règles d’exemption de recommandation](/azure/security-center/exempt-resource)                                                                                                  | Version préliminaire publique | Non disponible                  | Non disponible |
| - [Règles de suppression d’alerte](/azure/security-center/alerts-suppression-rules)                                                                                                | GA             | GA                             | GA            |
| - [Notifications par e-mail pour les alertes de sécurité](/azure/security-center/security-center-provide-security-contact-details)                                                        | GA             | GA                             | GA            |
| - [Approvisionnement automatique pour les agents et les extensions](/azure/security-center/security-center-enable-data-collection)                                                              | GA             | GA                             | GA            |
| - [Inventaire des ressources](/azure/security-center/asset-inventory)                                                                                                                 | GA             | GA                             | GA            |
| - [Rapports Azure Monitor des classeurs rapports dans la Galerie de classeurs Azure Security Center](/azure/security-center/custom-dashboards-azure-workbooks)                                  | GA             | GA                             | GA            |
| **Plans et extensions Azure Defender**                                                                                                                                     |                |                                |               |
| - [Azure Defender pour les serveurs](/azure/security-center/defender-for-servers-introduction)                                                                                    | GA             | GA                             | GA            |
| - [Azure Defender pour App Service](/azure/security-center/defender-for-app-service-introduction)                                                                            | GA             | Non disponible                  | Non disponible |
| - [Azure Defender pour DNS](/azure/security-center/defender-for-dns-introduction)                                                                                            | GA             | Non disponible                  | Non disponible |
| - [Azure Defender pour les registres de conteneurs](/azure/security-center/defender-for-container-registries-introduction) <sup>[1](#footnote1)</sup>                               | GA             | GA  <sup>[2](#footnote2)</sup> | GA  <sup>[2](#footnote2)</sup> |
| - [Azure Defender pour les registres de conteneurs analyse des images dans les flux de travail CI/CD](/azure/security-center/defender-for-container-registries-cicd) <sup> [3](#footnote3)</sup> | Version préliminaire publique | Non disponible                  | Non disponible |
| - [Azure Defender pour Kubernetes](/azure/security-center/defender-for-kubernetes-introduction) <sup>[4](#footnote4)</sup>                                                   | GA             | GA                             | GA            |
| - [Extension Azure Defender pour les clusters Kubernetes avec Azure Arc](/azure/security-center/defender-for-kubernetes-azure-arc) <sup>[5](#footnote5)</sup>                 | Version préliminaire publique | Non disponible                  | Non disponible |
| - [Azure Defender pour les serveurs de base de données Azure SQL](/azure/security-center/defender-for-sql-introduction)                                                                     | GA             | GA                             | GA  <sup>[9](#footnote9)</sup> |
| - [Azure Defender pour les serveurs SQL sur les machines](/azure/security-center/defender-for-sql-introduction)                                                                        | GA             | GA                             | Non disponible |
| - [Azure Defender pour les bases de données relationnelles open source](/azure/security-center/defender-for-databases-introduction)                                                         | GA             | Non disponible                  | Non disponible |
| - [Azure Defender pour Key Vault](/azure/security-center/defender-for-key-vault-introduction)                                                                                | GA             | Non disponible                  | Non disponible |
| - [Azure Defender pour Resource Manager](/azure/security-center/defender-for-resource-manager-introduction)                                                                  | GA             | Version préliminaire publique                 | Non disponible |
| - [Azure Defender pour Stockage](/azure/security-center/defender-for-storage-introduction) <sup>[6](#footnote6)</sup>                                                         | GA             | GA                             | Non disponible |
| - [Protection contre les menaces pour Cosmos DB](/azure/security-center/other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                       | Version préliminaire publique | Non disponible                  | Non disponible |
| - [Protection de charge de travail Kubernetes](/azure/security-center/kubernetes-workload-protections)                                                                                  | GA             | GA                             | GA            |
| **Fonctionnalités Azure Defender pour les serveurs** <sup>[7](#footnote7)</sup>                                                                                                          |                |                                |               |
| - [Accès juste-à-temps aux machines virtuelles](/azure/security-center/security-center-just-in-time)                                                                                             | GA             | GA                             | GA            |
| - [Supervision de l’intégrité des fichiers](/azure/security-center/security-center-file-integrity-monitoring)                                                                             | GA             | GA                             | GA            |
| - [Contrôles d’application adaptatifs](/azure/security-center/security-center-adaptive-application)                                                                              | GA             | GA                             | GA            |
| - [Sécurisation renforcée du réseau adaptative](/azure/security-center/security-center-adaptive-network-hardening)                                                                           | GA             | Non disponible                  | Non disponible |
| - [Sécurisation renforcée de l’hôte Docker](/azure/security-center/harden-docker-hosts)                                                                                                       | GA             | GA                             | GA            |
| - [Évaluation des vulnérabilités intégrée pour les machines](/azure/security-center/deploy-vulnerability-assessment-vm)                                                             | GA             | Non disponible                  | Non disponible |
| - [Tableau de bord et rapports de conformité réglementaire](/azure/security-center/security-center-compliance-dashboard) <sup>[8](#footnote8)</sup>                                       | GA             | GA                             | GA            |
| - [Déploiement de Microsoft Defender pour point de terminaison et licence intégrée](/azure/security-center/security-center-wdatp)                                                         | GA             | GA                             | Non disponible |
| - [Connecter un compte AWS](/azure/security-center/quickstart-onboard-aws)                                                                                                      | GA             | Non disponible                  | Non disponible |
| - [Connecter un compte GCP](/azure/security-center/quickstart-onboard-gcp)                                                                                                      | GA             | Non disponible                  | Non disponible |
|                                                                                                                                                                             |                |                                |

<sup><a name="footnote1" /></a>1</sup> Partiellement GA : la possibilité de désactiver des résultats spécifiques à partir d’analyses de vulnérabilités est disponible en préversion publique.

<sup><a name="footnote2" /></a>2</sup> Les analyses de vulnérabilité des registres de conteneurs sur Azure Gov ne peuvent uniquement être effectuées avec la fonctionnalité d’analyse à l’envoi (push).

<sup><a name="footnote3" /></a>3</sup> Requiert Azure Defender pour les registres de conteneurs.

<sup><a name="footnote4" /></a>4</sup> Partiellement GA : la prise en charge des clusters avec Arc est disponible en préversion publique et pas sur Azure Government.

<sup><a name="footnote5" /></a>5</sup> Requiert Azure Defender pour Kubernetes.

<sup><a name="footnote6" /></a>6</sup> Partiellement GA : certaines des alertes de protection contre les menaces d’Azure Defender pour Stockage sont disponibles en préversion publique.

<sup><a name="footnote7" /></a>7</sup> Ces fonctionnalités requièrent toutes [Azure Defender pour les serveurs](/azure/security-center/defender-for-servers-introduction).

<sup><a name="footnote8" /></a>8</sup> Des différences sont possible en termes de normes offertes par type de cloud.
 
<sup><a name="footnote9" /></a>9</sup> Partiellement GA : sous-ensemble d’alertes et évaluation des vulnérabilités pour les serveurs SQL. Les protections contre les menaces comportementales ne sont pas disponibles.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Security Center collecte des données avec l’agent Log Analytics](security-center-enable-data-collection.md).
- Découvrez comment [Security Center gère et protège les données](security-center-data-security.md).
- Passez en revue les [plateformes qui prennent en charge Security Center](security-center-os-coverage.md).