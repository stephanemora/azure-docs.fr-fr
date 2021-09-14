---
title: Connecteurs de données de partenaires pour Azure Sentinel | Microsoft Docs
description: Découvrez tous les connecteurs de données de partenaires pour Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 06/17/2021
ms.author: bagol
ms.openlocfilehash: 25b09b496ff505bb37452b80355cae705af8936d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524084"
---
# <a name="azure-sentinel-partner-data-connectors"></a>Connecteurs de données de partenaires Azure Sentinel

Cet article liste, par ordre alphabétique, les connecteurs de données pris en charge par Azure Sentinel pour les organisations partenaires non-Microsoft. Si vous connaissez le connecteur qui vous intéresse, accédez à son nom dans cette page, ou recherchez-le, ou bien utilisez les liens situés à droite ou en haut de l’article.

- Pour activer et configurer les connecteurs listés, consultez [Connecter des sources de données](connect-data-sources.md) et les liens dans les listes individuelles.
- Pour plus d’informations sur les connecteurs de données Microsoft et de service à service, consultez [Intégration de service à service](connect-data-sources.md#service-to-service-integration).
- Pour plus d’informations sur les modèles de prise en charge Azure Sentinel et le champ **Pris en charge par**, consultez [Prise en charge des connecteurs de données](connect-data-sources.md#data-connector-support).

> [!IMPORTANT]
> La plupart des connecteurs de données de partenaires Azure Sentinel suivants sont en **préversion**. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.

## <a name="agari-phishing-defense-and-brand-protection-preview"></a>Agari Phishing Defense and Brand Protection (préversion)

Le connecteur Agari Phishing Defense and Brand Protection connecte les journaux des solutions Brand Protection et Phishing Defense à Azure Sentinel.

Pour plus d’informations, consultez le [site des développeurs Agari](https://developers.agari.com/agari-platform) et [Connecter Agari Phishing Defense and Brand Protection à Azure Sentinel](connect-agari-phishing-defense.md).

**Méthode d’ingestion des données :** [Azure Functions et l’API REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Pris en charge par :** [Agari](https://support.agari.com/hc/en-us/articles/360000645632-How-to-access-Agari-Support)

## <a name="ai-analyst-aia-by-darktrace-preview"></a>AI Analyst (AIA) par Darktrace (préversion)

Le connecteur Darktrace envoie les violations de modèle et les incidents AIA à Azure Sentinel. Explorez les données de manière interactive par le biais de visualisations dans le workbook AIA Darktrace, qui comprend des graphiques de vue d’ensemble avec un balayage temporel. Vous pouvez afficher des explorations détaillées de violations et d’incidents spécifiques, puis afficher les incidents dans l’interface utilisateur Darktrace pour une exploration plus poussée.

AIA envoie les données des violations à la table CommonSecurityLog Azure Sentinel dans Azure Log Analytics. La table CommonSecurityLog prend en charge le classement et l’agrégation de requêtes simples sur différents connecteurs de partenaires.

**Méthode d’ingestion des données :** [Common Event Format (CEF)](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [Darktrace](https://customerportal.darktrace.com/)

## <a name="ai-vectra-detect-preview"></a>AI Vectra Detect (préversion)

Le connecteur de données AI Vectra Detect place vos données AI Vectra Detect dans Azure Sentinel. Avec le workbook AI Vectra, vous pouvez commencer à investiguer les attaques réseau directement à partir de Sentinel. Vous pouvez afficher les hôtes, les comptes, les campagnes et les détections critiques, et superviser l’intégrité du système et les journaux d’audit de Vectra.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter AI Vectra Detect à Azure Sentinel](connect-ai-vectra-detect.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [Vectra](https://www.vectra.ai/support)

## <a name="akamai-security-events-preview"></a>Akamai Security Events (préversion)

Le connecteur de données Akamai Security Events ingère les [événements de sécurité Akamai](https://www.akamai.com/us/en/products/security/) dans Azure Sentinel. Pour plus d’informations, consultez [Intégration de la solution SIEM d’Akamai pour Splunk et CEF Syslog](https://developer.akamai.com/tools/integrations/siem).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Akamai Security Events à Azure Sentinel](connect-akamai-security-events.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog. Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** [Akamai](https://www.akamai.com/us/en/support/)

## <a name="alcide-kaudit-preview"></a>Alcide kAudit (préversion)

Le connecteur Alcide kAudit exporte automatiquement vos journaux d’audit de cluster Kubernetes dans Azure Sentinel en temps réel. Le connecteur kAudit fournit une visibilité et une observabilité améliorées de vos journaux d’audit Kubernetes. Alcide kAudit vous offre des fonctionnalités de supervision et de sécurité robustes à des fins d’analyse forensique.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Alcide kAudit à Azure Sentinel](connect-alcide-kaudit.md).

**Méthode d’ingestion des données :** [Azure Functions et l’API REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Pris en charge par :** [Alcide](https://www.alcide.io/company/contact-us/)

## <a name="alsid-for-active-directory-preview"></a>Alsid pour Active Directory (préversion)

Le connecteur Alsid pour Active Directory exporte les indicateurs Alsid des expositions, de suivi à la trace et de journaux d’attaques vers Azure Sentinel en temps réel. Le connecteur fournit également un analyseur de données pour faciliter la manipulation des journaux. Les workbooks fournissent des visualisations de données et de supervision Active Directory. Les modèles analytiques aident à automatiser les réponses aux événements, aux expositions et aux attaques.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Alsid pour Active Directory à Azure Sentinel](connect-alsid-active-directory.md).

**Méthode d’ingestion des données :** [Journaux personnalisés de l’Agent Log Analytics](connect-data-sources.md#custom-logs). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** [Alsid](https://www.alsid.com/contact-us/)

## <a name="amazon-web-services"></a>Amazon Web Services

Le connecteur de données AWS importe les événements de gestion AWS CloudTrail dans Azure Sentinel. Pour activer ce connecteur, consultez [Connecter Azure Sentinel à AWS CloudTrail](connect-aws.md).

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** Microsoft

## <a name="apache-http-server-preview"></a>Apache HTTP Server (préversion)

Le connecteur de données Apache HTTP Server ingère les événements Apache HTTP Server dans Azure Sentinel. Pour plus d’informations, consultez la [documentation sur les journaux Apache](https://httpd.apache.org/docs/2.4/logs.html).

**Méthode d’ingestion des données :** [Journaux personnalisés de l’Agent Log Analytics](connect-data-sources.md#custom-logs). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="apache-tomcat-preview"></a>Apache Tomcat (préversion)

Le connecteur de données Apache Tomcat ingère les événements [Apache Tomcat](http://tomcat.apache.org/) dans Azure Sentinel. Pour plus d’informations, consultez la [documentation d’Apache Tomcat](http://tomcat.apache.org/tomcat-10.0-doc/logging.html).

**Méthode d’ingestion des données :** [Journaux personnalisés de l’Agent Log Analytics](connect-data-sources.md#custom-logs). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="aruba-clearpass-preview"></a>Aruba ClearPass (préversion)

Le connecteur Aruba ClearPass connecte les journaux d’audit, de session, système et d’insight d’Aruba ClearPass à Azure Sentinel. Pour plus d’informations sur la configuration de la solution Aruba ClearPass pour transférer les données Syslog, consultez [Ajout d’un filtre d’exportation Syslog](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Aruba ClearPass à Azure Sentinel](connect-aruba-clearpass.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog. Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="atlassian-confluence-audit-preview"></a>Atlassian Confluence Audit (préversion)

Le connecteur de données [Atlassian Confluence](https://www.atlassian.com/software/confluence) Audit ingère les événements Confluence Audit Records. Le connecteur peut obtenir les événements pour examiner les risques de sécurité potentiels, analyser la collaboration de votre équipe, diagnostiquer les problèmes de configuration, etc. Pour plus d’informations, consultez [Voir le journal d’audit](https://support.atlassian.com/confluence-cloud/docs/view-the-audit-log/).

**Méthode d’ingestion des données :** [Azure Functions et l’API REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Pris en charge par :** Microsoft

## <a name="atlassian-jira-audit-preview"></a>Atlassian Jira Audit (préversion)

Le connecteur de données Atlassian Jira Audit ingère les événements Jira Audit Records dans Azure Sentinel. Le connecteur peut obtenir les événements pour examiner les risques de sécurité potentiels, analyser la collaboration de votre équipe, diagnostiquer les problèmes de configuration, etc. Pour plus d’informations, consultez [Enregistrements d’audit](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-audit-records/).

**Méthode d’ingestion des données :** [Azure Functions et l’API REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Pris en charge par :** Microsoft

## <a name="barracuda-cloudgen-firewall-cgfw"></a>Barracuda CloudGen Firewall (CGFW)

Le connecteur Barracuda CGFW connecte vos journaux Barracuda CGFW à Azure Sentinel. Pour configurer le streaming Syslog pour Barracuda CGFW, consultez [Comment configurer le streaming Syslog](https://aka.ms/sentinel-barracudacloudfirewall-connector).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Barracuda CloudGen Firewall à Azure Sentinel](connect-barracuda-cloudgen-firewall.md).

**Méthode d’ingestion des données :** [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** [Barracuda](https://www.barracuda.com/support)

## <a name="barracuda-web-application-firewall-waf"></a>Pare-feu d’applications Web (WAF) Barracuda

Le connecteur Barracuda WAF connecte vos journaux Barracuda WAF à Azure Sentinel. Pour plus d’informations, consultez [Configurer Barracuda Web Application Firewall](https://aka.ms/asi-barracuda-connector).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Barracuda WAF à Azure Sentinel](connect-barracuda.md).

**Méthode d’ingestion des données :** [Journaux personnalisés de l’Agent Log Analytics](connect-data-sources.md#custom-logs).

**Pris en charge par :** [Barracuda](https://www.barracuda.com/support)

## <a name="better-mobile-threat-defense-mtd-preview"></a>BETTER Mobile Threat Defense (MTD) (préversion)

Le connecteur BETTER MTD permet aux entreprises de connecter leurs instances Better MTD à Azure Sentinel. Ce connecteur fournit un insight des appareils mobiles et de la posture de sécurité mobile actuelle d’une organisation, ce qui améliore les capacités d’opération de sécurité globales. Pour plus d’informations, consultez la [documentation sur la configuration d’Azure Sentinel](https://mtd-docs.bmobi.net/integrations/how-to-setup-azure-sentinel-integration#mtd-integration-configuration) pour BETTER Mobile.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter BETTER Mobile Threat Defense à Azure Sentinel](connect-better-mtd.md).

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** BETTER Mobile

## <a name="beyond-security-besecure-preview"></a>Beyond Security beSECURE (préversion)

Le connecteur Beyond Security beSECURE connecte vos événements d’analyse, résultats d’analyse et journal d’audit Beyond Security beSECURE à Azure Sentinel. Pour plus d’informations, consultez la [page de présentation](https://beyondsecurity.com/solutions/besecure.html) de beSECURE.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Beyond Security beSECURE à Azure Sentinel](connect-besecure.md).

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** [Beyond Security](https://beyondsecurity.freshdesk.com/support/home)

## <a name="blackberry-cylanceprotect-preview"></a>BlackBerry CylancePROTECT (préversion)

Le connecteur Blackberry CylancePROTECT connecte les journaux d’audit, de menace, de contrôle des applications, d’appareil, de protection de la mémoire et de classification des menaces CylancePROTECT à Azure Sentinel. Pour configurer CylancePROTECT afin de transférer les données Syslog, consultez le [Guide Syslog pour Cylance](https://docs.blackberry.com/content/dam/docs-blackberry-com/release-pdfs/en/cylance-products/syslog-guides/Cylance%20Syslog%20Guide%20v2.0%20rev12.pdf).

**Méthode d’ingestion des données :** [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="broadcom-symantec-data-loss-prevention-dlp-preview"></a>Broadcom Symantec Data Loss Prevention (DLP) (préversion)

Le connecteur Broadcom Symantec DLP connecte les déclencheurs de règles de stratégie/réponse Symantec DLP à Azure Sentinel. Avec ce connecteur, vous pouvez créer des tableaux de bord et des alertes personnalisés pour faciliter l’investigation. Pour configurer Symantec DLP afin de transférer les données Syslog, consultez [Configuration de l’action de journalisation sur un serveur Syslog](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Broadcom Symantec DLP à Azure Sentinel](connect-broadcom-symantec-dlp.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog. Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="check-point"></a>Check Point

Le connecteur de pare-feu Check Point connecte vos journaux Check Point à Azure Sentinel. Pour configurer votre produit Check Point en vue d’exporter des journaux, consultez [Exportateur de journaux - Exporter les journaux Check Point](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk122323).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Check Point à Azure Sentinel](connect-checkpoint.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [Check Point](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.checkpoint.com%2Fsupport-services%2Fcontact-support%2F&data=04%7C01%7CNayef.Yassin%40microsoft.com%7C9965f53402ed44988a6c08d913fc51df%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637562796697084967%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=Av1vTkeYoEXzRKO%2FotZLtMMexIQI%2FIKjJGnPQsbhqmE%3D&reserved=0)

## <a name="cisco-asa"></a>Cisco ASA

Le connecteur de pare-feu Cisco ASA connecte vos journaux Cisco ASA à Azure Sentinel. Pour configurer la connexion, suivez les instructions du [Guide de configuration de l’interface CLI pour les séries Cisco ASA](https://www.cisco.com/c/en/us/support/docs/security/pix-500-series-security-appliances/63884-config-asa-00.html).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Cisco ASA à Azure Sentinel](connect-cisco.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** Microsoft

## <a name="cisco-firepower-estreamer-preview"></a>Cisco Firepower eStreamer (préversion)

Le connecteur Cisco Firepower eStreamer connecte vos journaux eStreamer à Azure Sentinel. Cisco Firepower eStreamer est une API client-serveur conçue pour la solution Cisco Firepower NGFW. Pour plus d’informations, consultez le [Guide des opérations eStreamer eNcore pour Sentinel](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [Cisco](https://www.cisco.com/c/en/us/support/index.html)

## <a name="cisco-meraki-preview"></a>Cisco Meraki (préversion)

Le connecteur Cisco Meraki connecte les journaux des événements Cisco Meraki (MX/MR/MS), les URL, les flux, les alertes d’ID, les événements de sécurité et les événements Air Marshal à Azure Sentinel. Pour configurer des appareils Meraki pour transférer les données Syslog, suivez les instructions fournies dans [Création de rapports d’appareil Meraki - Syslog, SNMP et API](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Cisco Meraki à Azure Sentinel](connect-cisco-meraki.md).

**Méthode d’ingestion des données :** [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="cisco-unified-computing-system-ucs-preview"></a>Cisco Unified Computing System (UCS) (préversion)

Le connecteur Cisco UCS connecte les journaux d’audit, d’événement et d’erreur Cisco UCS à Azure Sentinel. Pour configurer le système Cisco UCS afin de transférer les données Syslog, suivez les instructions fournies dans [Procédure pour configurer Syslog sur un serveur Syslog distant](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Cisco Unified Computing System (UCS) à Azure Sentinel](connect-cisco-ucs.md).

**Méthode d’ingestion des données :** [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="cisco-umbrella-preview"></a>Cisco Umbrella (préversion)

Le connecteur de données Cisco Umbrella ingère les événements Cisco Umbrella stockés dans Amazon S3, comme les journaux DNS, proxy et IP, dans Azure Sentinel. Le connecteur de données Cisco Umbrella utilise l’API REST Amazon S3. Pour configurer la journalisation et obtenir des informations d’identification, consultez [Journalisation dans Amazon S3](https://docs.umbrella.com/deployment-umbrella/docs/log-management#section-logging-to-amazon-s-3).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Cisco Umbrella à Azure Sentinel](connect-cisco-umbrella.md).

**Méthode d’ingestion des données :** [Azure Functions et l’API REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Pris en charge par :** Microsoft

## <a name="citrix-analytics-security"></a>Citrix Analytics (Sécurité)

Le connecteur de données Citrix Analytics (Security) exporte les données d’événement à risque de Citrix Analytics (Security) dans Azure Sentinel. Vous pouvez créer des tableaux de bord personnalisés, analyser des données d’autres sources avec les données Citrix Analytics (Security) et créer des workflows Logic Apps personnalisés pour superviser et atténuer les événements de sécurité. Pour plus d’informations, consultez [Intégration Microsoft Azure Sentinel](https://docs.citrix.com/en-us/security-analytics/getting-started-security/azure-sentinel-integration.html).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Citrix Analytics (Security) à Azure Sentinel](connect-citrix-analytics.md).

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** [Citrix](https://www.citrix.com/support/)

## <a name="citrix-web-app-firewall-waf-preview"></a>Citrix Web App Firewall (WAF) (préversion)

Le connecteur de données Citrix WAF connecte vos journaux Citrix WAF à Azure Sentinel. Citrix WAF atténue les menaces contre vos ressources publiques, y compris les sites web, les applications et les API.
- Pour configurer WAF, consultez la page [WIKI de support - Configuration du pare-feu d’applications web avec NetScaler](https://support.citrix.com/article/CTX234174).
- Pour configurer les journaux CEF, consulter [Prise en charge de la journalisation CEF dans le pare-feu d’application](https://support.citrix.com/article/CTX136146).
- Pour transférer les journaux vers le proxy, consultez [Configuration de l’appliance Citrix ADC pour la journalisation d’audit](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Citrix WAF à Azure Sentinel](connect-citrix-waf.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [Citrix](https://www.citrix.com/support/)

## <a name="cognni-preview"></a>Cognni (préversion)

Le connecteur de données Cognni offre une intégration rapide et simple à Azure Sentinel. Vous pouvez utiliser Cognni pour mapper de manière autonome des informations importantes non classifiées et détecter les incidents associés. Cognni vous aide à identifier les risques liés à vos informations importantes, à comprendre la gravité des incidents et à examiner les détails que vous devez corriger, de manière suffisamment rapide pour faire la différence.

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** [Cognni](https://cognni.ai/contact-support/)

## <a name="cyberark-enterprise-password-vault-epv-events-preview"></a>CyberArk Enterprise Password Vault (EPV) Events (préversion)

Le connecteur de données CyberArk ingère les messages CyberArk EPV XML Syslog pour les actions effectuées dans le coffre. EPV convertit les messages XML au format CEF standard par le biais du traducteur *Sentinel.xsl*, puis les envoie à un serveur de préproduction Syslog que vous choisissez (syslog-ng, rsyslog). L’agent Log Analytics sur le serveur de préproduction Syslog importe les messages dans Log Analytics. Pour plus d’informations, consultez [Applications SIEM (Security Information and Event Management)](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) dans la documentation CyberArk.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter CyberArk Enterprise Password Vault à Azure Sentinel](connect-cyberark.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [CyberArk](https://www.cyberark.com/customer-support/)

## <a name="cyberpion-security-logs-preview"></a>Cyberpion Security Logs (préversion)

Le connecteur de données Cyberpion Security Logs ingère les journaux du système Cyberpion directement dans Azure Sentinel. Pour plus d’informations, consultez [Azure Sentinel](https://www.cyberpion.com/resource-center/integrations/azure-sentinel/) dans la documentation de Cyberpion.

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** Cyberpion

## <a name="eset-enterprise-inspector-preview"></a>ESET Enterprise Inspector (préversion)

Le connecteur de données ESET Enterprise Inspector ingère les détections depuis ESET Enterprise Inspector en utilisant l’API REST fournie dans ESET Enterprise Inspector versions 1.4 et ultérieures. Pour plus d’informations, consultez [API REST](https://help.eset.com/eei/1.5/en-US/api.html) dans la documentation d’ESET Enterprise Inspector.

**Méthode d’ingestion des données :** [Azure Functions et l’API REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Pris en charge par :** [ESET](https://support.eset.com/en)

## <a name="eset-security-management-center-smc-preview"></a>ESET Security Management Center (SMC) (préversion)

Le connecteur de données ESET SMC ingère les événements de menace, les journaux d’audit, les événements de pare-feu et les filtres de sites web d’ESET SMC dans Azure Sentinel. Pour plus d’informations, consultez [Serveur Syslog](https://help.eset.com/esmc_admin/70/en-US/admin_server_settings_syslog.html) dans la documentation d’ESET SMC.

**Méthode d’ingestion des données :** [Journaux personnalisés de l’Agent Log Analytics](connect-data-sources.md#custom-logs).

**Pris en charge par :** [ESET](https://support.eset.com/en)

## <a name="exabeam-advanced-analytics-preview"></a>Exabeam Advanced Analytics (préversion)

Le connecteur de données Exabeam Advanced Analytics ingère les événements Exabeam Advanced Analytics tels que l’intégrité du système, les sessions/anomalies notables, l’analytique avancée et l’état des travaux dans Azure Sentinel. Pour envoyer des journaux depuis Exabeam Advanced Analytics via Syslog, suivez les instructions indiquées dans [Configurer des notifications d’activité système Advanced Analytics](https://docs.exabeam.com/en/advanced-analytics/i54/advanced-analytics-administration-guide/113254-configure-advanced-analytics.html#UUID-7ce5ff9d-56aa-93f0-65de-c5255b682a08).

**Méthode d’ingestion des données :** [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="extrahop-revealx"></a>ExtraHop Reveal(x)

Le connecteur de données ExtraHop Reveal(x) connecte votre système Reveal(x) à Azure Sentinel. L’intégration d’Azure Sentinel nécessite le connecteur SIEM ExtraHop Detection. Pour installer le connecteur SIEM sur votre système Reveal(x), suivez les instructions indiquées dans [Connecteur SIEM ExtraHop Detection](https://aka.ms/asi-syslog-extrahop-forwarding).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter ExtraHop Reveal(x) à Azure Sentinel](connect-extrahop.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [ExtraHop](https://www.extrahop.com/support/)

## <a name="f5-big-ip"></a>F5 BIG-IP 

Le connecteur F5 BIG-IP connecte vos journaux F5 LTM, System et ASM à Azure Sentinel. Pour plus d’informations, consultez [Intégration de F5 BIGIP à Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter F5 BIG-IP à Azure Sentinel](connect-f5-big-ip.md).

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** Microsoft

## <a name="f5-networks"></a>F5 Networks

Le connecteur de pare-feu F5 connecte vos événements de sécurité d’application F5 à Azure Sentinel. Pour configurer la journalisation à distance, consultez [Configuration de la journalisation des événements de sécurité d’application](https://aka.ms/asi-syslog-f5-forwarding).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter F5 ASM à Azure Sentinel](connect-f5.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** Microsoft

## <a name="forcepoint-cloud-access-security-broker-casb-preview"></a>Forcepoint Cloud Access Security Broker (CASB) (préversion)

Le connecteur de données Forcepoint CASB exporte automatiquement les journaux et les événements CASB dans Azure Sentinel en temps réel. Pour plus d’informations, consultez [Forcepoint CASB et Azure Sentinel](https://forcepoint.github.io/docs/casb_and_azure_sentinel/).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter les produits Forcepoint à Azure Sentinel](connect-forcepoint-casb-ngfw.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-cloud-security-gateway-csg-preview"></a>Forcepoint Cloud Security Gateway (CSG) (préversion)

Le connecteur de données Forcepoint CSG exporte automatiquement les journaux CSG dans Azure Sentinel. CSG est un service de sécurité cloud convergé qui fournit une visibilité, un contrôle et une protection contre les menaces pour les utilisateurs et les données, où qu’ils se trouvent. Pour plus d’informations, consultez [Forcepoint Cloud Security Gateway et Azure Sentinel](https://forcepoint.github.io/docs/csg_and_sentinel/).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter les produits Forcepoint à Azure Sentinel](connect-forcepoint-casb-ngfw.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-data-loss-prevention-dlp-preview"></a>Forcepoint Data Loss Prevention (DLP) (préversion)

Le connecteur de données Forcepoint DLP exporte automatiquement les données d’incident DLP de Forcepoint DLP dans Azure Sentinel en temps réel. Pour plus d’informations, consultez [Forcepoint Data Loss Prevention et Azure Sentinel](https://forcepoint.github.io/docs/dlp_and_azure_sentinel/).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Forcepoint DLP à Azure Sentinel](connect-forcepoint-dlp.md).

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** [Forcepoint](https://support.forcepoint.com/)

## <a name="forcepoint-next-generation-firewall-ngfw-preview"></a>Forcepoint Next Generation Firewall (NGFW) (préversion)

Le connecteur de données Forcepoint NGFW exporte automatiquement les journaux Forcepoint NGFW définis par l’utilisateur dans Azure Sentinel en temps réel. Pour plus d’informations, consultez [Forcepoint Next-Gen Firewall et Azure Sentinel](https://forcepoint.github.io/docs/ngfw_and_azure_sentinel/).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter les produits Forcepoint à Azure Sentinel](connect-forcepoint-casb-ngfw.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [Forcepoint](https://support.forcepoint.com/)

## <a name="forgerock-common-audit-caud-for-cef-preview"></a>ForgeRock Common Audit (CAUD) for CEF (préversion)

La plateforme d’identité ForgeRock fournit un framework d’audit commun unique qui vous permet de suivre les données de journal de manière holistique. Vous pouvez extraire et agréger des données de journal sur l’ensemble de la plateforme avec des gestionnaires d’événements et des ID uniques CAUD. Le connecteur CAUD for CEF est ouvert et extensible, et vous pouvez utiliser ses fonctionnalités de journalisation d’audit et de création de rapports pour l’intégration à Azure Sentinel. Pour plus d’informations, consultez [ForgeRock Common Audit (CAUD) pour Azure Sentinel](https://github.com/javaservlets/SentinelAuditEventHandler).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [ForgeRock](https://www.forgerock.com/support)

## <a name="fortinet"></a>Fortinet 

Le connecteur de pare-feu Fortinet connecte les journaux Fortinet à Azure Sentinel. Pour plus d’informations, accédez à la [bibliothèque de documents Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary), choisissez votre version et utilisez les PDF du manuel d’utilisation (*Handbook*) et de la référence des messages de journaux (*Log Message Reference*).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Fortinet à Azure Sentinel](connect-fortinet.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [Fortinet](https://support.fortinet.com/)

## <a name="google-workspace-g-suite-preview"></a>Google Workspace (G-Suite) (préversion)

Le connecteur de données Google Workspace ingère des événements d’activité Google Workspace dans Azure Sentinel par le biais de l’API REST. La possibilité d’ingérer des événements vous aide à effectuer les opérations suivantes :
- Examiner les risques potentiels de sécurité.
- Analyser la collaboration de votre équipe.
- Diagnostiquer les problèmes de configuration.
- Suivre les personnes qui se connectent et à quel moment.
- Analyser l’activité de l’administrateur.
- Comprendre la manière dont les utilisateurs créent et partagent du contenu.
- Passer en revue les événements organisationnels.

Pour obtenir des informations d’identification, suivez les instructions indiquées dans [Effectuer une délégation d’autorité à l’échelle du domaine Google Workspace](https://developers.google.com/admin-sdk/reports/v1/guides/delegation).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Google Workspace (anciennement G Suite) à Azure Sentinel](connect-google-workspace.md).

**Méthode d’ingestion des données :** [Azure Functions et l’API REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Pris en charge par :** Microsoft

## <a name="illusive-attack-management-system-ams-preview"></a>Illusive Attack Management System (AMS) (préversion)

Le connecteur Illusive AMS partage les journaux d’incident et les données d’analyse de surfaces d’attaque Illusive vers Azure Sentinel. Vous pouvez voir ces informations dans des tableaux de bord dédiés. Le workbook ASM vous donne un insight du risque de surface d’attaque de votre organisation, tandis que le workbook ADS effectue le suivi du mouvement latéral non autorisé dans le réseau de votre organisation. Pour plus d’informations, consultez le [Guide d’administration d’Illusive Networks](https://support.illusivenetworks.com/hc/en-us/sections/360002292119-Documentation-by-Version).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Illusive Networks AMS à Azure Sentinel](connect-illusive-attack-management-system.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [Illusive Networks](https://www.illusivenetworks.com/technical-support/)

## <a name="imperva-waf-gateway-preview"></a>Imperva WAF Gateway (préversion)

Le connecteur Imperva connecte vos alertes Imperva WAF Gateway à Azure Sentinel. Pour plus d’informations, consultez [Procédure à suivre pour activer la journalisation des alertes Imperva WAF Gateway dans Azure Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Imperva WAF Gateway à Azure Sentinel](connect-imperva-waf-gateway.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [Imperva](https://www.imperva.com/support/technical-support/)

## <a name="infoblox-network-identity-operating-system-nios-preview"></a>Infoblox Network Identity Operating System (NIOS) (préversion)

Le connecteur Infoblox NIOS connecte vos journaux Infoblox NIOS à Azure Sentinel. Pour activer le transfert Syslog des journaux Infoblox NIOS, consultez le [Guide de déploiement - Configuration de SNMP et Syslog pour NIOS](https://www.infoblox.com/wp-content/uploads/infoblox-deployment-guide-slog-and-snmp-configuration-for-nios.pdf).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Infoblox NIOS à Azure Sentinel](connect-infoblox.md).

**Méthode d’ingestion des données :** [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="juniper-srx-preview"></a>Juniper SRX (préversion)

Le connecteur Juniper SRX connecte les journaux Juniper SRX à Azure Sentinel. Pour transférer les journaux du trafic, consultez [Configurer la journalisation du trafic (journaux de stratégie de sécurité) pour les appareils de branche SRX](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA). Pour transférer les journaux système, consultez [Configurer la journalisation du système](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Juniper SRX à Azure Sentinel](connect-juniper-srx.md).

**Méthode d’ingestion des données :** [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** [Juniper Networks](https://support.juniper.net/support/)

## <a name="morphisec-utpp-preview"></a>Morphisec UTPP (préversion)

Le connecteur de données Morphisec pour Azure Sentinel intègre des insights critiques de vos produits de sécurité. Vous pouvez étendre vos fonctionnalités analytiques avec la fonctionnalité recherche et corrélation, le renseignement sur les menaces et des alertes personnalisées. Le connecteur de données Morphisec offre une visibilité sur les menaces avancées, telles que les attaques sans fichier sophistiquées, les codes malveillants exploitant une faille de sécurité dans la mémoire et les attaques de type zero-day. Grâce à une vue unique et interproduit, vous pouvez prendre des décisions en temps réel et reposant sur des données pour protéger vos ressources les plus importantes.

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog. Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Morphisec

## <a name="netskope-preview"></a>Netskope (préversion)

Le connecteur Netskope Cloud Security Platform ingère les journaux et les événements Netskope dans Azure Sentinel. Pour plus d’informations, consultez [Netskope Cloud Security Platform](https://www.netskope.com/platform).

**Méthode d’ingestion des données :** [Azure Functions et l’API REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Pris en charge par :** Microsoft

## <a name="nginx-http-server-preview"></a>NGINX HTTP Server (préversion)

Le connecteur de données NGINX HTTP Server ingère les événements NGINX HTTP Server dans Azure Sentinel. Pour plus d’informations, consultez [Module ngx_http_log_module](https://nginx.org/en/docs/http/ngx_http_log_module.html).

**Méthode d’ingestion des données :** [Journaux personnalisés de l’Agent Log Analytics](connect-data-sources.md#custom-logs). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="nxlog-basic-security-module-bsm-macos-preview"></a>NXLog Basic Security Module (BSM) macOS (préversion)

Le connecteur de données NXLog BSM macOS utilise l’API d’audit BSM de Sun pour capturer les événements d’audit directement à partir du noyau sur la plateforme macOS. Ce connecteur de données peut exporter efficacement des événements d’audit macOS vers Azure Sentinel en temps réel. Pour plus d’informations, consultez le [Guide d’utilisateur de NXLog pour Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html).

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** [NXLog](https://nxlog.co/community-forum)

## <a name="nxlog-dns-logs-preview"></a>NXLog DNS Logs (préversion)

Le connecteur de données NXLog DNS Logs utilise le suivi d’événements pour Windows (ETW) afin de collecter les événements de serveur DNS d’audit et analytiques. Pour une efficacité maximale, le module NXLog im_etw lit les données de suivi d’événements directement, sans avoir à capturer le suivi des événements dans un fichier *.etl*. Ce connecteur d’API REST peut transférer des événements de serveur DNS vers Azure Sentinel en temps réel. Pour plus d’informations, consultez le [Guide d’utilisateur de NXLog pour Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter NXLog (Windows) DNS Logs à Azure Sentinel](connect-nxlog-dns.md).

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** [NXLog](https://nxlog.co/community-forum)

## <a name="nxlog-linuxaudit-preview"></a>NXLog LinuxAudit (préversion)

Le connecteur de données NXLog LinuxAudit prend en charge les règles d’audit personnalisées et collecte des journaux sans utiliser AuditD ou un autre logiciel utilisateur. Le connecteur résout les adresses IP et les ID de groupe/utilisateur dans leurs noms respectifs, ce qui rend les journaux d’audit Linux plus intelligibles. Ce connecteur d’API REST peut exporter des événements de sécurité Linux vers Azure Sentinel en temps réel. Pour plus d’informations, consultez le [Guide d’utilisateur de NXLog pour Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter NXLog LinuxAudit à Azure Sentinel](connect-nxlog-linuxaudit.md).

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** [NXLog](https://nxlog.co/community-forum)

## <a name="okta-single-sign-on-preview"></a>Okta Single Sign-On (préversion)

Le connecteur de données Okta Single Sign-On (SSO) ingère les journaux d’audit et d’événements de l’API Okta dans Azure Sentinel. Pour créer un jeton d’API, suivez les instructions de la procédure [Création du jeton](https://developer.okta.com/docs/guides/create-an-api-token/create-the-token/).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Okta SSO à Azure Sentinel](connect-okta-single-sign-on.md).

**Méthode d’ingestion des données :** [Azure Functions et l’API REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Pris en charge par :** Microsoft

## <a name="onapsis-platform-preview"></a>Onapsis Platform (préversion)

Le connecteur de données Onapsis exporte les alarmes déclenchées dans la plateforme Onapsis vers Azure Sentinel en temps réel.

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [Onapsis](https://onapsis.force.com/s/login/)

## <a name="one-identity-safeguard-preview"></a>One Identity Safeguard (préversion)

Le connecteur de données One Identity Safeguard CEF Sentinel améliore le connecteur CEF standard grâce à une protection pour les tableaux de bord spécifiques des sessions privilégiées. Ce connecteur utilise des événements d’appareil pour la visualisation, les alertes, les investigations, etc. Pour plus d’informations, consultez le [Guide d’administration de One Identity Safeguard pour les sessions privilégiées](https://aka.ms/sentinel-cef-oneidentity-forwarding).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter One Identity Safeguard à Azure Sentinel](connect-one-identity.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [One Identity](https://support.oneidentity.com/)

## <a name="oracle-weblogic-server-preview"></a>Oracle WebLogic Server (préversion)

Le connecteur de données OracleWebLogicServer ingère les événements OracleWebLogicServer dans Azure Sentinel. Pour plus d’informations, consultez la [documentation Oracle WebLogic Server](https://docs.oracle.com/en/middleware/standalone/weblogic-server/14.1.1.0/index.html).

**Méthode d’ingestion des données :** [Journaux personnalisés de l’Agent Log Analytics](connect-data-sources.md#custom-logs). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="orca-security-alert-preview"></a>Orca Security Alert (préversion)

Le connecteur Orca Security Alert exporte automatiquement les journaux d’alertes vers Azure Sentinel. Pour plus d’informations, consultez [Intégration Azure Sentinel](https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-configuration).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Orca Security à Azure Sentinel](connect-orca-security-alerts.md).

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** [Orca Security](http://support.orca.security/)

## <a name="ossec-preview"></a>OSSEC (préversion)

Le connecteur de données OSSEC ingère les événements OSSEC dans Azure Sentinel. Pour plus d’informations, consultez la [documentation OSSEC](https://www.ossec.net/docs/). Pour configurer l’envoi des alertes par OSSEC via Syslog, suivez les instructions de la procédure d’[envoi d’alertes via Syslog](https://www.ossec.net/docs/docs/manual/output/syslog-output.html).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** Microsoft

## <a name="palo-alto-networks"></a>Palo Alto Networks

Le connecteur de données de pare-feu Palo Alto Networks connecte les journaux Palo Alto Networks à Azure Sentinel. Pour plus d’informations, consultez les [guides de configuration CEF (Common Event Format)](https://aka.ms/asi-syslog-paloalto-forwarding) et [Configurer la supervision Syslog](https://aka.ms/asi-syslog-paloalto-configure).

Le connecteur de données Palo Alto Networks ingère les journaux dans Azure Sentinel [CEF](connect-common-event-format.md) via Syslog.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Palo Alto Networks à Azure Sentinel](connect-paloalto.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [Palo Alto Networks](https://www.paloaltonetworks.com/company/contact-support)

## <a name="perimeter-81-activity-logs-preview"></a>Perimeter 81 Activity Logs (préversion)

Le connecteur de données Perimeter 81 Activity Logs connecte les journaux d’activité Perimeter 81 à Azure Sentinel. Pour plus d’informations, consultez la documentation Perimeter 81 pour [Azure Sentinel](https://support.perimeter81.com/docs/360012680780).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter les journaux Perimeter 81 à Azure Sentinel](connect-perimeter-81-logs.md).

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** [Perimeter 81](https://support.perimeter81.com/)

## <a name="proofpoint-on-demand-pod-email-security-preview"></a>Proofpoint On Demand (POD) Email Security (préversion)

Le connecteur de données POD Email Security reçoit des données de protection d’e-mail POD. Avec ce connecteur, vous pouvez vérifier la traçabilité des messages et superviser l’activité des e-mails, les menaces et l’exfiltration des données par des attaquants et des utilisateurs infiltrés malveillants. Vous pouvez passer en revue les événements organisationnels de manière accélérée et obtenir des journaux d’événements par incréments horaires pour toute activité récente. Pour plus d’informations sur l’activation et la vérification de l’API de journal POD, [connectez-vous à la Communauté Proofpoint](https://proofpointcommunities.force.com/community/s/article/How-to-request-a-Community-account-and-gain-full-customer-access?utm_source=login&utm_medium=recommended&utm_campaign=public) et consultez [API de journal Proofpoint On Demand (POD)](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Proofpoint On Demand (POD) Email Security à Azure Sentinel](connect-proofpoint-pod.md).

**Méthode d’ingestion des données :** [Azure Functions et l’API REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Pris en charge par :** Microsoft

## <a name="proofpoint-targeted-attack-protection-tap-preview"></a>Proofpoint Targeted Attack Protection (TAP) (préversion)

Le connecteur Proofpoint TAP ingère les journaux et les événements Proofpoint TAP dans Azure Sentinel. Le connecteur fournit une visibilité sur les événements de message et de clic dans Azure Sentinel.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Proofpoint TAP à Azure Sentinel](connect-proofpoint-tap.md).

**Méthode d’ingestion des données :** [Azure Functions et l’API REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Pris en charge par :** Microsoft

## <a name="pulse-connect-secure-preview"></a>Pulse Connect Secure (préversion)

Le connecteur Pulse Connect Secure connecte vos journaux Pulse Connect Secure à Azure Sentinel. Pour activer le streaming Syslog des journaux Pulse Connect Secure, suivez les instructions indiquées dans [Configuration de Syslog](https://docs.pulsesecure.net/WebHelp/Content/PCS/PCS_AdminGuide_8.2/Configuring%20Syslog.htm).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Pulse Connect Secure à Azure Sentinel](connect-pulse-connect-secure.md).

**Méthode d’ingestion des données :** [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="qualys-vulnerability-management-vm-knowledgebase-kb-preview"></a>Qualys Vulnerability Management (VM) KnowledgeBase (KB) (préversion)

Le connecteur de données Qualys VM KB ingère les données de vulnérabilité les plus récentes de la base de connaissances Qualys dans Azure Sentinel. Vous pouvez utiliser ces données pour mettre en corrélation et enrichir les détections de vulnérabilités par le connecteur de données Qualys VM.

**Méthode d’ingestion des données :** [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="qualys-vm-preview"></a>Qualys VM (préversion)

Le connecteur de données Qualys VM ingère les données de détection d’hôte de vulnérabilité dans Azure Sentinel via l’API Qualys. Le connecteur offre une visibilité sur les données de détection d’hôte à partir d’analyses de vulnérabilité.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Qualys VM à Azure Sentinel](connect-qualys-vm.md).

**Méthode d’ingestion des données :** [Azure Functions et l’API REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Pris en charge par :** Microsoft

## <a name="salesforce-service-cloud-preview"></a>Salesforce Service Cloud (préversion)

Le connecteur de données Salesforce Service Cloud ingère les informations sur les événements opérationnels de Salesforce dans Azure Sentinel via l’API REST. Avec ce connecteur, vous pouvez passer en revue les événements organisationnels de manière accélérée et obtenir des journaux d’événements par incréments horaires pour toute activité récente. Pour obtenir plus d’informations et des informations d’identification, consultez le [Guide du développeur de l’API REST](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm) Salesforce.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Salesforce Service Cloud à Azure Sentinel](connect-salesforce-service-cloud.md).

**Méthode d’ingestion des données :** [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="sentinelone-preview"></a>SentinelOne (préversion)

Le connecteur de données SentinelOne ingère les événements SentinelOne dans Azure Sentinel. Les événements incluent des objets serveur tels que des menaces, des agents, des applications, des activités, des stratégies, des groupes, etc. Le connecteur peut obtenir les événements pour examiner les risques de sécurité potentiels, analyser la collaboration de votre équipe, diagnostiquer les problèmes de configuration, etc.

**Méthode d’ingestion des données :** [Azure Functions et l’API REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Pris en charge par :** Microsoft

## <a name="sonicwall-firewall-preview"></a>SonicWall Firewall (préversion)

Le connecteur de données SonicWall Firewall connecte les journaux de pare-feu à Azure Sentinel. Pour plus d’informations, consultez [Journal > Syslog](http://help.sonicwall.com/help/sw/eng/7020/26/2/3/content/Log_Syslog.120.2.htm).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [SonicWall](https://www.sonicwall.com/support/)

## <a name="sophos-cloud-optix-preview"></a>Sophos Cloud Optix (préversion)

Le connecteur de données Sophos Cloud Optix connecte vos journaux Sophos Cloud Optix à Azure Sentinel. Pour plus d’informations, dans vos paramètres Cloud Optix, consultez la [page des intégrations](https://optix.sophos.com/#/integrations/sentinel) Azure Sentinel.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Sophos Cloud Optix à Azure Sentinel](connect-sophos-cloud-optix.md).

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** [Sophos](https://secure2.sophos.com/en-us/support.aspx)

## <a name="sophos-xg-firewall-preview"></a>Pare-feu Sophos XG (préversion)

Le pare-feu Sophos XG connecte ses journaux à Azure Sentinel. Pour plus d’informations, consultez [Ajouter un serveur Syslog](https://docs.sophos.com/nsg/sophos-firewall/18.0/Help/en-us/webhelp/onlinehelp/nsg/tasks/SyslogServerAdd.html).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Sophos XG à Azure Sentinel](connect-sophos-xg-firewall.md).

**Méthode d’ingestion des données :** [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="squadra-technologies-secrmm"></a>Squadra Technologies secRMM

Le connecteur de données Squadra Technologies secRMM envoie des données d’événement de sécurité de stockage amovible USB dans Azure Sentinel. Pour plus d’informations, consultez le [Guide de l’administrateur Azure Sentinel secRMM](https://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Squadra Technologies secRMM à Azure Sentinel](connect-squadra-secrmm.md).

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** [Squadra Technologies](https://www.squadratechnologies.com/Contact.aspx)

## <a name="squid-proxy-preview"></a>Squid Proxy (préversion)

Le connecteur de données [Squid Proxy](http://www.squid-cache.org/) connecte les journaux Squid Proxy à Azure Sentinel.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Squid Proxy à Azure Sentinel](connect-squid-proxy.md).

**Méthode d’ingestion des données :** [Journaux personnalisés de l’Agent Log Analytics](connect-data-sources.md#custom-logs). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="symantec-integrated-cyber-defense-exchange-icdx"></a>Symantec Integrated Cyber Defense Exchange (ICDx)

Le connecteur de données Symantec ICDx connecte les journaux des solutions de sécurité Symantec à Azure Sentinel. Pour plus d’informations, consultez [Connecter votre appliance Symantec ICDx](connect-symantec.md).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Symantec ICDx à Azure Sentinel](connect-symantec.md).

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** [Broadcom Symantec](https://support.broadcom.com/security)

## <a name="symantec-proxysg-preview"></a>Symantec ProxySG (préversion)

Le connecteur de données Symantec ProxySG connecte les journaux Symantec ProxySG à Azure Sentinel. Pour plus d’informations, consultez [Envoi des journaux d’accès à un serveur Syslog](https://knowledge.broadcom.com/external/article/166529/sending-access-logs-to-a-syslog-server.html).

Le connecteur de données ProxySG de Symantec ingère les journaux dans Azure Sentinel via [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Symantec Proxy SG à Azure Sentinel](connect-symantec-proxy-sg.md).

**Méthode d’ingestion des données :** [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="symantec-vip-preview"></a>Symantec VIP (préversion)

Le connecteur de données Symantec VIP connecte les journaux de Symantec VIP à Azure Sentinel. Pour plus d’informations, consultez [Configuration de Syslog](https://help.symantec.com/cs/VIP_EG_INSTALL_CONFIG/VIP/v134652108_v128483142/Configuring-syslog?locale=EN_US).

Le connecteur de données VIP de Symantec ingère les journaux dans Azure Sentinel via [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Symantec VIP à Azure Sentinel](connect-symantec-vip.md).

**Méthode d’ingestion des données :** [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="thycotic-secret-server-preview"></a>Thycotic Secret Server (préversion)

Le connecteur de données Thycotic Secret Server connecte les journaux Secret Server à Azure Sentinel. Pour plus d’informations, consultez [Sécuriser la journalisation Syslog/CEF](https://docs.thycotic.com/ss/10.9.0/events-and-alerts/secure-syslog-cef).

Le connecteur de données Thycotic ingère les journaux dans Azure Sentinel [CEF](connect-common-event-format.md) via Syslog.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Thycotic Secret Server à Azure Sentinel](connect-thycotic-secret-server.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [Thycotic](https://thycotic.force.com/support/s/)

## <a name="trend-micro-deep-security"></a>Trend Micro Deep Security

Le connecteur de données Trend Micro Deep Security connecte les journaux Deep Security à Azure Sentinel. Pour plus d’informations, consultez [Transférer des événements Deep Security à un serveur Syslog ou SIEM](https://help.deepsecurity.trendmicro.com/12_0/on-premise/siem-syslog-forwarding-secure.html?redirected=true&Highlight=Configure%20Syslog#Protection_modules_DSM).

Le connecteur de données Trend Micro Deep Security ingère les journaux dans Azure Sentinel [CEF](connect-common-event-format.md) via Syslog. Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Trend Micro Deep Security à Azure Sentinel](connect-trend-micro.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog. Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** [Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="trend-micro-tippingpoint-preview"></a>Trend Micro TippingPoint (préversion)

Le connecteur de données Trend Micro TippingPoint connecte les événements TippingPoint SMS IPS à Azure Sentinel.

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Trend Micro TippingPoint à Azure Sentinel](connect-trend-micro-tippingpoint.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog. Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** [Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="trend-micro-xdr-preview"></a>Trend Micro XDR (préversion)

Le connecteur de données Trend Micro XDR ingère les alertes Workbench à partir de l’API Trend Micro XDR dans Azure Sentinel. Pour créer un compte et un jeton d’authentification d’API, suivez les instructions fournies dans [Obtention de clés d’API pour un accès tiers](https://docs.trendmicro.com/en-us/enterprise/trend-micro-xdr-help/ObtainingAPIKeys).

**Méthode d’ingestion des données :** [Azure Functions et l’API REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Pris en charge par :** [Trend Micro](https://success.trendmicro.com/technical-support)

## <a name="vmware-carbon-black-endpoint-standard-preview"></a>VMware Carbon Black Endpoint Standard (préversion)

Le connecteur de données VMware Carbon Black Endpoint Standard ingère des données Carbon Black Endpoint Standard dans Azure Sentinel. Pour créer une clé d’API, suivez les instructions de la procédure [Création d’une clé d’API](https://developer.carbonblack.com/reference/carbon-black-cloud/authentication/#creating-an-api-key).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter VMware Carbon Black Cloud Endpoint Standard à Azure Sentinel](connect-vmware-carbon-black.md).

**Méthode d’ingestion des données :** [Azure Functions et l’API REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Pris en charge par :** Microsoft

## <a name="vmware-esxi-preview"></a>VMware ESXi (préversion)

Le connecteur de données VMware ESXi connecte les journaux VMware ESXi à Azure Sentinel. Pour configurer le connecteur VMware ESXi pour transférer les données Syslog, consultez [Activation de Syslog sur ESXi 3.5 et 4.x](https://kb.vmware.com/s/article/1016621) et [Configurer Syslog sur les hôtes ESXi](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter VMware ESXi à Azure Sentinel](connect-vmware-esxi.md).

**Méthode d’ingestion des données :** [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft

## <a name="watchguard-firebox-preview"></a>WatchGuard Firebox (préversion)

Le connecteur de données WatchGuard Firebox ingère les journaux de pare-feu dans Azure Sentinel. Pour plus d’informations, consultez le [Guide d’intégration Microsoft Azure Sentinel](https://www.watchguard.com/help/docs/help-center/en-US/Content/Integration-Guides/General/Microsoft%20Azure%20Sentinel.html).

**Méthode d’ingestion des données :** [Syslog](connect-syslog.md). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** [WatchGuard Technologies](https://www.watchguard.com/wgrd-support/overview)

## <a name="wirex-network-forensics-platform-preview"></a>WireX Network Forensics Platform (préversion)

Le connecteur de données WireX Systems permet aux professionnels de la sécurité d’opérer une intégration à Azure Sentinel afin d’enrichir les investigations forensiques. Le connecteur englobe non seulement le contenu contextuel proposé par WireX mais peut analyser les données d’autres sources. Vous pouvez créer des tableaux de bord et des workflows personnalisés pour fournir l’image la plus complète pendant une investigation forensique. Pour obtenir plus d’informations et de l’aide pour la configuration, contactez le [support WireX](https://wirexsystems.com/contact-us/).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter WireX Network Forensics Platform à Azure Sentinel](connect-wirex-systems.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** WireX

## <a name="workplace-from-facebook-preview"></a>Workplace from Facebook (préversion)

Le connecteur de données Workplace ingère les événements Workplace courants dans Azure Sentinel par le biais de webhooks. Les webhooks permettent aux applications d’intégration personnalisées de s’abonner à des événements dans Workplace et de recevoir des mises à jour en temps réel. Quand une modification se produit, Workplace envoie une requête HTTPS POST avec des informations d’événement à une URL de connecteur de données de rappel. Pour plus d’informations, consultez la documentation relative aux webhooks. Le connecteur peut obtenir les événements pour examiner les risques de sécurité potentiels, analyser la collaboration de votre équipe, diagnostiquer les problèmes de configuration, etc.

**Méthode d’ingestion des données :** [Azure Functions et l’API REST](connect-data-sources.md#rest-api-integration-using-azure-functions).

**Pris en charge par :** Microsoft

## <a name="zimperium-mobile-thread-defense-preview"></a>Zimperium Mobile Thread Defense (préversion)

Le connecteur de données Zimperium Mobile Threat Defense connecte le journal des menaces Zimperium à Azure Sentinel pour afficher des tableaux de bord, créer des alertes personnalisées et améliorer l’investigation. Ce connecteur vous donne plus d’informations sur le paysage des menaces mobiles de votre organisation et améliore vos capacités d’opération de sécurité. Pour connaître les instructions à suivre, consultez le [portail du support client Zimperium](https://support.zimperium.com/).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Zimperium à Azure Sentinel](connect-zimperium-mtd.md).

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** [Zimperium](https://www.zimperium.com/support)

## <a name="zoom-reports-preview"></a>Zoom Reports (préversion)

Le connecteur de données Zoom Reports ingère les événements Zoom Reports dans Azure Sentinel. Le connecteur peut obtenir les événements pour examiner les risques de sécurité potentiels, analyser la collaboration de votre équipe, diagnostiquer les problèmes de configuration, etc. Pour obtenir des informations d’identification, suivez les instructions indiquées dans [JWT avec Zoom](https://marketplace.zoom.us/docs/guides/auth/jwt).

**Méthode d’ingestion des données :** [API REST](connect-data-sources.md#rest-api-integration-on-the-provider-side).

**Pris en charge par :** Microsoft

## <a name="zscaler"></a>Zscaler 

Le connecteur de données Zscaler connecte les journaux ZIA (Zscaler Internet Access) à Azure Sentinel. L’utilisation de Zscaler sur Azure Sentinel vous donne des insights sur l’utilisation d’Internet par votre organisation et améliore vos fonctionnalités des opérations de sécurité. Pour plus d’informations, consultez le [Guide de déploiement Zscaler et Microsoft Azure Sentinel](https://aka.ms/ZscalerCEFInstructions).

Pour plus d’informations sur la connexion à Azure Sentinel, consultez [Connecter Zscaler à Azure Sentinel](connect-zscaler.md).

**Méthode d’ingestion des données :** [CEF](connect-common-event-format.md) via Syslog.

**Pris en charge par :** [Zscaler](https://help.zscaler.com/submit-ticket-links)

## <a name="zscaler-private-access-zpa-preview"></a>Zscaler Private Access (ZPA) (préversion)

Le connecteur de données ZPA ingère les événements Zscaler Private Access dans Azure Sentinel. Pour plus d’informations, consultez la [documentation de Zscaler Private Access](https://help.zscaler.com/zpa).

**Méthode d’ingestion des données :** [Journaux personnalisés de l’Agent Log Analytics](connect-data-sources.md#custom-logs). Le connecteur utilise également un analyseur de journal basé sur une fonction Kusto.

**Pris en charge par :** Microsoft
