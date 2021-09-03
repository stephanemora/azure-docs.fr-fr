---
title: Informations de référence de schéma de normalisation DNS Azure Sentinel | Microsoft Docs
description: Cet article décrit le schéma de normalisation DNS Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/15/2021
ms.author: bagol
ms.openlocfilehash: 38f90522a465f6934c88249be3eae2b505c99303
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122566152"
---
# <a name="azure-sentinel-dns-normalization-schema-reference-public-preview"></a>Informations de référence de schéma de normalisation DNS Azure Sentinel (préversion publique)

Le modèle d’informations DNS est utilisé pour décrire les événements signalés par un serveur DNS ou un système de sécurité DNS, et il est utilisé par Azure Sentinel pour activer l’analytique indépendante de la source.

Pour plus d’informations, consultez [Normalisation et modèle ASIM (Azure Sentinel Information Model)](normalization.md).

> [!IMPORTANT]
> Le schéma de normalisation DNS est actuellement en PRÉVERSION. Cette fonctionnalité est fournie sans contrat de niveau de service et n’est pas recommandée pour des charges de travail de production.
>
> Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="guidelines-for-collecting-dns-events"></a>Instructions pour la collecte d’événements DNS

DNS est un protocole unique en ce qu’il peut croiser un grand nombre d’ordinateurs. En outre, étant donné que DNS utilise le protocole UDP, les requêtes et les réponses sont découplées et ne sont pas directement liées les unes aux autres.

L’illustration suivante montre un flux de requête DNS simplifié, y compris quatre segments. Une requête réelle peut être plus complexe, avec plus de segments impliqués.

:::image type="content" source="media/normalization/dns-request-flow.png" alt-text="Un flux de requête DNS simplifié.":::

Étant donné que les segments de requête et de réponse ne sont pas directement connectés les uns aux autres dans le flux de requête DNS, la journalisation complète peut entraîner une duplication importante.

Le segment le plus précieux à consigner est la réponse au client, qui fournit les requêtes de nom de domaine, le résultat de la recherche et l’adresse IP du client. Alors que de nombreux systèmes DNS consignent uniquement ce segment, il existe une valeur dans la journalisation des autres parties. Par exemple, une attaque par empoisonnement du cache DNS tire souvent parti des réponses factices d’un serveur en amont.

Si votre source de données prend en charge la journalisation DNS complète et que vous avez choisi de consigner plusieurs segments, vous devez ajuster vos requêtes pour empêcher la duplication des données dans Azure Sentinel.

Par exemple, vous pouvez modifier votre requête avec la normalisation suivante :

```kql
imDNS | where SrcIpAddr != "127.0.0.1" and EventSubType == "response"
```

## <a name="parsers"></a>Analyseurs

Les fonctions KQL qui implémentent le modèle d’informations DNS portent les noms suivants :

| Name | Description | Instructions d’utilisation |
| --- | --- | --- |
| **imDNS** | Analyseur d’agrégation qui utilise *union* pour inclure des événements normalisés de toutes les sources DNS. |– Mettez à jour cet analyseur si vous souhaitez ajouter ou supprimer des sources de l’analytique indépendante de la source. <br><br>– Utilisez cette fonction dans vos requêtes indépendantes de la source.|
| **imDNS\<vendor\>\<product\>** | Les analyseurs spécifiques à la source implémentent la normalisation pour une source spécifique, telle que *imDNSWindowsOMS*. |– Ajoutez un analyseur spécifique à la source pour une source lorsqu’il n’existe aucun analyseur de normalisation intégré. Mettez à jour l’analyseur d’agrégation pour inclure la référence à votre nouvel analyseur. <br><br>– Mettez à jour un analyseur spécifique à la source pour résoudre les problèmes d’analyse et de normalisation.<br><br>– Utilisez un analyseur spécifique à la source pour l’analytique spécifique à la source.|
| | | |

Les analyseurs peuvent être déployés à partir du [référentiel GitHub Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM).

## <a name="normalized-content"></a>Contenu normalisé

