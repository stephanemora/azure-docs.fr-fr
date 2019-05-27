---
title: API Synthèse vocale du service Microsoft Speech | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Permet d’utiliser l’API Synthèse vocale pour convertir du texte en parole en temps réel dans différentes voix et différentes langues.
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: priyar
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a046bec5d81d828d88716d31c84e9cbcdcea1a08
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "60515424"
---
# <a name="bing-text-to-speech-api"></a>API Synthèse vocale Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="Introduction"></a>Introduction

Avec l’API Synthèse vocale Bing, votre application peut envoyer des requêtes HTTP à un serveur dans le cloud. À partir de là, le texte est instantanément synthétisé en un discours ultra-réaliste, puis renvoyé sous la forme d’un fichier audio. Vous pouvez utiliser cette API dans de nombreux contextes pour proposer une synthèse vocale en temps réel dans différentes voix et différentes langues.

## <a name="VoiceSynReq"></a>Requête de synthèse vocale

### <a name="Subscription"></a>Jeton d’autorisation

Chaque requête de synthèse vocale requiert un jeton d’accès JSON Web Token (JWT). Celui-ci est transmis dans l’en-tête de la requête. Il expire dans un délai de 10 minutes. Pour plus d’informations sur la façon de vous abonner et d’obtenir des clés API permettant de récupérer des jetons d’accès JWT valides, consultez la page [Abonnement à Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

La clé API est transmise au service de jetons. Exemple :

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Les informations d’en-tête requises pour l’accès du jeton sont décrites ci-après.

Nom| Format | Description 
----|----|----
Ocp-Apim-Subscription-Key | ASCII | Votre clé d'abonnement

Le service de jetons renvoie le jeton d’accès JWT sous la forme `text/plain`. Par la suite, le jeton JWT est transmis en tant que `Base64 access_token` au point de terminaison de synthèse vocale sous la forme d’un en-tête d’autorisation ayant pour préfixe la chaîne `Bearer`. Exemple :

`Authorization: Bearer [Base64 access_token]`

Les clients doivent utiliser le point de terminaison suivant pour accéder au service de synthèse vocale :

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Tant que vous n’avez pas acquis de jeton d’accès avec votre clé d’abonnement comme décrit précédemment, ce lien génère une erreur de réponse `403 Forbidden`.

### <a name="Http"></a>En-têtes HTTP

Le tableau suivant répertorie les en-têtes HTTP utilisés pour les demandes de synthèse vocale.

En-tête |Valeur |Commentaires
----|----|----
Content-Type | application/ssml+xml | Type de contenu d’entrée.
X-Microsoft-OutputFormat | **1.** ssml-16khz-16bit-mono-tts <br> **2.** raw-16khz-16bit-mono-pcm <br>**3.** audio-16khz-16kbps-mono-siren <br> **4.** riff-16khz-16kbps-mono-siren <br> **5.** riff-16khz-16bit-mono-pcm <br> **6.** audio-16khz-128kbitrate-mono-mp3 <br> **7.** audio-16khz-64kbitrate-mono-mp3 <br> **8.** audio-16khz-32kbitrate-mono-mp3 | Format audio de sortie.
X-Search-AppId | Un GUID (hexadécimal uniquement, sans tiret) | Un ID identifiant de manière unique l’application cliente. Cela peut être l’ID du store pour les applications. Si aucun ID n’est pas disponible, reste la possibilité d’en générer un par utilisateur pour une application.
X-Search-ClientID | Un GUID (hexadécimal uniquement, sans tiret) | Un ID identifiant de manière unique une instance d’application pour chaque installation.
User-Agent. | Nom de l'application | Le nom de l’application est requis. Il doit utiliser moins de 255 caractères.
Authorization | Jeton d’autorisation |  Consultez la section <a href="#Subscription">Jeton d’autorisation</a>.

### <a name="InputParam"></a>Paramètres d’entrée

Les requêtes envoyées à l’API Synthèse vocale Bing le sont par l’intermédiaire des appels HTTP POST. Les en-têtes sont indiqués dans la section précédente. Le corps contient l’entrée SSML (Speech Synthesis Markup Language) qui représente le texte à synthétiser. Pour obtenir une description de la balise utilisée pour contrôler des aspects de reconnaissance vocale comme la langue et le sexe de l’orateur, consultez la section [Spécification SSML W3C](https://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>La taille maximale de l’entrée SSML prise en charge est de 1 024 caractères, balises comprises.

###  <a name="SampleVoiceOR"></a>Exemple : requête de sortie vocale

Voici un exemple de requête de sortie vocale :

```HTTP
POST /synthesize
HTTP/1.1
Host: speech.platform.bing.com

X-Microsoft-OutputFormat: riff-8khz-8bit-mono-mulaw
Content-Type: application/ssml+xml
Host: speech.platform.bing.com
Content-Length: 197
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>Microsoft Bing Voice Output API</voice></speak>
```

## <a name="VoiceOutResponse"></a>Réponse de sortie vocale

L’API Synthèse vocale Bing utilise HTTP POST pour renvoyer les données audio au client. La réponse de l’API contient le flux audio et le codec, et elle correspond au format de sortie demandé. Le fichier audio renvoyé suite à une requête bien précise ne doit pas dépasser 15 secondes.

### <a name="SuccessfulRecResponse"></a>Exemple : réponse de synthèse réussie

Le code suivant illustre un exemple de réponse JSON. Il correspond à une requête de synthèse vocale qui a abouti. Les commentaires et la mise en forme de ce code sont fournis pour les besoins de cet exemple uniquement et ne font pas partie de la réponse réelle.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Exemple : échec de synthèse

L’exemple de code suivant illustre une réponse JSON. Il correspond à une demande de synthèse vocale qui a échoué :

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Réponses d’erreur

Error | Description 
----|----
HTTP/400 (Requête incorrecte) | Un paramètre obligatoire est manquant, vide ou null, ou la valeur transmise à un paramètre obligatoire ou facultatif n’est pas valide. Une réponse « non valide » est parfois due au transfert d’une valeur de chaîne qui dépasse la longueur autorisée. Une brève description du paramètre en cause est fournie.
HTTP/401 (Non autorisé) | La requête n’est pas autorisée.
HTTP/413 (RequestEntityTooLarge)  | L’entrée SSML est supérieure à celle prise en charge.
HTTP/502 (BadGateway) | Vous rencontrez un problème de réseau ou de serveur.

Voici un exemple de réponse d’erreur :

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Modification d’une sortie vocale via SSML

L’API Synthèse vocale de Microsoft prend en charge le protocole SSML 1.0 tel que défini dans le document [Speech Synthesis Markup Language (SSML) Version 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/) du consortium W3C. Cette section présente des exemples de modification de certaines caractéristiques des sorties vocales générées (par exemple, le débit, la prononciation, etc.) à l’aide de balises SSML.

1. Ajout d’une pause

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
   ```

2. Modification du débit

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

3. Prononcer

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
   ```

4. Modifier le volume

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

5. Modification de la tonalité

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
   ```

6. Modification du contour prosodique

   ```
   <speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
   ```

> [!NOTE]
> Notez que les données audio doivent être des fichiers .wav 8k ou 16k au format suivant : **code CRC** (CRC-32) : 4 octets (DWORD) avec plage valide 0x00000000 ~ 0xFFFFFFFF ; **Indicateur de format audio** : 4 octets (DWORD) avec plage valide 0x00000000 ~ 0xFFFFFFFF ; **Nombre d’échantillons** : 4 octets (DWORD) avec plage valide 0x00000000 ~ 0x7FFFFFFF ; **Taille du corps binaire** : 4 octets (DWORD) avec plage valide 0x00000000 ~ 0x7FFFFFFF ; **Corps binaire** : n octets.

## <a name="SampleApp"></a>Exemple d’application

Pour obtenir des informations complémentaires sur la mise en œuvre, consultez la section [Exemple d’application de synthèse vocale Visual C#.NET](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Polices de voix et paramètres régionaux pris en charge

Le tableau suivant répertorie quelques-uns des paramètres régionaux pris en charge et des polices de voix correspondantes.

Paramètre régional | Sexe | Mappage du nom du service
---------|--------|------------
ar-EG* | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (ar-EG, Hoda) »
ar-SA | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (ar-SA, Naayf) »
bg-BG | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (bg-BG, Ivan) »
ca-ES | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (ca-ES, HerenaRUS) »
cs-CZ | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (cs-CZ, Jakub) »
da-DK | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (da-DK, HelleRUS) »
de-AT | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-AT, Michael) »
de-CH | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-CH, Karsten) »
de-DE | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-DE, Hedda) »
de-DE | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-DE, HeddaRUS) »
de-DE | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (de-DE, Stefan, Apollo) »
el-GR | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (el-GR, Stefanos) »
en-AU | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-AU, Catherine) »
en-AU | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-AU, HayleyRUS) »
en-CA | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-CA, Linda) »
en-CA | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-CA, HeatherRUS) »
en-GB | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-GB, Susan, Apollo) »
en-GB | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-GB, HazelRUS) »
en-GB | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-GB, George, Apollo) »
en-IE | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IE, Sean) »
en-IN | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IN, Heera, Apollo) »
en-IN | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IN, PriyaRUS) »
en-IN | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-IN, Ravi, Apollo) »
fr-FR | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-US, ZiraRUS) »
fr-FR | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-US, JessaRUS) »
fr-FR | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (en-US, BenjaminRUS) »
es-ES | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-ES, Laura, Apollo) »
es-ES | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-ES, HelenaRUS) »
es-ES | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-ES, Pablo, Apollo) »
es-MX | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-MX, HildaRUS) »
es-MX | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (es-MX, Raul, Apollo) »
fi-FI | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (fi-FI, HeidiRUS) »
fr-CA | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-CA, Caroline) »
fr-CA | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-CA, HarmonieRUS) »
fr-CH | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-CH, Guillaume) »
fr-FR | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-FR, Julie, Apollo) »
fr-FR | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-FR, HortenseRUS) »
fr-FR | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (fr-FR, Paul, Apollo) »
he-IL| Homme| « Voix de synthèse vocale pour le service Speech Microsoft Server (he-IL, Asaf) »
hi-IN | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (hi-IN, Kalpana, Apollo) »
hi-IN | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (hi-IN, Kalpana) »
hi-IN | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (hi-IN, Hemant) »
hr-HR | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (hr-HR, Matej) »
hu-HU | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (hu-HU, Szabolcs) »
id-ID | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (id-ID, Andika) »
it-IT | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (it-IT, Cosimo, Apollo) »
it-IT | Femme | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (it-IT, LuciaRUS) »
ja-JP | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (ja-JP, Ayumi, Apollo) »
ja-JP | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (ja-JP, Ichiro, Apollo) »
ja-JP | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (ja-JP, HarukaRUS) »
ko-KR | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (ko-KR, HeamiRUS) »
ms-MY | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (ms-MY, Rizwan) »
nb-NO | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (nb-NO, HuldaRUS) »
nl-NL | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (nl-NL, HannaRUS) »
pl-PL | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (pl-PL, PaulinaRUS) »
pt-br | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (pt-BR, HeloisaRUS) »
pt-br | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (pt-BR, Daniel, Apollo) »
pt-PT | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (pt-PT, HeliaRUS) »
ro-RO | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (ro-RO, Andrei) »
ru-RU | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (ru-RU, Irina, Apollo) »
ru-RU | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (ru-RU, Pavel, Apollo) »
ru-RU | Femme | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (ru-RU, Pavel, Apollo) »
sk-SK | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (sk-SK, Filip) »
sl-SI | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (sl-SI, Lado) »
sv-SE | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (sv-SE, HedvigRUS) »
ta-IN | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (ta-IN, Valluvar) »
th-TH | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (th-TH, Pattara) »
tr-TR | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (tr-TR, SedaRUS) »
vi-VN | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (vi-VN, An) »
zh-CN | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-CN, HuihuiRUS) »
zh-CN | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-CN, Yaoyao, Apollo) »
zh-CN | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-CN, Kangkang, Apollo) »
zh-HK | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-HK, Tracy, Apollo) »
zh-HK | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-HK, TracyRUS) »
zh-HK | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-HK, Danny, Apollo) »
zh-TW | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-TW, Yating, Apollo) »
zh-TW | Femme | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-TW, HanHanRUS) »
zh-TW | Homme | « Voix de synthèse vocale pour le service Speech Microsoft Server (zh-TW, Zhiwei, Apollo) »

 *ar-EG prend en charge l’arabe standard moderne (MSA).

> [!NOTE]
> Notez que les noms de service précédents **Voix de synthèse vocale pour le service Speech Microsoft Server (cs-CZ, Vit)** et **Voix de synthèse vocale pour le service Speech Microsoft Server (en-IE, Shaun)** seront supprimés après le 31 mars 2018 afin d’optimiser les fonctionnalités de l’API Reconnaissance vocale Bing. Veuillez mettre à jour votre code avec les nouveaux noms.

## <a name="TrouNSupport"></a>Dépannage et prise en charge

Veuillez publier toutes vos questions et tous vos problèmes sur le forum MSDN dédié au [service Speech Bing](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService). Insérez autant d’informations que possible, notamment :

* Un exemple de la chaîne de requête complète.
* Le cas échéant, la sortie complète d’une requête ayant échoué avec les identifiants des journaux.
* Le pourcentage de requêtes en échec.
