---
title: Journalisation dans Azure Front Door Standard/Premium (préversion)
description: Cet article explique comment fonctionne la journalisation dans Azure Front Door Standard/Premium.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 03/15/2021
ms.author: duau
ms.openlocfilehash: 531f4a9c9f535779e451ca316a8a5867f6cdaba5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573895"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Journalisation dans Azure Front Door Standard/Premium (préversion)

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Azure Front Door offre différentes formes de journalisation, vous permettant de suivre, superviser et déboguer votre instance Front Door. 

* Les journaux d’accès contiennent des informations détaillées sur chaque requête reçue par AFD. Ils vous permettent d’analyser et de superviser les modèles d’accès et de résoudre les problèmes. 
* Les journaux d’activité apportent une visibilité dans les opérations effectuées sur des ressources Azure.  
* Les journaux de sonde d’intégrité journalisent tous les échecs de sonde sur votre origine. 
* Les journaux WAF (Web Application Firewall) fournissent des informations détaillées sur les requêtes. Elles sont journalisées par le biais du mode de détection ou de prévention d’un point de terminaison Azure Front Door. Ces journaux permettent également de voir un domaine personnalisé configuré avec WAF.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les journaux d’accès, de sonde d’intégrité et WAF ne sont pas activés par défaut. Effectuez les étapes suivantes pour activer la journalisation. Les entrées du journal d’activité sont recueillies par défaut et vous pouvez les afficher dans le Portail Azure. Il peut s’écouler plusieurs minutes avant que les journaux ne soient disponibles. 

Pour stocker vos journaux d’activité, vous disposez de trois options : 

* **Compte de stockage** : Les comptes de stockage conviennent parfaitement aux scénarios où les journaux sont stockés pour une durée plus longue et consultés quand cela s’avère nécessaire. 
* **Hubs d’événements** : Les hubs d’événements constituent une excellente solution pour l’intégration à d’autres outils SIEM (Security Information and Event Management) ou à des magasins de données externes. Par exemple : Splunk/DataDog/Sumo. 
* **Azure Log Analytics :** Azure Log Analytics dans Azure Monitor convient particulièrement aux scénarios généraux de supervision et d’analyse en temps réel des performances d’Azure Front Door.

## <a name="configure-logs"></a>Configurer les journaux

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Recherchez Azure Front Door Standard/Premium, puis sélectionnez le profil Azure Front Door.

1. Dans le profil, accédez à **Supervision**, puis sélectionnez **Paramètres de diagnostic**. Sélectionnez **Ajouter le paramètre de diagnostic**.

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="Capture d’écran de la page d’arrivée des paramètres de diagnostic.":::

1. Sous **Paramètres de diagnostic**, entrez un nom sous  **Nom des paramètres de diagnostic**.

1. Sélectionnez les  **journaux** parmi **FrontDoorAccessLog**, **FrontDoorHealthProbeLog** et **FrontDoorWebApplicationFirewallLog**.

1. Sélectionnez les  **Détails de la destination**. Les options de destination sont les suivantes : 

    * **Envoyer à Log Analytics**
        * Sélectionnez l’*Abonnement* et l’*Espace de travail Log Analytics*.
    * **Archiver dans un compte de stockage**
        * Sélectionnez l’*Abonnement* et le *Compte de stockage*. Définissez le paramètre **Rétention (jours)** .
    * **Diffuser vers un hub d’événements**
        * Sélectionnez l’ *Abonnement, l’Espace de noms du hub d’événements, le Nom du hub d’événements (facultatif)* et le  *Nom de la stratégie du hub d’événements*. 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="Capture d’écran de la page de paramètres de diagnostic.":::

1. Cliquez sur **Save**(Enregistrer).

## <a name="access-log"></a>Journal d’accès

Actuellement, Azure Front Door journalise chaque requête d’API individuellement. Chaque entrée a le schéma suivant et est journalisée au format JSON comme indiqué plus bas. 

