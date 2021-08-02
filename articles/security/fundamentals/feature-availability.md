---
title: Disponibilité des fonctionnalités cloud des services Azure pour les clients du secteur public américain
description: Présente la disponibilité des fonctionnalités des services de sécurité Azure, comme Azure Sentinel pour les clients du secteur public américain
author: TerryLanfear
ms.author: terrylan
ms.service: security
ms.topic: reference
ms.date: 05/23/2021
ms.openlocfilehash: 817e23bfe21aeabde51064cd2606fa447ee6de22
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112060009"
---
# <a name="cloud-feature-availability-for-us-government-customers"></a>Disponibilité des fonctionnalités cloud pour les clients du secteur public américain


Cet article décrit la disponibilité des fonctionnalités des clouds Microsoft Azure et Azure Government pour les services de sécurité suivants :

- [Azure Sentinel](#azure-sentinel)

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

## <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel est une solution native Cloud et évolutive de type SIEM (Security Information and Event Management) et SOAR (Security Orchestrated Automated Response). Azure Sentinel assure une analyse de sécurité intelligente et fournit des informations sur les menaces dans l’ensemble de l’entreprise. Elle constitue une solution unique pour la détection des alertes, la visibilité des menaces, la chasse proactive et la réponse face aux menaces.

Pour plus d'informations, consultez la [documentation produit d'Azure Sentinel](../../sentinel/overview.md).

Les tableaux suivants indiquent la disponibilité actuelle des fonctionnalités Azure Sentinel dans Azure et Azure Government.


