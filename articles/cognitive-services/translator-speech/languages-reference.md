---
title: Méthode Languages de l’API de traduction de conversation Translator Speech
titleSuffix: Azure Cognitive Services
description: Utilisez la méthode Languages de l’API de traduction de conversation Translator Speech.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: v-jansko
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7498ba08b9ce7b6aae10f38a393eb8cba37f3f4e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60827899"
---
# <a name="translator-speech-api-languages"></a>API de traduction de conversation Translator Speech : Languages

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

La traduction de conversation Translator Speech continue d’étendre la liste des langues prises en charge par ses services. Utilisez cette API pour découvrir l’ensemble des langues qui peuvent actuellement être utilisées avec le service Translator Speech.

Vous pouvez trouver des exemples de code illustrant l’utilisation de l’API pour obtenir les langues disponibles sur le [site GitHub de Microsoft Translator](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Remarques relatives à l’implémentation

### <a name="get-languages"></a>GET /languages

Un vaste éventail de langues sont disponibles pour la transcription la parole, la traduction du texte transcrit et la production d’une synthèse vocale de la traduction.

Un client utilise le paramètre de requête `scope` pour définir les ensembles de langues qui l’intéressent.

* **Reconnaissance vocale :** utilisez le paramètre de requête `scope=speech` pour récupérer l’ensemble de langues disponibles pour la transcription de la parole en texte.
* **Traduction de texte :** utilisez le paramètre de requête `scope=text` pour récupérer l’ensemble de langues disponibles pour traduire du texte transcrit.
* **Synthèse vocale :**  utilisez le paramètre de requête `scope=tts` pour récupérer l’ensemble de langues et de voix disponibles pour synthétiser le texte traduit en parole.

Un client peut récupérer simultanément plusieurs ensembles de langues en spécifiant une liste de choix séparés par des virgules. Par exemple : `scope=speech,text,tts`.

Une réponse correcte est un objet JSON avec une propriété pour chaque ensemble demandé.

```
{
    "speech": {
        //... Set of languages supported for speech-to-text
    },
    "text": {
        //... Set of languages supported for text translation
    },
    "tts": {
        //... Set of languages supported for text-to-speech
    }
}
```

Dans la mesure où un client peut utiliser le paramètre de requête `scope` pour sélectionner les ensembles de langues prises en charge à renvoyer, une réponse réelle ne peut inclure qu’un sous-ensemble de toutes les propriétés indiquées ci-dessus.

Les valeurs fournies avec chaque propriété sont les suivantes.

### <a name="speech-to-text-speech"></a>Reconnaissance vocale (speech)

La valeur associée à la propriété de reconnaissance vocale, `speech`, est un dictionnaire de paires clé-valeur. Chaque clé identifie une langue prise en charge pour la reconnaissance vocale. La clé est l’identificateur que le client transmet à l’API. La valeur associée à la clé est un objet dont les propriétés sont les suivantes :

* `name`: nom d’affichage de la langue.
* `language`: balise de la langue écrite associée. Voir « Traduction de texte » ci-dessous.
Voici un exemple :

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Traduction de texte (text)

La valeur associée à la propriété `text` est également un dictionnaire dans lequel chaque clé identifie une langue prise en charge pour la traduction de texte. La valeur associée à la clé décrit la langue :

* `name`: nom d’affichage de la langue.
* `dir`: sens de l’écriture, `rtl` pour les langues qui s’écrivent de droite à gauche, ou `ltr` pour les langues qui s’écrivent de gauche à droite.

Voici un exemple :

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Synthèse vocale (tts)

La valeur associée à la propriété de synthèse vocale, tts, est également un dictionnaire dans lequel chaque clé identifie une voix prise en charge. Les attributs d’un objet vocal sont les suivants :

* `displayName`: nom d’affichage de la voix.
* `gender`: genre de la voix (masculin ou féminin).
* `locale`: balise de langue de la voix avec sous-balise de langue principale et sous-balise de région.
* `language`: balise de la langue écrite associée.
* `languageName`: nom d’affichage de la langue.
* `regionName`: nom d’affichage de la région pour cette langue.

Voici un exemple :

```
{
    "tts": {
        "en-US-Zira": {
            "displayName": "Zira",
            "gender": "female",
            "locale": "en-US",
            "languageName": "English",
            "regionName": "United States",
            "language": "en"
        }
}
```

### <a name="localization"></a>Localisation
Le service renvoie tous les noms dans la langue spécifiée par l’en-tête « Accept-Language », pour toutes les langues prises en charge par la traduction de texte.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
Objet décrivant l’ensemble des langues prises en charge.

Exemple de valeur du modèle :

Langagues { speech (object, optional), text (object, optional), tts (object, optional) }

### <a name="headers"></a>headers

|En-tête|Description|Type|
:--|:--|:--|
X-RequestId|Valeur générée par le serveur pour identifier la demande, et utilisée à des fins de dépannage.|chaîne|

### <a name="parameters"></a>parameters

|Paramètre|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|
|api-version    |Version de l’API demandée par le client. Valeurs autorisées : `1.0`.|query|chaîne|
|scope  |Ensemble des langues ou voix prises en charge pour renvoyer au client. Ce paramètre est spécifié sous forme de liste de mots clés séparés par des virgules. Les mots clés suivants sont disponibles :<ul><li>`speech`: fournit l’ensemble des langues prises en charge pour la transcription de la parole.</li><li>`tts`: fournit l’ensemble des voix prises en charge pour la conversion de texte en parole.</li><li>`text`: fournit l’ensemble des langues prises en charge pour la traduction de texte.</li></ul>Si une valeur n’est pas spécifiée, la valeur par défaut de `scope` est `text`.|query|chaîne|
|X-ClientTraceId    |GUID généré par le client utilisé pour suivre une requête. Pour faciliter la résolution des problèmes, les clients doivent fournir une nouvelle valeur avec chaque demande, et la journaliser.|en-tête|chaîne|
|Accept-Language    |Certains des champs dans la réponse sont des noms de langue ou de région. Utilisez ce paramètre pour définir la langue dans laquelle les noms sont renvoyés. La langue est spécifiée en fournissant une balise de langue BCP 47 bien formée. Sélectionnez une balise dans la liste des identificateurs de langue renvoyée avec l’étendue `text`. Pour les langues non prises en charge, les noms sont fournis en anglais.<br/>Par exemple, utilisez la valeur `fr` pour demander des noms en français, ou la valeur `zh-Hant` pour demander des noms en chinois traditionnel.|en-tête|chaîne|

### <a name="response-messages"></a>Messages de réponse

|Code d’état HTTP|Motif|
|:--|:--|
|400|Demande incorrecte. Vérifiez les paramètres d’entrée pour vous assurer qu’ils sont valides. L’objet de la réponse inclut une description plus détaillée de l’erreur.|
|429|Trop grand nombre de requêtes.|
|500|Une erreur est survenue. Si l’erreur persiste, veuillez la signaler avec l’identificateur de trace client (X-ClientTraceId) ou l’identificateur (ID requête X) de demande.|
|503|Serveur temporairement indisponible. Relancez la requête. Si l’erreur persiste, veuillez la signaler avec l’identificateur de trace client (X-ClientTraceId) ou l’identificateur (ID requête X) de demande.|