| Propriété | Description |
|----------|-------------| 
| TrackingReference | Chaîne de référence unique qui identifie une requête traitée par AFD, également envoyée comme en-tête X-Azure-Ref au client. Nécessaire pour pouvoir effectuer une recherche détaillée dans les journaux d’accès pour une requête spécifique. |
| Temps | Date et heure (UTC) de distribution du contenu demandé au client par la périphérie AFD. |
| HttpMethod | Méthode HTTP utilisée par la requête : DELETE, GET, HEAD, OPTIONS, PATCH, POST ou PUT. |
| HttpVersion | Version HTTP spécifiée par l’observateur dans la requête. |
| RequestUri | URI de la requête reçue. Champ composé du schéma, du port, du domaine, du chemin et de la chaîne de requête |
| HostName | Nom d’hôte dans la requête du client. Si vous activez les domaines personnalisés et que vous avez un domaine générique (*.contoso.com), le nom d’hôte est a.contoso.com. Si vous utilisez un domaine Azure Front Door (contoso.azurefd.net), le nom d’hôte est contoso.azurefd.net. |
| RequestBytes | Taille du message de requête HTTP en octets, en-têtes de requête et corps de requête compris. Nombre d’octets de données que l’observateur a inclus dans la requête, y compris les en-têtes. |
| ResponseBytes | Octets envoyés en tant que réponse par le serveur back-end. |
| UserAgent | Type de navigateur utilisé par le client. |
| ClientIp | Adresse IP du client à l’origine de la requête. S’il existait un en-tête X-Forwarded-For dans la demande, l’adresse IP du client est sélectionnée de la même façon. |
| SocketIp | Adresse IP de la connexion directe à la périphérie AFD. Si le client a utilisé un proxy HTTP ou un équilibreur de charge pour envoyer la requête, la valeur de SocketIp est l’adresse IP du proxy ou de l’équilibreur de charge. |
| Latence | Temps, en millisecondes, entre le moment où le serveur de périphérie AFD reçoit une requête d’un client et le moment où AFD envoie au client le dernier octet de la réponse. Ce champ ne tient pas compte de la latence du réseau et de la mise en mémoire tampon TCP. |
| RequestProtocol | Protocole spécifié par le client dans la requête : HTTP, HTTPS. |
| SecurityProtocol | Version du protocole TLS/SSL utilisée par la requête, ou Null si aucun chiffrement. Les valeurs possibles sont : SSLv3, TLSv1, TLSv1.1 et TLSv1.2 |
| SecurityCipher | Quand le protocole de la requête a la valeur HTTPS, ce champ indique le chiffrement TLS/SSL négocié par le client et AFD. |
| Point de terminaison | Nom de domaine du point de terminaison AFD, par exemple, contoso.z01.azurefd.net |
| HttpStatusCode | Code d’état HTTP retourné par AFD. |
| Pop | Pop de périphérie, qui a répondu à la demande de l’utilisateur.  |
| Cache Status | Code d’état indiquant comment le service CDN traite la requête pour la mise en cache. Les valeurs possibles sont les suivantes. HIT : La requête HTTP a été traitée à partir du cache POP de la périphérie AFD. <br> **MISS** : La requête HTTP a été traitée à partir de l’origine. <br/> **PARTIAL_HIT** : Certains octets d’une requête ont été traités à partir du cache POP de la périphérie AFD et d’autres à partir de l’origine pour les scénarios de segmentation d’objets. <br> **CACHE_NOCONFIG** : Transfert des requêtes sans paramètres de mise en cache, y compris dans le cas d’un contournement. <br/> **PRIVATE_NOSTORE** : Aucun cache configuré par les clients dans les paramètres de mise en cache. <br> **REMOTE_HIT** : La requête a été traitée par le cache du nœud parent. <br/> **N/A** :** Requête refusée par une URL signée et un ensemble de règles. |
| MatchedRulesSetName | Noms des règles traitées. |
| RouteName | Nom de la route à laquelle correspond la requête. |
| ClientPort | Adresse IP du port du client qui a effectué la requête. |
| Referrer | URL du site à l’origine de la requête. |
| TimetoFirstByte | Temps, en millisecondes, entre le moment où AFD reçoit la requête et le moment où le premier octet est envoyé au client tel que mesuré dans Azure Front Door. Cette propriété ne mesure pas les données clientes. |
| ErrorInfo | Ce champ fournit des informations détaillées sur le jeton d’erreur pour chaque réponse. <br> **NoError** : Indique qu’aucune erreur n’a été trouvée. <br> **CertificateError** : Erreur de certificat SSL générique. <br> **CertificateNameCheckFailed** : Le nom d’hôte dans le certificat SSL n’est pas valide ou ne correspond pas. <br> **ClientDisconnected** : Échec de la demande en raison de la connexion réseau du client. <br> **ClientGeoBlocked** : Le client a été bloqué en raison de la localisation géographique de l’IP. <br> **UnspecifiedClientError** : Erreur du client générique. <br> **InvalidRequest** : Demande non valide. Cela peut se produire en raison d’un en-tête, d’un corps et d’une URL incorrect(e)s. <br> **DNSFailure** : Échec DNS. <br> **DNSTimeout** : Requête DNS pour la résolution de l’expiration de back-end. <br> **DNSNameNotResolved** : Le nom ou l’adresse du serveur n’a pas pu être résolue. <br> **OriginConnectionAborted** : La connexion avec l’origine a été interrompue anormalement. <br> **OriginConnectionError** : Erreur de connexion d’origine générique. <br> **OriginConnectionRefused** : La connexion avec l’origine n’a pas été établie. <br> **OriginError** : Erreur d’origine générique. <br> **OriginInvalidRequest** : Une requête non valide a été envoyée à l’origine. <br> **ResponseHeaderTooBig** : L’origine a retourné un en-tête de réponse trop grand. <br> **OriginInvalidResponse** :** L’origine a retourné une réponse non valide ou non reconnue. <br> **OriginTimeout** : Le délai d’expiration de la demande d’origine a expiré. <br> **ResponseHeaderTooBig** : L’origine a retourné un en-tête de réponse trop grand. <br> **RestrictedIP** : La demande a été bloquée en raison d’une adresse IP restreinte. <br> **SSLHandshakeError** : Impossible d’établir la connexion avec l’origine en raison d’un échec d’établissement d’une liaison SSL. <br> **SSLInvalidRootCA** : L’autorité de certification racine n’était pas valide. <br> **SSLInvalidCipher** : Le chiffrement avec lequel la connexion HTTPS a été établie n’était pas valide. <br> **OriginConnectionAborted** : La connexion avec l’origine a été interrompue anormalement. <br> **OriginConnectionRefused** : La connexion avec l’origine n’a pas été établie. <br> **UnspecifiedError** : Une erreur ne correspondant à aucune des erreurs dans le tableau s’est produite. |
| OriginURL | URL complète de l’origine où les requêtes sont envoyées. Champ composé du schéma, de l’en-tête de l’hôte, du port, du chemin et de la chaîne de requête. <br> **Réécriture d’URL** : si l’ensemble de règles inclut une règle de réécriture d’URL, le chemin fait référence au chemin réécrit. <br> **Cache sur POP de périphérie** : S’il s’agit d’une correspondance dans le cache sur POP de périphérie, l’origine est N/A. <br> **Grande requête** : Si le contenu demandé est volumineux et que plusieurs requêtes segmentées retournent à l’origine, ce champ correspond à la première requête destinée à l’origine. Pour plus d’informations, consultez la documentation sur la segmentation des objets. |
| OriginIP | IP d’origine qui a traité la requête. <br> **Correspondance dans le cache sur POP de périphérie** : S’il s’agit d’une correspondance dans le cache sur POP de périphérie, l’origine est N/A. <br> **Grande requête** : Si le contenu demandé est volumineux et que plusieurs requêtes segmentées retournent à l’origine, ce champ correspond à la première requête destinée à l’origine. Pour plus d’informations, consultez la documentation sur la segmentation des objets. |
| OriginName| Nom DNS complet (nom d’hôte dans l’URL d’origine) de l’origine. <br> **Correspondance dans le cache sur POP de périphérie** : S’il s’agit d’une correspondance dans le cache sur POP de périphérie, l’origine est N/A. <br> **Grande requête** : Si le contenu demandé est volumineux et que plusieurs requêtes segmentées retournent à l’origine, ce champ correspond à la première requête destinée à l’origine. Pour plus d’informations, consultez la documentation sur la segmentation des objets. |

