---
title: Protocole WebSocket pour la reconnaissance vocale Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Documentation sur le protocole basé sur les WebSockets du service Reconnaissance vocale Bing
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e7f51d49624d5019bec058a2d12f6ca2f1366938
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966882"
---
# <a name="bing-speech-websocket-protocol"></a>Protocole WebSocket pour la reconnaissance vocale Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

La Reconnaissance vocale Bing est une plateforme cloud qui propose les algorithmes les plus avancés pour la conversion de contenu vocal en texte. Le protocole de la reconnaissance vocale Bing définit la [configuration de la connexion](#connection-establishment) entre les applications clientes et le service, et les messages de reconnaissance vocale échangés entre les différentes parties ([messages provenant du client](#client-originated-messages) et [messages provenant du service](#service-originated-messages)). En outre, les [messages de télémétrie](#telemetry-schema) et la [gestion des erreurs](#error-handling) sont décrits.

## <a name="connection-establishment"></a>Établissement de la connexion

Le protocole du service Speech suit la spécification de la norme WebSocket [IETF RFC 6455](https://tools.ietf.org/html/rfc6455). Une connexion WebSocket débute par une requête HTTP contenant des en-têtes HTTP qui indiquent le souhait du client de mettre à niveau la connexion à un WebSocket au lieu d’utiliser la sémantique HTTP. Le serveur indique sa volonté de participer à la connexion WebSocket en retournant une réponse HTTP `101 Switching Protocols`. Une fois la négociation effectuée, le client et le service maintiennent le socket ouvert et commencent à utiliser un protocole basé sur un message pour envoyer et recevoir des informations.

Pour commencer la négociation WebSocket, l’application cliente envoie une demande GET HTTPS au service. Elle inclut les en-têtes de mise à niveau WebSocket standard, ainsi que d’autres en-têtes qui sont spécifiques à la reconnaissance vocale.

```HTTP
GET /speech/recognition/interactive/cognitiveservices/v1 HTTP/1.1
Host: speech.platform.bing.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Authorization: t=EwCIAgALBAAUWkziSCJKS1VkhugDegv7L0eAAJqBYKKTzpPZOeGk7RfZmdBhYY28jl&p=
X-ConnectionId: A140CAF92F71469FA41C72C7B5849253
Origin: https://speech.platform.bing.com
```

Le service répond comme suit :

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Toutes les demandes de reconnaissance vocale nécessitent le chiffrement [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security). L’utilisation de demandes de reconnaissance vocale non chiffrées n’est pas prise en charge. La version TLS suivante est prise en charge :

* TLS 1.2

### <a name="connection-identifier"></a>Identificateur de connexion

Le service Speech requiert que tous les clients incluent un ID unique pour identifier la connexion. Les clients *doivent* inclure l’en-tête *X-ConnectionId* quand ils démarrent une négociation WebSocket. L’en-tête *X-ConnectionId* doit être une valeur [d’identificateur unique universel](https://en.wikipedia.org/wiki/Universally_unique_identifier) (UUID). Les demandes de mise à niveau WebSocket qui n’incluent pas l’en-tête *X-ConnectionId*, qui ne spécifient pas une valeur pour l’en-tête *X-ConnectionId* ou qui n’incluent pas une valeur UUID valide sont rejetées par le service avec une réponse HTTP `400 Bad Request`.

### <a name="authorization"></a>Authorization

Outre les en-têtes de négociation WebSocket standard, les demandes de reconnaissance vocale nécessitent un en-tête *Authorization*. Les demandes de connexion sans cet en-tête sont rejetées par le service avec une réponse HTTP `403 Forbidden`.

L’en-tête *Authorization* doit contenir un jeton d’accès JWT (JSON Web Token).

Pour plus d’informations sur la façon de s’abonner et d’obtenir des clés API permettant de récupérer des jetons d’accès JWT valides, consultez la page [Abonnement à Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

La clé API est transmise au service de jetons. Par exemple :

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Les informations d’en-tête suivantes sont requises pour l’accès au moyen d’un jeton.

| Name | Format | Description |
|----|----|----|
| Ocp-Apim-Subscription-Key | ASCII | Votre clé d’abonnement |

Le service de jetons renvoie le jeton d’accès JWT sous la forme `text/plain`. Ensuite, le jeton JWT est passé en tant que `Base64 access_token` au processus de négociation sous la forme d’un en-tête *Authorization* préfixé avec la chaîne `Bearer`. Par exemple :

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Cookies

Les clients *doivent* prendre en charge les cookies HTTP comme spécifié dans le document [RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>Redirection HTTP

Les clients *doivent* prendre en charge les mécanismes de redirection standard définis par la [spécification du protocole HTTP](https://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Points de terminaison du service Speech

Les clients *doivent* utiliser un point de terminaison approprié du service Speech. Le point de terminaison est basé sur la langue et le mode de reconnaissance. Le tableau présente quelques exemples.

| Mode | Path | URI de service |
| -----|-----|-----|
| Interactive | /speech/recognition/interactive/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Conversation | /speech/recognition/conversation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| Dictation | /speech/recognition/dictation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Pour plus d’informations, consultez la page [URI du service](../GetStarted/GetStartedREST.md#service-uri).

### <a name="report-connection-problems"></a>Signaler les problèmes de connexion

Les clients doivent signaler immédiatement tous les problèmes rencontrés pendant l’établissement d’une connexion. Le protocole de message pour signaler les connexions ayant échoué est décrit dans [Données de télémétrie sur les échecs de connexion](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Limitations des durée de connexion

Par rapport aux connexions HTTP aux services web habituelles, les connexions WebSocket durent *longtemps*. Le service Speech impose des limitations quant à la durée des connexions WebSocket au service :

 * La durée maximale de toute connexion WebSocket active est de 10 minutes. Une connexion est active si le service ou le client envoie des messages WebSocket via cette connexion. Le service met fin à la connexion sans avertissement quand la limite est atteinte. Les clients doivent développer des scénarios utilisateur qui ne nécessitent pas que la connexion reste active quand sa durée de vie maximale est atteinte ou sur le point de l’être.

 * La durée maximale de toute connexion WebSocket inactive est de 180 secondes. Une connexion est inactive si ni le service ni le client n’a envoyé un message WebSocket via la connexion. Une fois atteinte la durée de vie inactive maximale, le service met fin à la connexion WebSocket inactive.

## <a name="message-types"></a>Types de messages

Une fois qu’une connexion WebSocket est établie entre le client et le service, ces derniers peuvent envoyer des messages. Cette section décrit le format de ces messages WebSocket.

Le document [IETF RFC 6455](https://tools.ietf.org/html/rfc6455) spécifie que les messages WebSocket peuvent transmettre des données à l’aide d’un encodage binaire ou texte. Les deux encodages utilisent différents formats de communication. Chaque format est optimisé pour que la charge utile du message soit encodée, transmise et décodée de manière efficace.

### <a name="text-websocket-messages"></a>Messages WebSocket à encodage texte

Les messages WebSocket à encodage texte comporte une charge utile d’informations textuelles qui se compose d’une section d’en-têtes et d’un corps séparés par la paire de sauts de ligne à double retour chariot couramment utilisée pour les messages HTTP. En outre, comme les messages HTTP, les messages WebSocket à encodage texte spécifient les en-têtes au format *nom: valeur* séparé par une paire de sauts de ligne à simple retour chariot. Tout texte inclus dans un message WebSocket à encodage texte *doit* utiliser l’encodage [UTF-8](https://tools.ietf.org/html/rfc3629).

Les messages WebSocket à encodage texte doivent spécifier un chemin de message dans l’en-tête *Path*. La valeur de cet en-tête doit être un des types de messages de protocole de reconnaissance vocale définis plus loin dans ce document.

### <a name="binary-websocket-messages"></a>Messages WebSocket à encodage binaire

Les messages WebSocket à encodage binaire comportent une charge utile binaire. Dans le protocole du service Speech, le contenu audio est transmis au service et reçu de ce dernier à l’aide de messages WebSocket à encodage binaire. Tous les autres messages sont des messages WebSocket à encodage texte.

Comme les messages WebSocket à encodage texte, les messages WebSocket à encodage binaire se composent d’une section d’en-tête et d’une section de corps. Les 2 premiers octets du message WebSocket à encodage binaire spécifient, dans l’ordre [big-endian](https://en.wikipedia.org/wiki/Endianness), la taille de la section d’en-tête sous la forme d’un entier de 16 bits. La taille minimale de la section d’en-tête est de 0 octet. La taille maximale est de 8 192 octets. Le texte dans les en-têtes des messages WebSocket à encodage binaire *doit* utiliser l’encodage [US-ASCII](https://tools.ietf.org/html/rfc20).

Le format de l’encodage des en-têtes dans un message WebSocket à encodage binaire est le même format que celui utilisé dans les messages WebSocket à encodage texte. Le format *nom:valeur* est séparé par une paire de sauts de ligne à simple retour chariot. Les messages WebSocket à encodage binaire doivent spécifier un chemin de message dans l’en-tête *Path*. La valeur de cet en-tête doit être un des types de messages de protocole de reconnaissance vocale définis plus loin dans ce document.

Les deux types de messages WebSocket (à encodage texte et à encodage binaire), sont utilisés dans le protocole du service Speech.

## <a name="client-originated-messages"></a>Messages provenant du client

Une fois la connexion établie, le client et le service peuvent commencer à envoyer des messages. Cette section décrit le format et la charge utile des messages que les applications clientes envoient au service Speech. La section [Messages provenant du service](#service-originated-messages) présente les messages qui proviennent du service Speech et qui sont envoyés aux applications clientes.

Les principaux messages envoyés par le client aux services sont les messages `speech.config`, `audio` et `telemetry`. Nous décrivons ci-après les en-têtes de message requis communs à tous ces messages, avant d’aborder chacun de ces derniers en détail.

### <a name="required-message-headers"></a>En-têtes de message requis

Les en-têtes suivants sont requis pour tous les messages provenant du client.

| En-tête | Valeur |
|----|----|
| Path | Chemin du message, tel que spécifié dans ce document |
| X-RequestId | X-RequestId UUID au format "no-dash" |
| X-Timestamp | Horodatage de l’horloge UTC cliente au format ISO 8601 |

#### <a name="x-requestid-header"></a>En-tête X-RequestId

Les demandes provenant du client sont identifiées de manière unique par l’en-tête de message *X-RequestId*. Cet en-tête est requis pour tous les messages provenant du client. La valeur de l’en-tête *X-RequestId* doit être un UUID au format « sans tirets », par exemple, *123e4567e89b12d3a456426655440000*. Elle *ne peut pas* avoir la forme canonique *123e4567-e89b-12d3-a456-426655440000*. Si une demande n’a pas d’en-tête *X-RequestId* ou que la valeur de cet en-tête utilise un format d’UUID incorrect, le service met fin à la connexion WebSocket.

#### <a name="x-timestamp-header"></a>En-tête X-Timestamp

Chaque message envoyé au service Speech par une application cliente *doit* inclure un en-tête *X-Timestamp*. La valeur de cet en-tête est l’heure à laquelle le client envoie le message. Si une demande n’a pas d’en-tête *X-Timestamp* ou que la valeur de cet en-tête utilise un format incorrect, le service met fin à la connexion WebSocket.

La valeur de l’en-tête *X-Timestamp* doit avoir pour format 'aaaa'-'MM'-'jj'T'HH':'mm':'ss'.'fffffffZ' où « fffffff » est une fraction de seconde. Par exemple, « 12.5 » signifie « 12 + 5/10 secondes » et « 12.526 » signifie « 12 plus 526/1000 secondes ». Ce format est conforme à la norme [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) et, contrairement à l’en-tête HTTP *Date* standard, sa précision peut être de l’ordre du millième de seconde. Les applications clientes peuvent arrondir l’horodatage à la milliseconde la plus proche. Les applications clientes doivent s’assurer que l’horloge de l’appareil suit précisément l’heure à l’aide d’un [serveur NTP (Network Time Protocol)](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>Message `speech.config`

Le service Speech doit connaître les caractéristiques de votre application pour fournir la meilleure reconnaissance vocale possible. Les données de caractéristiques requises incluent des informations sur l’appareil et sur le système d’exploitation qui exécute votre application. Vous fournissez ces informations dans le message `speech.config`.

Les clients *doivent* envoyer un message `speech.config` dès qu’ils ont établi la connexion au service Speech et avant d’envoyer des messages `audio`. Vous ne devez envoyer un message `speech.config` qu’une seule fois par connexion.

| Champ | Description |
|----|----|
| Encodage des messages WebSocket | Texte |
| body | Charge utile sous la forme d’une structure JSON |

#### <a name="required-message-headers"></a>En-têtes de message requis

| Nom de l’en-tête | Valeur |
|----|----|
| Path | `speech.config` |
| X-Timestamp | Horodatage de l’horloge UTC cliente au format ISO 8601 |
| Content-Type | application/json; charset=utf-8 |

À l’image de tous les messages provenant d’un client dans le protocole du service Speech, le message `speech.config` *doit* inclure un en-tête *X-Timestamp* qui enregistre l’heure d’horloge UTC cliente à laquelle le message a été envoyé au service. Le message `speech.config` *ne requiert pas* un en-tête *X-RequestId*, car il n’est pas associé à une demande de reconnaissance vocale particulière.

#### <a name="message-payload"></a>Charge utile du message
La charge utile du message `speech.config` est une structure JSON qui contient des informations sur l’application. L’exemple ci-après illustre ces informations. Les informations du contexte du client et de l’appareil sont incluses dans l’élément *context* de la structure JSON.

```JSON
{
  "context": {
    "system": {
      "version": "2.0.12341",
    },
    "os": {
      "platform": "Linux",
      "name": "Debian",
      "version": "2.14324324"
    },
    "device": {
      "manufacturer": "Contoso",
      "model": "Fabrikan",
      "version": "7.341"
      }
    },
  }
}
```

##### <a name="system-element"></a>Élément system

L’élément system.version du message `speech.config` contient la version du logiciel du SDK de reconnaissance vocale utilisé par l’application cliente ou l’appareil. La valeur a pour format *version_majeure.version_mineure.build.branche*. Vous pouvez omettre le composant *branche* s’il n’est pas applicable.

##### <a name="os-element"></a>Élément os

| Champ | Description | Usage |
|-|-|-|
| os.platform | Plateforme de système d’exploitation qui héberge l’application, par exemple, Windows, Android, iOS ou Linux |Obligatoire |
| os.name | Nom du produit de système d’exploitation, par exemple, Debian ou Windows 10 | Obligatoire |
| os.version | Version du système au format *version_majeure.version_mineure.build.branche*. | Obligatoire |

##### <a name="device-element"></a>Élément device

| Champ | Description | Usage |
|-|-|-|
| device.manufacturer | Fabricant de l’appareil | Obligatoire |
| device.model | Modèle de l’appareil | Obligatoire |
| device.version | Version du logiciel de l’appareil fournie par le fabricant de l’appareil. Cette valeur spécifie une version de l’appareil qui peut être suivi par le fabricant. | Obligatoire |

### <a name="message-audio"></a>Message `audio`

Les applications clientes de reconnaissance vocale envoient des données audio au service Speech en convertissant le flux audio en une série de blocs audio. Chaque bloc de données audio comporte un segment de contenu vocal que le service doit transcrire. La taille maximale d’un seul bloc audio est de 8 192 octets. Les messages de flux audio sont des *messages WebSocket à encodage binaire*.

Les clients utilisent le message `audio` pour envoyer un bloc audio au service. Les clients segmentent le flux audio issu du microphone et envoie les blocs ainsi constitués au service Speech afin qu’il les transcrive. Le premier message `audio` doit contenir un en-tête bien formé qui spécifie correctement que le contenu audio est conforme à un des formats d’encodage pris en charge par le service. Les messages `audio` supplémentaires ne contiennent que des données de flux audio binaires lues à partir du microphone.

Les clients peuvent envoyer éventuellement un message `audio` avec un corps de longueur nulle. Ce message indique au service que le client sait que l’utilisateur a arrêté de parler, que l’énoncé est terminé et que le microphone est désactivé.

Le service Speech utilise le premier message `audio` qui contient un identificateur de demande unique pour indiquer le début d’un nouveau cycle de demande/réponse ou *tour*. Une fois que le service reçoit un message `audio` avec un nouvel identificateur de demande, il rejette les messages en file d’attente ou non envoyés qui sont associés à un tour précédent.

| Champ | Description |
|-------------|----------------|
| Encodage des messages WebSocket | Binary |
| body | Données binaires pour le bloc audio. La taille maximale est de 8 192 octets. |

#### <a name="required-message-headers"></a>En-têtes de message requis

Les en-têtes suivants sont requis pour tous les messages `audio`.

| En-tête         |  Valeur     |
| ------------- | ---------------- |
| Path | `audio` |
| X-RequestId | X-RequestId UUID au format "no-dash" |
| X-Timestamp | Horodatage de l’horloge UTC cliente au format ISO 8601 |
| Content-Type | Type de contenu audio. Le type doit être *audio/x-wav* (PCM) ou *audio/silk* (SILK). |

#### <a name="supported-audio-encodings"></a>Encodages audio pris en charge

Cette section décrit les codecs audio pris en charge par le service Speech.

##### <a name="pcm"></a>PCM

Le service Speech accepte le contenu audio PCM (Pulse Code Modulation) non compressé. Le contenu audio étant envoyé au service au format [WAV](https://en.wikipedia.org/wiki/WAV), le premier bloc audio *doit* contenir un en-tête [RIFF](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) (Resource Interchange File Format) valide. Si un client entame un tour avec un bloc audio qui *n’inclut pas* un en-tête RIFF valide, le service refuse la demande et met fin à la connexion WebSocket.

Le contenu audio PCM *doit* être échantillonné à une fréquence de 16 kHz avec 16 bits par échantillon et un canal (*riff-16khz-16bit-mono-pcm*). Le service Speech ne prend pas en charge les flux de données audio stéréo et rejette les flux audio qui n’utilisent pas la vitesse de transmission, l’échantillonnage ou le nombre de canaux spécifiés.

##### <a name="opus"></a>Opus

Opus est un codec audio ouvert, gratuit et polyvalent. Le service Speech prend en charge Opus à une vitesse de transmission constante de `32000` ou `16000`. Seul le conteneur `OGG` pour Opus est pris en charge, qui est spécifié par le type mime `audio/ogg`.

Pour utiliser Opus, modifiez [l’exemple JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) et changez la méthode `RecognizerSetup` à retourner.

```javascript
return SDK.CreateRecognizerWithCustomAudioSource(
            recognizerConfig,
            authentication,
            new SDK.MicAudioSource(
                     new SDK.OpusRecorder(
                     {
                         mimeType: "audio/ogg",
                         bitsPerSecond: 32000
                     }
              )
          ));
```

#### <a name="detect-end-of-speech"></a>Détecter la fin de l’énoncé

L’être humain ne signale pas de manière explicite quand il a fini de parler. Toute application qui accepte un énoncé en entrée a deux options pour gérer la fin de l’énoncé dans un flux audio : détection de la fin de l’énoncé côté service et détection de la fin de l’énoncé côté client. De ces deux options, la détection de la fin de l’énoncé côté service fournit généralement une meilleure expérience utilisateur.

##### <a name="service-end-of-speech-detection"></a>Détection de la fin de l’énoncé côté service

Pour procurer l’expérience de reconnaissance vocale mains libres idéale, les applications permettent au service de détecter quand l’utilisateur a fini de parler. Les clients envoient le flux audio issu du microphone sous la forme de blocs *audio* jusqu’à ce que le service détecte un silence et réponde avec un message `speech.endDetected`.

##### <a name="client-end-of-speech-detection"></a>Détection de la fin de l’énoncé côté client

Les applications clientes qui permettent à l’utilisateur de signaler la fin de l’énoncé d’une certaine façon peuvent également communiquer ce signal au service. Par exemple, une application cliente peut avoir un bouton « Stop » ou « Muet » sur lequel l’utilisateur peut appuyer. Pour signaler la fin de l’énoncé, les applications clientes envoient un message de bloc *audio* avec un corps de longueur nulle. Le service Speech interprète ce message comme étant la fin du flux audio entrant.

### <a name="message-telemetry"></a>Message `telemetry`

Les applications clientes *doivent* accuser réception de la fin de chaque tour en envoyant au service Speech les données de télémétrie relatives au tour. L’accusé de réception de la fin du tour permet au service Speech de s’assurer que tous les messages nécessaires au traitement de la demande et de sa réponse ont été correctement reçus par le client. En outre, cet accusé de réception permet au service Speech de vérifier que les applications clientes fonctionnent comme prévu. Ces informations sont très utiles si vous avez besoin d’aide pour dépanner votre application de reconnaissance vocale.

Les clients doivent accuser réception de la fin d’un tour en envoyant un message `telemetry` peu après la réception d’un message `turn.end`. Les clients doivent tenter d’accuser réception du `turn.end` dès que possible. Si une application cliente ne parvient pas à accuser réception de la fin du tour, le service Speech peut mettre fin à la connexion avec une erreur. Les clients ne doivent envoyer qu’un seul message `telemetry` par demande et réponse identifiées par la valeur *X-RequestId*.

| Champ | Description |
| ------------- | ---------------- |
| Encodage des messages WebSocket | Texte |
| Path | `telemetry` |
| X-Timestamp | Horodatage de l’horloge UTC cliente au format ISO 8601 |
| Content-Type | `application/json` |
| body | Structure JSON qui contient les informations clientes relatives au tour |

Le schéma du corps du message `telemetry` est défini dans la section [Schéma des données de télémétrie](#telemetry-schema).

#### <a name="telemetry-for-interrupted-connections"></a>Données de télémétrie des connexions interrompues

Si la connexion réseau échoue pour une raison quelconque pendant un tour et que le client *ne reçoit pas* de message `turn.end` de la part du service, le client envoie un message `telemetry`. Ce message décrit la demande ayant échoué la prochaine fois que le client établit une connexion au service. Les clients n’ont pas besoin d’essayer d’établir une connexion immédiatement pour envoyer le message `telemetry`. Le message peut être mis en mémoire tampon sur le client et envoyé ultérieurement à l’occasion d’une connexion à l’initiative de l’utilisateur. Le message `telemetry` pour la demande ayant échoué *doit* utiliser la valeur *X-RequestId* issue de cette demande. Il peut être envoyé au service dès qu’une connexion est établie, sans attendre l’envoi ou la réception d’autres messages.

## <a name="service-originated-messages"></a>Messages provenant du service

Cette section décrit les messages qui proviennent du service Speech et qui sont envoyés au client. Le service Speech gère un registre des fonctionnalités clientes et génère les messages requis par chaque client ; ainsi, tous les clients ne reçoivent pas tous les messages qui sont décrits ici. Par souci de concision, les messages sont référencés par la valeur de l’en-tête *Path*. Par exemple, nous nous référons à un message texte WebSocket avec la valeur *Path* `speech.hypothesis` en tant que message speech.hypothesis.

### <a name="message-speechstartdetected"></a>Message `speech.startDetected`

Le message `speech.startDetected` indique que le service Speech a détecté un énoncé dans le flux audio.

| Champ | Description |
| ------------- | ---------------- |
| Encodage des messages WebSocket | Texte |
| Path | `speech.startDetected` |
| Content-Type | application/json; charset=utf-8 |
| body | Structure JSON qui contient des informations sur les conditions dans lesquelles le début de l’énoncé a été détecté. Le champ *Offset* de cette structure spécifie le décalage (en unités de 100 nanosecondes) avec lequel l’énoncé a été détecté par rapport au début du flux audio. |

#### <a name="sample-message"></a>Exemple de message

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>Message `speech.hypothesis`

Pendant la reconnaissance vocale, le service Speech génère régulièrement des hypothèses sur les mots qu’il reconnaît. Le service Speech envoie ces hypothèses au client environ toutes les 300 millisecondes. Le message `speech.hypothesis` convient *uniquement* pour améliorer l’expérience de reconnaissance vocale de l’utilisateur. Ne vous en remettez pas au contenu ou à la précision du texte de ces messages.

 Le message `speech.hypothesis` est destiné aux clients qui disposent de fonctions de rendu de texte et souhaitent fournir à l’orateur des commentaires quasiment en temps réel sur la reconnaissance en cours.

| Champ | Description |
| ------------- | ---------------- |
| Encodage des messages WebSocket | Texte |
| Path | `speech.hypothesis` |
| X-RequestId | X-RequestId UUID au format "no-dash" |
| Content-Type | application/json |
| body | Structure JSON des hypothèses de la reconnaissance vocale |

#### <a name="sample-message"></a>Exemple de message

```HTML
Path: speech.hypothesis
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Text": "this is a speech hypothesis",
  "Offset": 0,
  "Duration": 23600000
}
```

L’élément *Offset* spécifie le décalage (en unités de 100 nanosecondes) avec lequel l’expression a été reconnue par rapport au début du flux audio.

L’élément *Duration* spécifie la durée (en unités de 100 nanosecondes) de cette expression de reconnaissance vocale.

Les clients ne doivent pas émettre d’hypothèses concernant la fréquence, le délai ou le texte contenu dans une hypothèse de reconnaissance vocale ou la cohérence du texte entre deux hypothèses de reconnaissance vocale. Les hypothèses sont simplement des instantanés du processus de transcription dans le service. Elles ne représentent pas une accumulation stable de la transcription. Par exemple, une première hypothèse de reconnaissance vocale peut contenir les mots « très amusant », et la deuxième les mots « trouver amusante ». Le service Speech n’effectue aucun post-traitement (par exemple, mise en majuscules ou insertion de signes de ponctuation) sur le texte dans l’hypothèse de reconnaissance vocale.

### <a name="message-speechphrase"></a>Message `speech.phrase`

Quand le service Speech détermine qu’il possède suffisamment d’informations pour produire un résultat de reconnaissance qui n’est pas susceptible d’évoluer, il génère un message `speech.phrase`. Le service Speech génère ces résultats une fois qu’il détecte que l’utilisateur a terminé une phrase ou une expression.

| Champ | Description |
| ------------- | ---------------- |
| Encodage des messages WebSocket | Texte |
| Path | `speech.phrase` |
| Content-Type | application/json |
| body | Structure JSON de l’expression de reconnaissance vocale |

Le schéma JSON de l’expression de reconnaissance vocale inclut les champs suivants : `RecognitionStatus`, `DisplayText`, `Offset` et `Duration`. Pour plus d’informations sur ces champs, consultez [Réponses de la transcription](../concepts.md#transcription-responses).

#### <a name="sample-message"></a>Exemple de message

```HTML
Path: speech.phrase
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": 0,
  "Duration": 12300000
}
```

### <a name="message-speechenddetected"></a>Message `speech.endDetected`

Le message `speech.endDetected` spécifie que l’application cliente doit arrêter d’envoyer du contenu audio au service.

| Champ | Description |
| ------------- | ---------------- |
| Encodage des messages WebSocket | Texte |
| Path | `speech.endDetected` |
| body | Structure JSON qui contient le décalage avec lequel la fin de l’énoncé a été détectée. Le décalage est représenté en unités de 100 nanosecondes par rapport au début du contenu audio utilisé pour la reconnaissance. |
| Content-Type | application/json; charset=utf-8 |

#### <a name="sample-message"></a>Exemple de message

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

L’élément *Offset* spécifie le décalage (en unités de 100 nanosecondes) avec lequel l’expression a été reconnue par rapport au début du flux audio.

### <a name="message-turnstart"></a>Message `turn.start`

Le message `turn.start` signale le début d’un tour du point de vue du service. Le message `turn.start` est toujours le premier message de réponse reçu pour une demande. Si vous ne recevez pas un message `turn.start`, vous pouvez considérer que l’état de la connexion au service n’est pas valide.

| Champ | Description |
| ------------- | ---------------- |
| Encodage des messages WebSocket | Texte |
| Path | `turn.start` |
| Content-Type | application/json; charset=utf-8 |
| body | Structure JSON |

#### <a name="sample-message"></a>Exemple de message

```HTML
Path: turn.start
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "context": {
    "serviceTag": "7B33613B91714B32817815DC89633AFA"
  }
}
```

Le corps du message `turn.start` est une structure JSON qui contient le contexte du début du tour. L’élément *context* contient une propriété *serviceTag*. Cette propriété spécifie une valeur de balise que le service a associée au tour. Microsoft peut utiliser cette valeur si vous avez besoin d’aide pour résoudre des erreurs dans votre application.

### <a name="message-turnend"></a>Message `turn.end`

Le message `turn.end` signale la fin d’un tour du point de vue du service. Le message `turn.end` est toujours le dernier message de réponse reçu pour une demande. Les clients peuvent utiliser la réception de ce message comme signal pour les activités de nettoyage et la transition vers un état inactif. Si vous ne recevez pas un message `turn.end`, vous pouvez considérer que l’état de la connexion au service n’est pas valide. Dans ces cas, fermez la connexion existante au service et reconnectez-vous.

| Champ | Description |
| ------------- | ---------------- |
| Encodage des messages WebSocket | Texte |
| Path | `turn.end` |
| body | Aucun |

#### <a name="sample-message"></a>Exemple de message

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Schéma des données de télémétrie

Le corps du message de *télémétrie* est une structure JSON qui contient des informations clientes relatives à un tour ou à une tentative de connexion. La structure est constituée des horodatages clients qui enregistrent quand des événements clients se produisent. Chaque horodatage doit être au format ISO 8601 comme décrit dans la section intitulée « En-tête X-Timestamp ». Si un message de *télémétrie* ne spécifie pas tous les champs obligatoires dans la structure JSON ou n’utilise pas le format d’horodatage correct, le service peut mettre fin à la connexion au client. Les clients *doivent* fournir des valeurs valides pour tous les champs obligatoires. Les clients *doivent* fournir des valeurs pour les champs facultatifs quand cela est approprié. Les valeurs indiquées dans les exemples de cette section sont fournies à titre d’illustration uniquement.

Le schéma des données de télémétrie comprend les parties suivantes : horodatages des messages reçus et métriques. Le format et l’utilisation de chaque partie sont spécifiés dans les sections suivantes.

### <a name="received-message-time-stamps"></a>Horodatages des messages reçus

Une fois connectés au service, les clients doivent inclure des valeurs d’heure de réception pour tous les messages qu’ils reçoivent. Ces valeurs doivent enregistrer l’heure à laquelle le client *a reçu* chaque message du réseau. La valeur ne doit pas enregistrer une autre heure. Par exemple, le client ne doit pas enregistrer l’heure à laquelle il *a agi* sur le message. Les horodatages des messages reçus sont spécifiés dans un tableau de paires *nom:valeur*. Le nom de la paire spécifie la valeur *Path* du message. La valeur de la paire spécifie l’heure cliente à laquelle le message a été reçu. Ou bien, si plusieurs messages portant le nom spécifié ont été reçus, la valeur de la paire est un tableau d’horodatages qui indique l’heure de réception de ces messages.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Les clients *doivent* accuser réception de tous les messages envoyés par le service en incluant les horodatages de ces messages dans le corps JSON. Si un client ne parvient pas à confirmer la réception d’un message, le service peut mettre fin à la connexion.

### <a name="metrics"></a>Mesures

Les clients doivent inclure les informations relatives aux événements qui se sont produits pendant la durée de vie d’une demande. Les métriques suivantes sont prises en charge : `Connection`, `Microphone` et `ListeningTrigger`.

### <a name="metric-connection"></a>Métrique `Connection`

La métrique `Connection` spécifie des détails sur les tentatives de connexion par le client. La métrique doit inclure les horodatages du début et de la fin de la connexion WebSocket. La métrique `Connection` est requise *uniquement pour le premier tour d’une connexion*. Il n’est pas nécessaire que ces informations figurent dans les tours suivants. Si un client effectue plusieurs tentatives de connexion avant qu’une connexion soit établie, les informations sur *toutes* les tentatives de connexion doivent être incluses. Pour plus d’informations, consultez [Données de télémétrie sur les échecs de connexion](#connection-failure-telemetry).

| Champ | Description | Usage |
| ----- | ----------- | ----- |
| Name | `Connection` | Obligatoire |
| Id | Valeur d’identificateur de connexion qui a été utilisée dans l’en-tête *X-ConnectionId* pour cette demande de connexion | Obligatoire |
| Start | Heure à laquelle le client a envoyé la demande de connexion | Obligatoire |
| End | Heure à laquelle le client a reçu la notification selon laquelle la connexion a été établie ou, en cas d’erreur, heure de son rejet, refus ou échec | Obligatoire |
| Error | Description de l’erreur qui s’est produite, le cas échéant. Si la connexion a réussi, les clients doivent omettre ce champ. La longueur maximale de ce champ est de 50 caractères. | Obligatoire pour les cas d’erreur, omis sinon |

La description de l’erreur ne doit pas dépasser 50 caractères et doit idéalement être une des valeurs répertoriées dans le tableau suivant. Si la condition d’erreur ne correspond pas à une des valeurs suivantes, les clients peuvent utiliser une description succincte de la condition d’erreur en recourant à la [technique du camel case](https://en.wikipedia.org/wiki/Camel_case) sans espace blanc. L’envoi d’un message de *télémétrie* supposant une connexion au service, seules les conditions d’erreur passagères ou temporaires peuvent être signalées dans ce message. Les conditions d’erreur qui empêchent *définitivement* un client d’établir une connexion au service l’empêche d’envoyer un message au service, y compris les messages de *télémétrie*.

| Error | Usage |
| ----- | ----- |
| DNSfailure | Le client n’a pas pu se connecter au service en raison d’une erreur DNS dans la pile réseau. |
| NoNetwork | Le client a tenté une connexion, mais la pile réseau a signalé qu’aucun réseau physique n’était disponible. |
| NoAuthorization | La connexion cliente a échoué pendant la tentative d’acquérir un jeton d’autorisation pour la connexion. |
| NoResources | Le client a manqué d’une ressource locale (par exemple, de mémoire) pendant qu’il essayait d’établir une connexion. |
| Interdit | Le client n’a pas pu se connecter au service, car ce dernier a retourné un code d’état HTTP `403 Forbidden` sur la demande de mise à niveau WebSocket. |
| Non autorisé | Le client n’a pas pu se connecter au service, car ce dernier a retourné un code d’état HTTP `401 Unauthorized` sur la demande de mise à niveau WebSocket. |
| BadRequest | Le client n’a pas pu se connecter au service, car ce dernier a retourné un code d’état HTTP `400 Bad Request` sur la demande de mise à niveau WebSocket. |
| ServerUnavailable | Le client n’a pas pu se connecter au service, car ce dernier a retourné un code d’état HTTP `503 Server Unavailable` sur la demande de mise à niveau WebSocket. |
| ServerError | Le client n’a pas pu se connecter au service, car ce dernier a retourné un code d’état d’erreur interne `HTTP 500` sur la demande de mise à niveau WebSocket. |
| Délai d'expiration | La demande de connexion du client est arrivé à expiration sans réponse de la part du service. Le champ *End* contient l’heure à laquelle le client a expiré et a arrêté d’attendre la connexion. |
| ClientError | Le client a mis fin à la connexion en raison d’une erreur cliente interne. |

### <a name="metric-microphone"></a>Métrique `Microphone`

La métrique `Microphone` est requise pour tous les tours de reconnaissance vocale. Cette métrique mesure le temps sur le client au cours duquel l’entrée audio est activement utilisée pour une demande de reconnaissance vocale.

Appuyez-vous sur les exemples suivants pour enregistrer les valeurs d’heure *Start* de la métrique `Microphone` dans votre application cliente :

* Une application cliente nécessite qu’un utilisateur appuie sur un bouton physique pour démarrer le microphone. Une fois le bouton enfoncé, l’application cliente lit l’entrée du microphone et l’envoie au service Speech. La valeur *Start* de la métrique `Microphone` enregistre l’heure, une fois le bouton enfoncé, à laquelle le microphone est initialisé et prêt pour fournir une entrée. La valeur *End* de la métrique `Microphone` enregistre l’heure à laquelle l’application cliente a arrêté d’envoyer du contenu audio au service une fois qu’elle a reçu le message `speech.endDetected` de la part ce dernier.

* Une application cliente utilise un détecteur de mots clés qui est « toujours » à l’écoute. L’application cliente attend que le détecteur de mots clés repère une expression parlée déclenchante pour collecter l’entrée du microphone et l’envoyer au service Speech. La valeur *Start* de la métrique `Microphone` enregistre l’heure à laquelle le détecteur de mots clés a indiqué au client de commencer à utiliser l’entrée du microphone. La valeur *End* de la métrique `Microphone` enregistre l’heure à laquelle l’application cliente a arrêté d’envoyer du contenu audio au service une fois qu’elle a reçu le message `speech.endDetected` de la part ce dernier.

* Une application cliente a accès à un flux audio constant et effectue une détection de silence et d’énoncé sur le flux audio dans un *module de détection d’énoncé*. La valeur *Start* de la métrique `Microphone` enregistre l’heure à laquelle le *module de détection d’énoncé* a indiqué au client de commencer à utiliser l’entrée du flux audio. La valeur *End* de la métrique `Microphone` enregistre l’heure à laquelle l’application cliente a arrêté d’envoyer du contenu audio au service une fois qu’elle a reçu le message `speech.endDetected` de la part ce dernier.

* Une application cliente traite le deuxième tour d’une demande à plusieurs tours et reçoit un message de réponse du service l’informant d’activer le microphone afin de recueillir l’entrée pour le deuxième tour. La valeur *Start* de la métrique `Microphone` enregistre l’heure à laquelle l’application cliente active le microphone et commence à utiliser l’entrée de la source audio. La valeur *End* de la métrique `Microphone` enregistre l’heure à laquelle l’application cliente a arrêté d’envoyer du contenu audio au service une fois qu’elle a reçu le message `speech.endDetected` de la part ce dernier.

La valeur d’heure *End* de la métrique `Microphone` enregistre l’heure à laquelle l’application cliente a arrêté d’envoyer l’entrée audio. Dans la plupart des cas, cet événement se produit dès que le client a reçu le message `speech.endDetected` de la part du service. Les applications clientes peuvent vérifier qu’elles respectent le protocole en s’assurant que la valeur d’heure *End* de la métrique `Microphone` est postérieure à la valeur d’heure de la réception du message `speech.endDetected`. En outre, comme il existe généralement un délai entre la fin d’un tour et le début d’un autre, les clients peuvent vérifier la conformité au protocole en s’assurant que l’heure *Start* de la métrique `Microphone` pour tout tour ultérieur enregistre correctement l’heure à laquelle le client *a commencé* à utiliser le microphone pour communiquer une entrée audio au service.

| Champ | Description | Usage |
| ----- | ----------- | ----- |
| Name | Microphone | Obligatoire |
| Start | Heure à laquelle le client a commencé à utiliser l’entrée audio du microphone ou un autre flux audio ou a reçu un déclencheur du détecteur de mots clés | Obligatoire |
| End | Heure à laquelle le client a arrêté d’utiliser le microphone ou le flux audio | Obligatoire |
| Error | Description de l’erreur qui s’est produite, le cas échéant. En cas d’échec des opérations de microphone, les clients doivent omettre ce champ. La longueur maximale de ce champ est de 50 caractères. | Obligatoire pour les cas d’erreur, omis sinon |

### <a name="metric-listeningtrigger"></a>Métrique `ListeningTrigger`
La métrique `ListeningTrigger` mesure l’heure à laquelle l’utilisateur exécute l’action qui lance l’entrée vocale. La métrique `ListeningTrigger` est facultative, mais les clients qui peuvent fournir cette métrique sont invités à le faire.

Appuyez-vous sur les exemples suivants pour enregistrer les valeurs d’heure *Start* et *End* de la métrique `ListeningTrigger` dans votre application cliente :

* Une application cliente nécessite qu’un utilisateur appuie sur un bouton physique pour démarrer le microphone. La valeur *Start* de cette métrique enregistre l’heure à laquelle l’utilisateur a appuyé sur le bouton. La valeur *End* enregistre l’heure à laquelle l’utilisateur a arrêté d’appuyer sur le bouton.

* Une application cliente utilise un détecteur de mots clés qui est « toujours » à l’écoute. Une fois que le détecteur de mots clés a repéré une expression parlée déclenchante, l’application cliente lit l’entrée du microphone et l’envoie au service Speech. La valeur *Start* de cette métrique enregistre l’heure à laquelle le détecteur de mots clés a reçu le contenu audio qui a été ensuite détecté en tant qu’expression déclenchante. La valeur *End* enregistre l’heure à laquelle le dernier mot d’une expression déclenchante a été prononcé par l’utilisateur.

* Une application cliente a accès à un flux audio constant et effectue une détection de silence et d’énoncé sur le flux audio dans un *module de détection d’énoncé*. La valeur *Start* de cette métrique enregistre l’heure à laquelle le *module de détection d’énoncé* a reçu le contenu audio qui a été ensuite détecté en tant qu’énoncé. La valeur *End* enregistre l’heure à laquelle le *module de détection d’énoncé* a détecté un énoncé.

* Une application cliente traite le deuxième tour d’une demande à plusieurs tours et reçoit un message de réponse du service l’informant d’activer le microphone afin de recueillir l’entrée pour le deuxième tour. L’application cliente *ne doit pas* inclure de métrique `ListeningTrigger` pour ce tour.

| Champ | Description | Usage |
| ----- | ----------- | ----- |
| Name | ListeningTrigger | Facultatif |
| Start | Heure à laquelle a démarré le déclencheur d’écoute du client | Obligatoire |
| End | Heure à laquelle a terminé le déclencheur d’écoute du client | Obligatoire |
| Error | Description de l’erreur qui s’est produite, le cas échéant. Si l’opération du déclencheur a réussi, les clients doivent omettre ce champ. La longueur maximale de ce champ est de 50 caractères. | Obligatoire pour les cas d’erreur, omis sinon |

#### <a name="sample-message"></a>Exemple de message

L’exemple suivant montre un message de télémétrie avec les parties ReceivedMessages et Metrics :

```HTML
Path: telemetry
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000
X-Timestamp: 2016-08-16T15:03:54.183Z

{
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.171Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ],
  "Metrics": [
    {
      "Name": "Connection",
      "Id": "A140CAF92F71469FA41C72C7B5849253",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:48.000Z",
    },
    {
      "Name": "ListeningTrigger",
      "Start": "2016-08-16T15:03:48.776Z",
      "End": "2016-08-16T15:03:48.777Z",
    },
    {
      "Name": "Microphone",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:51.921Z",
    },
  ],
}
```

## <a name="error-handling"></a>Gestion des erreurs

Cette section décrit les types de messages et conditions d’erreur que votre application doit gérer.

### <a name="http-status-codes"></a>Codes d’état HTTP

Pendant la demande de mise à niveau WebSocket, le service Speech peut retourner un des codes d’état HTTP standard, tels que `400 Bad Request`. Votre application doit traiter correctement ces conditions d’erreur.

#### <a name="authorization-errors"></a>Erreurs d’autorisation

Si une autorisation incorrecte est fournie pendant la mise à niveau WebSocket, le service Speech retourne le code d’état HTTP `403 Forbidden`. Voici certaines des conditions qui peuvent déclencher ce code d’erreur :

* En-tête *Authorization* manquant

* Jeton d’autorisation non valide

* Jeton d’autorisation arrivé à expiration

Le message d’erreur `403 Forbidden` n’indique pas un problème lié au service Speech. Ce message d’erreur signale un problème qui affecte l’application cliente.

### <a name="protocol-violation-errors"></a>Erreurs de violation de protocole

Si le service Speech détecte des violations de protocole émanant d’un client, le service met fin à la connexion WebSocket après avoir retourné un *code d’état* et une *raison* de l’arrêt. Les applications clientes peuvent utiliser ces informations pour résoudre et corriger les violations.

#### <a name="incorrect-message-format"></a>Format de message incorrect

Si un client envoie au service un message texte ou binaire qui n’est pas encodé au format approprié indiqué dans cette spécification, le service ferme la connexion avec un code d’état *1007 (données de charge utile non valides)* .

Le service retourne ce code d’état pour diverses raisons, comme indiqué dans les exemples suivants :

* « Format de message incorrect. Le message binaire a un préfixe de taille d’en-tête non valide. » Le client a envoyé un message binaire qui a un préfixe de taille d’en-tête non valide.

* « Format de message incorrect. Le message binaire a une taille d’en-tête non valide. » Le client a envoyé un message binaire qui a spécifié une taille d’en-tête non valide.

* « Format de message incorrect. Le décodage des en-têtes de message binaire au format UTF-8 a échoué. » Le client a envoyé un message binaire contenant des en-têtes qui n’ont pas été correctement encodés au format UTF-8.

* « Format de message incorrect. Le message texte ne contient aucune donnée. » Le client a envoyé un message texte dont le corps ne contient aucune donnée.

* « Format de message incorrect. Le décodage du message texte au format UTF-8 a échoué. » Le client a envoyé un message texte qui n’a pas été correctement encodé au format UTF-8.

* « Format de message incorrect. Le message texte ne contient aucun séparateur d’en-tête. » Le client a envoyé un message texte qui ne contenait pas de séparateur d’en-tête ou utilisait un séparateur d’en-tête incorrect.

#### <a name="missing-or-empty-headers"></a>En-têtes manquants ou vides

Si un client envoie un message qui n’a pas les en-têtes requis *X-RequestId* ou *Path*, le service ferme la connexion avec un code d’état *1002 (erreur de protocole)* . Le message est « En-tête manquant/vide. {nom de l’en-tête}. »

#### <a name="requestid-values"></a>Valeurs d’identificateur de demande

Si un client envoie un message qui spécifie un en-tête *X-RequestId* avec un format incorrect, le service ferme la connexion et retourne un état *1002 (erreur de protocole)* . Le message est « Demande non valide. La valeur de l’en-tête X-RequestId n’a pas été spécifiée dans un format d’UUID dépourvu de tirets. »

#### <a name="audio-encoding-errors"></a>Erreurs d’encodage audio

Si un client envoie un bloc audio qui lance un tour et que l’encodage ou le format audio n’est pas conforme à la spécification requise, le service ferme la connexion et retourne un code d’état *1007 (données de charge utile non valides)* . Le message indique la source de l’erreur d’encodage du format.

#### <a name="requestid-reuse"></a>Réutilisation de l’identificateur de demande

Après la fin d’un tour, si un client envoie un message qui réutilise l’identificateur de demande associé à ce tour, le service ferme la connexion et retourne un code d’état *1002 (erreur de protocole)* . Le message est « Demande non valide. La réutilisation des identificateurs de demande n’est pas autorisée. »

## <a name="connection-failure-telemetry"></a>Données de télémétrie sur les échecs de connexion

Pour garantir la meilleure expérience utilisateur possible, les clients doivent indiquer au service Speech les horodatages des points de contrôle importants au sein d’une connexion en utilisant le message de *télémétrie*. Il est tout aussi important que les clients informent le service des connexions dont la tentative a échoué.

Pour chaque tentative de connexion qui a échoué, les clients créent un message de *télémétrie* avec une valeur d’en-tête *X-RequestId* unique. Étant donné que le client n’a pas pu établir de connexion, le champ *ReceivedMessages* dans le corps JSON peut être omis. Seule l’entrée `Connection` du champ *Metrics* est incluse. Cette entrée inclut les horodatages de début et de fin, ainsi que la condition d’erreur qui s’est produite.

### <a name="connection-retries-in-telemetry"></a>Tentatives de connexion dans les données de télémétrie

Les clients doivent différencier les *nouvelles tentatives* des *tentatives de connexion multiples* d’après l’événement qui déclenche la tentative de connexion. Les tentatives de connexion qui sont effectuées par programmation sans entrée utilisateur sont des nouvelles tentatives. Plusieurs tentatives de connexion qui sont effectuées en réponse à une entrée utilisateur sont des tentatives de connexion multiples. Les clients donnent à chaque tentative de connexion déclenchée par l’utilisateur un *X-RequestId* unique et un message de *télémétrie*. Les clients réutilisent le *X-RequestId* pour les nouvelles tentatives par programmation. Si plusieurs nouvelles tentatives ont été effectuées pour une tentative de connexion unique, chaque nouvelle tentative est incluse en tant qu’entrée `Connection` dans le message de *télémétrie*.

Par exemple, supposons qu’un utilisateur prononce le mot clé déclencheur pour démarrer une connexion et que la première tentative de connexion échoue en raison d’erreurs DNS. Toutefois, une deuxième tentative effectuée par programmation par le client réussit. Étant donné que le client a retenté la connexion sans entrée supplémentaire de l’utilisateur, le client utilise un seul message de *télémétrie* avec plusieurs entrées `Connection` pour décrire la connexion.

Autre exemple : supposons qu’un utilisateur prononce le mot clé déclencheur pour démarrer une connexion et que cette tentative de connexion échoue après trois tentatives. Le client abandonne, n’essaie plus de se connecter au service, puis informe l’utilisateur que quelque chose d’anormal s’est produit. Ensuite, l’utilisateur reprononce le mot clé déclencheur. Cette fois-ci, supposons que le client se connecte au service. Une fois connecté, le client envoie immédiatement au service un message de *télémétrie* qui contient trois entrées `Connection` décrivant les échecs de connexion. Après avoir reçu le message `turn.end`, le client envoie un autre message de *télémétrie* qui décrit la connexion ayant réussi.

## <a name="error-message-reference"></a>Informations de référence sur les messages d’erreur

### <a name="http-status-codes"></a>Codes d’état HTTP

| Code d'état HTTP | Description | Résolution de problèmes |
| - | - | - |
| 400 Demande incorrecte | Le client a envoyé une demande de connexion WebSocket qui était incorrecte. | Vérifiez que vous avez fourni tous les paramètres et en-têtes HTTP requis et que les valeurs sont correctes. |
| 401 Non autorisé | Le client n’a pas inclus les informations d’autorisation requises. | Vérifiez que vous envoyez l’en-tête *Authorization* dans la connexion WebSocket. |
| 403 Interdit | Le client a envoyé les informations d’autorisation, mais elles n’étaient pas valides. | Vérifiez que vous n’envoyez pas une valeur ayant expiré ou non valide dans l’en-tête *Authorization*. |
| 404 Introuvable | Le client a tenté d’accéder à un chemin d’URL qui n’est pas pris en charge. | Vérifiez que vous utilisez l’URL correcte pour la connexion WebSocket. |
| 500 Erreur de serveur | Le service a rencontré une erreur interne qui l’empêche de satisfaire à la demande. | Dans la plupart des cas, cette erreur est temporaire. relancez la requête. |
| 503 Service indisponible | Le service n’était pas disponible pour traiter la demande. | Dans la plupart des cas, cette erreur est temporaire. relancez la requête. |

### <a name="websocket-error-codes"></a>Codes d’erreur WebSocket

| Code WebSocketsStatus | Description | Résolution de problèmes |
| - | - | - |
| 1000 Fermeture normale | Le service a fermé la connexion WebSocket sans erreur. | Si la fermeture de WebSocket était inattendue, relisez la documentation pour vérifier que vous comprenez comment et quand le service peut mettre fin à la connexion WebSocket. |
| 1002 Erreur de protocole | Le client n’est pas parvenu à se conformer aux exigences du protocole. | Vérifiez que vous comprenez bien la documentation du protocole et les exigences. Lisez la documentation précédente sur les raisons de l’erreur pour voir si vous violez les exigences du protocole. |
| 1007 Données de charge utile non valides | Le client a envoyé une charge utile non valide dans un message de protocole. | Vérifiez si le dernier message que vous avez envoyé au service contient des erreurs. Lisez la documentation précédente sur les erreurs de charge utile. |
| 1011 Erreur de serveur | Le service a rencontré une erreur interne qui l’empêche de satisfaire à la demande. | Dans la plupart des cas, cette erreur est temporaire. relancez la requête. |

## <a name="related-topics"></a>Rubriques connexes

Consultez un [SDK JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) qui est une implémentation du protocole du service Speech basé sur WebSocket.
