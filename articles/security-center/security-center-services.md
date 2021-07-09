---
title: Fonctionnalités d’Azure Security Center en fonction du système d’exploitation, du type de machine et du cloud
description: Découvrez les fonctionnalités d’Azure Security Center disponibles en fonction du système d’exploitation, du type et du déploiement cloud.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 05/13/2021
ms.author: memildin
ms.openlocfilehash: b68a9946908697d6e9113af586a134b0b3d8784a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110468336"
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



## <a name="feature-support-in-government-clouds"></a>Prise en charge des fonctionnalités dans les clouds des secteurs publics

| Service / Fonctionnalité | Gouvernement des États-Unis | China Gov |
|------|:----:|:----:|
|[Accès juste-à-temps aux machines virtuelles](security-center-just-in-time.md) (1)|✔|✔|
|[Supervision de l’intégrité des fichiers](security-center-file-integrity-monitoring.md) (1)|✔|✔|
|[Contrôles d’application adaptatifs.](security-center-adaptive-application.md) (1)|✔|✔|
|[Sécurisation renforcée du réseau adaptative](security-center-adaptive-network-hardening.md) (1)|-|-|
|[Sécurisation renforcée de l’hôte Docker](harden-docker-hosts.md) (1)|✔|✔|
|[Évaluation des vulnérabilités intégrée pour les machines](deploy-vulnerability-assessment-vm.md) (1)|-|-|
|[Microsoft Defender pour point de terminaison](harden-docker-hosts.md) (1)|✔|-|
|[Connecter un compte AWS](quickstart-onboard-aws.md) (1)|-|-|
|[Connecter des comptes GCP](quickstart-onboard-gcp.md) (1)|-|-|
|[Exportation continue](continuous-export.md)|✔|✔|
|[Automatisation de workflow](workflow-automation.md)|✔|✔|
|[Règles d’exemption de recommandation](exempt-resource.md)|-|-|
|[Règles de suppression d’alerte](alerts-suppression-rules.md)|✔|✔|
|[Notifications par e-mail pour les alertes de sécurité](security-center-provide-security-contact-details.md)|✔|✔|
|[Inventaire des ressources](asset-inventory.md)|✔|✔|
|[Azure Defender pour App Service](defender-for-app-service-introduction.md)|-|-|
|[Azure Defender pour Stockage](defender-for-storage-introduction.md)|✔|-|
|[Azure Defender pour SQL](defender-for-sql-introduction.md)|✔|✔ (2)|
|[Azure Defender pour Key Vault](defender-for-key-vault-introduction.md)|-|-|
|[Azure Defender pour Resource Manager](defender-for-resource-manager-introduction.md)|-|-|
|[Azure Defender pour DNS](defender-for-dns-introduction.md)|-|-|
|[Azure Defender pour les registres de conteneurs](defender-for-container-registries-introduction.md)|✔ (2)|✔ (2)|
|[Azure Defender pour Kubernetes](defender-for-kubernetes-introduction.md)|✔|✔|
|[Azure Defender pour les bases de données relationnelles open source](defender-for-databases-introduction.md)|-|-|
|[Protection de charge de travail Kubernetes](kubernetes-workload-protections.md)|✔|✔|
|||

(1) Nécessite **Azure Defender pour les serveurs**

(2) Partiel


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Security Center collecte des données avec l’agent Log Analytics](security-center-enable-data-collection.md).
- Découvrez comment [Security Center gère et protège les données](security-center-data-security.md).
- Passez en revue les [plateformes qui prennent en charge Security Center](security-center-os-coverage.md).