## <a name="health-probe-log"></a>Journal de sonde d’intégrité

Les journaux de sonde d’intégrité journalisent tous les échecs de sonde et vous permettent ainsi de diagnostiquer votre origine. Ces journaux fournissent des informations que vous pouvez utiliser pour remettre l’origine en service. Ils peuvent être utiles dans une diversité de scénarios, notamment :

* Vous avez remarqué que le trafic Azure Front Door est envoyé uniquement à certaines origines. Par exemple, seules trois origines sur quatre reçoivent le trafic. Vous souhaitez savoir si les origines reçoivent des sondes et, si ce n’est pas le cas, connaître la raison de l’échec.  

* Vous avez remarqué que le pourcentage d’intégrité de l’origine est plus faible qu’attendu et vous souhaitez savoir quelle origine a échoué et pourquoi.

### <a name="health-probe-log-properties"></a>Propriétés des journaux de sonde d’intégrité

Chaque journal de sonde d’intégrité présente le schéma suivant.

| Propriété | Description |
| --- | --- |
| HealthProbeId  | ID unique d’identification de la requête. |
| Temps | Durée d’exécution de la sonde |
| HttpMethod | Méthode HTTP utilisée par la requête de sonde d’intégrité. Les valeurs peuvent être GET ou HEAD selon les configurations des sondes d’intégrité. |
| Résultats | État de la sonde d’intégrité à l’origine. Il peut s’agir d’une réussite ou d’un texte d’erreur. |
| HttpStatusCode  | Code d’état HTTP retourné par l’origine. |
| ProbeURL (cible) | URL complète de l’origine où les requêtes sont envoyées. Champ composé du schéma, de l’en-tête de l’hôte, du chemin et de la chaîne de requête. |
| OriginName  | Origine où les requêtes sont envoyées. Ce champ permet de localiser les origines concernées si l’origine est configurée avec un nom de domaine complet. |
| POP | Pop de périphérie qui a envoyé la requête de sonde. |
| Adresse IP d’origine | IP d’origine cible. Ce champ est utile pour localiser les origines concernées si vous configurez l’origine avec un nom de domaine complet. |
| TotolaLatency | Temps entre le moment où la périphérie AFDX envoie la requête à l’origine et le moment où l’origine envoie la dernière réponse à la périphérie AFDX. |
| ConnectionLatency| Durée de la configuration de la connexion TCP pour envoyer la requête de sonde HTTP à l’origine. | 
| DNSResolution Latency | Durée de la résolution DNS si l’origine est configurée comme nom de domaine complet et non comme IP. N/A si l’origine est configurée comme IP. |

