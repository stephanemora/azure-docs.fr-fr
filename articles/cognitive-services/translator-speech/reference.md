---
title: Informations de référence de l’API de traduction de conversation Translator Speech
titleSuffix: Azure Cognitive Services
description: Documentation de référence pour l’API de traduction de conversation Translator Speech.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: reference
ms.date: 05/18/2018
ms.author: v-jansko
ms.openlocfilehash: e1796b2cb3efee6ff610f9dade7a10b2c2637bba
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466181"
---
# <a name="translator-speech-api"></a>API de traduction de conversation Translator Speech

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Ce service offre une API de diffusion en continu pour la transcription de parole conversationnelle dans une langue en texte dans une autre langue. L’API intègre également des fonctionnalités de synthèse vocale pour prononcer le texte traduit. L’API de traduction de conversation Translator Speech permet, par exemple, des scénarios de traduction en temps réel de conversations, comme dans Skype Translator.

Avec l’API de traduction de conversation Translator Speech, les applications clientes diffusent en continu l’audio vocal vers le service, et reçoivent un flux de résultats textuels, notamment le texte reconnu dans la langue source et sa traduction dans la langue cible. Les résultats textuels sont générés par l’application au flux audio entrant d’une reconnaissance vocale automatique qui s’appuie sur des réseaux neuronaux profonds. La sortie de reconnaissance vocale brute est améliorée par une nouvelle technique nommée TrueText afin de mieux refléter l’intention de l’utilisateur. Par exemple, TrueText élimine les disfluences (euh, toux) et restaure une ponctuation ainsi qu’une casse appropriées. La possibilité de masquer ou d’exclure des vulgarités est également incluse. Les moteurs de reconnaissance et de traduction sont spécifiquement entraînés pour traiter la parole conversationnelle. Le service de traduction vocale détecte les silences pour déterminer la fin des énoncés. Après une pause dans l’activité vocale, le service renvoie un résultat final pour l’énoncé complet. Le service peut également renvoyer des résultats partiels, qui donnent des reconnaissances et traductions intermédiaires d’un énoncé en cours. Pour les résultats finaux, le service permet de convertir par synthèse vocale le texte parlé dans les langues cibles. L’audio de synthèse vocale est créé dans le format spécifié par le client. Les formats disponibles sont WAV et MP3.

L’API de traduction de conversation Translator Speech tire parti du protocole WebSocket pour fournir un canal de communication en duplex intégral entre le client et le serveur. Pour utiliser le service, une application requiert les étapes suivantes :

