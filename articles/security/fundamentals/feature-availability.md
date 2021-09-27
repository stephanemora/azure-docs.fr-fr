---
title: Disponibilité des fonctionnalités cloud des services Azure pour les clients du secteur public américain
description: Présente la disponibilité des fonctionnalités des services de sécurité Azure, comme Azure Sentinel pour les clients du secteur public américain
author: TerryLanfear
ms.author: terrylan
ms.service: security
ms.topic: reference
ms.date: 09/13/2021
ms.openlocfilehash: d535cff28e87de0f2dcf4afb90ab452303a6d0e0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609210"
---
# <a name="cloud-feature-availability-for-us-government-customers"></a>Disponibilité des fonctionnalités cloud pour les clients du secteur public américain

Cet article décrit la disponibilité des fonctionnalités des clouds Microsoft Azure et Azure Government pour les services de sécurité suivants :

- [Centre de sécurité Azure](#azure-security-center)
- [Azure Sentinel](#azure-sentinel)
- [Azure Defender pour IoT](#azure-defender-for-iot)

> [!NOTE]
> D'autres services de sécurité seront bientôt ajoutés à cet article.
> 

## <a name="azure-government"></a>Azure Government

Azure Government utilise les mêmes technologies sous-jacentes qu'Azure (parfois appelées Azure Commercial ou Azure Public). Celles-ci comprennent les composants de base des modèles IaaS (infrastructure-as-a-service), PaaS (Platform-as-a-service) et SaaS (Software-as-a-service). Azure et Azure Government disposent de contrôles de sécurité complets, et Microsoft s'engage à assurer la protection des données client.

Azure Government est un environnement cloud physiquement isolé dédié au secteur public des États-Unis (autorités fédérales, étatiques, locales et tribales) ainsi qu'à leurs partenaires. Les deux environnements cloud sont conformes au niveau d'impact FedRAMP High, mais Azure Government offre une couche supplémentaire de protection aux clients par le biais d'engagements contractuels en matière de stockage des données client aux États-Unis et en limitant l'accès potentiel aux systèmes traitant les données client à des personnes spécifiques basées aux États-Unis. Ces engagements peuvent intéresser les clients qui utilisent le cloud pour stocker ou traiter des données soumises aux réglementations américaines sur le contrôle des exportations, comme les réglementations EAR, ITAR et DoE 10 CFR Part 810.

Pour plus d'informations sur Azure Government, consultez [Qu'est-ce qu'Azure Government ?](../../azure-government/documentation-government-welcome.md)

## <a name="microsoft-365-integration"></a>Intégration de Microsoft 365

Les intégrations entre produits reposent sur l'interopérabilité entre les plateformes Azure et Office. Les offres hébergées dans l'environnement Azure sont accessibles à partir des plateformes Microsoft 365 Entreprise et Microsoft 365 Government. Office 365 et Office 365 GCC sont associés à Azure Active Directory (Azure AD) dans Azure. Office 365 GCC High et Office 365 DoD sont associés à Azure AD dans Azure Government.

Le diagramme suivant présente la hiérarchie des clouds Microsoft et leurs relations les uns avec les autres.

:::image type="content" source="media/microsoft-365-cloud-integration.png" alt-text="Intégration du cloud Microsoft 365.":::

L'environnement Office 365 GCC aide les clients à se conformer aux exigences du secteur public américain, comme les normes FedRAMP High, CJIS et IRS 1075. Les environnements Office 365 GCC High et DoD prennent en charge les clients qui ont besoin de se conformer aux normes DoD IL4/5, DFARS 7012, NIST 800-171 et ITAR.

Pour plus d'informations sur les environnements Office 365 US Government, consultez :

- [Office 365 GCC](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc)
- [Office 365 GCC High et DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)


Les sections suivantes répertorient les services qui disposent d'une intégration à Microsoft 365 et indiquent la disponibilité des fonctionnalités pour Office 365 GCC, Office 365 High et Office 365 DoD.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center est un système de gestion de la sécurité de l’infrastructure unifié qui renforce la posture de sécurité de vos centres de données et fournit une protection avancée contre les menaces pour vos charges de travail hybrides dans le cloud (dans Azure ou non), ainsi qu’en local.

Pour plus d’informations, consultez la [documentation produit Azure Security Center](../../security-center/security-center-introduction.md).

Le tableau suivant présente la disponibilité actuelle des fonctionnalités Security Center dans Azure et Azure Government.


| Fonctionnalité/Service                                                                                                                                                               | Azure          | Azure Government               |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|--------------------------------|
| **Fonctionnalités Security Center gratuites**                                                                                                                                             |                |                                |
| - [Exportation continue](../../security-center/continuous-export.md)                                                                                                             | GA             | GA                             |
| - [Automatisation des workflows](../../security-center/continuous-export.md)                                                                                                           | GA             | GA                             |
| - [Règles d’exemption de recommandation](../../security-center/exempt-resource.md)                                                                                                  | Version préliminaire publique | Non disponible                  | 
| - [Règles de suppression d’alerte](../../security-center/alerts-suppression-rules.md)                                                                                                | GA             | GA                             | 
| - [Notifications par e-mail pour les alertes de sécurité](../../security-center/security-center-provide-security-contact-details.md)                                                        | GA             | GA                             | 
| - [Approvisionnement automatique pour les agents et les extensions](../../security-center/security-center-enable-data-collection.md)                                                              | GA             | GA                             | 
| - [Inventaire des ressources](../../security-center/asset-inventory.md)                                                                                                                 | GA             | GA                             | 
| - [Rapports Azure Monitor des classeurs rapports dans la Galerie de classeurs Azure Security Center](../../security-center/custom-dashboards-azure-workbooks.md)                                  | GA             | GA                             | 
| **Plans et extensions Azure Defender**                                                                                                                                       |                |                                | 
| - [Azure Defender pour les serveurs](../../security-center/defender-for-servers-introduction.md)                                                                                    | GA             | GA                             | 
| - [Azure Defender pour App Service](../../security-center/defender-for-app-service-introduction.md)                                                                            | GA             | Non disponible                  | 
| - [Azure Defender pour DNS](../../security-center/defender-for-dns-introduction.md)                                                                                            | GA             | GA                             | 
| - [Azure Defender pour les registres de conteneurs](../../security-center/defender-for-container-registries-introduction.md) <sup>[1](#footnote1)</sup>                               | GA             | GA  <sup>[2](#footnote2)</sup> | 
| - [Azure Defender pour les registres de conteneurs analyse des images dans les flux de travail CI/CD](../../security-center/defender-for-container-registries-cicd.md) <sup> [3](#footnote3)</sup> | Version préliminaire publique | Non disponible                  | 
| - [Azure Defender pour Kubernetes](../../security-center/defender-for-kubernetes-introduction.md) <sup>[4](#footnote4)</sup>                                                   | GA             | GA                             | 
| - [Extension Azure Defender pour les clusters Kubernetes avec Azure Arc](../../security-center/defender-for-kubernetes-azure-arc.md) <sup>[5](#footnote5)</sup>                 | Version préliminaire publique | Non disponible                  | 
| - [Azure Defender pour les serveurs de base de données Azure SQL](../../security-center/defender-for-sql-introduction.md)                                                                     | GA             | GA                             | 
| - [Azure Defender pour les serveurs SQL sur les machines](../../security-center/defender-for-sql-introduction.md)                                                                        | GA             | GA                             |
| - [Azure Defender pour les bases de données relationnelles open source](../../security-center/defender-for-databases-introduction.md)                                                         | GA             | Non disponible                  |
| - [Azure Defender pour Key Vault](../../security-center/defender-for-key-vault-introduction.md)                                                                                | GA             | Non disponible                  |
| - [Azure Defender pour Resource Manager](../../security-center/defender-for-resource-manager-introduction.md)                                                                  | GA             | GA                             |
| - [Azure Defender pour Stockage](../../security-center/defender-for-storage-introduction.md) <sup>[6](#footnote6)</sup>                                                         | GA             | GA                             |
| - [Protection contre les menaces pour Cosmos DB](../../security-center/other-threat-protections.md#threat-protection-for-azure-cosmos-db-preview)                                          | Version préliminaire publique | Non disponible                  |
| - [Protection de charge de travail Kubernetes](../../security-center/kubernetes-workload-protections.md)                                                                                  | GA             | GA                             |
| - [Synchronisation d’alerte bidirectionnelle avec Sentinel](../../sentinel/connect-azure-security-center.md)                                                                       | Version préliminaire publique | Non disponible                  | 
| **Fonctionnalités Azure Defender pour les serveurs** <sup>[7](#footnote7)</sup>                                                                                                            |                |                                |
| - [Accès juste-à-temps aux machines virtuelles](../../security-center/security-center-just-in-time.md)                                                                                             | GA             | GA                             |
| - [Supervision de l’intégrité des fichiers](../../security-center/security-center-file-integrity-monitoring.md)                                                                             | GA             | GA                             |
| - [Contrôles d’application adaptatifs](../../security-center/security-center-adaptive-application.md)                                                                              | GA             | GA                             |
| - [Sécurisation renforcée du réseau adaptative](../../security-center/security-center-adaptive-network-hardening.md)                                                                           | GA             | Non disponible                  |
| - [Sécurisation renforcée de l’hôte Docker](../../security-center/harden-docker-hosts.md)                                                                                                       | GA             | GA                             |
| - [Évaluation des vulnérabilités intégrée pour les machines](../../security-center/deploy-vulnerability-assessment-vm.md)                                                             | GA             | Non disponible                  |
| - [Tableau de bord et rapports de conformité réglementaire](../../security-center/security-center-compliance-dashboard.md) <sup>[8](#footnote8)</sup>                                       | GA             | GA                             |
| - [Déploiement de Microsoft Defender pour point de terminaison et licence intégrée](../../security-center/security-center-wdatp.md)                                                         | GA             | GA                             |
| - [Connecter un compte AWS](../../security-center/quickstart-onboard-aws.md)                                                                                                      | GA             | Non disponible                  |
| - [Connecter un compte GCP](../../security-center/quickstart-onboard-gcp.md)                                                                                                      | GA             | Non disponible                  |
|                                                                                                                                                                               |                |                                |

<sup><a name="footnote1" /></a>1</sup> Partiellement GA : la possibilité de désactiver des résultats spécifiques à partir d’analyses de vulnérabilités est disponible en préversion publique.

<sup><a name="footnote2" /></a>2</sup> Les analyses de vulnérabilité des registres de conteneurs sur Azure Gov ne peuvent uniquement être effectuées avec la fonctionnalité d’analyse à l’envoi (push).

<sup><a name="footnote3" /></a>3</sup> Requiert Azure Defender pour les registres de conteneurs.

<sup><a name="footnote4" /></a>4</sup> Partiellement GA : la prise en charge des clusters avec Arc est disponible en préversion publique et pas sur Azure Government.

<sup><a name="footnote5" /></a>5</sup> Requiert Azure Defender pour Kubernetes.

<sup><a name="footnote6" /></a>6</sup> Partiellement GA : certaines des alertes de protection contre les menaces d’Azure Defender pour Stockage sont disponibles en préversion publique.

<sup><a name="footnote7" /></a>7</sup> Ces fonctionnalités requièrent toutes [Azure Defender pour les serveurs](../../security-center/defender-for-servers-introduction.md).

<sup><a name="footnote8" /></a>8</sup> Des différences sont possible en termes de normes offertes par type de cloud.

## <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel est une solution native Cloud et évolutive de type SIEM (Security Information and Event Management) et SOAR (Security Orchestrated Automated Response). Azure Sentinel assure une analyse de sécurité intelligente et fournit des informations sur les menaces dans l’ensemble de l’entreprise. Elle constitue une solution unique pour la détection des alertes, la visibilité des menaces, la chasse proactive et la réponse face aux menaces.

Pour plus d'informations, consultez la [documentation produit d'Azure Sentinel](../../sentinel/overview.md).

Les tableaux suivants indiquent la disponibilité actuelle des fonctionnalités Azure Sentinel dans Azure et Azure Government.


| Fonctionnalité | Azure | Azure Government  |
| ----- | ----- | ---- |
|- [Règles d’automatisation](../../sentinel/automate-incident-handling-with-automation-rules.md) | Version préliminaire publique | Version préliminaire publique |
|- [BYO-ML (Bring Your Own ML)](../../sentinel/bring-your-own-ml.md) | Version préliminaire publique | Version préliminaire publique |
| - [Vue des incidents inter-clients/inter-espaces de travail](../../sentinel/multiple-workspace-view.md) |Version préliminaire publique | Version préliminaire publique |
| - [Entity Insights](../../sentinel/enable-entity-behavior-analytics.md) | GA | Version préliminaire publique |
| - [Fusion](../../sentinel/fusion.md)<br>Détection avancée des attaques en plusieurs étapes <sup>[1](#footnote1)</sup> | GA | GA |
| - [Chasse](../../sentinel/hunting.md) | GA | GA |
|- [Notebooks](../../sentinel/notebooks.md) | GA | GA |
|- [Métriques d'audit des incidents SOC](../../sentinel/manage-soc-with-incident-metrics.md) | GA | GA |
|- [Watchlists](../../sentinel/watchlists.md) | GA | GA |
| **Support Threat Intelligence** | | |
| - [Connecteur de données Threat Intelligence – TAXII](../../sentinel/understand-threat-intelligence.md)  | Version préliminaire publique | Non disponible |
| - [Connecteur de données Threat Intelligence Platform](../../sentinel/understand-threat-intelligence.md)  | Version préliminaire publique | Non disponible |
| - [Panneau de recherche Threat Intelligence](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-threat-intelligence-menu-item-in-public-preview/ba-p/1646597)  | Version préliminaire publique | Non disponible |
| - [URL Detonation](https://techcommunity.microsoft.com/t5/azure-sentinel/using-the-new-built-in-url-detonation-in-azure-sentinel/ba-p/996229) | Version préliminaire publique | Non disponible |
| - [Classeur Threat Intelligence](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)  | GA | Non disponible |
|**Support lié à la détection** | | |
| - [Détection d'un accès anormal au partage de fichiers de Windows](../../sentinel/fusion.md)  | Version préliminaire publique | Non disponible |
| - [Détection de connexions RDP anormales](../../sentinel/connect-windows-security-events.md#configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection)<br>Détection ML intégrée | Version préliminaire publique | Non disponible |
| - [Détection de connexions SSH anormales](../../sentinel/connect-syslog.md#configure-the-syslog-connector-for-anomalous-ssh-login-detection)<br>Détection ML intégrée | Version préliminaire publique | Non disponible |
| **Connecteurs de service Azure** |  |  |
| - [Journaux d'activité Azure](../../sentinel/data-connectors-reference.md#azure-activity) | GA | GA |
| - [Azure Active Directory](../../sentinel/connect-azure-active-directory.md) | GA | GA |
| - [Azure ADIP](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection) | GA | GA |
| - [Azure DDoS Protection](../../sentinel/data-connectors-reference.md#azure-ddos-protection) | GA | GA |
| - [Azure Defender](../../sentinel/connect-azure-security-center.md) | GA | GA |
| - [Azure Defender pour IoT](../../sentinel/data-connectors-reference.md#azure-defender-for-iot) | Version préliminaire publique | Non disponible |
| - [Pare-feu Azure ](../../sentinel/data-connectors-reference.md#azure-firewall) | GA | GA |
| - [Azure Information Protection](../../sentinel/data-connectors-reference.md#azure-information-protection) | Version préliminaire publique | Non disponible |
| - [Azure Key Vault ](../../sentinel/data-connectors-reference.md#azure-key-vault) | Version préliminaire publique | Non disponible |
| - [Azure Kubernetes Services (AKS)](../../sentinel/data-connectors-reference.md#azure-kubernetes-service-aks) | Version préliminaire publique | Non disponible |
| - [Azure SQL Databases](../../sentinel/data-connectors-reference.md#azure-sql-databases) | GA | GA |
| - [Pare-feu d'applications web Azure](../../sentinel/data-connectors-reference.md#azure-web-application-firewall-waf) | GA | GA |
| **Connecteurs Windows** |  |  |
| - [Pare-feu Windows](../../sentinel/data-connectors-reference.md#windows-firewall) | GA | GA |
| - [Événements de sécurité Windows](../../sentinel/connect-windows-security-events.md) | GA | GA |
| **Connecteurs externes** |  |  |
| - [Agari Phishing Defense and Brand Protection](../../sentinel/data-connectors-reference.md#agari-phishing-defense-and-brand-protection-preview) | Version préliminaire publique | Version préliminaire publique |
| - [AI Analyst Darktrace](../../sentinel/connect-data-sources.md) | Version préliminaire publique | Version préliminaire publique |
| - [AI Vectra Detect](../../sentinel/data-connectors-reference.md#ai-vectra-detect-preview) | Version préliminaire publique | Version préliminaire publique |
| - [Akamai Security Events](../../sentinel/data-connectors-reference.md#akamai-security-events-preview) | Version préliminaire publique | Version préliminaire publique |
| - [Alcide kAudit](../../sentinel/data-connectors-reference.md#alcide-kaudit) | Version préliminaire publique | Non disponible |
| - [Alsid pour Active Directory](../../sentinel/data-connectors-reference.md#alsid-for-active-directory) | Version préliminaire publique | Non disponible |
| - [Apache HTTP Server](../../sentinel/data-connectors-reference.md#apache-http-server) | Version préliminaire publique | Non disponible |
| - [Aruba ClearPass](../../sentinel/data-connectors-reference.md#aruba-clearpass-preview) | Version préliminaire publique | Version préliminaire publique |
| - [AWS](../../sentinel/connect-data-sources.md) | GA | GA |
| - [Barracuda CloudGen Firewall](../../sentinel/data-connectors-reference.md#barracuda-cloudgen-firewall) | GA | GA |
| - [Barracuda Web App Firewall](../../sentinel/data-connectors-reference.md#barracuda-waf) | GA | GA |
| - [BETTER Mobile Threat Defense MTD](../../sentinel/data-connectors-reference.md#better-mobile-threat-defense-mtd-preview) | Version préliminaire publique | Non disponible |
| - [Beyond Security beSECURE](../../sentinel/data-connectors-reference.md#beyond-security-besecure) | Version préliminaire publique | Non disponible |
| - [Blackberry CylancePROTECT](../../sentinel/connect-data-sources.md) | Version préliminaire publique | Version préliminaire publique |
| - [Broadcom Symantec DLP](../../sentinel/data-connectors-reference.md#broadcom-symantec-data-loss-prevention-dlp-preview) | Version préliminaire publique | Version préliminaire publique |
| - [Check Point](../../sentinel/data-connectors-reference.md#check-point) | GA | GA |
| - [Cisco ASA](../../sentinel/data-connectors-reference.md#cisco-asa) | GA | GA |
| - [Cisco Meraki](../../sentinel/data-connectors-reference.md#cisco-meraki-preview) | Version préliminaire publique | Version préliminaire publique |
| - [Cisco Umbrella](../../sentinel/data-connectors-reference.md#cisco-umbrella-preview) | Version préliminaire publique | Version préliminaire publique |
| - [Cisco UCS](../../sentinel/data-connectors-reference.md#cisco-unified-computing-system-ucs-preview) | Version préliminaire publique | Version préliminaire publique |
| - [Cisco Firepower EStreamer](../../sentinel/connect-data-sources.md) | Version préliminaire publique | Version préliminaire publique |
| - [Citrix Analytics WAF](../../sentinel/data-connectors-reference.md#citrix-web-app-firewall-waf-preview) | GA | GA |
| - [Common Event Format (CEF)](../../sentinel/connect-common-event-format.md) | GA | GA |
| - [CyberArk Enterprise Password Vault (EPV) Events](../../sentinel/data-connectors-reference.md#cyberark-enterprise-password-vault-epv-events-preview) | Version préliminaire publique | Version préliminaire publique |
| - [ESET Enterprise Inspector](../../sentinel/connect-data-sources.md)                       | Version préliminaire publique | Non disponible      |
| - [Eset Security Management Center](../../sentinel/connect-data-sources.md)                  | Version préliminaire publique | Non disponible      |
| - [ExtraHop Reveal(x)](../../sentinel/data-connectors-reference.md#extrahop-revealx)                               | GA             | GA             |
| - [F5 BIG-IP ](../../sentinel/data-connectors-reference.md#f5-big-ip)                                       | GA             | GA             |
| - [F5 Networks](../../sentinel/data-connectors-reference.md#f5-networks-asm)                                     | GA             | GA             |
| - [Forcepoint NGFW](../../sentinel/data-connectors-reference.md#forcepoint-cloud-access-security-broker-casb-preview)                                  | Version préliminaire publique | Version préliminaire publique |
| - [Forcepoint CASB](../../sentinel/data-connectors-reference.md#forcepoint-cloud-access-security-broker-casb-preview)                                  | Version préliminaire publique | Version préliminaire publique |
| - [Forcepoint DLP ](../../sentinel/data-connectors-reference.md#forcepoint-data-loss-prevention-dlp-preview)                                   | Version préliminaire publique | Non disponible      |
| - [ForgeRock Common Audit for CEF](../../sentinel/connect-data-sources.md)                  | Version préliminaire publique | Version préliminaire publique |
| - [Fortinet](../../sentinel/data-connectors-reference.md#fortinet)                                         | GA             | GA             |
| - [Google Workspace (G Suite) ](../../sentinel/data-connectors-reference.md#google-workspace-g-suite-preview)                      | Version préliminaire publique | Non disponible      |
| - [Illusive Attack Management System](../../sentinel/data-connectors-reference.md#illusive-attack-management-system-ams-preview)                | Version préliminaire publique | Version préliminaire publique |
| - [Imperva WAF Gateway](../../sentinel/data-connectors-reference.md#imperva-waf-gateway-preview)                             | Version préliminaire publique | Version préliminaire publique |
| - [Infoblox NIOS](../../sentinel/data-connectors-reference.md#infoblox-network-identity-operating-system-nios-preview)                                    | Version préliminaire publique | Version préliminaire publique |
| - [Juniper SRX](../../sentinel/data-connectors-reference.md#juniper-srx-preview)                                      | Version préliminaire publique | Version préliminaire publique |
| - [Morphisec UTPP](../../sentinel/connect-data-sources.md)                                   | Version préliminaire publique | Version préliminaire publique |
| - [Netskope](../../sentinel/connect-data-sources.md)                                         | Version préliminaire publique | Version préliminaire publique |
| - [NXLog Windows DNS](../../sentinel/data-connectors-reference.md#nxlog-dns-logs-preview)                                             | Version préliminaire publique | Non disponible      |
| - [NXLog LinuxAudit](../../sentinel/data-connectors-reference.md#nxlog-linuxaudit-preview)                                 | Version préliminaire publique | Non disponible      |
| - [Okta Single Sign On](../../sentinel/data-connectors-reference.md#okta-single-sign-on-preview)                              | Version préliminaire publique | Version préliminaire publique |
| - [Onapsis Platform](../../sentinel/connect-data-sources.md)                                 | Version préliminaire publique | Version préliminaire publique |
| - [One Identity Safeguard](../../sentinel/data-connectors-reference.md#one-identity-safeguard-preview)                          | GA             | GA             |
| - [Orca Security Alerts](../../sentinel/data-connectors-reference.md#orca-security-preview)                            | Version préliminaire publique | Non disponible      |
| - [Palo Alto Networks](../../sentinel/data-connectors-reference.md#palo-alto-networks)                               | GA             | GA             |
| - [Perimeter 81 Activity Logs](../../sentinel/data-connectors-reference.md#perimeter-81-activity-logs-preview)                      | GA             | Non disponible      |
| - [Proofpoint On Demand Email Security](../../sentinel/data-connectors-reference.md#proofpoint-on-demand-pod-email-security-preview)             | Version préliminaire publique | Non disponible      |
| - [Proofpoint TAP](../../sentinel/data-connectors-reference.md#proofpoint-targeted-attack-protection-tap-preview)                                   | Version préliminaire publique | Version préliminaire publique |
| - [Pulse Connect Secure](../../sentinel/data-connectors-reference.md#proofpoint-targeted-attack-protection-tap-preview)                             | Version préliminaire publique | Version préliminaire publique |
| - [Qualys Vulnerability Management](../../sentinel/data-connectors-reference.md#qualys-vulnerability-management-vm-preview)                  | Version préliminaire publique | Version préliminaire publique |
| - [Salesforce Service Cloud](../../sentinel/data-connectors-reference.md#salesforce-service-cloud-preview)                         | Version préliminaire publique | Non disponible      |
| - [SonicWall Firewall ](../../sentinel/data-connectors-reference.md#sophos-cloud-optix-preview)                              | Version préliminaire publique | Version préliminaire publique |
| - [Sophos Cloud Optix](../../sentinel/data-connectors-reference.md#sophos-cloud-optix-preview)                               | Version préliminaire publique | Non disponible      |
| - [Sophos XG Firewall](../../sentinel/data-connectors-reference.md#sophos-xg-firewall-preview)                               | Version préliminaire publique | Version préliminaire publique |
| - [Squadra Technologies secRMM](../../sentinel/data-connectors-reference.md#squadra-technologies-secrmm)               | GA             | GA             |
| - [Squid Proxy](../../sentinel/data-connectors-reference.md#squid-proxy-preview)                                      | Version préliminaire publique | Non disponible      |
| - [Symantec Integrated Cyber Defense Exchange](../../sentinel/data-connectors-reference.md#symantec-integrated-cyber-defense-exchange-icdx)       | GA             | GA             |
| - [Symantec ProxySG](../../sentinel/data-connectors-reference.md#symantec-proxysg-preview)                                | Version préliminaire publique | Version préliminaire publique |
| - [Symantec VIP](../../sentinel/data-connectors-reference.md#symantec-vip-preview)                                     | Version préliminaire publique | Version préliminaire publique |
| - [Syslog](../../sentinel/connect-syslog.md)                                           | GA             | GA             |
| - [Threat Intelligence Platform](../../sentinel/connect-threat-intelligence-tip.md)s                   | Version préliminaire publique | Non disponible      |
| - [Threat Intelligence TAXII](../../sentinel/connect-threat-intelligence-tip.md)                       | Version préliminaire publique | Non disponible      |
| - [Thycotic Secret Server](../../sentinel/data-connectors-reference.md#thycotic-secret-server-preview)                          | Version préliminaire publique | Version préliminaire publique |
| - [Trend Micro Deep Security](../../sentinel/data-connectors-reference.md#trend-micro-deep-security)                       | GA             | GA             |
| - [Trend Micro TippingPoint](../../sentinel/data-connectors-reference.md#trend-micro-tippingpoint-preview)                         | Version préliminaire publique | Version préliminaire publique |
| - [Trend Micro XDR](../../sentinel/connect-data-sources.md)                                  | Version préliminaire publique | Non disponible      |
| - [VMware Carbon Black Endpoint Standard](../../sentinel/data-connectors-reference.md#vmware-carbon-black-endpoint-standard-preview)           | Version préliminaire publique | Version préliminaire publique |
| - [VMware ESXi](../../sentinel/data-connectors-reference.md#vmware-esxi-preview)                                      | Version préliminaire publique | Version préliminaire publique |
| - [WireX Network Forensics Platform](../../sentinel/data-connectors-reference.md#wirex-network-forensics-platform-preview)                | Version préliminaire publique | Version préliminaire publique |
| - [Zimperium Mobile Threat Defense](../../sentinel/data-connectors-reference.md#zimperium-mobile-thread-defense-preview)                  | Version préliminaire publique | Non disponible      |
| - [Zscaler](../../sentinel/data-connectors-reference.md#zscaler)                                         | GA             | GA             |
| | | |


<sup><a name="footnote1" /></a>1</sup> Les détections SSH et RDP ne sont pas prises en charge pour les clouds souverains car la plateforme Databricks ML n'est pas disponible.

### <a name="microsoft-365-data-connectors"></a>Connecteurs de données Microsoft 365

Office 365 GCC est associé à Azure Active Directory (Azure AD) dans Azure. Office 365 GCC High et Office 365 DoD sont associés à Azure AD dans Azure Government.

> [!TIP]
> Veillez à prêter attention à l'environnement Azure pour déterminer où l'[interopérabilité est possible](#microsoft-365-integration). Dans le tableau suivant, l'interopérabilité qui n'est *pas* possible est signalée par un tiret (-) pour indiquer que la prise en charge n'est pas pertinente.
>

| Connecteur | Azure | Azure Government |
|--|--|--|
| **[Dynamics365](../../sentinel/data-connectors-reference.md#dynamics-365)** |  |  |
| - Office 365 GCC | Version préliminaire publique | - |
| - Office 365 GCC High | - | Non disponible |
| - Office 365 DoD | - | Non disponible |
| **[Microsoft 365 Defender](../../sentinel/connect-microsoft-365-defender.md)** |  |  |
| - Office 365 GCC | Version préliminaire publique | - |
| - Office 365 GCC High | - | Non disponible |
| - Office 365 DoD | - | Non disponible |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/data-connectors-reference.md#microsoft-cloud-app-security-mcas)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/data-connectors-reference.md#microsoft-cloud-app-security-mcas)** <br>Journaux d'informatique fantôme |  |  |
| - Office 365 GCC | Version préliminaire publique | - |
| - Office 365 GCC High | - | Version préliminaire publique |
| - Office 365 DoD | - | Version préliminaire publique |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/data-connectors-reference.md#microsoft-cloud-app-security-mcas)**                  <br>Alertes |  |  |
| - Office 365 GCC | Version préliminaire publique | - |
| - Office 365 GCC High | - | Version préliminaire publique |
| - Office 365 DoD | - | Version préliminaire publique |
| **[Microsoft Defender for Endpoint](../../sentinel/data-connectors-reference.md#microsoft-defender-for-endpoint)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
| **[Microsoft Defender pour Identity](../../sentinel/data-connectors-reference.md#microsoft-defender-for-identity)** |  |  |
| - Office 365 GCC | Version préliminaire publique | - |
| - Office 365 GCC High | - | Non disponible |
| - Office 365 DoD | - | Non disponible |
| **[Microsoft Defender pour Office 365](../../sentinel/data-connectors-reference.md#microsoft-defender-for-office-365)** |  |  |
| - Office 365 GCC | Version préliminaire publique | - |
| - Office 365 GCC High | - | Non disponible |
| - Office 365 DoD | - | Non disponible |
| **[Office 365](../../sentinel/data-connectors-reference.md#microsoft-office-365)** |  |  |
| - Office 365 GCC | GA | - |
| - Office 365 GCC High | - | GA |
| - Office 365 DoD | - | GA |
|  |  |

## <a name="azure-defender-for-iot"></a>Azure Defender pour IoT

Azure Defender pour IoT vous permet d’accélérer l’innovation IoT/OT avec une sécurité complète sur l’ensemble de vos appareils IoT/OT.Pour les organisations d’utilisateurs finaux, Azure Defender pour IoT offre une sécurité sans agent, au niveau de la couche réseau, qui est rapidement déployée, fonctionne avec divers équipements industriels et interagit avec Azure Sentinel et d’autres outils SOC. La solution peut être déployée localement ou dans des environnements connectés à Azure.Pour les fabricants d’appareils IoT, les agents de sécurité Azure Defender pour IoT vous permettent d’intégrer la sécurité directement dans vos nouveaux appareils IoT et projets Azure IoT. Le micro-agent dispose d’options de déploiement souples, notamment la possibilité de le déployer sous forme de package binaire ou de modifier le code source. Le micro-agent est également disponible pour les systèmes d’exploitation IoT standard tels que Linux et Azure RTOS. Pour plus d’informations, consultez la [documentation produit Azure Defender pour IoT](../../defender-for-iot/index.yml).

Le tableau suivant présente la disponibilité actuelle des fonctionnalités Azure Defender pour IoT dans Azure et Azure Government.

### <a name="for-organizations"></a>Pour les entreprises

| Fonctionnalité | Azure | Azure Government |
|--|--|--|
| [Inventaire et détection des appareils locaux](../../defender-for-iot/how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md) | GA | GA |
| [Gestion des vulnérabilités](../../defender-for-iot/how-to-create-risk-assessment-reports.md) | GA | GA |
| [Détection des menaces avec IoT et analyse comportementale OT](../../defender-for-iot/how-to-work-with-alerts-on-your-sensor.md) | GA | GA |
| [Mises à jour automatiques de Threat Intelligence](../../defender-for-iot/how-to-work-with-threat-intelligence-packages.md) | GA | GA |
| **Unifier la sécurité OT et IT avec SIEM, SOAR et XDR** |  |  |
| - [Transférer les informations d’alerte](../../defender-for-iot/how-to-forward-alert-information-to-partners.md) | GA | GA |
| - [Configurer Sentinel avec Azure Defender pour IoT](../../defender-for-iot/how-to-configure-with-sentinel.md) | Préversion publique | Non disponible |
| - [Systèmes SOC](../../defender-for-iot/integration-splunk.md) | GA | GA |
| - [Système de tickets et CMDB (Service Now)](../../defender-for-iot/integration-servicenow.md) | GA | GA |
| - [Provisionnement de capteur](../../defender-for-iot/how-to-manage-sensors-on-the-cloud.md) | GA | GA |

### <a name="for-device-builders"></a>Pour les fabricants d’appareils

| Fonctionnalité | Azure | Azure Government |
|--|--|--|
| [Micro-agent pour Azure RTOS](../../defender-for-iot/iot-security-azure-rtos.md) | GA | GA |
| - [Configurer Sentinel avec Azure Defender pour IoT](../../defender-for-iot/how-to-configure-with-sentinel.md) | GA | Non disponible |
| **Micro-agent autonome pour Linux** |  |  |
| - [Vue d’ensemble du micro-agent autonome](../../defender-for-iot/concept-standalone-micro-agent-overview.md) | Version préliminaire publique | Version préliminaire publique |
| - [Installation du fichier binaire de l’agent autonome](../../defender-for-iot/quickstart-standalone-agent-binary-installation.md) | Version préliminaire publique | Version préliminaire publique |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez le modèle de [responsabilité partagée](shared-responsibility.md) et apprenez à distinguer les tâches de sécurité qui dépendent du fournisseur de cloud de celles qui vous incombent.
- Découvrez les fonctionnalités [Azure Government Cloud](../../azure-government/documentation-government-welcome.md) ainsi que la conception et la sécurité sans faille utilisées pour prendre en charge la conformité applicable aux organisations gouvernementales fédérales, d'état et locales et à leurs partenaires.
- Découvrez le [plan Office 365 Government](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/office-365-us-government#about-office-365-government-environments).
- Découvrez la [conformité Azure](../../compliance/index.yml) pour les normes légales et réglementaires.