### <a name="health-probe-log-sample-in-json"></a>Exemple de journal de sonde d’intégrité en JSON

`{ "records": [ { "time": "2021-02-02T07:15:37.3640748Z",
      "resourceId": "/SUBSCRIPTIONS/27CAFCA8-B9A4-4264-B399-45D0C9CCA1AB/RESOURCEGROUPS/AFDXPRIVATEPREVIEW/PROVIDERS/MICROSOFT.CDN/PROFILES/AFDXPRIVATEPREVIEW-JESSIE",
      "category": "FrontDoorHealthProbeLog",
      "operationName": "Microsoft.Cdn/Profiles/FrontDoorHealthProbeLog/Write",
      "properties": { "healthProbeId": "9642AEA07BA64675A0A7AD214ACF746E",
        "POP": "MAA",
        "httpVerb": "HEAD",
        "result": "OriginError",
        "httpStatusCode": "400",
        "probeURL": "http://afdxprivatepreview.blob.core.windows.net:80/",
        "originName": "afdxprivatepreview.blob.core.windows.net",
        "originIP": "52.239.224.228:80",
        "totalLatencyMilliseconds": "141",
        "connectionLatencyMilliseconds": "68",
        "DNSLatencyMicroseconds": "1814" } } ]
} `

## <a name="activity-logs"></a>Journaux d’activité

Les journaux d’activité fournissent des informations sur les opérations effectuées sur Azure Front Door Standard/Premium. Ils incluent des informations détaillées sur les opérations d’écriture (quoi, qui, quand) effectuées sur Azure Front Door. 

> [!NOTE]
> Les journaux d’activité n’incluent pas les opérations GET. Ils n’incluent pas non plus les opérations que vous effectuez à l’aide du portail Azure ou de l’API Gestion d’origine. 

Vous pouvez accéder aux journaux d’activité de votre service Front Door ou à tous les journaux de vos ressources Azure dans Azure Monitor.

Pour afficher les journaux d’activité :

1. Sélectionnez votre profil Front Door.

1. Sélectionnez **Journal d’activité**.

1. Choisissez une étendue de filtrage, puis sélectionnez **Appliquer**.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur les [rapports Azure Front Door Standard/Premium (préversion)](how-to-reports.md).
- Apprenez-en davantage sur les [métriques de supervision en temps réel Azure Front Door Standard/Premium (préversion)](how-to-monitor-metrics.md).