La prise en charge du schéma ASIM DNS comprend également la prise en charge des règles d’analytique intégrées suivantes avec des analyseurs d’authentification normalisés. Tandis que les liens au référentiel GitHub Azure Sentinel sont fournis ci-dessous comme référence, vous pouvez aussi trouver ces règles dans la [galerie de règles Azure Sentinel Analytics](detect-threats-built-in.md). Utilisez les pages GitHub liées pour copier toutes les requêtes de chasse appropriées pour les règles listées.

Les règles d’analytique intégrées suivantes fonctionnent désormais avec les analyseurs DNS normalisés :
 - [Trop de requêtes DNS NXDOMAIN (DNS normalisé)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_ExcessiveNXDOMAINDNSQueries.yaml)
 - [Événements DNS liés aux pools d’exploration (DNS normalisé)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_Miners.yaml)
 - [Événements DNS liés aux proxys ToR (DNS normalisé)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_TorProxies.yaml)
 - [Domaines Barium connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumDomainIOC112020.yaml)
 - [Adresses IP Barium connues](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumIPIOC112020.yaml) 
 - [Des vulnérabilités Exchange Server ont divulgué la correspondance IoC de mars 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
 - [Domaines et hachages GALLIUM connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
 - [Known IRIDIUM IP (Adresse IP IRIDIUM connue)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
 - [NOBELIUM - Domaine et IOC IP - Mars 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)
 - [Domaines/adresses IP de groupe Phosphorus connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
 - [Domaines de groupe STRONTIUM connus – Juillet 2019](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
 - [Balise réseau Solorigate](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
 - [Domaines THALLIUM inclus dans DCU](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
 - [Codes de hachage de programme malveillant Comebacker et Klackring de ZINC connus](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)



## <a name="schema-details"></a>Détails du schéma

Le modèle d’informations DNS est aligné sur le [schéma d’entité DNS OSSEM](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/dns.md).

Pour plus d’informations, consultez les [informations de référence sur les paramètres système du nom de domaine IANA (Internet Assigned Numbers Authority)](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).

### <a name="log-analytics-fields"></a>Champs Log Analytics

Les champs suivants sont générés par Log Analytics pour chaque enregistrement et vous pouvez les remplacer quand [vous créez un connecteur personnalisé](create-custom-connector.md).

| **Champ** | **Type** | **Description** |
| --- | --- | --- |
| <a name=timegenerated></a>**TimeGenerated** | Date/heure | Heure à laquelle l’événement a été généré par l’appareil de création de rapports. |
| **\_ResourceId** | guid | ID de ressource Azure de l’appareil ou du service de création de rapports, ou l’ID de ressource de redirecteur de journal pour les événements transférés à l’aide de Syslog, CEF ou WEF. |
| | | |

> [!NOTE]
> D’autres champs de Log Analytics, moins relatifs à la sécurité, sont documentés avec [Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
> 

### <a name="event-fields"></a>Champs de l’événement

Les champs d’événement sont communs à tous les schémas et décrivent l’activité elle-même et l’appareil de création de rapports.

| **Champ** | **Classe** | **Type** | **Exemple** | **Discussion** |
| --- | --- | --- | --- | --- |
| **EventMessage** | Facultatif | Chaîne | | Message général ou description, inclus dans l’enregistrement ou généré depuis l’enregistrement. |
| **EventCount** | Obligatoire | Integer | `1` | Nombre d’événements décrits par l’enregistrement. <br><br>Cette valeur est utilisée lorsque la source prend en charge l’agrégation, et un seul enregistrement peut représenter plusieurs événements. <br><br>Pour les autres sources, la valeur doit être **1**. |
| **EventStartTime** | Obligatoire | Date/heure | | Si la source prend en charge l’agrégation et que l’enregistrement représente plusieurs événements, utilisez ce champ pour spécifier l’heure à laquelle le premier événement a été généré. <br><br>Dans d’autres cas, In other cases, mettez en alias le champ [TimeGenerated](#timegenerated). |
| **EventEndTime** | | Alias || Alias au champ [TimeGenerated](#timegenerated). |
| **EventType** | Obligatoire | Énuméré | `lookup` | Indique l’opération signalée par l’enregistrement. <br><Br> Pour les enregistrements DNS, cette valeur serait le [code d’opération DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). |
| **EventSubType** | Facultatif | Énuméré || **requête** ou **réponse**. Pour la plupart des sources, [seules les réponses sont consignées](#guidelines-for-collecting-dns-events) et par conséquent, la valeur est souvent **response**.  |
| **EventResult** | Obligatoire | Énuméré | `Success` | L’une des valeurs suivantes : **Succès**, **Partiel**, **Échec**, **NA** (Non applicable).<br> <br>La valeur peut être fournie dans l’enregistrement source à l’aide de termes différents, qui doivent être normalisés avec ces valeurs. Sinon, la source peut fournir uniquement le champ [EventResultDetails](#eventresultdetails) qui devrait être analysé pour dériver la valeur EventResult.<br> <br>Si cet enregistrement représente une requête et non une réponse, affectez la valeur **NA**. |
| <a name=eventresultdetails></a>**EventResultDetails** | Obligatoire | Alias | `NXDOMAIN` | Raison ou détails du résultat rapporté dans le champ **_EventResult_**. Met en alias le champ [ResponseCodeName](#responsecodename).|
| **EventOriginalUid** | Facultatif | Chaîne | | ID unique de l’enregistrement d’origine, s’il est fourni par la source. |
| **EventOriginalType**   | Facultatif    | Chaîne  | `lookup` |   Type ou ID d’événement d’origine, s’il est fourni par la source. |
| <a name ="eventproduct"></a>**EventProduct** | Obligatoire | Chaîne | `DNS Server` | Produit générant l’événement. Ce champ peut ne pas être disponible dans l’enregistrement source, auquel cas il doit être défini par l’analyseur. |
| **EventProductVersion** | Facultatif | Chaîne | `12.1` | Version du produit générant l’événement. Ce champ peut ne pas être disponible dans l’enregistrement source, auquel cas il doit être défini par l’analyseur. |
| **EventVendor** | Obligatoire | Chaîne | `Microsoft` | Fournisseur du produit générant l’événement. Ce champ peut ne pas être disponible dans l’enregistrement source, auquel cas il doit être défini par l’analyseur. |
| **EventSchemaVersion** | Obligatoire | Chaîne | `0.1.1` | La version du schéma documenté ici est **0.1.1**. |
| **EventReportUrl** | Facultatif | Chaîne | | URL fournie dans l’événement pour une ressource qui apporte plus d’informations sur l’événement. |
| <a name="dvc"></a>**Dvc** | Obligatoire       | Chaîne     |    `ContosoDc.Contoso.Azure` |           Identificateur unique de l’appareil sur lequel l’événement s’est produit. <br><br>Ce champ peut prendre l’alias des champs [DvcId](#dvcid), [DvcHostname](#dvchostname) ou [DvcIpAddr](#dvcipaddr). Les sources cloud pour lesquelles il n’y a pas d’appareil apparent, utilisez la même valeur que celle du champ [Event Product](#eventproduct).         |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Recommandé | Adresse IP |  `45.21.42.12` |       Adresse IP de l’appareil sur lequel l’événement de processus s’est produit.  |
| <a name ="dvchostname"></a>**DvcHostname**         | Recommandé | Nom d’hôte   | `ContosoDc.Contoso.Azure` |              Nom d’hôte de l’appareil sur lequel l’événement de processus s’est produit.                |
| <a name ="dvcid"></a>**DvcId**               | Facultatif    | Chaîne     || ID unique de l’appareil sur lequel l’événement de processus s’est produit. <br><br>Exemple : `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| <a name=additionalfields></a>**AdditionalFields** | Facultatif | Dynamique | | Si votre source fournit d’autres informations à conserver, conservez-les avec les noms de champs d’origine ou créez le champ dynamique **AdditionalFields**, puis ajoutez-y les informations supplémentaires sous forme de paires clé/valeur. |
| | | | | |

### <a name="dns-specific-fields"></a>Champs spécifiques à DNS

Les champs ci-dessous sont spécifiques aux événements DNS. Cela dit, beaucoup d’entre eux ont des similitudes dans d’autres schémas et par conséquent, suivent la même convention d’affectation de noms.

| **Champ** | **Classe** | **Type** | **Exemple** | **Remarques** |
| --- | --- | --- | --- | --- |
| **SrcIpAddr** | Obligatoire | Adresse IP |  `192.168.12.1 `| L’adresse IP du client qui envoie la requête DNS. Pour une requête DNS récursive, cette valeur correspond généralement à l’appareil de création de rapports et, dans la plupart des cas, à **127.0.0.1**. |
| **SrcPortNumber** | Facultatif | Integer |  `54312` | Port source de la requête DNS. |
| **DstIpAddr** | Facultatif | Adresse IP |  `127.0.0.1` | Adresse IP du serveur qui reçoit la requête DNS. Pour une requête DNS régulière, cette valeur correspond généralement à l’appareil de création de rapports et, dans la plupart des cas, à **127.0.0.1**. |
| **DstPortNumber** | Facultatif | Integer |  `53` | Numéro du port de destination |
| **IpAddr** | | Alias | | Alias pour SrcIpAddr |
| <a name=query></a>**DnsQuery** | Obligatoire | FQDN | `www.malicious.com` | Le domaine qui doit être résolu. <br><br>**Remarque** : Certaines sources envoient cette requête dans des formats différents. Par exemple, dans le protocole DNS lui-même, la requête comprend un point ( **.** ) à la fin, qui doit être supprimé.<br><br>Bien que le protocole DNS autorise plusieurs requêtes dans une seule requête, ce scénario est rare, voire même inexistant. Si la requête comporte plusieurs requêtes, stockez la première dans ce champ, puis conservez éventuellement le reste dans le champ [AdditionalFields](#additionalfields). |
| **Domaine** | | Alias || Prendre l’alias [Requête](#query). |
| **DnsQueryType** | Facultatif | Integer | `28` | Ce champ peut contenir des [codes de type d’enregistrement de ressource DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml)). |
| **DnsQueryTypeName** | Obligatoire | Énuméré | `AAAA` | Ce champ peut contenir des noms de [type d’enregistrement de ressource DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>**Remarque** : IANA ne définit pas la casse des valeurs, donc Analytics doit normaliser la casse en fonction des besoins. Si la source fournit uniquement un code de type de requête numérique et non un nom de type de requête, l’analyseur doit inclure une table de recherche pour enrichir cette valeur. |
| <a name=responsename></a>**DnsResponseName** | Facultatif | Chaîne | | Contenu de la réponse, tel qu’il est inclus dans l’enregistrement.<br> <br> Les données de réponse DNS sont incohérentes entre les appareils de création de rapports, sont complexes à analyser et ont moins de valeur pour l’analyse indépendante de la source. Par conséquent, le modèle d’information ne nécessite pas d’analyse ni de normalisation, et Azure Sentinel utilise une fonction auxiliaire pour fournir des informations de réponse. Pour plus d’informations, consultez [Gestion des réponses DNS](#handling-dns-response).|
| <a name=responsecodename></a>**DnsResponseCodeName** |  Obligatoire | Énuméré | `NXDOMAIN` | Le [code de réponse DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>**Remarque** : IANA ne définit pas la casse des valeurs, donc l’analyse doit normaliser la casse. Si la source fournit uniquement un code de réponse et non un nom de code de réponse, l’analyseur doit inclure une table de recherche pour enrichir cette valeur. <br><br> Si cet enregistrement représente une requête et non une réponse, affectez la valeur **NA**. |
| **DnsResponseCode** | Facultatif | Integer | `3` | Le [code de réponse numérique DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).|
| **TransactionIdHex** | Recommandé | Chaîne | | ID de transaction hexadécimal unique DNS. |
| **NetworkProtocol** | Facultatif | Énuméré | `UDP` | Protocole de transport utilisé par l’événement de résolution de réseau. La valeur peut être **UDP** ou **TCP**, et est couramment définie sur **UDP** pour DNS. |
| **DnsQueryClass** | Facultatif | Integer | | L’[ID de classe DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>En pratique, seule la classe **IN** (ID 1) est utilisée, réduisant ainsi la valeur de ce champ.|
| **DnsQueryClassName** | Facultatif | Chaîne | `"IN"` | Le [nom de la classe DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>En pratique, seule la classe **IN** (ID 1) est utilisée, réduisant ainsi la valeur de ce champ. |
| <a name=flags></a>**DnsFlags** | Facultatif | Liste de chaînes | `["DR"]` | Le champ d’indicateurs, tel que fourni par l’appareil de création de rapports. Si les informations d’indicateur sont fournies dans plusieurs champs, concaténez-les avec une virgule comme séparateur. <br><br>Étant donné que les indicateurs DNS sont complexes à analyser et sont moins souvent utilisés par l’analyse, l’analyse et la normalisation ne sont pas nécessaires, et Azure Sentinel utilise une fonction auxiliaire pour fournir des informations d’indicateurs. Pour plus d’informations, consultez [Gestion des réponses DNS](#handling-dns-response).|
| <a name=UrlCategory></a>**UrlCategory** |   | Chaîne | `Educational \\ Phishing` | Une source d’événement DNS peut également rechercher la catégorie des domaines demandés. Ce champ est appelé **_UrlCategory_** pour s’aligner avec le schéma de réseau Azure Sentinel. <br><br>**_DomainCategory_** est ajouté en tant qu’alias qui s’adapte à DNS. |
| **DomainCategory** | | Alias | | Alias pour [UrlCategory](#UrlCategory). |
| **ThreatCategory** |   | Chaîne |   | Si une source d’événement DNS fournit également la sécurité DNS, elle peut également évaluer l’événement DNS. Par exemple, elle peut rechercher l’adresse IP ou le domaine dans une base de données de renseignement sur les menaces et peut affecter le domaine ou l’adresse IP avec une catégorie de menace. |
| **EventSeverity** | Facultatif | Chaîne | `"Informational"` | Si une source d’événement DNS fournit également la sécurité DNS, elle peut également évaluer l’événement DNS. Par exemple, elle peut rechercher l’adresse IP ou le domaine dans une base de données de renseignement sur les menaces, et affecter une gravité en fonction de l’évaluation. |
| **DvcAction** | Facultatif | Chaîne | `"Blocked"` | Si une source d’événement DNS fournit également la sécurité DNS, elle peut entreprendre une action sur la requête, par exemple la bloquer. |
| | | | | |

### <a name="deprecated-aliases"></a>Alias déconseillés

Les champs suivants sont des alias qui sont actuellement déconseillés, mais qui sont conservés à des fins de compatibilité descendante :

- Query (alias vers DnsQuery)
- QueryType (alias vers DnsQueryType)
- QueryTypeName (alias vers DnsQueryTypeName)
- ResponseName (alias vers DnsReasponseName)
- ResponseCodeName (alias vers DnsResponseCodeName)
- ResponseCode (alias vers DnsResponseCode)
- QueryClass (alias vers DnsQueryClass)
- QueryClassName (alias vers DnsQueryClassName)
- Flags (alias vers DnsFlags)

### <a name="additional-entities"></a>Entités supplémentaires

Les événements évoluent autour des entités, telles que les utilisateurs, les hôtes, les processus ou les fichiers, et chaque entité peut nécessiter plusieurs champs pour la décrire. Par exemple :

- Un hôte peut avoir un nom et une adresse IP
- Un seul enregistrement peut inclure plusieurs entités du même type, comme un hôte source et un hôte de destination.

Le schéma DNS décrit dans cet article comprend des champs qui décrivent les entités. Si votre source comprend d’autres informations pour décrire les entités, ajoutez d’autres champs basés sur les entités listées dans le tableau suivant pour capturer ces informations.

Pour plus d’informations, consultez [Normalisations dans Azure Sentinel](normalization.md).


| **Entité** | **Fields** | **Type** | **Champs obligatoires** | **Remarques** |
| --- | --- | --- | --- | --- |
| **Actor** | Acteur\* | Utilisateur |  | La plupart des sources d’événements DNS ne fournissent pas d’informations sur l’utilisateur, ce qui n’est généralement pas une partie du protocole DNS. <br><br>Dans certains cas, l’appareil de création de rapports fournit des informations sur l’utilisateur, généralement en résolvant l’adresse IP source en informations utilisateur. Dans ce cas, représentez l’utilisateur comme décrit dans la documentation relative aux entités de schéma. Utilisez **Actor** comme descripteur, dans la mesure où les informations sont basées sur l’adresse IP source. <br><br>**Remarque** : Lorsque qu’**Actor** est utilisé en tant que descripteur d’entité, il n’est pas nécessaire d’ajouter le champ **Utilisateur**. |
| **Appareil source** | Src\* | Appareil | `SrcIpAddr` | Les sources d’événement DNS signalent en général l’adresse IP de la source de la requête, et par conséquent **SrcIpAddr** est obligatoire. <br><br>Si l’appareil de création de rapports fournit plus d’informations sur la source de la requête, ou si vous enrichissez les données, utilisez les instructions d’entité d’appareil pour normaliser à l’aide de **Src** comme préfixe du champ. |
| **Appareil de destination** | Dst\* | Appareil |  | En règle générale, les sources d’événements DNS ne signalent pas d’informations sur la destination de la requête. Si l’appareil de création de rapports fournit des informations sur la destination de la requête, ou si vous enrichissez les données, utilisez les instructions d’entité d’appareil pour normaliser à l’aide de **Dst** comme préfixe. |
| **Appareil de création de rapport** | Dvc\* | Appareil | `Dvc` | La plupart des événements incluent des informations sur l’appareil de création de rapports. Utiliser le préfixe Dvc pour ces champs|
| **Informations sur le processus** | Processus\* | Processus |  | Informations relatives au processus qui a généré la requête DNS sur l’appareil client. |
| | | | | |

## <a name="handling-dns-response"></a>Gestion de la réponse DNS

Dans la plupart des cas, les événements DNS consignés n’incluent pas les informations de réponse, qui peuvent être volumineuses et détaillées. Si votre enregistrement inclut plus d’informations de réponse, stockez-les dans le champ [ResponseName](#responsename) comme elles apparaissent dans l’enregistrement.

Vous pouvez également fournir une fonction KQL supplémentaire appelée `_imDNS<vendor>Response_`, qui prend la réponse non analysée comme entrée et retourne une valeur dynamique avec la structure suivante :

```kql
[
    {
        "part": "answer"
        "query": "yahoo.com."
        "TTL": 1782
        "Class": "IN"
        "Type": "A"
        "Response": "74.6.231.21"
    }
    {
        "part": "authority"
        "query": "yahoo.com."
        "TTL": 113066
        "Class": "IN"
        "Type": "NS"
        "Response": "ns5.yahoo.com"
    }
    ...
]
```

Les champs de chaque dictionnaire dans la valeur dynamique correspondent aux champs de chaque réponse DNS. L’entrée `part` doit inclure `answer`, `authority` ou `additional` pour refléter la partie dans la réponse à laquelle le dictionnaire appartient.

> [!TIP]
> Pour garantir des performances optimales, appelez la fonction `imDNS<vendor>Response` uniquement lorsque cela est nécessaire, et uniquement après un filtrage initial pour garantir de meilleures performances.
>

## <a name="handling-dns-flags"></a>Gestion des indicateurs DNS

L’analyse et la normalisation ne sont pas requises pour les données d’indicateur. À la place, stockez les données d’indicateur fournies par l’appareil dans le champ [Indicateurs](#flags).

Vous pouvez également fournir une fonction KQL supplémentaire appelée `_imDNS<vendor>Flags_`, qui prend la réponse non analysée comme entrée et retourne une liste dynamique, avec des valeurs booléennes qui représentent chaque indicateur dans l’ordre suivant :

- Authentifié (AD)
- Faisant autorité (AA)
- Vérification désactivée (CD)
- Récurrence disponible (RA)
- Récurrence souhaitée (RD)
- Tronqué (TC)
- Z

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Normalisation dans Azure Sentinel](normalization.md)
- [Informations de référence sur le schéma de normalisation de l’authentification Azure Sentinel (préversion publique)](authentication-normalization-schema.md)
- [Référence de schéma de normalisation des données Azure Sentinel](normalization-schema.md)
- [Informations de référence sur le schéma de normalisation des événements de fichier Azure Sentinel (préversion publique)](file-event-normalization-schema.md)
- [Informations de référence sur le schéma de normalisation des événements de processus Azure Sentinel](process-events-normalization-schema.md)
- [Informations de référence sur le schéma de normalisation des événements du registre Azure Sentinel (préversion publique)](registry-event-normalization-schema.md)
