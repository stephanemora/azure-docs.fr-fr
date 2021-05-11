---
title: Disponibilité des fonctionnalités cloud des services Azure pour les clients du secteur public américain
description: Présente la disponibilité des fonctionnalités des services de sécurité Azure, comme Azure Sentinel pour les clients du secteur public américain
author: batami
ms.author: bagol
ms.service: security
ms.topic: reference
ms.date: 04/29/2021
ms.openlocfilehash: 6cead1e9dfb8cf1d12ef09079e97779c08523e36
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108290575"
---
# <a name="cloud-feature-availability-for-us-government-customers"></a>Disponibilité des fonctionnalités cloud pour les clients du secteur public américain

Cet article décrit la disponibilité des fonctionnalités des clouds Microsoft Azure et Azure Government pour les services de sécurité suivants :

- Azure Sentinel

> [!NOTE]
> D'autres services de sécurité seront bientôt ajoutés à cet article.
> 

## <a name="azure-government"></a>Azure Government

Azure Government utilise les mêmes technologies sous-jacentes qu'Azure (parfois appelées Azure Commercial ou Azure Public). Celles-ci comprennent les composants de base des modèles IaaS (infrastructure-as-a-service), PaaS (Platform-as-a-service) et SaaS (Software-as-a-service). Azure et Azure Government disposent de contrôles de sécurité complets, et Microsoft s'engage à assurer la protection des données client.

Azure Government est un environnement cloud physiquement isolé dédié au secteur public des États-Unis (autorités fédérales, étatiques, locales et tribales) ainsi qu'à leurs partenaires. Les deux environnements cloud sont conformes au niveau d'impact FedRAMP High, mais Azure Government offre une couche supplémentaire de protection aux clients par le biais d'engagements contractuels en matière de stockage des données client aux États-Unis et en limitant l'accès potentiel aux systèmes traitant les données client à des personnes spécifiques basées aux États-Unis. Ces engagements peuvent intéresser les clients qui utilisent le cloud pour stocker ou traiter des données soumises aux réglementations américaines sur le contrôle des exportations, comme les réglementations EAR, ITAR et DoE 10 CFR Part 810.

Pour plus d'informations sur Azure Government, consultez [Qu'est-ce qu'Azure Government ?](/azure/azure-government/documentation-government-welcome)

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

Pour plus d'informations, consultez la [documentation produit d'Azure Sentinel](/azure/sentinel/overview).

Les tableaux suivants indiquent la disponibilité actuelle des fonctionnalités Azure Sentinel dans Azure et Azure Government.


