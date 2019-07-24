---
title: Concepts de streaming en direct dans Azure Media Services - Événements en direct et Sorties en direct | Microsoft Docs
description: Cet article offre une vue d'ensemble des concepts de streaming en direct dans Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/19/2019
ms.author: juliako
ms.openlocfilehash: a951ebd46335ad4639b8499283ddd30f13edd64e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605650"
---
# <a name="live-events-and-live-outputs"></a>Événements en direct et Sorties en direct

Azure Media Services vous permet de transmettre des événements en direct auprès de vos clients dans le cloud Azure. Pour configurer vos événements de streaming en direct dans Media Services v3, vous devez comprendre les concepts abordés dans cet article.

> [!TIP]
> Pour les clients migrant depuis les API de Media Services v2, l’entité **Live Event** remplace **Channel** dans la version 2 et **Live Output** remplace **Program**.

## <a name="live-events"></a>Événements en direct

Les [événements en direct](https://docs.microsoft.com/rest/api/media/liveevents) sont chargés de la réception et du traitement des flux vidéo en direct. Quand vous créez un événement en direct, un point de terminaison d’entrée primaire et secondaire est également créé. Vous pouvez utiliser ce point de terminaison pour envoyer un signal en direct à partir d’un encodeur à distance. L’encodeur live à distance envoie le flux de contribution à ce point de terminaison d’entrée par le biais du protocole d’entrée [RTMP](https://www.adobe.com/devnet/rtmp.html) ou [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (MP4 fragmenté). Pour le protocole de réception RTMP, le contenu peut être envoyé en clair (`rtmp://`) ou chiffré pour plus de sécurité sur la connexion filaire (`rtmps://`). Pour le protocole de réception Smooth Streaming, les schémas d’URL pris en charge sont `http://` ou `https://`.  

## <a name="live-event-types"></a>Types d’événements en direct

Un [événement en direct](https://docs.microsoft.com/rest/api/media/liveevents) peut être de deux types : pass-through et Live Encoding. Durant la création, les types sont définis à l’aide de [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype) :

* **LiveEventEncodingType.None** - Un encodeur live local envoie un flux à débits multiples. Les flux reçus transitent par l’Événement en temps réel sans traitement supplémentaire. 
* **LiveEventEncodingType.Standard** - Un encodeur live local envoie un flux à débit unique à l’Événement en direct, puis Media Services crée des flux à débits multiples. Si la résolution du flux de contribution est de 720p ou plus, la présélection **Default720p** encode un jeu de 6 paires résolution/débits.
* **LiveEventEncodingType.Premium1080p** - Un encodeur live local envoie un flux à débit unique à l’Événement en direct, puis Media Services crée des flux à débits multiples. La présélection Default1080p spécifie le jeu de sortie des paires résolution/débits. 

### <a name="pass-through"></a>Requête directe

![transmission directe](./media/live-streaming/pass-through.svg)

Quand vous utilisez l’**événement en direct** de type pass-through, vous chargez l’encodeur live local de générer un flux vidéo à vitesse de transmission multiple et d’envoyer ce flux comme flux de contribution à l’événement en direct (à l’aide du protocole RTMP ou MP4 fragmenté). L’événement en direct est ensuite transmis dans les flux vidéo entrants sans traitement supplémentaire. Une transmission LiveEvent est optimisée pour les événements en direct de longue durée ou le streaming en direct linéaire sans interruption (24 heures sur 24, 365 jours par an). Si vous créez ce type d’événement en direct, spécifiez le paramètre None (LiveEventEncodingType.None).

Vous pouvez envoyer le flux de contribution à une résolution jusqu’à 4 K et à une fréquence de 60 images/seconde, avec des codecs vidéo H.264/AVC ou H.265/HEVC et des codecs audio AAC (AAC-LC, HE-AACv1 ou HE-AACv2).  Pour plus d’informations, consultez l’article [Comparaison des types d’événements en direct](live-event-types-comparison.md).

> [!NOTE]
> La méthode pass-through est le moyen le plus économique de diffuser des vidéos en continu si plusieurs événements vous concernent sur une longue période, et si vous avez déjà investi dans des encodeurs locaux. Consultez les détails de la [tarification](https://azure.microsoft.com/pricing/details/media-services/) .
> 

Consultez un exemple de code .NET dans [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Encodage en direct  

![encodage en temps réel](./media/live-streaming/live-encoding.svg)

Quand vous utilisez Live Encoding avec Media Services, vous configurez votre encodeur live local pour qu’il envoie un flux vidéo à une seule vitesse de transmission comme flux de contribution à l’événement en direct (à l’aide du protocole RTMP ou MP4 fragmenté). Vous configurerez ensuite un événement en direct de sorte qu’il encode ce flux vidéo à une seule vitesse de transmission entrant en [flux vidéo à vitesse de transmission multiple](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), pour rendre la transmission et la lecture de la sortie possibles sur les appareils via des protocoles comme MPEG-DASH, HLS et Smooth Streaming.

Lorsque vous utilisez un encodage en direct, vous pouvez envoyer le flux de contribution uniquement aux résolutions jusqu’à 1080p et à une fréquence de 30 images/seconde, avec un codec vidéo H.264/AVC et un codec audio AAC (AAC-LC, HE-AACv1 ou HE-AACv2). Notez que les événements en direct pass-through peut prendre en charge les résolutions jusqu’à 4K à 60 images/seconde. Pour plus d’informations, consultez l’article [Comparaison des types d’événements en direct](live-event-types-comparison.md).

La présélection détermine les résolutions et débits de la sortie émanant de l’encodeur live. Si vous utilisez un encodeur live **Standard** (LiveEventEncodingType.Standard), la présélection *Default720p* spécifie un jeu de 6 paires résolution/débit, allant de 720p à 3,5 Mbits/s à 192p à 200 Kbits/s. Autrement, si vous utilisez un encodeur live **Premium1080p** (LiveEventEncodingType.Premium1080p), la présélection *Default1080p* spécifie un jeu de 6 paires résolution/débit, allant de 1080p à 3,5 Mbits/s à 180p à 200 Kbits/s. Pour plus d’informations, consultez [Présélections système](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Si vous avez besoin de personnaliser la présélection d’encodage live, ouvrez un ticket de support sur le Portail Azure. Vous devez spécifier la table de résolution et de débits binaires souhaitée. Vérifiez qu’il n’existe qu’un calque à 720p (si vous demandez un préréglage pour un encodeur live Standard) ou à 1080p (si vous demandez un préréglage pour un encodeur live Premium1080p). Le nombre maximal de calques est de 6.

## <a name="live-event-creation-options"></a>Options de création d’événements en direct

Quand vous créez un événement en direct, vous pouvez spécifier les options suivantes :

* Protocole de streaming de l’événement en direct (les protocoles RTMP et Smooth Streaming sont actuellement pris en charge).<br/>Vous ne pouvez pas changer l’option de protocole pendant l’exécution de l’événement en direct ou des sorties en direct qui lui sont associées. Si vous avez besoin d’autres protocoles, vous devez créer des événements en direct distincts pour chaque protocole de streaming.  
* Restictions IP sur l’ingestion et la préversion. Vous pouvez définir les adresses IP autorisées à recevoir du contenu vidéo sur cet événement en direct. Les adresses IP autorisées peuvent être définies sous forme d’adresse IP unique (par exemple, « 10.0.0.1 »), de plage d’adresses IP constituée d’une adresse IP et d’un masque de sous-réseau CIDR (par exemple, « 10.0.0.1/22 ») ou de plage d’adresses IP constituée d’une adresse IP et d’un masque de sous-réseau au format décimal séparé par des points (par exemple, « 10.0.0.1(255.255.252.0) »).<br/>Si aucune adresse IP n’est spécifiée et qu’il n’existe pas de définition de règle, alors aucune adresse IP ne sera autorisée. Pour autoriser toutes les adresses IP, créez une règle et définissez la valeur 0.0.0.0/0.<br/>Les adresses IP doivent utiliser un des formats suivants : adresses IpV4 avec 4 nombres, plage d’adresses CIDR.
* Lors de la création de l’événement, vous pouvez préciser que vous voulez que le démarrage soit automatique. <br/>Lorsque le démarrage automatique est défini sur true, l’événement en direct démarre après sa création. La facturation commence donc dès que son exécution démarre. Vous devez appeler explicitement la commande Stop sur la ressource de l’événement en direct pour arrêter toute facturation supplémentaire. Sinon, lancez-le dès que vous souhaitez commencer le streaming. 

    Pour plus d’informations, consultez [États et facturation des événements en direct](live-event-states-billing.md).

## <a name="live-event-ingest-urls"></a>URL de réception des événements en direct

Une fois l’événement en direct créé, vous pouvez obtenir des URL de réception que vous devez fournir à l’encodeur live local. L’encodeur live utilise ces URL pour entrer un flux temps réel. Pour plus d’informations, consultez [Encodeurs live locaux recommandés](recommended-on-premises-live-encoders.md). 

Vous pouvez utiliser des URL de redirection ou de non-redirection vers un microsite. 

> [!NOTE] 
> Pour qu’une URL de réception soit prédictive, réglez le mode « personnel ».

* URL de non-redirection vers un microsite

    L’URL de non-redirection vers un microsite est le mode par défaut dans Media Services v3. Vous obtenez éventuellement l’événement en direct rapidement, mais l’URL de réception est connue uniquement quand l’événement en direct est démarré. L’URL change si vous arrêtez/démarrez l’événement en direct. <br/>La non-redirection vers un microsite est utile dans les scénarios quand un utilisateur final souhaite diffuser à l’aide d’une application là où l’application souhaite obtenir un événement en direct dès que possible et qu’une URL de réception dynamique n’est pas un problème.
    
    Si une application client n’a pas besoin de prégénérer une URL de réception avant la création de l’événement en direct, laissez simplement Media Services générer automatiquement le jeton d’accès pour l’événement en direct.
* URL de redirection vers un microsite

    Le mode de redirection vers un microsite est préféré par les diffuseurs multimédias importants qui utilisent des encodeurs de diffusion matériels et ne veulent pas reconfigurer leurs encodeurs quand ils démarrent l’événement en direct. Ils veulent une URL de réception prédictive, qui ne change pas au fil du temps.
    
    Pour spécifier ce mode, définissez `vanityUrl` sur `true` lors de la création (valeur par défaut : `false`). Vous devez également transmettre votre jeton d’accès (`LiveEventInput.accessToken`) lors de la création. Vous spécifiez la valeur du jeton pour éviter qu’un jeton aléatoire soit inséré dans l’URL. Le jeton d’accès doit être une chaîne GUID valide (avec ou sans traits d’union). Une fois défini, le mode ne peut pas être mis à jour.

    Le jeton d’accès doit être unique dans votre centre de données. Si votre application doit utiliser une URL personnelle, il est recommandé de toujours créer une nouvelle instance GUID pour votre jeton d’accès (au lieu de réutiliser un GUID existant). 

    Utiliser les API suivantes pour activer l’URL personnelle et de définir le jeton d’accès sur un GUID valide (par exemple `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`).  
    
    |Langage|Activer une URL de redirection vers un microsite|Définir le jeton d’accès|
    |---|---|---|
    |REST|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |Interface de ligne de commande|[--vanity-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Règles de nommage de l’URL de réception en direct

* La chaîne *aléatoire* ci-dessous est un nombre hexadécimal de 128 bits (qui se compose de 32 caractères de 0 à 9 et de a à f).
* *votre jeton d’accès* - Chaîne GUID valide que vous définissez lorsque vous utilisez le mode personnel. Par exemple : `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *nom de flux* -indique le nom du flux pour une connexion spécifique. La valeur du nom du flux est généralement ajoutée par l’encodeur live que vous utilisez. Vous pouvez configurer l’encodeur live pour qu’il utilise n’importe quel nom afin de décrire la connexion, par exemple : « video1_audio1 », « video2_audio1 », « stream ».

#### <a name="non-vanity-url"></a>URL de non-redirection vers un microsite

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>URL de redirection vers un microsite

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>URL de l’aperçu des événements en direct

Une fois que l’événement en direct commence à recevoir le flux de contribution, vous pouvez utiliser son point de terminaison d’aperçu pour prévisualiser et valider le flux temps réel que vous recevez avant de continuer la publication. Après avoir vérifié que le flux d’aperçu est correct, vous pouvez utiliser l’événement en direct pour rendre le flux temps réel diffusable via un ou plusieurs points de terminaison de streaming (créés au préalable). Pour cela, vous créez une [sortie en direct](https://docs.microsoft.com/rest/api/media/liveoutputs) sur l’événement en direct. 

> [!IMPORTANT]
> Veillez à ce que la vidéo transite par l’URL d’aperçu avant de poursuivre !

## <a name="live-event-long-running-operations"></a>Opérations de longue durée d’un événement en direct

Pour plus de détails, consultez [Opérations de longue durée](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Sorties en direct

Une fois que le flux transite dans l’événement en direct, vous pouvez commencer l’événement de streaming en créant un [actif multimédia](https://docs.microsoft.com/rest/api/media/assets), une [sortie en direct](https://docs.microsoft.com/rest/api/media/liveoutputs) et un [localisateur de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators). La sortie en direct archive le flux et le met à la disposition des observateurs via le [point de terminaison de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

> [!NOTE]
> Les sorties en direct démarrent dès leur création et s’arrêtent à leur suppression. Quand vous supprimez la sortie en direct, vous ne supprimez pas l’actif multimédia sous-jacent ni le contenu de celui-ci. 

La relation entre un **événement en direct** et ses **sorties en direct** est comparable à la diffusion télévisuelle traditionnelle, où un canal (événement en direct) représente un flux vidéo constant et un enregistrement (sortie en direct) est limité à une plage horaire spécifique (par exemple, un journal télévisé de 18h30 à 19h). Vous pouvez enregistrer une émission de télévision à l’aide d’un magnétoscope numérique (DVR). La fonctionnalité équivalente dans les événements en direct est gérée par la propriété **archiveWindowLength**. Il s’agit d’une période ISO-8601 (par exemple, PTHH:MM:SS), qui spécifie la capacité du magnétoscope numérique. Sa valeur est comprise entre 3 minutes et 25 heures.

L’objet Sortie en direct fonctionne comme un magnétoscope qui va capturer et enregistrer le flux temps réel dans un actif multimédia dans votre compte Media Services. Le contenu enregistré est conservé dans le compte de stockage Azure associé à votre compte, dans le conteneur défini par l’actif multimédia. La sortie en direct vous permet également de contrôler certaines propriétés du flux temps réel sortant, notamment la quantité du flux conservée dans l’enregistrement archive (par exemple, la capacité du magnétoscope numérique cloud) et le fait que les destinataires sont autorisés ou non à démarrer la lecture du flux. L’archive sur le disque est une archive circulaire de type « fenêtre » qui stocke uniquement la quantité de contenu spécifiée dans la propriété archiveWindowLength de la sortie en direct. Le contenu qui dépasse cette fenêtre d’archive est automatiquement supprimé du conteneur de stockage et n’est pas récupérable. Vous pouvez créer plusieurs sorties en direct (trois maximum) sur un événement en direct avec différents paramètres et longueurs de fenêtre d’archive.  

Si vous avez publié l’**actif multimédia** de la sortie en direct à l’aide d’un **localisateur de streaming**, l’événement en direct (jusqu’à la longueur de la fenêtre DVR) restera visible jusqu’à l’expiration ou la suppression du localisateur de streaming, en fonction de ce qui se produit en premier.

Pour plus d’informations, consultez [Utilisation d’un magnétoscope numérique cloud](live-event-cloud-dvr.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel sur le streaming en direct](stream-live-tutorial-with-api.md)