## <a name="1-getting-started"></a>1. Prise en main
Pour accéder à l’API de traduction de texte Translator Text, vous devez [vous inscrire à Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Authentification

Utilisez la clé d’abonnement pour vous authentifier. L’API de traduction de conversation Translator Speech prend en charge deux modes d’authentification :

* **En utilisant un jeton d’accès :** Dans votre application, obtenez un jeton d’accès à partir du service de jetons. Utilisez votre clé d’abonnement à l’API de traduction de conversation Translator Speech pour obtenir un jeton d’accès auprès du service d’authentification d’Azure Cognitive Services. Chaque jeton d’accès est valide pendant 10 minutes. Obtenez un nouveau jeton d’accès toutes les 10 minutes, et utilisez-le pour des requêtes répétées pendant 10 minutes.

* **En utilisant une clé d’abonnement directement :** Dans votre application, passez votre clé d’abonnement en tant que valeur dans l’en-tête `Ocp-Apim-Subscription-Key`.

Traitez votre clé d’abonnement et le jeton d’accès comme des secrets qui doivent être masqués.

## <a name="3-query-languages"></a>3. Interroger la ressource Langues
**Interrogez la ressource Langues pour connaître l’ensemble actuel des langues prises en charge.** La [ressource Langues](languages-reference.md) expose l’ensemble des langues et des voix disponibles pour la reconnaissance vocale, la traduction de texte et la synthèse vocale. Chaque langue ou voix est dotée d’un identificateur que l’API de traduction de conversation Translator Speech utilise pour les identifier.

## <a name="4-stream-audio"></a>4. Diffuser l’audio
**Ouvrez une connexion et commencez à diffuser l’audio vers le service.** L’URL du service est `wss://dev.microsofttranslator.com/speech/translate`. Les paramètres et les formats audio attendus par le service sont décrits ci-dessous, dans l’opération `/speech/translate`. L’un des paramètres est utilisé pour passer le jeton d’accès de l’étape 2 ci-dessus.

## <a name="5-process-the-results"></a>5. Traiter les résultats
**Traitez les résultats diffusés par le service.** Les formats des résultats partiels, des résultats finaux et des segments audio de synthèse vocale sont décrits dans la documentation de l’opération `/speech/translate` ci-dessous.

Des exemples de code illustrant l’utilisation de l’API de traduction de conversation Translator Speech sont disponibles sur le [site GitHub de Microsoft Translator](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Remarques relatives à l’implémentation

GET /speech/translate Établit une session pour la traduction vocale

### <a name="connecting"></a>Connexion
Avant de vous connecter au service, examinez la liste des paramètres, plus loin dans cette section. Voici un exemple de demande :

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

La demande spécifie que l’anglais parlé sera diffusé vers le service et traduit en italien. Chaque résultat final de reconnaissance génère une réponse audio en synthèse vocale avec la voix féminine nommée Elsa. Notez que la demande inclut des informations d’identification dans l’en-tête `Ocp-Apim-Subscription-Key header`. La demande suit également une meilleure pratique en définissant un identificateur global unique dans l’en-tête `X-ClientTraceId`. Une application cliente doit journaliser l’ID de trace afin de pouvoir l’utiliser pour résoudre des problèmes quand ils se produisent.

### <a name="sending-audio"></a>Envoi de l’audio
Une fois la connexion est établie, le client commence à diffuser l’audio vers le service. Le client envoie l’audio dans des blocs. Chaque segment est transmis à l’aide d’un message WebSocket de type Binary.

L’entrée audio est au format de fichier audio Waveform (WAVE, ou plus communément appelé WAV en raison de son extension de nom de fichier). L’application cliente doit diffuser sur un canal unique un signal audio MIC 16 bits signé et échantillonné à 16 kHz. Le premier jeu d’octets diffusé par le client inclut l’en-tête WAV. Voici en-tête de 44 octets pour un flux MIC 16 bits signé échantillonné à 16 kHz sur canal unique :

|Offset|Valeur|
|:---|:---|
|0 - 3|"RIFF"|
|4 - 7|0|
|8 - 11|"WAVE"|
|12 - 15|"fmt"|
|16 - 19|16|
|20 - 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 - 33|2|
|34 - 35|16|
|36 - 39|"data"|
|40 - 43|0|

Notez que la taille totale du fichier (octets 4-7) et la taille des « data » (octets 40-43) sont définies sur zéro. Cela convient pour le scénario de diffusion où la taille totale n’est pas nécessairement connue d’avance.

Après l’envoi de l’en-tête WAV (RIFF), le client envoie des blocs des données audio. Le client diffuse en général des blocs de taille fixe représentant une durée fixe (par exemple, 100 ms d’audio à la fois).

### <a name="signal-the-end-of-the-utterance"></a>Signaler la fin de l’énoncé
L’API de traduction de conversation Translator Speech retourne la transcription et la traduction du flux audio au fur et à mesure que vous envoyez les données audio. La transcription finale, la traduction finale et les données audio traduites ne vous sont retournées qu’à la fin de l’énoncé. Dans certains cas, vous pouvez être amené à forcer la fin de l’énoncé. Envoyez 2,5 secondes de silence pour forcer la fin de l’énoncé. 

### <a name="final-result"></a>Résultat final
Un résultat final de reconnaissance vocale est généré à la fin d’un énoncé. Le service transmet au client un résultat à l’aide d’un message WebSocket de type Text. Le contenu du message est la sérialisation JSON d’un objet avec les propriétés suivantes :

* `type`: constante de chaîne pour identifier le type de résultat. La valeur est finale pour les résultat finaux.
* `id`: identificateur de chaîne affecté au résultat de la reconnaissance.
* `recognition`: texte reconnu dans la langue source. Le texte peut être une chaîne vide en cas d’échec de la reconnaissance.
* `translation`: texte reconnu traduit dans la langue cible.
* `audioTimeOffset`: décalage temporel du début de la reconnaissance, exprimé en tics (1 tic = 100 nanosecondes). Le décalage est relatif au début de la diffusion.
* `audioTimeSize`: durée de la reconnaissance exprimée en tics (100 nanosecondes).
* `audioStreamPosition`: décalage d’octet du début de la reconnaissance. Le décalage est relatif au début de la diffusion.
* `audioSizeBytes`: taille en octets de la reconnaissance.

Notez que, par défaut, le positionnement de la reconnaissance dans le flux audio n’est pas inclus dans les résultats. La fonctionnalité `TimingInfo` doit être sélectionnée par le client (voir le paramètre `features`).

Voici un exemple de résultat final :

```
{
  type: "final"
  id: "23",
  recognition: "what was said", 
  translation: "translation of what was said",
  audioStreamPosition: 319680,
  audioSizeBytes: 35840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 21900000
}
```

### <a name="partial-result"></a>Résultat partiel
Par défaut, les résultats partiels ou intermédiaires de la reconnaissance vocale ne sont pas diffusés vers le client. Le client peut utiliser le paramètre de requête de fonctionnalités pour les demander.

Le service transmet au client un résultat partiel à l’aide d’un message WebSocket de type Text. Le contenu du message est la sérialisation JSON d’un objet avec les propriétés suivantes :

* `type`: constante de chaîne pour identifier le type de résultat. La valeur est partielle pour les résultats partiels.
* `id`: identificateur de chaîne affecté au résultat de la reconnaissance.
* `recognition`: texte reconnu dans la langue source.
* `translation`: texte reconnu traduit dans la langue cible.
* `audioTimeOffset`: décalage temporel du début de la reconnaissance, exprimé en tics (1 tic = 100 nanosecondes). Le décalage est relatif au début de la diffusion.
* `audioTimeSize`: durée de la reconnaissance exprimée en tics (100 nanosecondes).
* `audioStreamPosition`: décalage d’octet du début de la reconnaissance. Le décalage est relatif au début de la diffusion.
* `audioSizeBytes`: taille en octets de la reconnaissance.

Notez que, par défaut, le positionnement de la reconnaissance dans le flux audio n’est pas inclus dans les résultats. La fonctionnalité TimingInfo doit être sélectionnée par le client (voir le paramétrage des fonctionnalités).

Voici un exemple de résultat final :

```
{
  type: "partial"
  id: "23.2",
  recognition: "what was", 
  translation: "translation of what was",
  audioStreamPosition: 319680,
  audioSizeBytes: 25840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 11900000
}
```

### <a name="text-to-speech"></a>Synthèse vocale
Lorsque la fonctionnalité de synthèse vocale est activée (voir le paramètre `features` ci-dessous), un résultat final est suivi de l’audio du texte traduit prononcé. Les données audio sont découpées en blocs et envoyées par le service au client sous la forme d’une séquence de messages WebSocket de type Binary. Un client peut détecter la fin du flux en vérifiant le bit FIN de chaque message. Le bit FIN du dernier message de type Binary sera défini sur un pour indiquer la fin du flux. Le format du flux de données dépend de la valeur du paramètre `format`.

### <a name="closing-the-connection"></a>Fermeture de la connexion
Quand une application cliente a fini de diffuser l’audio et a reçu le dernier résultat final, elle doit fermer la connexion en initiant la séquence de fermeture WebSocket. Il existe des conditions qui amènent le serveur à mettre fin à la connexion. Le client peut recevoir les codes de fermeture WebSocket suivants :

* `1003 - Invalid Message Type`: le serveur met fin à la connexion parce qu’il ne peut pas accepter le type de données reçu. Cela se produit généralement quand l’audio entrant ne commence pas par un en-tête approprié.
* `1000 - Normal closure`: la connexion a été fermée après exécution de la requête. Le serveur ferme la connexion dans les cas suivants : aucune donnée audio n’a été reçue du client pendant une période prolongée ; un silence a été diffusé pendant une période prolongée ; une session a atteint la durée maximale autorisée (environ 90 minutes).
* `1001 - Endpoint Unavailable`: indique que le serveur va devenir indisponible. L’application cliente peut tenter de se reconnecter, mais le nombre de tentatives est limité.
* `1011 - Internal Server Error`: le serveur va fermer la connexion en raison d’une erreur sur le serveur.

### <a name="parameters"></a>parameters

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:---|:---|:---|:---|:---|
|api-version|1.0|Version de l’API demandée par le client. Valeurs autorisées : `1.0`.|query   |chaîne|
|from|(empty)   |Spécifie la langue du flux vocal entrant. La valeur est l’un des identificateurs de langue de l’étendue `speech` dans la réponse de l’API Langues.|query|chaîne|
|to|(empty)|Spécifie la langue dans laquelle traduire le texte transcrit. La valeur est l’un des identificateurs de langue de l’étendue `text` dans la réponse de l’API Langues.|query|chaîne|
|features|(empty)   |Ensemble de fonctionnalités séparées par des virgules sélectionnées par le client. Les fonctionnalités disponibles sont les suivantes :<ul><li>`TextToSpeech` : spécifie que le service doit renvoyer l’audio traduit de la dernière phrase traduite.</li><li>`Partial` : spécifie que le service doit renvoyer les résultats intermédiaires de la reconnaissance pendant la diffusion de l’audio vers le service.</li><li>`TimingInfo` : spécifie que le service doit renvoyer les informations de durée associées à chaque reconnaissance.</li></ul>Par exemple, un client doit spécifier `features=partial,texttospeech` pour recevoir les résultats partiels et la synthèse vocale, mais aucune information de durée. Notez que les résultats finaux sont toujours diffusés vers le client.|query|chaîne|
|voice|(empty)|Identifie la voix à utiliser pour la synthèse vocale du texte traduit. La valeur est l’un des identificateurs de voix de l’étendue de synthèse vocale dans la réponse de l’API Langues. Si aucune voix n’est spécifiée, le système choisit automatiquement quand la fonctionnalité de synthèse vocale est activée.|query|chaîne|
|format|(empty)|Spécifie le format du flux audio de synthèse vocale renvoyé par le service. Options disponibles :<ul><li>`audio/wav`: flux audio Waveform. Le client doit utiliser l’en-tête WAV pour interpréter correctement le format audio. L’audio WAV pour la synthèse vocale est MIC sur canal unique 16 bits, avec un taux d’échantillonnage de 24 ou 16 kHz.</li><li>`audio/mp3`: flux audio MP3.</li></ul>La valeur par défaut est `audio/wav`.|query|chaîne|
|ProfanityAction    |(empty)    |Spécifie comment le service doit traiter les vulgarités reconnues dans l’entrée vocale. Les actions valides sont les suivantes :<ul><li>`NoAction`: les vulgarités restent inchangées.</li><li>`Marked`: les vulgarités sont remplacées par un marqueur. Voir le paramètre `ProfanityMarker`.</li><li>`Deleted`: les vulgarités sont supprimées. Par exemple, si le mot `"jackass"` est traité comme une vulgarité, l’expression `"He is a jackass."` devient `"He is a .".`.</li></ul>La valeur par défaut est Marked.|query|chaîne|
|ProfanityMarker|(empty)    |Spécifie comment les vulgarités détectées sont traitées quand `ProfanityAction` a la valeur `Marked`. Les options valides sont les suivantes :<ul><li>`Asterisk`: les vulgarités sont remplacées par la chaîne `***`. Par exemple, si le mot `"jackass"` est traité comme une vulgarité, la phrase `"He is a jackass."` devient `"He is a ***.".`.</li><li>`Tag`: toute vulgarité est entourée de balises XML indiquant sa présence. Par exemple, si le mot `"jackass"` est traité comme une vulgarité, la phrase `"He is a jackass."` devient `"He is a <profanity>jackass</profanity>."`.</li></ul>Par défaut, il s’agit de `Asterisk`.|query|chaîne|
|Authorization|(empty)  |Spécifie la valeur du jeton du porteur du client. Utilisez le préfixe `Bearer` suivi par la valeur `access_token` renvoyée par le service de jetons d’authentification.|en-tête   |chaîne|
|Ocp-Apim-Subscription-Key|(empty)|Obligatoire si l’en-tête `Authorization` n’est pas spécifié.|en-tête|chaîne|
|access_token|(empty)   |Autre manière de passer un jeton d’accès OAuth valide. Le jeton du porteur est généralement fourni avec un en-tête `Authorization`. Certaines bibliothèques WebSocket n’autorisent pas le code client à définir des en-têtes. Dans ce cas, le client peut utiliser le paramètre de requête `access_token` pour passer un jeton valide. Lorsque vous utilisez un jeton d’accès pour l’authentification, si un en-tête `Authorization` n’est pas défini, `access_token` doit être défini. Si un en-tête et un paramètre de requête sont définis, le paramètre de requête est ignoré. Les clients ne doivent utiliser qu’une seule méthode pour passer le jeton.|query|chaîne|
|subscription-key|(empty)   |Autre manière de passer une clé d’abonnement. Certaines bibliothèques WebSocket n’autorisent pas le code client à définir des en-têtes. Dans ce cas, le client peut utiliser le paramètre de requête `subscription-key` pour passer une clé d’abonnement valide. Lorsque vous utilisez une clé d’abonnement pour l’authentification, si un en-tête `Ocp-Apim-Subscription-Key` n’est pas défini, la clé d’abonnement doit être définie. Si un en-tête et un paramètre de requête sont définis, le paramètre de requête est ignoré. Les clients ne doivent utiliser qu’une seule méthode pour passer le `subscription key`.|query|chaîne|
|X-ClientTraceId    |(empty)    |GUID généré par le client utilisé pour suivre une requête. À des fins de résolution appropriée des problèmes, les clients doivent fournir une nouvelle valeur avec chaque demande, et la journaliser.<br/>Au lieu d’utiliser un en-tête, cette valeur peut être passée avec le paramètre de requête `X-ClientTraceId`. Si un en-tête et un paramètre de requête sont définis, le paramètre de requête est ignoré.|en-tête|chaîne|
|X-CorrelationId|(empty)    |Identificateur généré par le client utilisé pour mettre en corrélation plusieurs canaux dans une conversation. Plusieurs sessions de traduction vocale peuvent être créées pour permettre les conversations entre utilisateurs. Dans un tel scénario, toutes les sessions de traduction vocale utilisent le même ID de corrélation pour relier les canaux. Cela facilite le suivi et les diagnostics. L’identificateur doit être conforme à ceci : `^[a-zA-Z0-9-_.]{1,64}$`<br/>Au lieu d’utiliser un en-tête, cette valeur peut être passée avec le paramètre de requête `X-CorrelationId`. Si un en-tête et un paramètre de requête sont définis, le paramètre de requête est ignoré.|en-tête|chaîne|
|X-ClientVersion|(empty)    |Identifie la version de l’application cliente. Exemple : « 2.1.0.123 ».<br/>Au lieu d’utiliser un en-tête, cette valeur peut être passée avec le paramètre de requête `X-ClientVersion`. Si un en-tête et un paramètre de requête sont définis, le paramètre de requête est ignoré.|en-tête|chaîne|
|X-OsPlatform|(empty)   |Identifie le nom et la version du système d’exploitation sur lequel s’exécute l’application cliente. Exemples : « Android 5.0 », « iOs 8.1.3 », « Windows 8.1 ».<br/>Au lieu d’utiliser un en-tête, cette valeur peut être passée avec le paramètre de requête `X-OsPlatform`. Si un en-tête et un paramètre de requête sont définis, le paramètre de requête est ignoré.|en-tête|chaîne|

### <a name="response-messages"></a>Messages de réponse

|Code d’état HTTP|Motif|Modèle de réponse|headers|
|:--|:--|:--|:--|
|101    |Mise à niveau de WebSocket.|Exemple de valeur de modèle <br/> Object {}|X-RequestId<br/>Valeur identifiant la demande à des fins de dépannage.<br/>chaîne|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée pour vous assurer qu’ils sont valides. L’objet de la réponse inclut une description plus détaillée de l’erreur.|||
|401    |Non autorisé. Vérifiez que les informations d’identification sont définies, qu’elles sont valides, et que votre abonnement Azure Data Market est en règle avec un solde disponible.|||
|500    |Une erreur est survenue. Si l’erreur persiste, veuillez la signaler avec l’identificateur de trace client (X-ClientTraceId) ou l’identificateur (ID requête X) de demande.|||
|503    |Serveur temporairement indisponible. Relancez la requête. Si l’erreur persiste, veuillez la signaler avec l’identificateur de trace client (X-ClientTraceId) ou l’identificateur (ID requête X) de demande.|||

    


    





    
    




    




    




    

            




        









