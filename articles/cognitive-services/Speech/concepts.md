---
title: Concepts de la reconnaissance vocale Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Concepts de base utilisés dans le service Microsoft Speech.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: b5e6853653bab96cd11196ba03fbeadbdc6b337f
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816207"
---
# <a name="basic-concepts"></a>Concepts de base

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Cette page décrit certains concepts de base propres au service de reconnaissance vocale Microsoft. Nous vous conseillons de lire cette page avant d’utiliser l’API Reconnaissance vocale Microsoft dans votre application.

## <a name="understanding-speech-recognition"></a>Présentation de la reconnaissance vocale

Si pour la première fois vous créez une application à fonctionnalités vocales ou ajoutez des fonctionnalités vocales à une application existante, cette section vous aidera à faire vos premiers pas. Si vous avez déjà de l’expérience avec les applications à fonctionnalités vocales, vous pouvez choisir de parcourir rapidement cette section, voire de l’ignorer entièrement si vous êtes un expert des fonctionnalités vocales et souhaitez accéder directement aux détails du protocole.

### <a name="audio-streams"></a>Flux audio

Le *flux audio* figure au premier rang des concepts de base des fonctionnalités vocales. Contrairement à un appui sur une touche, qui se produit à un instant unique et qui contient un seul élément d’information, une demande parlée s’étend sur des centaines de millisecondes et contient de nombreux kilo-octets d’informations. La durée des énoncés parlés présente certaines difficultés aux développeurs qui souhaitent fournir une expérience vocale rationalisée et élégante pour leur application. Aujourd'hui, les ordinateurs et les algorithmes effectuent la transcription de la parole dans approximativement la moitié de la durée de l’énoncé, si bien qu’un énoncé de 2 secondes peut être transcrit en environ 1 seconde, mais une application qui présente 1 seconde de délai de traitement de l’utilisateur n’est ni rationalisée, ni élégante.

Heureusement, il existe des façons de « masquer » la durée de transcription en effectuant la transcription d’une partie de l’énoncé pendant que l’utilisateur énonce une autre partie. Par exemple, en divisant un énoncé de 1 seconde en 10 segments de 100 millisecondes et en effectuant la transcription de chaque segment, l’un après l’autre. Plus de 450 des 500 millisecondes au total à transcrire peuvent être « masquées », afin que l’utilisateur n’ait pas conscience que la transcription est en cours de réalisation pendant qu’il parle. Dans le cadre de cet exemple, n’oubliez pas que le service effectue la transcription sur les 100 millisecondes précédentes de données audio pendant que l’utilisateur énonce les 100 millisecondes suivantes, si bien que lorsque l’utilisateur arrête de parler, le service n’a plus qu’à transcrire environ 100 millisecondes de données audio pour produire un résultat.

Pour réaliser une telle expérience utilisateur, les informations audio énoncées sont collectées dans des segments et transcrites au fur et à mesure que l’utilisateur parle. Ces segments audio sont extraits collectivement du *flux audio* et le processus d’envoi de ces segments audio au service est appelé *streaming audio.* Le streaming audio est une partie importante de toute application à fonctionnalités vocales. Le réglage de la taille des segments et l’optimisation de l’implémentation de streaming sont quelques-unes des manières les plus efficaces d’améliorer l’expérience des utilisateurs de votre application.

### <a name="microphones"></a>Microphones

Nous traitons les énoncés à l’aide de nos oreilles, mais le matériel inanimé utilise des microphones. Pour activer des fonctionnalités vocales dans une application quelconque, vous devez intégrer le microphone qui fournit le flux audio pour votre application.

Les API relatives à votre microphone doivent vous permettre de commencer et d’arrêter de recevoir des octets de données audio du microphone. Vous devez décider quelles actions utilisateur serviront de déclencheur au microphone pour qu’il commence à écouter les paroles. Vous pouvez choisir d’utiliser un appui sur une touche comme déclencheur d’écoute ou d’avoir un détecteur de *mot clé* ou de *mot déclencheur* constamment à l’écoute du microphone, et d’utiliser la sortie de ce module pour déclencher l’envoi des données audio au service Microsoft Speech.

### <a name="end-of-speech"></a>Fin de l’énoncé

