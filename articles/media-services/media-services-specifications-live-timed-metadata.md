---
title: Azure Media Services - Signalisation de métadonnées chronométrées dans une vidéo en streaming en direct | Documents Microsoft
description: Cette spécification décrit les méthodes de signalisation des métadonnées chronométrées lors de la réception et de la diffusion en continu vers Azure Media Services. Cela inclut la prise en charge de signaux génériques de métadonnées chronométrées (ID3), ainsi que la signalisation SCTE-35 pour l'insertion de jointures publicitaires et la signalisation des conditions de la jointure.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/2/2019
ms.author: johndeu
ms.openlocfilehash: 444d5ca996c014bdbf2e62cacf2563c7b63372e4
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "69015721"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Signalisation de métadonnées chronométrées dans une vidéo en flux continu 

Dernière mise à jour : 2019-07-02

### <a name="conformance-notation"></a>Notation de conformité

« DOIT », « NE DOIT PAS », « OBLIGATOIRE », « DEVRA », « NE DEVRA PAS », « DEVRAIT », « NE DEVRAIT PAS », « RECOMMANDÉ », « NON RECOMMANDÉE », « PEUT » et « OPTIONNELLE » figurant dans ce document doivent être interprétés de la manière décrite dans le document RFC 2119.

## <a name="1-introduction"></a>1. Introduction 

Afin de signaler l'insertion d’annonces ou d'événements de métadonnées personnalisés sur un lecteur de client, les diffuseurs utilisent souvent des métadonnées chronométrées incorporées dans la vidéo. Pour permettre ces scénarios, Media Services prend en charge le transport de métadonnées chronométrées, du point de réception du canal de diffusion en continu en direct à l'application cliente.
Cette spécification présente plusieurs modes pris en charge par Media Services pour les métadonnées chronométrées à l’intérieur des signaux de diffusion en continu en direct.

1. Signalement [SCTE-35] conforme aux normes indiquées par [SCTE-35], [SCTE-214-1], [SCTE-214-3] et [RFC8216]

2. Signalement [SCTE-35] conforme à la spécification héritée [Adobe-Primetime] pour la signalisation d’annuaire RTMP.
   
3. Un mode de signalisation de métadonnées générique chronométré, pour les messages qui ne sont **PAS** [SCTE-35] et qui peuvent comporter [ID3v2] ou d’autres schémas personnalisés définis par le développeur d’applications.

## <a name="11-terms-used"></a>1.1 Termes utilisés

| Terme              | Définition |
|-------------------|------------|
| Interruption publicitaire          | Emplacement ou moment où une ou plusieurs annonces peuvent être planifiées pour la remise; identique à l’opportunité de disponibilité et de placement. |
| Service de décision publicitaire| Service externe qui détermine la ou les annonces et les durées à afficher à l’utilisateur. Les services sont généralement fournis par un partenaire et ne relèvent pas de ce document.|
| Signal               | Indication du temps et des paramètres de la prochaine pause publicitaire. Notez que les signaux peuvent indiquer un basculement en attente vers une pause publicitaire, un basculement en attente vers la publicité suivante au sein d’une pause publicitaire et un basculement en attente d’une pause publicitaire vers le contenu principal. |
| Packager          | Le « point de terminaison de streaming » Azure Media Services fournit des fonctionnalités d’empaquetage dynamique pour DASH et TLS et est appelé « Packager » dans le secteur des médias. 
| Heure de présentation | Heure à laquelle un événement est présenté à un utilisateur. Il s’agit de l’heure dans la chronologie du média à laquelle un utilisateur voit l’événement. Par exemple, l’heure de présentation d’un message de commande splice_info() SCTE-35 est splice_time(). |
| Heure d’arrivée      | Heure à laquelle un message d’événement arrive. L’heure d’arrivée est généralement distincte de l’heure de présentation de l’événement, dans la mesure où les événements sont envoyés avant l’heure de leur présentation.                                     |
| Piste partiellement allouée      | Piste de média qui n’est pas continue et qui est synchronisée avec une piste parente ou de contrôle.                                                                                                                                    |
| Origine            | Service de diffusion en continu de média Azure.                                                                                                                                                                                                |
| Récepteur de canal      | Service de diffusion en continu en direct Azure.                                                                                                                                                                                           |
| HLS               | Protocole de diffusion en continu HTTP Apple                                                                                                                                                                                               |
| DASH              | Diffusion en continu adaptative dynamique sur HTTP.                                                                                                                                                                                             |
| Lisse            | Protocole de diffusion en continu lisse.                                                                                                                                                                                                        |
| MPEG2-TS          | Flux de transport MPEG-2.                                                                                                                                                                                                         |
| RTMP              | Protocole multimédia en temps réel.                                                                                                                                                                                                    |
| uimsbf            | Entier non signé, avec le bit le plus significatif en tête.                                                                                                                                                                                    |

---

## <a name="12-normative-references"></a>1.2 Références normatives

Les documents suivants contiennent des dispositions qui, par référence à ce texte, constituent des dispositions du présent document. Tous les documents sont soumis à des révisions par les organismes de normalisation et les lecteurs sont encouragés à examiner la possibilité d’appliquer les éditions les plus récentes des documents répertoriés ci-dessous. Il est également rappelé aux lecteurs que les éditions plus récentes des documents référencés peuvent ne pas être compatibles avec cette version des spécifications de métadonnées chronométrées pour Azure Media Services.


