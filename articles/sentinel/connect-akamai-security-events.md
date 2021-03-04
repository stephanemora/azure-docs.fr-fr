---
title: Connecter votre collecteur Akamai Security Events à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur Akamai Security Events pour extraire des journaux de sécurité des solutions Akamai dans Azure Sentinel. Affichez les données Akamai dans des classeurs, créez des alertes et améliorez l’investigation.
services: sentinel
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 8aa5a52a06713b4f00b43205a57148049a8ef8da
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711958"
---
# <a name="connect-your-akamai-security-events-collector-to-azure-sentinel"></a>Connecter votre collecteur Akamai Security Events à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Akamai Security Events est actuellement disponible en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Cet article explique comment connecter votre collecteur Akamai Security Events à Azure Sentinel. Le connecteur de données Akamai Security Events vous permet de connecter facilement vos journaux Akamai à Azure Sentinel, pour ensuite afficher les données dans des classeurs, les interroger pour créer des alertes personnalisées et les incorporer pour améliorer l’investigation. L’intégration entre le collecteur Akamai Security Events et Azure Sentinel utilise Syslog au format CEF, un redirecteur de journaux Linux et l’agent Log Analytics. Elle utilise également un analyseur de journal personnalisé basé sur une fonction Kusto.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

- Vous devez disposer d’autorisations de lecture sur les clés partagées pour accéder à l’espace de travail. [En savoir plus sur les clés d’espace de travail](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-akamai-security-events-logs-to-azure-sentinel"></a>Envoyer les journaux Akamai Security Events à Azure Sentinel

Pour récupérer ses journaux dans Azure Sentinel, configurez votre collecteur Akamai Security Events pour qu’il envoie des messages Syslog au format CEF à un serveur de transfert de journaux Linux (exécutant rsyslog ou syslog-ng). L’agent Log Analytics sera installé sur ce serveur et transfèrera les journaux à votre espace de travail Azure Sentinel.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez **Akamai Security Events (préversion)** , puis sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les instructions de l’onglet **Instructions**, sous **Configuration** :

    1. Sous **1. Configurer l’agent Syslog Linux** : Effectuez cette étape si vous n’avez pas encore de redirecteur de journal en cours d’exécution, ou si vous en avez besoin d’un autre. Consultez [ÉTAPE 1 : Déployer le redirecteur de journal](connect-cef-agent.md) dans la documentation d’Azure Sentinel pour obtenir des informations sur la taille, des instructions et des explications plus détaillées.

    1. Sous **2. Transférer les journaux CEF (Common Event Format) vers l’agent Syslog** – Suivez les instructions d’Akamai pour [configurer l’intégration SIEM](https://developer.akamai.com/tools/integrations/siem) et [configurer un connecteur CEF](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector). Ce connecteur reçoit des événements de sécurité de vos solutions Akamai quasiment en temps réel à l’aide de l’API OPEN SIEM et les convertit du format JSON au format CEF.
    
        Cette configuration doit inclure les éléments suivants :
    
        - Destination du journal : nom d’hôte et/ou adresse IP de votre serveur de transfert de journaux
        - Protocole et port : TCP 514 (si cela est recommandé, veillez à effectuer la modification parallèle dans le démon Syslog sur votre serveur de transfert de journaux)
        - Format du journal : CEF
        - Types de journaux : tous ceux disponibles

    1. Sous **3. Valider la connexion**  : Vérifiez l’ingestion des données en copiant la commande sur la page du connecteur et en l’exécutant sur votre redirecteur de journal. Consultez [ÉTAPE 3 : Valider la connectivité](connect-cef-verify.md) dans la documentation d’Azure Sentinel pour obtenir des instructions et des explications plus détaillées.

        Environ 20 minutes peuvent être nécessaires avant que vos journaux commencent à apparaître dans Log Analytics.

## <a name="find-your-data"></a>Recherche de données

Après l’établissement d’une connexion réussie, les données s’affichent dans **Journaux**, sous la section **Azure Sentinel** dans le tableau *CommonSecurityLog*.

Ce connecteur de données dépend d’un analyseur basé sur une fonction Kusto pour pouvoir fonctionner normalement. Pour configurer la fonction Kusto **AkamaiSIEMEvent** à utiliser dans les requêtes et les classeurs, procédez comme suit.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Journaux**.

1. Copiez la requête suivante et collez-la dans la fenêtre de requête.
    ```kusto
    CommonSecurityLog 
    | where DeviceVendor == 'Akamai'
    | where DeviceProduct == 'akamai_siem'
    | extend EventVendor = 'Akamai'
    | extend EventProduct = 'akamai_siem'
    | extend EventProductVersion = '1.0'
    | extend EventId = DeviceEventClassID
    | extend EventCategory = Activity
    | extend EventSeverity = LogSeverity
    | extend DvcAction = DeviceAction
    | extend NetworkApplicationProtocol = ApplicationProtocol
    | extend Ipv6Src = DeviceCustomIPv6Address2
    | extend RuleName = DeviceCustomString1
    | extend RuleMessages = DeviceCustomString2
    | extend RuleData = DeviceCustomString3
    | extend RuleSelectors = DeviceCustomString4
    | extend ClientReputation = DeviceCustomString5
    | extend ApiId = DeviceCustomString6
    | extend RequestId = DevicePayloadId
    | extend DstDvcHostname = DestinationHostName
    | extend DstPortNumber = DestinationPort
    | extend ConfigId = FlexString1
    | extend PolicyId = FlexString2
    | extend NetworkBytes = SentBytes
    | extend UrlOriginal = RequestURL
    | extend HttpRequestMethod = RequestMethod
    | extend SrcIpAddr = SourceIP
    | extend EventStartTime = datetime(1970-01-01) + tolong(extract(@'.*start=(.*?);', 1, AdditionalExtensions)) * 1s 
    | extend SlowPostAction = extract(@'.*AkamaiSiemSlowPostAction=(.*?);', 1, AdditionalExtensions)
    | extend SlowPostRate = extract(@'.*AkamaiSiemSlowPostRate=(.*?);', 1, AdditionalExtensions)
    | extend RuleVersions = extract(@'.*AkamaiSiemRuleVersions=,?(.*?);', 1, AdditionalExtensions)
    | extend RuleTags = extract(@'.*AkamaiSiemRuleTags=(.*?);', 1, AdditionalExtensions)
    | extend ApiKey = extract(@'.*AkamaiSiemApiKey=(.*?);', 1, AdditionalExtensions)
    | extend Tls = extract(@'.*AkamaiSiemTLSVersion=(.*?);', 1, AdditionalExtensions)
    | extend RequestHeaders = extract(@'.*AkamaiSiemRequestHeaders=;?(.*?);', 1, AdditionalExtensions)
    | extend ResponseHeaders = extract(@'.*AkamaiSiemResponseHeaders=(.*?);', 1, AdditionalExtensions)
    | extend HttpStatusCode = extract(@'.*AkamaiSiemResponseStatus=(.*?);', 1, AdditionalExtensions)
    | extend GeoContinent = extract(@'.*AkamaiSiemContinent=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCountry = extract(@'.*AkamaiSiemCountry=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCity = extract(@'.*AkamaiSiemCity=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoRegion = extract(@'.*AkamaiSiemRegion=(.*?);', 1, AdditionalExtensions)
    | extend GeoAsn = extract(@'.*AkamaiSiemASN=(\d+)', 1, AdditionalExtensions)
    | extend Custom = extract(@'.*AkamaiSiemCusomData=(.*?)', 1, AdditionalExtensions)
    | project TimeGenerated
            , EventVendor
            , EventProduct
            , EventProductVersion
            , EventStartTime
            , EventId
            , EventCategory
            , EventSeverity
            , DvcAction
            , NetworkApplicationProtocol
            , Ipv6Src
            , RuleName
            , RuleMessages
            , RuleData
            , RuleSelectors
            , ClientReputation
            , ApiId
            , RequestId
            , DstDvcHostname
            , DstPortNumber
            , ConfigId
            , PolicyId
            , NetworkBytes
            , UrlOriginal
            , HttpRequestMethod
            , SrcIpAddr
            , SlowPostAction
            , SlowPostRate
            , RuleVersions
            , RuleTags
            , ApiKey
            , Tls
            , RequestHeaders
            , ResponseHeaders
            , HttpStatusCode
            , GeoContinent
            , SrcGeoCountry
            , SrcGeoCity
            , SrcGeoRegion
            , GeoAsn
            , Custom
    ```

1. Cliquez sur la liste déroulante **Enregistrer**, puis sur **Enregistrer**. Dans le panneau **Enregistrer** :

    1. Sous **Nom**, entrez **AkamaiSIEMEvent**.

    1. Sous **Enregistrer sous**, choisissez **Fonction**.

    1. Sous **Alias de fonction**, entrez **AkamaiSIEMEvent**.

    1. Sous **Catégorie**, entrez **Fonctions**.

    1. Cliquez sur **Enregistrer**.

    L’activation des applications de fonction prend généralement entre 10 et 15 minutes.

Vous êtes maintenant prêt à interroger les données Akamai en entrant `AkamaiSIEMEvent` dans la ligne supérieure de la fenêtre de requête.

Pour obtenir plus d’exemples de requêtes, consultez l’onglet **Étapes suivantes** dans la page du connecteur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter Akamai Security Events à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.