Détecter *quand* un interlocuteur a *arrêté* de parler paraît relativement simple à des hommes, mais représente un problème plutôt difficile en dehors des conditions d’un laboratoire. Il ne suffit pas de rechercher simplement un silence parfait après un énoncé, car il y a souvent beaucoup de bruit ambiant qui complique les choses. Le service Microsoft Speech est très efficace pour détecter rapidement quand un utilisateur a arrêté de parler, et il peut en informer votre application, mais cette configuration signifie que votre application est la dernière à savoir quand l’utilisateur arrête de parler. Cette situation diffère complètement d’autres formes d’entrée de données où votre application est la *première* à savoir quand l’entrée utilisateur démarre *et* prend fin.

### <a name="asynchronous-service-responses"></a>Réponses asynchrones du service

Le fait que votre application a besoin d’être informée du moment où l’entrée utilisateur est terminée n’impose aucune pénalité de performances ni difficulté de programmation à votre application. En revanche, cela nécessite que vous réfléchissiez aux demandes vocales autrement qu’aux modèles de demande d’entrée/réponse qui vous sont familiers. Étant donné que votre application ne sait pas quand l’utilisateur arrête de parler, elle doit continuer à diffuser des données audio au service tout en attendant simultanément et de façon asynchrone une réponse de ce service. Ce modèle diffère des autres protocoles web de demande/réponse tels que HTTP. Dans ces protocoles, vous devez effectuer une demande avant de recevoir une réponse. Dans le protocole du service Microsoft Speech, vous recevez des réponses *pendant que vous diffusez encore des données audio pour la demande*.

> [!NOTE]
> Cette fonctionnalité n’est pas prise en charge lorsque vous utilisez l’API REST HTTP Speech.

### <a name="turns"></a>Tours

Speech est un vecteur d’informations. Lorsque vous parlez, vous tentez de transmettre des informations en votre possession à une personne qui est à l’écoute de ces informations. Lors d’une transmission d’informations, vous parlez et écoutez habituellement tour à tour. De même, votre application à fonctionnalités vocales interagit avec les utilisateurs en écoutant et en répondant alternativement, bien que votre application écoute généralement la plupart du temps. L’entrée vocale de l’utilisateur et la réponse du service à cette entrée constituent un *tour*. Un *tour* commence lorsque l’utilisateur parle et se termine lorsque votre application a terminé le traitement de la réponse du service vocal.

### <a name="telemetry"></a>Télémétrie

La création d’un appareil ou d’une application à fonctionnalités vocales peut être difficile, même pour des développeurs expérimentés. Les protocoles basés sur des flux paraissent souvent déroutants au premier abord et des détails importants, tels que la détection des silences, peuvent s’avérer entièrement nouveaux. Avec autant de messages à envoyer et recevoir correctement pour terminer une paire demande/réponse individuelle, il est *très* important de collecter des données complètes et exactes sur ces messages. Le protocole du service Microsoft Speech assure la collecte de ces données. Vous devez effectuer tous les efforts nécessaires pour fournir les données requises aussi précisément que possible. Il est de votre propre intérêt de fournir des données complètes et exactes : si avez besoin de l’aide de l’équipe du service Microsoft Speech pour résoudre des problèmes liés à l’implémentation de votre client, la qualité des données de télémétrie recueillies sera essentielle pour l’analyse du problème.

> [!NOTE]
> Cette fonctionnalité n’est pas prise en charge lorsque vous utilisez l’API REST de reconnaissance vocale.

### <a name="speech-application-states"></a>États vocaux de l’application

Les étapes que vous suivez pour activer l’entrée vocale dans votre application diffèrent quelque peu des étapes à suivre pour d’autres formes d’entrée, telles que les clics de souris ou les appuis tactiles. Vous devez effectuer le suivi des périodes où votre application est à l’écoute du microphone et envoie des données au service vocal, où elle attend une réponse du service et où elle est dans un état inactif. La relation entre ces états est indiquée dans le diagramme ci-dessous.

![Diagramme des états vocaux de l’application](Images/speech-application-state-diagram.png)