| Fonctionnalité | Azure | Azure Government  |
| ----- | ----- | ---- |
|- [BYO-ML (Bring Your Own ML)](/azure/sentinel/bring-your-own-ml) | Version préliminaire publique | Version préliminaire publique |
| - [Vue des incidents inter-clients/inter-espaces de travail](/azure/sentinel/multiple-workspace-view) |Version préliminaire publique | Version préliminaire publique |
| - [Entity Insights](/azure/sentinel/enable-entity-behavior-analytics) | Version préliminaire publique | Non disponible |
| - [Fusion](/azure/sentinel/fusion)<br>Détection avancée des attaques en plusieurs étapes <sup>[1](#footnote1)</sup> | GA | Non disponible |
|- [Notebooks](/azure/sentinel/notebooks) | GA | GA |
|- [Métriques d'audit des incidents SOC](/azure/sentinel/manage-soc-with-incident-metrics) | GA | GA |
|- [Watchlists](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-watchlist-is-now-in-public-preview/ba-p/1765887) | Version préliminaire publique | Non disponible |
| **Support Threat Intelligence** | | |
| - [Connecteur de données Threat Intelligence – TAXII](/azure/sentinel/import-threat-intelligence)  | Version préliminaire publique | Non disponible |
| - [Connecteur de données Threat Intelligence Platform](/azure/sentinel/import-threat-intelligence)  | Version préliminaire publique | Non disponible |
| - [Panneau de recherche Threat Intelligence](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-threat-intelligence-menu-item-in-public-preview/ba-p/1646597)  | Version préliminaire publique | Non disponible |
| - [URL Detonation](https://techcommunity.microsoft.com/t5/azure-sentinel/using-the-new-built-in-url-detonation-in-azure-sentinel/ba-p/996229) | GA | Non disponible |
| - [Classeur Threat Intelligence](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)  | GA | Non disponible |
|**Support lié à la détection** | | |
| - [Détection d'un accès anormal au partage de fichiers de Windows](/azure/sentinel/fusion)  | Version préliminaire publique | Non disponible |
| - [Détection de connexions RDP anormales](/azure/sentinel/connect-windows-security-events#configure-the-security-events-connector-for-anomalous-rdp-login-detection)<br>Détection ML intégrée | Version préliminaire publique | Non disponible |
| - [Détection de connexions SSH anormales](/azure/sentinel/connect-syslog#configure-the-syslog-connector-for-anomalous-ssh-login-detection)<br>Détection ML intégrée | Version préliminaire publique | Non disponible |
| **Connecteurs de service Azure** | | |
| - [Journaux d'activité Azure](/azure/sentinel/connect-azure-activity)                                  |   GA           |    GA         |
| - [Azure Active Directory](/azure/sentinel/connect-azure-active-directory)                |      GA        |       GA        |
| - [Azure ADIP](/azure/sentinel/connect-azure-ad-identity-protection)                         |  GA            |        GA              |
| - [Azure DDoS Protection](/azure/sentinel/connect-azure-ddos-protection)                |     GA         |       GA               |
| - [Azure Defender](/azure/sentinel/connect-azure-security-center)                  |    GA          |        GA              |
| - [Azure Defender pour IoT](/azure/sentinel/connect-asc-iot)           |       GA       |  Non disponible           |
| - [Pare-feu Azure ](/azure/sentinel/connect-azure-firewall)                        |   GA           |        GA              |
| - [Azure Information Protection](/azure/sentinel/connect-azure-information-protection)              |     Version préliminaire publique         |         Non disponible             |
| - [Azure Key Vault ](/azure/sentinel/connect-azure-key-vault)                           |       Version préliminaire publique         |         Non disponible                       |
| - [Azure Kubernetes Services (AKS)](/azure/sentinel/connect-azure-kubernetes-service)           |       Version préliminaire publique         |         Non disponible                |
| - [Azure SQL Databases](/azure/sentinel/connect-azure-sql-logs)                        |     GA         |         GA             |
| - [Pare-feu d'applications web Azure](/azure/sentinel/connect-azure-waf)                                  |      GA        |      GA                |
| **Connecteurs Windows** | | |
| - [Pare-feu Windows](/azure/sentinel/connect-windows-firewall)                                 |     GA         |   GA           |
| - [Événements de sécurité Windows](/azure/sentinel/connect-windows-security-events)                                  |      GA        |         GA     |
| **Connecteurs externes**| | |
| - [Agari Phishing Defense and Brand Protection](/azure/sentinel/connect-agari-phishing-defense)       | Version préliminaire publique | Version préliminaire publique |
| - [AI Analyst Darktrace](/azure/sentinel/connect-data-sources)                            | Version préliminaire publique | Version préliminaire publique |
| - [AI Vectra Detect](/azure/sentinel/connect-ai-vectra-detect)                                 | Version préliminaire publique | Version préliminaire publique |
| - [Akamai Security Events](/azure/sentinel/connect-akamai-security-events)                           | Version préliminaire publique | Version préliminaire publique |
| - [Alcide kAudit](/azure/sentinel/connect-alcide-kaudit)                                   | Version préliminaire publique | Non disponible      |
| - [Alsid pour Active Directory](/azure/sentinel/connect-alsid-active-directory)                      | Version préliminaire publique | Non disponible      |
| - [Apache HHTP Server](/azure/sentinel/connect-apache-http-server)                               | Version préliminaire publique | Non disponible      |
| - [Aruba ClearPass](/azure/sentinel/connect-aruba-clearpass)                                  | Version préliminaire publique | Version préliminaire publique |
| - [AWS](/azure/sentinel/connect-data-sources)                                             | GA             | GA             |
| - [Barracuda CloudGen Firewall](/azure/sentinel/connect-barracuda-cloudgen-firewall)                      | GA             | GA             |
| - [Barracuda Web App Firewall](/azure/sentinel/connect-barracuda)                       | GA             | GA             |
| - [BETTER Mobile Threat Defense MTD](/azure/sentinel/connect-better-mtd)                 | Version préliminaire publique | Non disponible      |
| - [Beyond Security beSECURE](/azure/sentinel/connect-besecure)                        | Version préliminaire publique | Non disponible      |
| - [Blackberry CylancePROTECT](/azure/sentinel/connect-data-sources)                        | Version préliminaire publique | Version préliminaire publique |
| - [Broadcom Symantec DLP](/azure/sentinel/connect-broadcom-symantec-dlp)                            | Version préliminaire publique | Version préliminaire publique |
| - [Check Point](/azure/sentinel/connect-checkpoint)                                      | GA             | GA             |
| - [Cisco ASA](/azure/sentinel/connect-cisco)                                        | GA             | GA             |
| - [Cisco Meraki](/azure/sentinel/connect-cisco-meraki)                                     | Version préliminaire publique | Version préliminaire publique |
| - [Cisco Umbrella](/azure/sentinel/connect-cisco-umbrella)                                   | Version préliminaire publique | Version préliminaire publique |
| - [Cisco UCS](/azure/sentinel/connect-cisco-ucs)                                        | Version préliminaire publique | Version préliminaire publique |
| - [Cisco Firepower EStreamer](/azure/sentinel/connect-data-sources)                          | Version préliminaire publique | Version préliminaire publique |
| - [Citrix Analytics WAF](/azure/sentinel/connect-citrix-waf)                             | GA             | GA             |
| - [Common Event Format (CEF)](/azure/sentinel/connect-common-event-format)                        | GA             | GA             |
| - [CyberArk Enterprise Password Vault (EPV) Events](/azure/sentinel/connect-cyberark) | Version préliminaire publique | Version préliminaire publique |
| - [ESET Enterprise Inspector](/azure/sentinel/connect-data-sources)                       | Version préliminaire publique | Non disponible      |
| - [Eset Security Management Center](/azure/sentinel/connect-data-sources)                  | Version préliminaire publique | Non disponible      |
| - [ExtraHop Reveal(x)](/azure/sentinel/connect-extrahop)                               | GA             | GA             |
| - [F5 BIG-IP ](/azure/sentinel/connect-f5-big-ip)                                       | GA             | GA             |
| - [F5 Networks](/azure/sentinel/connect-f5)                                     | GA             | GA             |
| - [Forcepoint NGFW](/azure/sentinel/connect-forcepoint-casb-ngfw)                                  | Version préliminaire publique | Version préliminaire publique |
| - [Forepoint CASB](/azure/sentinel/connect-forcepoint-casb-ngfw)                                  | Version préliminaire publique | Version préliminaire publique |
| - [Forepoint DLP ](/azure/sentinel/connect-forcepoint-dlp)                                   | Version préliminaire publique | Non disponible      |
| - [ForgeRock Common Audit for CEF](/azure/sentinel/connect-data-sources)                  | Version préliminaire publique | Version préliminaire publique |
| - [Fortinet](/azure/sentinel/connect-fortinet)                                         | GA             | GA             |
| - [Google Workspace (G Suite) ](/azure/sentinel/connect-google-workspace)                      | Version préliminaire publique | Non disponible      |
| - [Illusive Attack Management System](/azure/sentinel/connect-illusive-attack-management-system)                | Version préliminaire publique | Version préliminaire publique |
| - [Imperva WAF Gateway](/azure/sentinel/connect-imperva-waf-gateway)                             | Version préliminaire publique | Version préliminaire publique |
| - [Infoblox NIOS](/azure/sentinel/connect-infoblox)                                    | Version préliminaire publique | Version préliminaire publique |
| - [Juniper SRX](/azure/sentinel/connect-juniper-srx)                                      | Version préliminaire publique | Version préliminaire publique |
| - [Morphisec UTPP](/azure/sentinel/connect-data-sources)                                   | Version préliminaire publique | Version préliminaire publique |
| - [Netskope](/azure/sentinel/connect-data-sources)                                         | Version préliminaire publique | Version préliminaire publique |
| - [NXLog Windows DNS](/azure/sentinel/connect-nxlog-dns)                                             | Version préliminaire publique | Non disponible      |
| - [NXLog LinuxAudit](/azure/sentinel/connect-nxlog-linuxaudit)                                 | Version préliminaire publique | Non disponible      |
| - [Okta Single Sign On](/azure/sentinel/connect-okta-single-sign-on)                              | Version préliminaire publique | Version préliminaire publique |
| - [Onapsis Platform](/azure/sentinel/connect-data-sources)                                 | Version préliminaire publique | Version préliminaire publique |
| - [One Identity Safeguard](/azure/sentinel/connect-one-identity)                          | GA             | GA             |
| - [Orca Security Alerts](/azure/sentinel/connect-orca-security-alerts)                            | Version préliminaire publique | Non disponible      |
| - [Palo Alto Networks](/azure/sentinel/connect-paloalto)                               | GA             | GA             |
| - [Perimeter 81 Activity Logs](/azure/sentinel/connect-perimeter-81-logs)                      | GA             | Non disponible      |
| - [Proofpoint On Demand Email Security](/azure/sentinel/connect-proofpoint-pod)             | Version préliminaire publique | Non disponible      |
| - [Proofpoint TAP](/azure/sentinel/connect-proofpoint-tap)                                   | Version préliminaire publique | Version préliminaire publique |
| - [Pulse Connect Secure](/azure/sentinel/connect-proofpoint-tap)                             | Version préliminaire publique | Version préliminaire publique |
| - [Qualys Vulnerability Management](/azure/sentinel/connect-qualys-vm)                  | Version préliminaire publique | Version préliminaire publique |
| - [Salesforce Service Cloud](/azure/sentinel/connect-salesforce-service-cloud)                         | Version préliminaire publique | Non disponible      |
| - [SonicWall Firewall ](/azure/sentinel/connect-sophos-cloud-optix)                              | Version préliminaire publique | Version préliminaire publique |
| - [Sophos Cloud Optix](/azure/sentinel/connect-sophos-cloud-optix)                               | Version préliminaire publique | Non disponible      |
| - [Sophos XG Firewall](/azure/sentinel/connect-sophos-xg-firewall)                               | Version préliminaire publique | Version préliminaire publique |
| - [Squadra Technologies secRMM](/azure/sentinel/connect-squadra-secrmm)               | GA             | GA             |
| - [Squid Proxy](/azure/sentinel/connect-squid-proxy)                                      | Version préliminaire publique | Non disponible      |
| - [Symantec Integrated Cyber Defense Exchange](/azure/sentinel/connect-symantec)       | GA             | GA             |
| - [Symantec ProxySG](/azure/sentinel/connect-symantec-proxy-sg)                                | Version préliminaire publique | Version préliminaire publique |
| - [Symantec VIP](/azure/sentinel/connect-symantec-vip)                                     | Version préliminaire publique | Version préliminaire publique |
| - [Syslog](/azure/sentinel/connect-syslog)                                           | GA             | GA             |
| - [Threat Intelligence Platform](/azure/sentinel/connect-threat-intelligence)s                   | Version préliminaire publique | Non disponible      |
| - [Threat Intelligence TAXII](/azure/sentinel/connect-threat-intelligence)                       | Version préliminaire publique | Non disponible      |
| - [Thycotic Secret Server](/azure/sentinel/connect-thycotic-secret-server)                          | Version préliminaire publique | Version préliminaire publique |
| - [Trend Micro Deep Security](/azure/sentinel/connect-trend-micro)                       | GA             | GA             |
| - [Trend Micro TippingPoint](/azure/sentinel/connect-trend-micro-tippingpoint)                         | Version préliminaire publique | Version préliminaire publique |
| - [Trend Micro XDR](/azure/sentinel/connect-data-sources)                                  | Version préliminaire publique | Non disponible      |
| - [VMware Carbon Black Endpoint Standard](/azure/sentinel/connect-vmware-carbon-black)           | Version préliminaire publique | Version préliminaire publique |
| - [VMware ESXi](/azure/sentinel/connect-vmware-esxi)                                      | Version préliminaire publique | Version préliminaire publique |
| - [WireX Network Forensics Platform](/azure/sentinel/connect-wirex-systems)                | Version préliminaire publique | Version préliminaire publique |
| - [Zimperium Mobile Threat Defense](/azure/sentinel/connect-zimperium-mtd)                  | Version préliminaire publique | Non disponible      |
| - [Zscaler](/azure/sentinel/connect-zscaler)                                         | GA             | GA             |
| | | |


<sup><a name="footnote1" /></a>1</sup> Les détections SSH et RDP ne sont pas prises en charge pour les clouds souverains car la plateforme Databricks ML n'est pas disponible.

### <a name="microsoft-365-data-connectors"></a>Connecteurs de données Microsoft 365

Office 365 GCC est associé à Azure Active Directory (Azure AD) dans Azure. Office 365 GCC High et Office 365 DoD sont associés à Azure AD dans Azure Government.

> [!TIP]
> Veillez à prêter attention à l'environnement Azure pour déterminer où l'[interopérabilité est possible](#microsoft-365-integration). Dans le tableau suivant, l'interopérabilité qui n'est *pas* possible est signalée par un tiret (-) pour indiquer que la prise en charge n'est pas pertinente.
>

| Connecteur | Azure  | Azure Government  |
| ------------------------------------ | -------------- | ---------------------- |
| **[Dynamics365](/azure/sentinel/connect-dynamics-365)**                               |              |                      |
| - Office 365 GCC |Version préliminaire publique | -|
| - Office 365 GCC High | -|Non disponible |
| - Office 365 DoD |- | Non disponible|
| **[Microsoft 365 Defender](/azure/sentinel/connect-microsoft-365-defender)**                             |              |                      |
| - Office 365 GCC | Version préliminaire publique| -|
| - Office 365 GCC High |- |Non disponible |
| - Office 365 DoD |- | Non disponible|
| **[Microsoft Cloud App Security (MCAS)](/azure/sentinel/connect-cloud-app-security)**                                      |              |                      |
| - Office 365 GCC | GA| -|
| - Office 365 GCC High |-|GA |
| - Office 365 DoD |- |GA |
| **[Microsoft Cloud App Security (MCAS)](/azure/sentinel/connect-cloud-app-security)** <br>Journaux d'informatique fantôme                                  |              |                      |
| - Office 365 GCC | Version préliminaire publique| -|
| - Office 365 GCC High |-|Version préliminaire publique |
| - Office 365 DoD |- |Version préliminaire publique |
| **[Microsoft Cloud App Security (MCAS)](/azure/sentinel/connect-cloud-app-security)**                  <br>Alertes                    |              |                      |
| - Office 365 GCC | Version préliminaire publique| -|
| - Office 365 GCC High |-|Version préliminaire publique |
| - Office 365 DoD |- |Version préliminaire publique |
| **[Microsoft Defender for Endpoint](/azure/sentinel/connect-microsoft-defender-advanced-threat-protection)**                                       |              |                      |
| - Office 365 GCC | GA|- |
| - Office 365 GCC High |- |Non disponible |
| - Office 365 DoD |- | Non disponible|
| **[Microsoft Defender pour Identity](/azure/sentinel/connect-azure-atp)**                                        |              |                      |
| - Office 365 GCC |Version préliminaire publique | -|
| - Office 365 GCC High |- | Non disponible |
| - Office 365 DoD |- |Non disponible |
| **[Microsoft Defender pour Office 365](/azure/sentinel/connect-office-365-advanced-threat-protection)**               |              |                      |
| - Office 365 GCC |Version préliminaire publique |- |
| - Office 365 GCC High |- |Non disponible |
| - Office 365 DoD | -|Non disponible |
| **[Office 365](/azure/sentinel/connect-office-365)**                                      |              |                      |
| - Office 365 GCC | GA|- |
| - Office 365 GCC High |- |GA |
| - Office 365 DoD |- |GA |
| | |


## <a name="next-steps"></a>Étapes suivantes

- Découvrez le modèle de [responsabilité partagée](https://docs.microsoft.com/azure/security/fundamentals/shared-responsibility) et apprenez à distinguer les tâches de sécurité qui dépendent du fournisseur de cloud de celles qui vous incombent.
- Découvrez les fonctionnalités [Azure Government Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) ainsi que la conception et la sécurité sans faille utilisées pour prendre en charge la conformité applicable aux organisations gouvernementales fédérales, d'état et locales et à leurs partenaires.
- Découvrez le [plan Office 365 Government](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/office-365-us-government#about-office-365-government-environments).
- Découvrez la [conformité Azure](/azure/compliance/) pour les normes légales et réglementaires.