| Fonctionnalité | Azure | Azure Government  |
| ----- | ----- | ---- |
|- [BYO-ML (Bring Your Own ML)](../../sentinel/bring-your-own-ml.md) | Version préliminaire publique | Version préliminaire publique |
| - [Vue des incidents inter-clients/inter-espaces de travail](../../sentinel/multiple-workspace-view.md) |Version préliminaire publique | Version préliminaire publique |
| - [Entity Insights](../../sentinel/enable-entity-behavior-analytics.md) | GA | Version préliminaire publique |
| - [Fusion](../../sentinel/fusion.md)<br>Détection avancée des attaques en plusieurs étapes <sup>[1](#footnote1)</sup> | GA | GA |
| - [Chasse](../../sentinel/hunting.md) | GA | GA |
|- [Notebooks](../../sentinel/notebooks.md) | GA | GA |
|- [Métriques d'audit des incidents SOC](../../sentinel/manage-soc-with-incident-metrics.md) | GA | GA |
|- [Watchlists](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-watchlist-is-now-in-public-preview/ba-p/1765887) | Version préliminaire publique | Non disponible |
| **Support Threat Intelligence** | | |
| - [Connecteur de données Threat Intelligence – TAXII](../../sentinel/import-threat-intelligence.md)  | Version préliminaire publique | Non disponible |
| - [Connecteur de données Threat Intelligence Platform](../../sentinel/import-threat-intelligence.md)  | Version préliminaire publique | Non disponible |
| - [Panneau de recherche Threat Intelligence](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-threat-intelligence-menu-item-in-public-preview/ba-p/1646597)  | Version préliminaire publique | Non disponible |
| - [URL Detonation](https://techcommunity.microsoft.com/t5/azure-sentinel/using-the-new-built-in-url-detonation-in-azure-sentinel/ba-p/996229) | GA | Non disponible |
| - [Classeur Threat Intelligence](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)  | GA | Non disponible |
|**Support lié à la détection** | | |
| - [Détection d'un accès anormal au partage de fichiers de Windows](../../sentinel/fusion.md)  | Version préliminaire publique | Non disponible |
| - [Détection de connexions RDP anormales](../../sentinel/connect-windows-security-events.md#configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection)<br>Détection ML intégrée | Version préliminaire publique | Non disponible |
| - [Détection de connexions SSH anormales](../../sentinel/connect-syslog.md#configure-the-syslog-connector-for-anomalous-ssh-login-detection)<br>Détection ML intégrée | Version préliminaire publique | Non disponible |
| **Connecteurs de service Azure** | | |
| - [Journaux d'activité Azure](../../sentinel/connect-azure-activity.md)                                  |   GA           |    GA         |
| - [Azure Active Directory](../../sentinel/connect-azure-active-directory.md)                |      GA        |       GA        |
| - [Azure ADIP](../../sentinel/connect-azure-ad-identity-protection.md)                         |  GA            |        GA              |
| - [Azure DDoS Protection](../../sentinel/connect-azure-ddos-protection.md)                |     GA         |       GA               |
| - [Azure Defender](../../sentinel/connect-azure-security-center.md)                  |    GA          |        GA              |
| - [Azure Defender pour IoT](../../sentinel/connect-asc-iot.md)           |       GA       |  Non disponible           |
| - [Pare-feu Azure ](../../sentinel/connect-azure-firewall.md)                        |   GA           |        GA              |
| - [Azure Information Protection](../../sentinel/connect-azure-information-protection.md)              |     Version préliminaire publique         |         Non disponible             |
| - [Azure Key Vault ](../../sentinel/connect-azure-key-vault.md)                           |       Version préliminaire publique         |         Non disponible                       |
| - [Azure Kubernetes Services (AKS)](../../sentinel/connect-azure-kubernetes-service.md)           |       Version préliminaire publique         |         Non disponible                |
| - [Azure SQL Databases](../../sentinel/connect-azure-sql-logs.md)                        |     GA         |         GA             |
| - [Pare-feu d'applications web Azure](../../sentinel/connect-azure-waf.md)                                  |      GA        |      GA                |
| **Connecteurs Windows** | | |
| - [Pare-feu Windows](../../sentinel/connect-windows-firewall.md)                                 |     GA         |   GA           |
| - [Événements de sécurité Windows](../../sentinel/connect-windows-security-events.md)                                  |      GA        |         GA     |
| **Connecteurs externes**| | |
| - [Agari Phishing Defense and Brand Protection](../../sentinel/connect-agari-phishing-defense.md)       | Version préliminaire publique | Version préliminaire publique |
| - [AI Analyst Darktrace](../../sentinel/connect-data-sources.md)                            | Version préliminaire publique | Version préliminaire publique |
| - [AI Vectra Detect](../../sentinel/connect-ai-vectra-detect.md)                                 | Version préliminaire publique | Version préliminaire publique |
| - [Akamai Security Events](../../sentinel/connect-akamai-security-events.md)                           | Version préliminaire publique | Version préliminaire publique |
| - [Alcide kAudit](../../sentinel/connect-alcide-kaudit.md)                                   | Version préliminaire publique | Non disponible      |
| - [Alsid pour Active Directory](../../sentinel/connect-alsid-active-directory.md)                      | Version préliminaire publique | Non disponible      |
| - [Apache HTTP Server](../../sentinel/connect-apache-http-server.md)                               | Version préliminaire publique | Non disponible      |
| - [Aruba ClearPass](../../sentinel/connect-aruba-clearpass.md)                                  | Version préliminaire publique | Version préliminaire publique |
| - [AWS](../../sentinel/connect-data-sources.md)                                             | GA             | GA             |
| - [Barracuda CloudGen Firewall](../../sentinel/connect-barracuda-cloudgen-firewall.md)                      | GA             | GA             |
| - [Barracuda Web App Firewall](../../sentinel/connect-barracuda.md)                       | GA             | GA             |
| - [BETTER Mobile Threat Defense MTD](../../sentinel/connect-better-mtd.md)                 | Version préliminaire publique | Non disponible      |
| - [Beyond Security beSECURE](../../sentinel/connect-besecure.md)                        | Version préliminaire publique | Non disponible      |
| - [Blackberry CylancePROTECT](../../sentinel/connect-data-sources.md)                        | Version préliminaire publique | Version préliminaire publique |
| - [Broadcom Symantec DLP](../../sentinel/connect-broadcom-symantec-dlp.md)                            | Version préliminaire publique | Version préliminaire publique |
| - [Check Point](../../sentinel/connect-checkpoint.md)                                      | GA             | GA             |
| - [Cisco ASA](../../sentinel/connect-cisco.md)                                        | GA             | GA             |
| - [Cisco Meraki](../../sentinel/connect-cisco-meraki.md)                                     | Version préliminaire publique | Version préliminaire publique |
| - [Cisco Umbrella](../../sentinel/connect-cisco-umbrella.md)                                   | Version préliminaire publique | Version préliminaire publique |
| - [Cisco UCS](../../sentinel/connect-cisco-ucs.md)                                        | Version préliminaire publique | Version préliminaire publique |
| - [Cisco Firepower EStreamer](../../sentinel/connect-data-sources.md)                          | Version préliminaire publique | Version préliminaire publique |
| - [Citrix Analytics WAF](../../sentinel/connect-citrix-waf.md)                             | GA             | GA             |
| - [Common Event Format (CEF)](../../sentinel/connect-common-event-format.md)                        | GA             | GA             |
| - [CyberArk Enterprise Password Vault (EPV) Events](../../sentinel/connect-cyberark.md) | Version préliminaire publique | Version préliminaire publique |
| - [ESET Enterprise Inspector](../../sentinel/connect-data-sources.md)                       | Version préliminaire publique | Non disponible      |
| - [Eset Security Management Center](../../sentinel/connect-data-sources.md)                  | Version préliminaire publique | Non disponible      |
| - [ExtraHop Reveal(x)](../../sentinel/connect-extrahop.md)                               | GA             | GA             |
| - [F5 BIG-IP ](../../sentinel/connect-f5-big-ip.md)                                       | GA             | GA             |
| - [F5 Networks](../../sentinel/connect-f5.md)                                     | GA             | GA             |
| - [Forcepoint NGFW](../../sentinel/connect-forcepoint-casb-ngfw.md)                                  | Version préliminaire publique | Version préliminaire publique |
| - [Forepoint CASB](../../sentinel/connect-forcepoint-casb-ngfw.md)                                  | Version préliminaire publique | Version préliminaire publique |
| - [Forepoint DLP ](../../sentinel/connect-forcepoint-dlp.md)                                   | Version préliminaire publique | Non disponible      |
| - [ForgeRock Common Audit for CEF](../../sentinel/connect-data-sources.md)                  | Version préliminaire publique | Version préliminaire publique |
| - [Fortinet](../../sentinel/connect-fortinet.md)                                         | GA             | GA             |
| - [Google Workspace (G Suite) ](../../sentinel/connect-google-workspace.md)                      | Version préliminaire publique | Non disponible      |
| - [Illusive Attack Management System](../../sentinel/connect-illusive-attack-management-system.md)                | Version préliminaire publique | Version préliminaire publique |
| - [Imperva WAF Gateway](../../sentinel/connect-imperva-waf-gateway.md)                             | Version préliminaire publique | Version préliminaire publique |
| - [Infoblox NIOS](../../sentinel/connect-infoblox.md)                                    | Version préliminaire publique | Version préliminaire publique |
| - [Juniper SRX](../../sentinel/connect-juniper-srx.md)                                      | Version préliminaire publique | Version préliminaire publique |
| - [Morphisec UTPP](../../sentinel/connect-data-sources.md)                                   | Version préliminaire publique | Version préliminaire publique |
| - [Netskope](../../sentinel/connect-data-sources.md)                                         | Version préliminaire publique | Version préliminaire publique |
| - [NXLog Windows DNS](../../sentinel/connect-nxlog-dns.md)                                             | Version préliminaire publique | Non disponible      |
| - [NXLog LinuxAudit](../../sentinel/connect-nxlog-linuxaudit.md)                                 | Version préliminaire publique | Non disponible      |
| - [Okta Single Sign On](../../sentinel/connect-okta-single-sign-on.md)                              | Version préliminaire publique | Version préliminaire publique |
| - [Onapsis Platform](../../sentinel/connect-data-sources.md)                                 | Version préliminaire publique | Version préliminaire publique |
| - [One Identity Safeguard](../../sentinel/connect-one-identity.md)                          | GA             | GA             |
| - [Orca Security Alerts](../../sentinel/connect-orca-security-alerts.md)                            | Version préliminaire publique | Non disponible      |
| - [Palo Alto Networks](../../sentinel/connect-paloalto.md)                               | GA             | GA             |
| - [Perimeter 81 Activity Logs](../../sentinel/connect-perimeter-81-logs.md)                      | GA             | Non disponible      |
| - [Proofpoint On Demand Email Security](../../sentinel/connect-proofpoint-pod.md)             | Version préliminaire publique | Non disponible      |
| - [Proofpoint TAP](../../sentinel/connect-proofpoint-tap.md)                                   | Version préliminaire publique | Version préliminaire publique |
| - [Pulse Connect Secure](../../sentinel/connect-proofpoint-tap.md)                             | Version préliminaire publique | Version préliminaire publique |
| - [Qualys Vulnerability Management](../../sentinel/connect-qualys-vm.md)                  | Version préliminaire publique | Version préliminaire publique |
| - [Salesforce Service Cloud](../../sentinel/connect-salesforce-service-cloud.md)                         | Version préliminaire publique | Non disponible      |
| - [SonicWall Firewall ](../../sentinel/connect-sophos-cloud-optix.md)                              | Version préliminaire publique | Version préliminaire publique |
| - [Sophos Cloud Optix](../../sentinel/connect-sophos-cloud-optix.md)                               | Version préliminaire publique | Non disponible      |
| - [Sophos XG Firewall](../../sentinel/connect-sophos-xg-firewall.md)                               | Version préliminaire publique | Version préliminaire publique |
| - [Squadra Technologies secRMM](../../sentinel/connect-squadra-secrmm.md)               | GA             | GA             |
| - [Squid Proxy](../../sentinel/connect-squid-proxy.md)                                      | Version préliminaire publique | Non disponible      |
| - [Symantec Integrated Cyber Defense Exchange](../../sentinel/connect-symantec.md)       | GA             | GA             |
| - [Symantec ProxySG](../../sentinel/connect-symantec-proxy-sg.md)                                | Version préliminaire publique | Version préliminaire publique |
| - [Symantec VIP](../../sentinel/connect-symantec-vip.md)                                     | Version préliminaire publique | Version préliminaire publique |
| - [Syslog](../../sentinel/connect-syslog.md)                                           | GA             | GA             |
| - [Threat Intelligence Platform](../../sentinel/connect-threat-intelligence.md)s                   | Version préliminaire publique | Non disponible      |
| - [Threat Intelligence TAXII](../../sentinel/connect-threat-intelligence.md)                       | Version préliminaire publique | Non disponible      |
| - [Thycotic Secret Server](../../sentinel/connect-thycotic-secret-server.md)                          | Version préliminaire publique | Version préliminaire publique |
| - [Trend Micro Deep Security](../../sentinel/connect-trend-micro.md)                       | GA             | GA             |
| - [Trend Micro TippingPoint](../../sentinel/connect-trend-micro-tippingpoint.md)                         | Version préliminaire publique | Version préliminaire publique |
| - [Trend Micro XDR](../../sentinel/connect-data-sources.md)                                  | Version préliminaire publique | Non disponible      |
| - [VMware Carbon Black Endpoint Standard](../../sentinel/connect-vmware-carbon-black.md)           | Version préliminaire publique | Version préliminaire publique |
| - [VMware ESXi](../../sentinel/connect-vmware-esxi.md)                                      | Version préliminaire publique | Version préliminaire publique |
| - [WireX Network Forensics Platform](../../sentinel/connect-wirex-systems.md)                | Version préliminaire publique | Version préliminaire publique |
| - [Zimperium Mobile Threat Defense](../../sentinel/connect-zimperium-mtd.md)                  | Version préliminaire publique | Non disponible      |
| - [Zscaler](../../sentinel/connect-zscaler.md)                                         | GA             | GA             |
| | | |


<sup><a name="footnote1" /></a>1</sup> Les détections SSH et RDP ne sont pas prises en charge pour les clouds souverains car la plateforme Databricks ML n'est pas disponible.

### <a name="microsoft-365-data-connectors"></a>Connecteurs de données Microsoft 365

Office 365 GCC est associé à Azure Active Directory (Azure AD) dans Azure. Office 365 GCC High et Office 365 DoD sont associés à Azure AD dans Azure Government.

> [!TIP]
> Veillez à prêter attention à l'environnement Azure pour déterminer où l'[interopérabilité est possible](#microsoft-365-integration). Dans le tableau suivant, l'interopérabilité qui n'est *pas* possible est signalée par un tiret (-) pour indiquer que la prise en charge n'est pas pertinente.
>

| Connecteur | Azure  | Azure Government  |
| ------------------------------------ | -------------- | ---------------------- |
| **[Dynamics365](../../sentinel/connect-dynamics-365.md)**                               |              |                      |
| - Office 365 GCC |Version préliminaire publique | -|
| - Office 365 GCC High | -|Non disponible |
| - Office 365 DoD |- | Non disponible|
| **[Microsoft 365 Defender](../../sentinel/connect-microsoft-365-defender.md)**                             |              |                      |
| - Office 365 GCC | Version préliminaire publique| -|
| - Office 365 GCC High |- |Non disponible |
| - Office 365 DoD |- | Non disponible|
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/connect-cloud-app-security.md)**                                      |              |                      |
| - Office 365 GCC | GA| -|
| - Office 365 GCC High |-|GA |
| - Office 365 DoD |- |GA |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/connect-cloud-app-security.md)** <br>Journaux d'informatique fantôme                                  |              |                      |
| - Office 365 GCC | Version préliminaire publique| -|
| - Office 365 GCC High |-|Version préliminaire publique |
| - Office 365 DoD |- |Version préliminaire publique |
| **[Microsoft Cloud App Security (MCAS)](../../sentinel/connect-cloud-app-security.md)**                  <br>Alertes                    |              |                      |
| - Office 365 GCC | Version préliminaire publique| -|
| - Office 365 GCC High |-|Version préliminaire publique |
| - Office 365 DoD |- |Version préliminaire publique |
| **[Microsoft Defender for Endpoint](../../sentinel/connect-microsoft-defender-advanced-threat-protection.md)**                                       |              |                      |
| - Office 365 GCC | GA|- |
| - Office 365 GCC High |- |Non disponible |
| - Office 365 DoD |- | Non disponible|
| **[Microsoft Defender pour Identity](../../sentinel/connect-azure-atp.md)**                                        |              |                      |
| - Office 365 GCC |Version préliminaire publique | -|
| - Office 365 GCC High |- | Non disponible |
| - Office 365 DoD |- |Non disponible |
| **[Microsoft Defender pour Office 365](../../sentinel/connect-office-365-advanced-threat-protection.md)**               |              |                      |
| - Office 365 GCC |Version préliminaire publique |- |
| - Office 365 GCC High |- |Non disponible |
| - Office 365 DoD | -|Non disponible |
| **[Office 365](../../sentinel/connect-office-365.md)**                                      |              |                      |
| - Office 365 GCC | GA|- |
| - Office 365 GCC High |- |GA |
| - Office 365 DoD |- |GA |
| | |


## <a name="next-steps"></a>Étapes suivantes

- Découvrez le modèle de [responsabilité partagée](shared-responsibility.md) et apprenez à distinguer les tâches de sécurité qui dépendent du fournisseur de cloud de celles qui vous incombent.
- Découvrez les fonctionnalités [Azure Government Cloud](../../azure-government/documentation-government-welcome.md) ainsi que la conception et la sécurité sans faille utilisées pour prendre en charge la conformité applicable aux organisations gouvernementales fédérales, d'état et locales et à leurs partenaires.
- Découvrez le [plan Office 365 Government](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/office-365-us-government#about-office-365-government-environments).
- Découvrez la [conformité Azure](../../compliance/index.yml) pour les normes légales et réglementaires.