Étant donné que le service Microsoft Speech participe à certains de ces états, le protocole du service définit des messages qui aident votre application à passer d’un état à l’autre. Votre application doit interpréter et traiter ces messages de protocole pour effectuer le suivi et la gestion de ses états vocaux.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>Utilisation du service de reconnaissance vocale à partir de vos applications

Le service de reconnaissance vocale Microsoft fournit aux développeurs deux façons d’ajouter des fonctionnalités vocales à leurs applications.

- [API REST](GetStarted/GetStartedREST.md) : Les développeurs peuvent utiliser des appels HTTP au service à partir de leurs applications pour la reconnaissance vocale.
- [Bibliothèques clientes](GetStarted/GetStartedClientLibraries.md) : Pour bénéficier de fonctionnalités avancées, les développeurs peuvent télécharger les bibliothèques clientes Microsoft Speech et les lier dans leurs applications.  Les bibliothèques clientes sont disponibles sur différentes plateformes (Windows, Android, iOS) dans différents langages (C#, Java, JavaScript, ObjectiveC).

| Cas d'utilisation | [API REST](GetStarted/GetStartedREST.md) | [Bibliothèques clientes](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Convertir un énoncé bref, tel que des commandes (durée < 15 s), sans résultats intermédiaires | Oui | Oui |
| Convertir un long énoncé (> 15 s) | Non  | Oui |
| Diffuser en continu un énoncé avec les résultats intermédiaires souhaités | Non  | Oui |
| Comprendre le texte converti à partir d’un énoncé à l’aide de LUIS | Non  | Oui |

 Si votre langage ou plateforme n’a pas encore de kit SDK, vous pouvez créer votre propre implémentation basée sur la [documentation du protocole](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Modes de reconnaissance

Il existe trois modes de reconnaissance : `interactive`, `conversation` et `dictation`. Le mode de reconnaissance ajuste la reconnaissance vocale en fonction de la façon dont les utilisateurs sont susceptibles de parler. Choisissez le mode de reconnaissance approprié pour votre application.

> [!NOTE]
> Selon le protocole utilisé, REST ou WebSocket, les comportements des modes de reconnaissance peuvent être différents. Par exemple, l’API REST ne prend pas en charge la reconnaissance continue, même en mode de conversation ou de dictée.
> [!NOTE]
> Ces modes sont applicables lorsque vous utilisez directement le protocole REST ou WebSocket. Les [bibliothèques clientes](GetStarted/GetStartedClientLibraries.md) utilisent des paramètres différents pour spécifier le mode de reconnaissance. Pour plus d’informations, consultez la bibliothèque cliente de votre choix.

Le service Microsoft Speech retourne un seul résultat d’expression de reconnaissance pour tous les modes de reconnaissance. Il existe une limite de 15 secondes pour tout énoncé individuel.

### <a name="interactive-mode"></a>Mode interactif

En mode `interactive`, un utilisateur effectue de courtes demandes et attend de l’application qu’elle effectue une action en réponse.

Les caractéristiques suivantes sont typiques des applications en mode interactif :

- Les utilisateurs savent qu’ils parlent à une machine et non à une autre personne.
- Les utilisateurs de l’application savent à l’avance ce qu’ils veulent dire en fonction de ce qu’ils veulent que l’application fasse.
- Les énoncés durent généralement 2 ou 3 secondes.

### <a name="conversation-mode"></a>Mode de conversation

En mode de `conversation`, les utilisateurs sont engagés dans une conversation entre humains.

Les caractéristiques suivantes sont typiques des applications en mode de conversation :

- Les utilisateurs savent qu’ils s’adressent à une autre personne.
- La reconnaissance vocale améliore les conversations humaines en permettant à l’un des participants ou aux deux de visualiser le texte énoncé.
- Les utilisateurs ne planifient pas toujours ce qu’ils veulent dire.
- Les utilisateurs emploient fréquemment des expressions familières, voire argotiques.

### <a name="dictation-mode"></a>Mode de dictée

En mode de dictée (`dictation`), les utilisateurs formulent de longs énoncés pour que l’application les traite ultérieurement.

Les caractéristiques suivantes sont typiques des applications en mode de dictée :

- Les utilisateurs savent qu’ils s’adressent à une machine.
- Les utilisateurs visualisent le texte obtenu par reconnaissance vocale.
- Les utilisateurs planifient souvent ce qu’ils souhaitent dire et utilisent un langage plus soigné.
- Les utilisateurs produisent des phrases complètes qui durent 5 à 8 secondes.

> [!NOTE]
> Dans les modes de dictée et de conversation, le service Microsoft Speech ne retourne pas de résultats partiels. À la place, le service retourne les expressions stables après des seuils de silence dans le flux audio. Microsoft peut améliorer le protocole de reconnaissance vocale pour améliorer l’expérience utilisateur dans ces modes de reconnaissance en continu.

## <a name="recognition-languages"></a>Langues de reconnaissance

La *langue de reconnaissance* spécifie la langue que parle l’utilisateur de votre application. Spécifiez la *langue de reconnaissance* avec le paramètre de requête *language* dans l’URL de connexion. La valeur du paramètre de requête *language* utilise la balise de langue IETF [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag) et **doit** correspondre à l’une des langues prises en charge par l’API de reconnaissance vocale. La liste complète des langues prises en charge par le service Speech est disponible dans la page [Langues prises en charge](API-Reference-REST/supportedlanguages.md).

Le service Microsoft Speech rejette les demandes de connexion non valides en affichant une réponse `HTTP 400 Bad Request`. Une demande non valide est une demande qui :

- N’inclut pas de valeur pour le paramètre de requête *language*.
- Inclut un paramètre de requête *language* dans un format incorrect.
- Inclut un paramètre de requête *language* qui ne correspond à aucune des langues prises en charge.

Vous pouvez choisir de générer une application prenant en charge une ou toutes les langues prises en charge par le service.

### <a name="example"></a>Exemples

Dans l’exemple suivant, une application utilise le mode de reconnaissance vocale de *conversation* pour un intervenant américain.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Réponses de transcription

Les réponses de transcription retournent aux clients le texte converti à partir des données audio. Une réponse de transcription contient les champs suivants :

- `RecognitionStatus` spécifie le statut de la reconnaissance. Les valeurs possibles sont indiquées dans le tableau ci-dessous.

| Statut | Description |
| ------------- | ---------------- |
| Succès | La reconnaissance a réussi et le champ DisplayText est présent. |
| NoMatch | Des paroles ont été détectées dans le flux audio, mais aucun mot de la langue cible n’a été mis en correspondance. Consultez la section Statut de reconnaissance NoMatch pour plus d’informations.  |
| InitialSilenceTimeout | Le début du flux audio contenait uniquement un silence et le service a dépassé son délai d’attente de paroles. |
| BabbleTimeout | Le début du flux audio contenait uniquement du bruit et le service a dépassé son délai d’attente de paroles. |
| Error | Le service de reconnaissance a rencontré une erreur interne et n’a pas pu continuer. |

- `DisplayText` représente l’expression reconnue après l’application de la mise en majuscules, de la ponctuation et de la normalisation de texte inversé, et après le masquage des grossièretés à l’aide d’astérisques. Le champ DisplayText est présent *uniquement* si le champ `RecognitionStatus` a la valeur `Success`.

- `Offset` spécifie le décalage (en unités de 100 nanosecondes) avec lequel l’expression a été reconnue par rapport au début du flux audio.

- `Duration` spécifie la durée (en unités de 100 nanosecondes) de cette expression vocale.

Une réponse de transcription retourne plus d’informations si vous le souhaitez. Consultez [Format de sortie](#output-format) pour savoir comment retourner des données plus détaillées en sortie.

Le service Microsoft Speech prend en charge un processus de transcription supplémentaire qui comprend l’ajout de la mise en majuscules et de la ponctuation, le masquage des grossièretés et la normalisation du texte en formes courantes. Par exemple, si un utilisateur énonce une expression représentée par les mots « remind me to buy six iPhones » (rappelle-moi d’acheter six iPhones), le service Microsoft Speech retourne le texte transcrit « Remind me to buy 6 iPhones. » (Rappelle-moi d’acheter 6 iPhones.). Le processus qui convertit le mot « six » en chiffre « 6 » est appelé *normalisation de texte inverse* (acronyme anglais *ITN*).

### <a name="nomatch-recognition-status"></a>Statut de reconnaissance NoMatch

La réponse de transcription retourne `NoMatch` dans `RecognitionStatus` lorsque le service Microsoft Speech détecte des paroles dans le flux audio mais ne parvient pas à les mettre en correspondance avec la grammaire de la langue utilisée pour la demande. Par exemple, une condition *NoMatch* peut intervenir si un utilisateur dit quelque chose en allemand alors que le module de reconnaissance attend l’anglais (États-Unis) comme langue parlée. Le modèle de forme d’onde de l’énoncé indique la présence d’un langage humain, mais aucun des mots prononcés ne correspond au lexique de l’anglais (États-Unis) utilisé par le module de reconnaissance.

Une autre condition *NoMatch* se produit lorsque l’algorithme de reconnaissance est incapable de trouver une correspondance exacte pour les sons contenus dans le flux audio. Lorsque cette condition se produit, le service Microsoft Speech peut générer des messages *speech.hypothesis* contenant un *texte hypothétique* mais générera un message *speech.phrase* dans lequel l’élément *RecognitionStatus* a pour valeur *NoMatch*. Cette condition est normale. Vous ne devez faire aucune hypothèse quant à l’exactitude ou la fidélité du texte dans le message *speech.hypothesis*. De plus, vous ne devez pas supposer que, parce que le service Microsoft Speech génère des messages *speech.hypothesis*, le service est en mesure de générer un message *speech.phrase* avec un élément *RecognitionStatus* ayant pour valeur *Success*.

## <a name="output-format"></a>Format de sortie

Le service Microsoft Speech peut retourner divers formats de charge utile dans les réponses de transcription. Toutes les charges utiles sont des structures JSON.

Vous pouvez contrôler le format des résultats d’expression en spécifiant le paramètre de requête d’URL `format`. Par défaut, le service retourne les résultats `simple`.

| Format | Description |
|-----|-----|
| `simple` | Résultat d’expression simplifiée qui contient le statut de reconnaissance et le texte reconnu sous sa forme d’affichage. |
| `detailed` | Statut de reconnaissance et liste des N meilleurs résultats d’expression où chaque résultat d’expression contient les quatre formes de reconnaissance et un score de confiance. |

Le format `detailed` contient [N meilleures valeurs](#n-best-values), en plus de `RecognitionStatus`, `Offset` et `duration`, dans la réponse.

### <a name="n-best-values"></a>N meilleures valeurs

Des auditeurs, qu’ils soient humains ou machines, ne peuvent jamais être certains d’avoir entendu *exactement* ce qui a été dit. Un auditeur peut affecter une *probabilité* seulement à une interprétation particulière d’un énoncé. 

Dans des conditions normales, lorsqu’une personne parle à d’autres personnes avec lesquelles elle communique fréquemment, ces personnes ont une probabilité élevée de reconnaître les mots qui sont prononcés. Les auditeurs machines s’efforcent d’atteindre des niveaux de précision similaires et, dans des conditions favorables, [ils atteignent la parité avec les êtres humains](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

Les algorithmes utilisés en reconnaissance vocale explorent des interprétations alternatives d’un énoncé dans le cadre d’un traitement normal. En règle générale, ces alternatives sont ignorées lorsque les preuves en faveur d’une interprétation deviennent trop importantes. Dans des conditions moins optimales, toutefois, le module de reconnaissance vocale finit avec une liste d’interprétations alternatives possibles. Les *N* alternatives en haut de cette liste constituent la *liste des N meilleures valeurs*. Chaque alternative se voit attribuer un [score de confiance](#confidence). Les scores de confiance sont compris entre 0 et 1. Un score de 1 représente le plus haut niveau de confiance. Un score de 0 représente le plus bas niveau de confiance.

> [!NOTE]
> Le nombre d’entrées dans la liste des N meilleures valeurs varie selon les énoncés. Le nombre d’entrées peut varier selon les reconnaissances d’un *même* énoncé. Cette variation est un résultat naturel et prévu de la nature probabiliste de l’algorithme de reconnaissance vocale.

Chaque entrée retournée dans la liste des N meilleures valeurs contient

- `Confidence`, qui représente les [scores de confiance](#confidence) de cette entrée.
- `Lexical`, qui est la [forme lexicale](#lexical-form) du texte reconnu.
- `ITN`, qui est la [forme ITN](#itn-form) du texte reconnu.
- `MaskedITN`, qui est la [forme ITN masquée](#masked-itn-form) du texte reconnu.
- `Display`, qui est la [forme d’affichage](#display-form) du texte reconnu.

### Scores de confiance <a id="confidence"></a>

Les scores de confiance font partie intégrante des systèmes de reconnaissance vocale. Le service Microsoft Speech obtient les scores de confiance à partir d’un *classifieur de confiance*. Microsoft entraîne le classifieur de confiance sur un ensemble de fonctionnalités conçues pour différencier au maximum des reconnaissances correctes et incorrectes. Les scores de confiance sont évalués pour des mots individuels et des énoncés entiers.

Si vous choisissez d’utiliser les scores de confiance qui sont retournés par le service, tenez compte du comportement suivant :

- Les scores de confiance peuvent être comparés uniquement dans un même mode de reconnaissance et une même langue. Ne comparez pas des scores entre des langues différentes ou des modes de reconnaissance différents. Par exemple, un score de confiance en mode de reconnaissance interactif n’a *aucune* corrélation avec un score de confiance en mode de dictée.
- Les scores de confiance sont utilisés au mieux sur un ensemble restreint d’énoncés. Il existe naturellement un fort degré de variabilité des scores pour un grand ensemble d’énoncés.

Si vous choisissez d’utiliser une valeur de score de confiance comme *seuil* pour votre application, utilisez la reconnaissance vocale pour établir ces valeurs de seuil.

- Exécutez la reconnaissance vocale sur un échantillon représentatif des énoncés pour votre application.
- Collectez les scores de confiance pour chaque reconnaissance dans l’ensemble d’échantillons.
- Basez votre valeur de seuil sur un certain percentile de confiance pour cet échantillon.

Aucune valeur de seuil unique n’est appropriée pour toutes les applications. Un score de confiance acceptable pour une application peut être inacceptable pour une autre application.

### <a name="lexical-form"></a>Forme lexicale

La forme lexicale est le texte reconnu, exactement comme il est apparu dans l’énoncé, sans ponctuation ni mise en majuscules. Par exemple, la forme lexicale de l’adresse « 1020 Enterprise Way » serait *ten twenty enterprise way* (dix vingt enterprise way), en supposant qu’elle a été prononcée de cette façon. La forme lexicale de la phrase « Remind me to buy 5 pencils » (Rappelle-moi d’acheter 5 crayons) est *remind me to buy five pencils* (rappelle-moi d’acheter cinq crayons).

La forme lexicale est plus appropriée pour les applications qui ont besoin d’effectuer une normalisation du texte non standard. La forme lexicale est également appropriée pour les applications nécessitant des mots de reconnaissance non traités.

Les grossièretés ne sont jamais masquées dans la forme lexicale.

### <a name="itn-form"></a>Forme ITN

La normalisation de texte est le processus de conversion d’un texte d’une forme en une autre forme « canonique ». Par exemple, le numéro de téléphone « 555-1212 » peut être converti dans la forme canonique *cinq cinq cinq un deux un deux*. La normalisation de texte *inverse* (ITN) inverse ce processus en convertissant les mots « cinq cinq cinq un deux un deux » dans la forme canonique inversée *555-1212*. La forme ITN d’un résultat de reconnaissance n’inclut pas de mise en majuscules ni de ponctuation.

La forme ITN convient particulièrement pour les applications qui agissent sur le texte reconnu. Par exemple, une application qui permet à un utilisateur d’énoncer des termes de recherche, puis utilise ces termes dans une requête web peut utiliser la forme ITN. Les grossièretés ne sont jamais masquées dans la forme ITN. Pour masquer les grossièretés, utilisez la *forme ITN masquée*.

### <a name="masked-itn-form"></a>Forme ITN masquée

Comme les grossièretés et la vulgarité ont une place naturelle dans la langue parlée, le service Microsoft Speech reconnaît ces mots et expressions lorsqu’ils sont prononcés. La vulgarité n’est toutefois pas appropriée pour certaines applications, notamment celles qui s’adressent à un public non adulte restreint.

La forme ITN masquée applique un masquage des grossièretés à la forme de normalisation de texte inverse. Pour masquer les grossièretés, définissez la valeur du paramètre de grossièretés sur `masked`. Lorsque les grossièretés sont masquées, les mots qui sont reconnus comme appartenant au lexique des grossièretés de la langue sont remplacés par des astérisques. Par exemple : *rappelle-moi d’acheter 5 **** de crayons*. La forme ITN masquée d’un résultat de reconnaissance n’inclut pas de mise en majuscules ni de ponctuation.

> [!NOTE]
> Si la valeur du paramètre de requête de vulgarité est définie sur `raw`, la forme ITN masquée est la même que la forme ITN. Les grossièretés *ne sont pas* masquées.

### <a name="display-form"></a>Forme d’affichage

Signes de ponctuation et majuscules indiquant où mettre l’accent, où faire une pause, etc., qui facilitent la compréhension du texte. La forme d’affichage ajoute des signes de ponctuation et des majuscules aux résultats de reconnaissance, ce qui en fait la forme la plus appropriée pour les applications qui affichent le texte énoncé.

Comme la forme d’affichage étend la forme ITN masquée, vous pouvez définir la valeur du paramètre de vulgarité sur `masked` ou `raw`. Si la valeur est définie sur `raw`, les résultats de reconnaissance incluent toute grossièreté énoncée par l’utilisateur. Si la valeur est définie sur `masked`, les mots reconnus comme appartenant au lexique des grossièretés de la langue sont remplacés par des astérisques.

### <a name="sample-responses"></a>Exemples de réponses

Toutes les charges utiles sont des structures JSON.

Résultat du format de charge utile de l’expression `simple` :

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Résultat du format de charge utile de l’expression `detailed` :

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="profanity-handling-in-speech-recognition"></a>Gestion de la vulgarité dans le cadre de la reconnaissance vocale

Le service Microsoft Speech reconnaît toutes les formes du langage humain, y compris des mots et des expressions que de nombreuses personnes classeraient comme « grossièretés ». Vous pouvez contrôler la manière dont le service gère la vulgarité à l’aide du paramètre de requête de *vulgarité*. Par défaut, le service masque les grossièretés dans les résultats *speech.phrase* et ne retourne pas les messages *speech.hypothesis* qui contiennent des grossièretés.

| Valeur de *vulgarité* | Description |
| - | - |
| `masked` | Masque les grossièretés par des astérisques. Il s’agit du comportement par défaut. | 
| `removed` | Supprime les grossièretés de tous les résultats. |
| `raw` | Reconnaît et retourne les grossièretés dans tous les résultats. |

### <a name="profanity-value-masked"></a>Valeur de vulgarité `Masked`

Pour masquer les grossièretés, définissez le paramètre de requête de *vulgarité* sur la valeur *masked*. Lorsque le paramètre de requête de *vulgarité* a cette valeur ou n’est pas spécifié pour une demande, le service *masque* les grossièretés. Le service effectue ce masquage en remplaçant les grossièretés dans les résultats de reconnaissance par des astérisques. Lorsque vous spécifiez une gestion avec masquage des grossièretés, le service ne retourne pas les messages *speech.hypothesis* qui contiennent des grossièretés.

### <a name="profanity-value-removed"></a>Valeur de vulgarité `Removed`

Lorsque le paramètre de requête de *vulgarité* a la valeur *removed*, le service supprime les grossièretés des messages *speech.phrase* et *speech.hypothesis*. Les résultats sont les mêmes *que si les grossièretés n’avaient pas été prononcées*.

#### <a name="profanity-only-utterances"></a>Énoncés composés uniquement de grossièretés

Un utilisateur peut énoncer *uniquement* des grossièretés alors qu’une application a configuré le service pour supprimer les grossièretés. Dans ce scénario, si le mode de reconnaissance est *dictée* ou *conversation*, le service ne retourne pas *speech.result*. Si le mode de reconnaissance est *interactif*, le service retourne un élément *speech.result* avec le code d’état *NoMatch*. 

### <a name="profanity-value-raw"></a>Valeur de vulgarité `Raw`

Lorsque le paramètre de requête de *vulgarité* a la valeur *raw*, le service ne supprime pas et ne masque pas les grossièretés dans les messages *speech.phrase* et *speech.hypothesis*.