|standard  |Définition  |
|---------|---------|
|[Adobe-Primetime] | [Spécification de signalement d’insertion de programme numérique Primetime 1.2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf) |
|[Adobe-Flash-AS] | [Informations de référence sur le langage Flash ActionScript](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf) |
| [AMF0]            | [« Action Message Format AMF0 »](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf) |
| [DASH-IF-IOP]     | Guide d’interopérabilité du Forum du secteur DASH v4.2 [https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html) |
| [HLS-TMD]         | Métadonnées chronométrées pour HTTP Live Streaming - [https://developer.apple.com/streaming](https://developer.apple.com/streaming) |
| [ID3v2]           | Version de balise ID3 2.4.0  [http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure) |
| [ISO-14496-12]    | ISO/IEC 14496-12 : Partie 12: Format ISO de base pour les fichiers médias, quatrième édition 15/07/2012  |
| [MPEGDASH]        | Information technology – Dynamic adaptive streaming over HTTP (DASH) – Part 1: Media presentation description and segment formats. Mai 2014. Publié. URL : https://www.iso.org/standard/65274.html |
| [MPEGCMAF]        | Information technology -- Multimedia application format (MPEG-A) -- Part 19 : Common media application format (CMAF) for segmented media. Janvier 2018. Publié. URL : https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | [ISO/IEC 7] Technologies de l'information -- Technologies des systèmes MPEG -- Partie 7 : Cryptage commun des fichiers au format de fichier de médias de la base ISO. Février 2016. Publié. URL : https://www.iso.org/standard/68042.html |
| [MS-SSTR]         | [« Microsoft Smooth Streaming Protocol », 15 Mai 15 2014](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) |
| [MS-SSTR-Ingest]  | [Spécification d’ingestion en direct au format MP4 fragmenté Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview) |
| [RFC8216]         | R. Pantos, Ed.; W. May. HTTP Live Streaming. Août 2017. Informatif. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216) |
| [RFC4648]         |The Base16, Base32, and Base64 Data Encodings - [https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648) |
| [RTMP]            |[« Adobe’s Real-Time Messaging Protocol », 21 décembre 2012](https://www.adobe.com/devnet/rtmp.html)  |
| [SCTE-35-2019]    | SCTE 35 : 2019 - Digital Program Insertion Cueing Message for Cable - https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf  |
| [SCTE-214-1]      | SCTE 214-1 2016 – MPEG DASH for IP-Based Cable Services Part 1: MPD Constraints and Extensions |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG DASH for IP-Based Cable Services Part 3: DASH/FF Profile |
| [SCTE-224]        | SCTE 224 2018r1 – Event Scheduling and Notification Interface |
| [SCTE-250]        | Event and Signaling Management API (ESAM) |

---


## <a name="2-timed-metadata-ingest"></a>2. Réception de métadonnées chronométrées

## <a name="21-rtmp-ingest"></a>2.1 Réception RTMP

Le RTMP prend en charge les signaux de métadonnées chronométrées envoyés sous forme de messages de signal AMF0 incorporés dans le flux RTMP. Les messages de signal peuvent être envoyés avant que l’événement réel ou d’un signal de jointure publicitaire [SCTE35] doivent se produire. Pour prendre en charge ce scénario, l’heure réelle de l’événement est envoyée dans le message de signal. Pour plus d'informations, voir [AMF0].

Le tableau suivant décrit le format de la charge utile du message AMF que Media Services ingère pour les modes de message [SCTE35] et « simple ».

Le nom du message [AMF0] peut être utilisé pour différencier plusieurs flux d’événements du même type.  Pour les messages [SCTE-35] et le mode « simple », le nom du message AMF DOIT être « onAdCue », comme requis dans la spécification [Adobe-Primetime].  Tous les champs non listés ci-dessous sont ignorés par Azure Media Services à la réception.

## <a name="211-rtmp-signal-syntax"></a>2.1.1 Syntaxe de signal RTMP

Azure Media Services peut écouter et répondre à plusieurs types de messages [AMF0] utilisables pour signaler différentes métadonnées synchronisées en temps réel dans le flux en direct.  La spécification [Adobe-Primetime] définit deux types de signal appelés modes « simple » et « SCTE-35 ». Pour le mode « simple », Media Services prend en charge un seul message de signal AMF appelé « onAdCue » à l’aide d’une charge utile qui correspond à la table ci-dessous définie pour le signal « mode simple ».  

La section suivante présente la charge utile du « mode simple » RTMP, utilisable pour signaler un signal publicitaire « spliceOut » de base qui sera transmis au manifeste du client pour TLS, DASH et Microsoft Smooth Streaming. Cela s’avère très utile dans les scénarios où le client ne dispose pas d’un système complexe d’insertion ou de déploiement de signalement d’annonces basées sur SCTE-35, et où il utilise un encodeur local de base pour envoyer le message de signal via une API. En règle générale, l’encodeur local prend en charge une API REST pour déclencher ce signal, ce qui permet également « d’épisser » le flux vidéo en insérant une image IDR dans la vidéo et en démarrant un nouveau groupe d’images.

## <a name="212--simple-mode-ad-signaling-with-rtmp"></a>2.1.2  Signalisation d’annonce en mode simple avec RTMP

| Nom du champ | Type de champ | Requis ? | Descriptions                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| cue        | Chaîne     | Obligatoire | Message de l'événement.  Doit être « SpliceOut » pour désigner une jointure en mode simple.                                              |
| id         | Chaîne     | Obligatoire | Identificateur unique décrivant la jointure ou le segment. Identifie cette instance du message.                            |
| duration   | Number     | Obligatoire | Durée de la jointure. Les unités sont des fractions de seconde.                                                                |
| elapsed    | Number     | Facultatif | Lorsque le signal est répété pour prendre en charge le réglage, ce champ est la quantité de temps de présentation qui s’est écoulée depuis le début de la jointure. Les unités sont des fractions de seconde. Lorsque vous utilisez le mode simple, cette valeur ne doit pas dépasser la durée originale de la jointure.                                                  |
| time       | Number     | Obligatoire | Doit être l’heure de la jointure, dans le temps de présentation. Les unités sont des fractions de seconde.                                     |

---
 
## <a name="213-scte-35-mode-ad-signaling-with-rtmp"></a>2.1.3 Signalisation d’annonce en mode SCTE-35 avec RTMP

Lorsque vous travaillez avec un flux de travail de production de diffusion plus avancé qui requiert la transmission du message de charge utile SCTE-35 complet au manifeste TLS ou DASH, il est préférable d’utiliser le « mode SCTE-35 » de la spécification [Adobe-Primetime].  Ce mode prend en charge les signaux SCTE-35 intrabande envoyés directement dans un encodeur live local, qui encode ensuite les signaux dans le flux RTMP à l’aide du « mode SCTE-35 » indiqué dans la spécification [Adobe-Primetime]. 

En général, les messages SCTE-35 peuvent apparaître uniquement dans des entrées de flux de transport MPEG-2 (TS) sur un encodeur local. Contactez le fabricant de votre encodeur pour plus d’informations sur la configuration d’une réception de flux de transport contenant SCTE-35 et son activation pour le transfert vers RTMP en mode Adobe SCTE-35.

Dans ce scénario, la charge utile suivante DOIT être envoyée à partir de l’encodeur local à l’aide du type de message **« onAdCue »** [AMF0].

| Nom du champ | Type de champ | Requis ? | Descriptions                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| cue        | Chaîne     | Obligatoire | Message de l'événement.  Pour les messages [SCTE-35], il DOIT s’agir de [RFC4648] binary splice_info_section() codé en Base64 de façon à ce que les messages soient envoyés aux clients HLS, Smooth Streaming et Dash.                                              |
| Type       | Chaîne     | Obligatoire | URN ou URL identifiant le schéma du message. Pour les messages [SCTE-35], cela **DOIT** être **« scte35 »** de façon à ce que les messages soient envoyés aux clients HLS, Smooth Streaming et Dash conformément à la norme [Adobe-Primetime]. Éventuellement, l’URN « urn: SCTE:scte35:2013:bin » peut également être utilisée pour signaler un message [SCTE-35]. |
| id         | Chaîne     | Obligatoire | Identificateur unique décrivant la jointure ou le segment. Identifie cette instance du message.  Les messages dont la sémantique est identique auront la même valeur.|
| duration   | Number     | Obligatoire | Durée de l’événement ou du segment de jointure publicitaire, si elle est connue. Si elle est inconnue, la valeur **DOIT** être 0.                                                                 |
| elapsed    | Number     | Facultatif | Lorsque le signal publicitaire [SCTE-35] est répété pour le réglage, ce champ est la quantité de temps de présentation qui s’est écoulée depuis le début de la jointure. Les unités sont des fractions de seconde. En mode de [SCTE-35], cette valeur peut dépasser la durée originale spécifiée de la jointure ou du segment.                                                  |
| time       | Number     | Obligatoire | Heure de présentation de l’événement ou de la jointure publicitaire.  L’heure et la durée de présentation **DOIVENT** être en phase avec les points d’accès du flux (SAP) de type 1 ou 2, comme défini dans la norme [ISO-14496-12] Annexe I. Pour les sorties HLS, l’heure et la durée **DOIVENT** correspondre aux limites de segment. L’heure et la durée de présentation de messages d’événements différents au sein du même flux d’événements NE DOIVENT PAS se chevaucher. Les unités sont des fractions de seconde.

---
## <a name="214-elemental-live-oncuepoint-ad-markers-with-rtmp"></a>2.1.4 les marqueurs publicitaires « onCuePoint » Elemental Live avec RTMP

L’encodeur local Elemental Live prend en charge les marqueurs publicitaires dans le signal RTMP. Azure Media Services prend actuellement en charge uniquement le type de marqueur publicitaire « onCuePoint » pour RTMP.  Vous pouvez activer cette option dans les paramètres de groupe Adobe RTMP au sein des paramètres ou de l’API de l’encodeur live Elemental Media en définissant « **ad_markers** » sur « onCuePoint ».  Pour plus de détails, veuillez vous référer à la documentation d’Elemental Live. L’activation de cette fonctionnalité dans le groupe RTMP transmettra les signaux SCTE-35 aux sorties Adobe RTMP à traiter par Azure Media Services.

Le type de message « onCuePoint » est défini dans [Adobe-Flash-AS] et possède la structure de charge utile suivante une fois envoyé à partir de la sortie RTMP Elemental Live.


| Propriété  |Description  |
|---------|---------|
|  Nom     | Le nom DOIT être «**scte35**» par Elemental Live. |
|time     |  La durée en secondes au cours de laquelle le point de repère s’est produit dans le fichier vidéo pendant la chronologie |
| Type     | Le type de point de repère DOIT être défini sur« **event** ». |
| parameters | Un tableau associatif de chaînes de paires nom/valeur contenant les informations du message SCTE-35, y compris l’ID et la durée. Ces valeurs sont analysées par Azure Media Services et incluses dans la balise de décoration du manifeste.  |


Quand ce mode de marqueur publicitaire est utilisé, la sortie du manifeste TLS est similaire au mode « simple » d’Adobe. 

### <a name="215-cancellation-and-updates"></a>2.1.5 Annulation et mises à jour

Des messages peuvent être annulés ou mis à jour par l’envoi de plusieurs messages avec les mêmes heure et ID de présentation. L’heure et l’ID de présentation identifient de façon unique l’événement, et le dernier message reçu pour une heure de présentation spécifique qui correspond aux contraintes de décompte est le message traité. L’événement mis à jour remplace tout message reçu précédemment. La contrainte de décompte est de quatre secondes. Les messages reçus au moins quatre secondes avant l’heure de présentation seront traités.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Réception de MP4 fragmentée (diffusion en continu lisse)

Pour les exigences relatives à la réception de flux en direct, voir [MS-SSTR-Ingest]. Les sections suivantes fournissent des détails sur la réception de métadonnées de présentation chronométrées.  Les métadonnées de présentation chronométrées sont reçues en tant que piste partiellement allouée, telle que définie dans la zone de manifeste du serveur en direct (voir MS-SSTR) et la zone de vidéo (« moov »).  

Chaque fragment se compose d’une zone de fragment vidéo (« moof ») et d’une zone de données multimédias (« mdat »), où la zone « mdat » est le message binaire.

Pour permettre l’insertion précise de publicités, l’encodeur DOIT diviser le fragment à l’heure de la présentation, où l’insertion du signal est requise.  Un nouveau fragment DOIT être créé, commençant par une nouvelle image IDR ou des points d’accès de flux (SAP) de type 1 ou 2, comme défini dans l’Annexe I de la norme [ISO-14496-12]. Cela permet à Azure Media Packager de générer correctement un manifeste HLS et un manifeste à plusieurs périodes DASH lorsque la nouvelle période commence au moment précis de la jointure.

### <a name="221-live-server-manifest-box"></a>2.2.1 Zone de manifeste du serveur en direct

La piste partiellement allouée **DOIT** être déclarée dans la zone de manifeste du serveur en direct avec une entrée **\<textstream\>** , et **DOIT** avoir l’ensemble d’attributs suivants :

| **Nom de l’attribut** | **Type de champ** | **Obligatoire ?** | **Description**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Number         | Obligatoire      | **DOIT** être « 0 », indiquant une piste avec un débit binaire variable inconnu.                                                                                                                                                                                                 |
| parentTrackName    | Chaîne         | Obligatoire      | **DOIT** être le nom de la piste parent sur laquelle les codes temporels de la piste partiellement allouée sont alignés à l’échelle de temps. La piste parent ne peut pas être une piste partiellement allouée.                                                                                                                    |
| manifestOutput     | Booléen        | Obligatoire      | **DOIT** être « true » pour indiquer que la piste partiellement allouée sera incorporée dans le manifeste du client lisse.                                                                                                                                                               |
| Subtype            | Chaîne         | Obligatoire      | **DOIT** être le code « DATA » de quatre caractères.                                                                                                                                                                                                                        |
| Schéma             | Chaîne         | Obligatoire      | **DOIT** être un URN ou une URL identifiant le schéma de message. Pour les messages [SCTE-35], il **DOIT** s’agir de « urn : scte:scte35:2013:bin » de façon à ce que les messages soient envoyés aux clients HLS, Smooth Streaming et Dash conformément à la norme [SCTE-35]. |
| trackName          | Chaîne         | Obligatoire      | **DOIT** être le nom de la piste partiellement allouée. Le trackName peut servir à différencier plusieurs flux d’événements dont le schéma est identique. Chaque flux d’événements **DOIT** avoir un nom de piste unique.                                                                           |
| timescale          | Number         | Facultatif      | **DOIT** être l’échelle de temps de la piste parent.                                                                                                                                                                                                                      |

---

### <a name="222-movie-box"></a>2.2.2 Zone de vidéo

La zone de vidéo (« moov ») suit la zone de manifeste du serveur en direct en tant que partie de l’en-tête de flux pour une piste partiellement allouée.

La zone « moov » **DOIT** contenir une zone **TrackHeaderBox (« tkhd »)** , telle que définie dans la norme [ISO-14496-12] avec les contraintes suivantes :

| **Nom du champ** | **Type de champ**          | **Obligatoire ?** | **Description**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duration       | Entier non signé 64 bits | Obligatoire      | **DOIT** être 0, étant donné que la zone de piste contient zéro exemple et que la durée totale des exemples dans la zone de piste est 0. |

---

La zone « moov » **DOIT** contenir un **HandlerBox (« hdlr »)** , tel que défini dans la norme [ISO-14496-12] avec les contraintes suivantes :

| **Nom du champ** | **Type de champ**          | **Obligatoire ?** | **Description**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | Entier non signé 32 bits | Obligatoire      | **DOIT** être « meta ». |

---

La zone « stsd » **DOIT** contenir une zone de MetaDataSampleEntry avec un nom de codage tel que défini dans la norme [ISO-14496-12].  Par exemple, pour les messages SCTE-35, le nom de codage **DOIT** être « scte ».

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Zone de fragment vidéo et zone de données multimédias

Les fragments de piste partiellement allouée se composent d’une zone de fragment vidéo (« moof ») et d’une zone de données multimédias (« mdat »).

> [!NOTE]
> Pour permettre l’insertion précise de publicités, l’encodeur DOIT diviser le fragment à l’heure de la présentation, où l’insertion du signal est requise.  Un nouveau fragment DOIT être créé, commençant par une nouvelle image IDR ou des points d’accès de flux (SAP) de type 1 ou 2, comme défini dans l’Annexe I de la norme [ISO-14496-12]
> 

La zone MovieFragmentBox (« moof ») **DOIT** contenir une zone **TrackFragmentExtendedHeaderBox (« uuid »)** telle que définie dans [MS-SSTR] avec les champs suivants :

| **Nom du champ**         | **Type de champ**          | **Obligatoire ?** | **Description**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | Entier non signé 64 bits | Obligatoire      | **DOIT** être l’heure d’arrivée de l’événement. Cette valeur aligne le message sur la piste parent.   |
| fragment_duration      | Entier non signé 64 bits | Obligatoire      | **DOIT** être la durée de l’événement. La durée peut être zéro pour indiquer que la durée est inconnue. |

---


La zone de MediaDataBox (« mdat ») **DOIT** avoir le format suivant :

| **Nom du champ**          | **Type de champ**                   | **Obligatoire ?** | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | Entier non signé 32 bits (uimsbf) | Obligatoire      | Détermine le format du contenu de la zone « mdat ». Les versions non reconnues seront ignorées. Actuellement, la seule version prise en charge est 1.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | Entier non signé 32 bits (uimsbf) | Obligatoire      | Identifie cette instance du message. Des messages dont la sémantique est identique ont la même valeur ; le traitement de toute zone de message d’événement ayant le même ID est suffisant.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | Entier non signé 32 bits (uimsbf) | Obligatoire      | La somme de fragment_absolute_time, spécifié dans TrackFragmentExtendedHeaderBox et le presentation_time_delta, **DOIT** être l’heure de présentation de l’événement. L’heure et la durée de présentation **DOIVENT** être en phase avec les points d’accès du flux (SAP) de type 1 ou 2, comme défini dans la norme [ISO-14496-12] Annexe I. Pour les sorties HLS, l’heure et la durée **DOIVENT** correspondre aux limites de segment. L’heure et la durée de présentation de messages d’événements différents au sein du même flux d’événements **NE DOIVENT PAS** se chevaucher. |
| message                 | tableau d’octets                       | Obligatoire      | Message de l'événement. Pour les messages [SCTE-35], le message est le binaire splice_info_section(). Pour les messages [SCTE-35], il **DOIT** s’agir de splice_info_section() de façon à ce que les messages soient envoyés aux clients HLS, Smooth Streaming et Dash conformément à la norme [SCTE-35]. Pour les messages [SCTE-35], le binaire splice_info_section() est la charge utile de la zone « mdat », et il n’**EST PAS** codé en Base64.                                                            |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 Annulation et mises à jour

Des messages peuvent être annulés ou mis à jour par l’envoi de plusieurs messages avec les mêmes heure et ID de présentation.  L’heure et l’ID de présentation identifient l’événement. Le dernier message reçu pour une heure de présentation spécifique qui répond aux contraintes de décompte est le message traité. Le message mis à jour remplace tous les messages reçus précédemment.  La contrainte de décompte est de quatre secondes. Les messages reçus au moins quatre secondes avant l’heure de présentation seront traités. 


## <a name="3-timed-metadata-delivery"></a>3 Remise de métadonnées chronométrées

Les données de flux de données d’événements sont opaques pour Media Services. Media Services transmet simplement trois éléments d’informations entre le point de terminaison de réception et le point de terminaison client. Les propriétés suivantes sont fournies au client conformément à la norme [SCTE-35] et/ou au protocole de diffusion en continu du client :

1.  Schéma : URN ou URL identifiant le schéma du message.
2.  Heure de présentation : heure de présentation de l’événement dans la chronologie du média.
3.  Durée : durée de l’événement.
4.  ID : identificateur unique facultatif pour l’événement.
5.  Message : données de l’événement.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Manifeste Microsoft Smooth Streaming  

Pour plus d’informations sur la mise en forme d’une piste de message partiellement allouée, consultez Gestion d’une piste partiellement allouée [MS-SSTR]. Pour les messages [SCTE35], Smooth Streaming génère le splice_info_section () encodé en Base64 dans un fragment partiellement alloué.
StreamIndex **DOIT** avoir un sous-type « Data » et CustomAttributes **DOIT** contenir un attribut avec Nom = « Schema » et Valeur = « urn: scte: scte35:2013:bin ».

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Exemple de manifeste de client lissé avec un codage Base64 [SCTE35] splice_info_section ()
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-sparse-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t=”600000000” d=”300000000”>    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Décoration de manifeste Apple HLS

Azure Media Services prend en charge les balises de manifeste TLS suivantes pour signaler les informations d’usage publicitaire lors d’un événement en direct ou à la demande. 

- EXT-X-DATERANGE tel que défini dans Apple TLS [RFC8216]
- EXT-X-CUE tel que défini dans [Adobe-Primetime] - ce mode est considéré comme « hérité ». Les clients doivent adopter la balise EXT-X-DATERANGE lorsque cela est possible.

La sortie des données pour chaque balise varie selon le mode de signal de réception utilisé. Par exemple, la réception RTMP avec le mode simple de Adobe ne contient pas la charge utile encodée en base64 SCTE-35.

## <a name="321-apple-hls-with-adobe-primetime-ext-x-daterange-recommended"></a>3.2.1 Apple TLS avec Adobe Primetime EXT-X-DATERANGE (recommandé)

La spécification HTTP Live Streaming [RFC8216] Apple autorise la signalisation des messages [SCTE-35]. Les messages sont insérés dans la sélection de segments dans une balise EXT-X-DATERANGE d’après la section [RFC8216] intitulée « Mapping SCTE-35 into EXT-X-DATERANGE ».  La couche d’application cliente peut analyser la sélection M3U et traiter les balises M3U, ou recevoir les événements par le biais de l’infrastructure Apple Player.  

L’approche **RECOMMANDÉE** dans Azure Media Services (version d’API 3) consiste à suivre [RFC8216] et à utiliser la balise ext-X_DATERANGE pour la décoration de disponibilité publicitaire [SCTE35] dans le manifeste.

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple TLS avec Adobe Primetime EXT-X-CUE (hérité)

Il existe également une implémentation « héritée » fournie dans Azure Media Services (API version 2 et 3) qui utilise la balise EXT-X-CUE telle que définie dans le « mode SCTE-35 » [Adobe-Primetime]. Dans ce mode, Azure Media Services incorpore le code Base64 [SCTE-35] splice_info_section () dans la balise EXT-X-CUE.  

La balise EXT-X-CUE « héritée » est définie comme ci-dessous et peut également faire référence dans la spécification [Adobe-Primetime]. Cette option ne doit être utilisée que pour les signaux SCTE35 hérités, si nécessaire. Sinon, la balise recommandée est définie comme EXT-X-DATERANGE dans [RFC8216]. 

| **Nom de l’attribut** | **Type**                      | **Obligatoire ?**                             | **Description**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CUE                | Chaîne entre guillemets.                 | Obligatoire                                  | Message encodé sous forme de chaîne en Base64, comme décrit dans [RFC4648]. Pour les messages [SCTE-35], il s’agit de la zone splice_info_section() en Base64.                                                                                                |
| TYPE               | Chaîne entre guillemets.                 | Obligatoire                                  | URN ou URL identifiant le schéma du message. Pour les messages [SCTE-35], le type prend la valeur spéciale « scte35 ».                                                                                                                                |
| ID                 | Chaîne entre guillemets.                 | Obligatoire                                  | Identificateur unique de l’événement. Si l’ID n’est pas spécifié lors de la réception du message, Azure Media Services génère un id unique.                                                                                                                                          |
| DURATION           | Nombre décimal à virgule flottante. | Obligatoire                                  | Durée de l’événement. Si elle est inconnue, la valeur **DOIT** être 0. Les unités sont des fractions de seconde.                                                                                                                                                                                           |
| ELAPSED            | Nombre décimal à virgule flottante. | Facultatif, mais requis pour la fenêtre glissante. | Lorsque le signal est répété pour prendre en charge une fenêtre glissante de présentation, ce champ **DOIT** être le temps de présentation qui s’est écoulé depuis le début de l’événement. Les unités sont des fractions de seconde. Cette valeur peut dépasser la durée originale spécifiée de la jointure ou du segment. |
| TEMPS               | Nombre décimal à virgule flottante. | Obligatoire                                  | Heure de présentation de l’événement. Les unités sont des fractions de seconde.                                                                                                                                                                                                                    |


La couche d’application du lecteur HLS utilisera le TYPE pour identifier le format du message, décoder celui-ci, appliquer les conversions de temps nécessaires, et traiter l’événement.  Les événements sont synchronisé dans la sélection de segments de la piste parent, en fonction de leur horodatage.  Ils sont insérés devant le segment le plus proche (balise #EXTINF).

### <a name="323-hls-segment-playlist-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 Exemple de sélection de segment HLS à l’aide de la balise « héritée » EXT-X-CUE Adobe Primetime

L’exemple suivant montre l’ornement de manifeste HLS à l’aide de la balise EXT-X-CUE Adobe Primetime.  Le paramètre « CUE » contient la charge utile SCTE-35 splice_info complète codée en base64, qui indique que ce signal s’est révélé par RTMP en mode signal SCTE-35 d’Adobe, ou qu’il est arrivé en mode signal SCTE-35 Smooth Streaming. 

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:346
#EXT-X-TARGETDURATION:6
#EXT-X-I-FRAMES-ONLY
#EXT-X-PROGRAM-DATE-TIME:2018-12-13T15:54:19.462Z
#EXTINF:4.000000,no-desc
KeyFrames(video_track=15447164594627600,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447164634627600,format=m3u8-aapl)
#EXT-X-CUE:ID="1026",TYPE="scte35",DURATION=30.000000,TIME=1544716520.022760,CUE="/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w=="
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447165474627600,format=m3u8-aapl)
~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3.2.4 Gestion des messages HLS pour une balise « héritée » EXT-X-CUE Adobe Primetime

Les événements sont signalés dans la sélection de segments de chaque piste audio et vidéo. La position de la balise EXT-X-CUE **DOIT** toujours être immédiatement devant le premier segment HLS (pour jointure externe ou début de segment) ou immédiatement derrière les derniers segments HLS (pour jointure interne ou fin du segment) auxquels ses attributs TIME et DURATION se rapportent, comme requis par [Adobe-Primetime].

Quand une fenêtre glissante de présentation est activée, la balise EXT-X-CUE **DOIT** être répétée assez souvent pour que la jointure ou le segment soient toujours entièrement décrits dans la sélection de segments, et l’attribut ELAPSED **DOIT** être utilisé pour indiquer la durée d’activité de la jointure ou du segment, comme requis par [Adobe-Primetime].

Quand une fenêtre glissante de présentation est activée, les balises EXT-X-CUE sont supprimées de la sélection de segments lorsque le temps de multimédia auquel elles font référence est en dehors de la fenêtre glissante de présentation.

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 Décoration de manifeste DASH (MPD)

[MPEGDASH] permet de signaler les événements de trois façons :

1.  Événements signalés dans le MPD EventStream
2.  Événements signalés dans la bande à l’aide de la boîte de message d’événement (« EMSG »)
3.  Une combinaison de 1 et 2

Les événements signalés dans le MPD EventStream sont utiles pour une diffusion en continu VOD, car les clients ont accès à tous les événements, dès que le MPD est téléchargé. Ils sont également utiles pour la signalisation SSAI, où le fournisseur SSAI en aval doit analyser les signaux à partir d’un manifeste MPD de plusieurs périodes, et insérer du contenu publicitaire de manière dynamique.  La solution intrabande (« EMSG ») est utile pour le streaming en direct où les clients n’ont pas besoin de télécharger à nouveau le MPD, ou il n’y a aucune manipulation de manifeste SSAI entre le client et l’origine. 

Le comportement par défaut Azure Media Services pour DASH est de signaler dans MPD EventStreams MPD et intrabande à l’aide de la boîte de message d’événement (« EMSG »).

Les messages de signal ingérés via [RTMP] ou [MS-SSTR-Ingest] sont mappés dans des événements DASH, à l’aide de boîtes « EMSG » intrabandes et/ou MPD EventStreams. 

La signalisation SCTE-35 intrabande pour DASH suit la définition et les exigences définies dans [SCTE-214-3] et également dans la section 13.12.2 de la norme [DASH-IF-IOP] (« SCTE35 Events »). 

Pour le transport intrabande [SCTE-35], la boîte de message d’événement (« EMSG ») utilise le schemeId = « urn:scte:scte35:2013:bin ». Pour la décoration du manifeste MPD, le schemeId EventStream utilise « urn:scte:scte35:2014:xml+bin ».  Ce format est une représentation XML de l’événement, qui comprend une sortie binaire codée en base64 du message SCTE-35 complet qui est arrivé à la réception. 

Les définitions de référence normative de transport des messages de signal [SCTE-35] en DASH sont disponibles dans la section 6.7.4 de la norme [SCTE-214-1] (MPD) et la section 7.3.2 de la norme [SCTE-214-3] (transport des messages de signal SCTE 35).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 Signalisation EventStream MPEG DASH (MPD)

La décoration de manifeste (MPD) sera signalée dans le MPD à l’aide de l’élément EventStream qui apparaît dans l’élément Period. Le schemeId utilisé est « urn:scte:scte35:2014:xml+bin ».

> [!NOTE]
> À des fins de concision, [SCTE-35] autorise l’utilisation de la section codée en Base64 dans l’élément Signal.Binary (plutôt que l’élément Signal.SpliceInfoSection) comme alternative au transport d’un message de signal entièrement analysé.
> Azure Media Services utilise cette approche « xml+bin » pour signaler dans le manifeste MPD.
> Il s’agit également de la méthode recommandée utilisée dans la norme [DASH-IF-IOP] - Voir la section intitulée [« flux d’événements d’insertion publicitaire » du guide DASH-IF-IOP](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 

L’élément EventStream a les attributs suivants :

| **Nom de l’attribut** | **Type**                | **Obligatoire ?** | **Description**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | chaîne                  | Obligatoire      | Identifie le schéma du message. Le schéma est défini sur la valeur de l’attribut Scheme dans la zone de manifeste du serveur en direct. La valeur **DOIT**  être un URN ou une URL identifiant le schéma de message. Le schemeId de sortie pris en charge doit être « urn:scte:scte35:2014: xml+bin » par la section 6.7.4 de la norme [SCTE-214-1] (MPD), car le service prend en charge uniquement « xml+bin » à ce stade pour la concision dans le MPD.  |
| value              | chaîne                  | Facultatif      | Valeur de chaîne supplémentaire utilisée par les propriétaires du schéma pour personnaliser la sémantique du message. Afin de différencier plusieurs flux d’événements dont le schéma est identique, la valeur **DOIT** être définie sur le nom du flux d’événements (trackName pour [MS-SSTR-Ingest] ou nom du message AMF pour une réception [RTMP]). |
| Échelle de temps          | Entier non signé 32 bits | Obligatoire      | L’échelle de temps, en cycles par seconde.                                                                                                                                                                                                       |


### <a name="332-example-mpeg-dash-manifest-mpd-signaling-of-scte-35-using-eventstream"></a>3.3.2 Exemple de signalement de manifeste MPEG DASH (MPD) de SCTE-35 à l’aide de EventStream

~~~ xml
<!-- Example MPD using xml+bin style signaling per [SCTE-214-1] -->
  <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">
            <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">
           <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
    </EventStream>
~~~

> [!IMPORTANT]
> Notez que presentationTime est l’heure de présentation de l’événement [SCTE-35] traduite pour être relative à l’heure de début de la période, et non à l’heure d’arrivée du message.
> [MPEGDASH] définit le Event@presentationTime comme « Spécifie le temps de présentation multimédia de l’événement par rapport au début de la période.
> La valeur de la durée de présentation en secondes est la division de la valeur de cet attribut et la valeur de l’attribut EventStream@timescale.
> Si elle n’est pas présente, la valeur de l’heure de présentation est 0.


### <a name="333-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.3 Signalisation de zone de message d’événement dans la bande MPEG DASH

Un flux d’événement dans la bande nécessite que le MPD ait un élément InbandEventStream au niveau défini de l’adaptation.  Cet élément a un attribut schemeIdUri obligatoire et un attribut d’échelle de temps facultatif, qui s’affichent également dans la zone de message d’événement (« emsg »).  Les zones de message d’événement avec des identificateurs de schéma qui ne sont pas définis dans le MPD ne **DOIVENT PAS** être présentes.

Pour le transport intrabande [SCTE-35], les signaux **DOIVENT** utiliser le schemeId=« urn:scte:scte35:2013:bin ».
Les définitions normatives du transport des messages intrabandes [SCTE-35] sont définies dans la section 7.3.2 de la norme [SCTE-214-3] (transport des messages de signal SCTE 35).

Les détails suivants décrivent les valeurs spécifiques que le client doit attendre dans le « EMSG » en conformité avec la norme [SCTE-214-3] :

| **Nom du champ**          | **Type de champ**          | **Obligatoire ?** | **Description**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | chaîne                  | Obligatoire      | Identifie le schéma du message. Le schéma est défini sur la valeur de l’attribut Scheme dans la zone de manifeste du serveur en direct. La valeur **DOIT** être un URN identifiant le schéma de message. Pour les messages [SCTE-35], il **DOIT** s’agir de « urn:scte:scte35:2013:bin » en conformité avec la norme [SCTE-214-3] |
| Valeur                   | chaîne                  | Obligatoire      | Valeur de chaîne supplémentaire utilisée par les propriétaires du schéma pour personnaliser la sémantique du message. Afin de différencier plusieurs flux d’événements dont le schéma est identique, la valeur sera définie sur le nom du flux d’événements (trackName pour une réception lisse ou nom du message AMF pour une réception RTMP).                                                                  |
| Échelle de temps               | Entier non signé 32 bits | Obligatoire      | Échelle de temps, en battements par seconde, des champs d’heures et de durée dans la zone « emsg ».                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | Entier non signé 32 bits | Obligatoire      | Delta de temps de présentation multimédia entre l’heure de la présentation de l’événement et l’heure de présentation la plus précoce dans ce segment. L’heure et la durée de présentation **DOIVENT** être en phase avec les points d’accès de flux (SAP) de type 1 ou 2, comme définis dans [ISO-14496-12] l’annexe I.                                                                                            |
| event_duration          | Entier non signé 32 bits | Obligatoire      | Durée de l’événement ou 0xFFFFFFFF pour indiquer une durée inconnue.                                                                                                                                                                                                                                                                                          |
| ID                      | Entier non signé 32 bits | Obligatoire      | Identifie cette instance du message. Les messages dont la sémantique est identique auront la même valeur. Si l’ID n’est pas spécifié lors de la réception du message, Azure Media Services génère un id unique.                                                                                                                                                    |
| Message_data            | tableau d’octets              | Obligatoire      | Message de l'événement. Pour les messages [SCTE-35], leurs données sont le binaire splice_info_section() en conformité avec la norme [SCTE-214-3] |

### <a name="334-dash-message-handling"></a>3.3.4 Gestion des messages DASH

Les événements sont signalés dans la bande, dans la zone « emsg », pour les pistes tant audio que vidéo.  La signalisation se produit pour toutes les demandes de segments pour lesquelles la valeur presentation_time_delta est inférieure ou égale à 15 secondes. 

Quand une fenêtre de présentation glissante est activée, les messages d’événement sont supprimés du MPD lorsque la somme du temps et la durée du message d’événement sont inférieures au temps des données multimédias dans le manifeste.  En d’autres termes, les messages d’événements sont supprimés du manifeste lorsque le temps multimédia auquel ils font référence sort de la fenêtre glissante de présentation.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. Guide d’implémentation de l’ingestion SCTE-35 pour les fournisseurs d’encodeurs

Les instructions suivantes sont des problèmes courants qui peuvent avoir un impact sur l’implémentation d’un fournisseur d’encodeurs de cette spécification.  Les instructions ci-dessous ont été collectées en fonction des commentaires de partenaires réels pour faciliter l’implémentation de cette spécification pour d’autres. 

Les messages [SCTE-35] sont reçus dans un format binaire à l’aide du schéma **« urn:scte:scte35:2013:bin »** pour la [réception MS-SSTR] et le type **« scte35 »** pour la réception RTMP. Pour faciliter la conversion d’une synchronisation [SCTE-35], qui est basée sur des horodatages de présentation de flux de transport MPEG-2 (PTS), un mappage entre PTS (pts_time + pts_adjustment de splice_time()) et la chronologie du média sont fournis par l’heure de présentation de l’événement (champ fragment_absolute_time pour la réception lisse et champ d’heure pour la réception RTMP). Le mappage est nécessaire, car la valeur PTS 33 bits change environ toutes les 26,5 heures.

La réception de diffusion en continu lisse [MS-SSTR-Ingest] **nécessite** que la zone de données multimédias (« mdat ») contienne la valeur **splice_info_section()** définie dans la norme [SCTE-35]. 

Pour la réception RTMP, l’attribut cue du message AMF est défini sur la valeur **splice_info_section()** codée en base64 définie dans la norme [SCTE-35].  

Lorsque les messages ont le format décrit ci-dessus, ils sont envoyés aux clients HLS, Lisse et DASH comme définis ci-dessus.  

Lors du test de votre implémentation avec la plateforme Azure Media Services, commencez par effectuer des tests avec un LiveEvent « pass-through » avant de passer au test sur un LiveEvent d’encodage.

---

## <a name="next-steps"></a>Étapes suivantes
Afficher les parcours d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
