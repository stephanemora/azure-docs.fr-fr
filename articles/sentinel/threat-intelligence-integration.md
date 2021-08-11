---
title: Intégration des renseignements sur les menaces dans Azure Sentinel | Microsoft Docs
description: Découvrez les différentes façons dont les flux d’informations sur les menaces sont intégrés et utilisés par Azure Sentinel.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2021
ms.author: yelevin
ms.openlocfilehash: d8f7fff97a92a98fbcd0198ce601c88df914d9e2
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110471481"
---
# <a name="threat-intelligence-integration-in-azure-sentinel"></a>Intégration des renseignements sur les menaces dans Azure Sentinel

> [!IMPORTANT]
> Les connecteurs de données Threat Intelligence dans Azure Sentinel sont actuellement en préversion publique.
> Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel vous offre différentes façons d’utiliser des [flux d’informations sur les menaces](import-threat-intelligence.md) pour améliorer la capacité des analystes de la sécurité à détecter et hiérarchiser les menaces connues. 

Vous pouvez utiliser l’un des nombreux produits de la plateforme décisionnelle intégrée (TIP) disponibles, vous pouvez vous connecter aux serveurs TAXII pour tirer parti de toute source d’intelligence des menaces compatible avec STIX, et vous pouvez également utiliser les solutions personnalisées qui peuvent communiquer directement avec [l'API Microsoft Graph Security tiIndicators](/graph/api/resources/tiindicator).

Vous pouvez également vous connecter à des sources de renseignement sur les menaces à partir de règles, afin d’enrichir les incidents avec des informations TI qui peuvent aider à effectuer des investigations et des actions de réponse.

## <a name="taxii-threat-intelligence-feeds"></a>Flux de renseignement sur le renseignement sur les menaces TAXII

Pour vous connecter à des flux d’informations sur les menaces TAXIi, utilisez le connecteur de données [Renseignement sur les menaces - TAXII](connect-threat-intelligence.md#connect-azure-sentinel-to-taxii-servers), ainsi que les données fournies par chaque fournisseur lié ci-dessous. Vous devrez peut-être contacter le fournisseur directement pour obtenir les données nécessaires à utiliser avec le connecteur.

### <a name="anomali-limo"></a>Anomali Limo

- [Découvrez ce dont vous avez besoin pour vous connecter au flux Anomali Limo](https://www.anomali.com/resources/limo).

### <a name="cybersixgill-darkfeed"></a>Cybersixgill Darkfeed

- [En savoir plus sur l’intégration de Cybersixgill avec Azure Sentinel @Cybersixgill](https://www.cybersixgill.com/partners/azure-sentinel/)
- Pour connecter Azure Sentinel au serveur Cybersixgill TAXIi et accéder à Darkfeed, [contactez Cybersixgill](mailto://azuresentinel@cybersixgill.com) pour obtenir la racine de l’API, l’ID de regroupement, le nom d’utilisateur et le mot de passe.

### <a name="financial-services-information-sharing-and-analysis-center-fs-isac"></a>Centre d’analyse et de partage des informations sur les services financiers (FS-ISAC)

- Rejoignez [FS-ISAC](https://www.fsisac.com/membership?utm_campaign=ThirdParty&utm_source=MSFT&utm_medium=ThreatFeed-Join) pour récupérer les informations d’identification permettant d’accéder à ce flux.

### <a name="health-intelligence-sharing-community-h-isac"></a>Communauté de partage des renseignements sur le fonctionnement (H-ISAC)

- Rejoignez [H-ISAC](https://h-isac.org/soltra/) pour récupérer les informations d’identification permettant d’accéder à ce flux.

### <a name="ibm-x-force"></a>IBM X-Force

- [En savoir plus sur l’intégration d’IBM X-Force](https://www.ibm.com/security/xforce)

### <a name="intsights"></a>IntSights

- [En savoir plus sur l’intégration de IntSights avec Azure Sentinel @IntSights](https://intsights.com/resources/intsights-microsoft-azure-sentinel)
- Pour connecter Azure Sentinel au serveur IntSights TAXIi, obtenez la racine de l’API, l’ID de regroupement, le nom d’utilisateur et le mot de passe à partir du portail IntSights après avoir configuré une stratégie des données que vous souhaitez envoyer à Azure Sentinel.

### <a name="threatconnect"></a>ThreatConnect

- [En savoir plus sur STIX et TAXIi @ThreatConnect](https://threatconnect.com/stix-taxii/)
- [Documentation TAXII Services@ThreatConnect](https://docs.threatconnect.com/en/latest/rest_api/taxii/taxii.html)

## <a name="integrated-threat-intelligence-platform-products"></a>Produits de la plateforme Threat Intelligence intégrée

Pour vous connecter aux flux de Threat Intelligence Platform (TIP), suivez les instructions pour connecter les flux de la [Plateforme de renseignement sur les menaces](connect-threat-intelligence.md#connect-azure-sentinel-to-your-threat-intelligence-platform) à Azure Sentinel. La deuxième partie de ces instructions vous invite à entrer des informations dans votre solution TIP. Consultez les liens ci-après pour plus d'informations.

### <a name="agari-phishing-defense-and-brand-protection"></a>Agari Phishing Defense and Brand Protection

- Pour connecter la [protection contre l’hameçonnage et la protection de marque Agari](https://agari.com/products/phishing-defense/), utilisez le [connecteur de données Agari](connect-agari-phishing-defense.md) intégré dans Azure Sentinel.

### <a name="anomali-threatstream"></a>ThreatStream d’Anomali

- Pour télécharger [l’intégrateur et les extensions ThreatStream](https://www.anomali.com/products/threatstream), ainsi que les instructions pour la connexion de l’intelligence ThreatStream à l’API Microsoft Graph Security, consultez la page des [téléchargements ThreatStream](https://ui.threatstream.com/downloads).

### <a name="alienvault-open-threat-exchange-otx-from-att-cybersecurity"></a>AlienVault Open Threat Exchange (OTX) from AT&T Cybersecurity

- [ALIENVAULT OTX](https://otx.alienvault.com/) utilise des Azure Logic Apps (playbooks) pour se connecter à Azure Sentinel. Consultez les [instructions spécifiques](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) nécessaires pour tirer pleinement parti de l’offre complète.

### <a name="eclecticiq-platform"></a>Plateforme EclecticIQ

- Découvrir plus d’informations sur [EclecticIQ Platform](https://www.eclecticiq.com/platform/).

### <a name="groupib-threat-intelligence-and-attribution"></a>GroupIB Threat Intelligence and Attribution

- Pour connecter [GroupIB Threat Intelligence and Attribution](https://www.group-ib.com/intelligence-attribution.html) à Azure Sentinel, GroupIB utilise Azure Logic Apps. Consultez les [instructions spécifiques](https://techcommunity.microsoft.com/t5/azure-sentinel/group-ib-threat-intelligence-and-attribution-connector-azure/ba-p/2252904) nécessaires pour tirer pleinement parti de l’offre complète.

### <a name="misp-open-source-threat-intelligence-platform"></a>Plateforme Threat Intelligence open source MISP

- Pour obtenir un exemple de script qui fournit aux clients des instances MISP pour migrer les indicateurs de menace vers l’API Microsoft Graph Security, voir [MISP pour le script Microsoft Graph Security](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).
- [En savoir plus sur le projet MISP](https://www.misp-project.org/).

### <a name="palo-alto-networks-minemeld"></a>Palo Alto Networks MineMeld

- Pour configurer [Palo Alto MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld) avec les informations de connexion à Azure Sentinel, consultez [Envoi de IOC à l’API Microsoft Graph Security à l’aide de MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540) et passer à l’en-tête de **Configuration MineMeld** .

### <a name="recorded-future-security-intelligence-platform"></a>Recorded Future Security Intelligence Platform

- [Recorded Future](https://www.recordedfuture.com/integrations/microsoft-azure/) utilise des Azure Logic Apps (playbooks) pour se connecter à Azure Sentinel. Consultez les [instructions spécifiques](https://go.recordedfuture.com/hubfs/partners/microsoft-azure-installation-guide.pdf) nécessaires pour tirer pleinement parti de l’offre complète.

### <a name="threatconnect-platform"></a>Plateforme ThreatConnect

- Pour obtenir des instructions sur la connexion de [ThreatConnect](https://threatconnect.com/solution/) à Azure Sentinel, consultez [Guide de configuration Microsoft Graph Security Threat Indicators](https://training.threatconnect.com/learn/article/microsoft-graph-security-threat-indicators-integration-configuration-guide-kb-article).

### <a name="threatquotient-threat-intelligence-platform"></a>ThreatQuotient Threat Intelligence Platform

- Pour obtenir des informations de support technique et des instructions pour connecter [ThreatQuotient TIP](https://www.threatq.com/) à Azure Sentinel, consultez [Connecteur Microsoft Sentinel pour l’intégration de ThreatQ](https://appsource.microsoft.com/product/web-apps/threatquotientinc1595345895602.microsoft-sentinel-connector-threatq?src=health&tab=DetailsAndSupport) .

## <a name="incident-enrichment-sources"></a>Sources d’enrichissement des incidents

Outre leur utilisation pour importer des indicateurs de menace, les flux d’informations sur les menaces peuvent également servir de source pour enrichir les informations de vos incidents et fournir plus de contexte à vos investigations. Les flux suivants servent à cet effet et fournissent des règles d’application logique à utiliser dans votre [réponse automatique aux incidents](automate-responses-with-playbooks.md).

### <a name="hyas-insight"></a>HYAS Insight

- Recherchez et activez les règles d’enrichissement des incidents pour [HYAS Insight](https://www.hyas.com/hyas-insight) dans le [référentiel GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) d’Azure Sentinel. Recherchez des sous-dossiers commençant par « Erich-Sentinel-Incident-HYAS-Insight- ».
- Consultez la [documentation du connecteur](/connectors/hyasinsight/) d’application logique HYAS Insight.

### <a name="recorded-future-security-intelligence-platform"></a>Recorded Future Security Intelligence Platform

- Recherchez et activez les règles d’enrichissement des incidents pour [Recorded Future](https://www.recordedfuture.com/integrations/microsoft-azure/) dans le [référentiel GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) d’Azure Sentinel. Recherchez des sous-dossiers commençant par « RecordedFuture_ ».
- Consultez la [documentation du connecteur](/connectors/recordedfuture/) d’application logique Recorded Future.

### <a name="reversinglabs-titaniumcloud"></a>ReversingLabs TitaniumCloud

- Recherchez et activez les règles d’enrichissement des incidents pour [ReversingLabs](https://www.reversinglabs.com/products/file-reputation-service) dans le [référentiel GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Enrich-SentinelIncident-ReversingLabs-File-Information) d’Azure Sentinel.
- Consultez la [documentation du connecteur](/connectors/reversinglabsintelligence/) d’application logique ReversingLabs intelligence.

### <a name="riskiq-passive-total"></a>RiskIQ Passive Total

- Recherchez et activez les règles d’enrichissement des incidents pour [RiskIQ Passive Total](https://www.riskiq.com/products/passivetotal/) dans le [référentiel GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) d’Azure Sentinel. Recherchez des sous-dossiers commençant par « Erich-Sentinel-Incident-RiskIQ- ».
- Découvrez [plus d’informations](https://techcommunity.microsoft.com/t5/azure-sentinel/enrich-azure-sentinel-security-incidents-with-the-riskiq/ba-p/1534412) sur l’utilisation des playbooks RiskIQ.
- Consultez la [documentation du connecteur](/connectors/riskiqpassivetotal/) d’application logique RiskIQ PassiveTotal.

### <a name="virus-total"></a>Nombre Total de virus

- Recherchez et activez les règles d’enrichissement des incidents pour [Nombre total de virus](https://developers.virustotal.com/v3.0/reference) dans le [référentiel GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) d’Azure Sentinel. Recherchez des sous-dossiers commençant par « Get-VirusTotal » et « Get-VTURL ».
- Consultez la [documentation de connecteur](/connectors/virustotal/) d’application logique Virus Total.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter votre fournisseur d’intelligence des menaces à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, consultez les articles suivants.

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](./tutorial-detect-threats-built-in.md).
