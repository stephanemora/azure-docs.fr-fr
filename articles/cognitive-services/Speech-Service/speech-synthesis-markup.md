---
title: Langage de balisage de synthèse vocale (SSML) - Services Speech
titleSuffix: Azure Cognitive Services
description: Utilisation du langage de balisage de synthèse vocale pour contrôler la prononciation et la prosodie dans la synthèse vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: e2b1e02a622dfe4ae488e372e44c8440f20d7034
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501153"
---
# <a name="speech-synthesis-markup-language-ssml"></a>SSML (Speech Synthesis Markup Language)

Le langage de balisage de synthèse vocale (SSML) est un langage de balisage basé sur XML qui permet aux développeurs de spécifier la manière dont un texte en entrée est converti en parole synthétisée via le service de synthèse vocale. Comparé à du texte brut, le SSML permet aux développeurs de régler finement la tonalité, la prononciation, le débit, le volume et d’autres paramètres de la synthèse vocale. La ponctuation normale, telle que la pause après un point ou l’utilisation de l’intonation correcte quand une phrase se termine par un point d’interrogation, est traitée automatiquement.

L’implémentation par SSML des services vocaux est basée sur le [Speech Synthesis Markup Language Version 1.0](https://www.w3.org/TR/speech-synthesis) du World Wide Web Consortium.

> [!IMPORTANT]
> Les caractères chinois, japonais et coréens comptent pour deux en matière de facturation. Pour plus d’informations, voir la [tarification](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Voix standard, neuronales et personnalisées

Choisissez parmi les voix standard et neuronales, ou créez une voix personnalisée propre à votre produit ou votre marque. Plus de 75 voix standard sont disponibles dans plus de 45 langues et paramètres régionaux, et 5 voix neuronales sont disponibles dans 4 langues et paramètres régionaux. Pour obtenir la liste complète des langues, paramètres régionaux et voix (neuronales et standard) pris en charge, consultez [prise en charge linguistique](language-support.md).

Pour en savoir plus sur les voix standard, neuronales et personnalisées, voir [Vue d’ensemble de la synthèse vocale](text-to-speech.md).

## <a name="supported-ssml-elements"></a>Éléments SSML pris en charge

Chaque document SSML est créé avec des éléments SSML (ou les balises). Ces éléments sont utilisés pour ajuster la tonalité, la prosodie, le volume et d’autres paramètres. Les sections suivantes détaillent la manière dont chaque élément est utilisé, et quand il est obligatoire ou facultatif.  

> [!IMPORTANT]
> N’oubliez pas d’entourer les valeurs d’attribut de guillemets. Les normes pour un code XML bien formé valide exigent que les valeurs d’attribut soient placées entre guillemets. Par exemple, `<prosody volume="90">` est un élément bien formé valide, mais `<prosody volume=90>` ne l’est pas. Le SSML peut ne pas reconnaître des valeurs d’attribut non entourées de guillemets.

## <a name="create-an-ssml-document"></a>Créer un document SSML

`speak` est l’élément racine **requis** pour tous les documents SSML. L’élément `speak` contient des informations importantes, telles que la version, la langue et la définition de vocabulaire de balisage.

**Syntaxe**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attributs**

| Attribut | Description | Obligatoire/facultatif |
|-----------|-------------|---------------------|
| version | Indique la version de la spécification SSML utilisée pour interpréter le balisage de document. La version actuelle est 1.0. | Obligatoire |
| xml:lang | Spécifie la langue du document racine. La valeur peut contenir un code langue de deux lettres minuscules (par exemple, **fr**), ou le code langue associé au code du pays ou de la région en majuscules (par exemple, **fr-FR**). | Obligatoire |
| xmlns | Spécifie l’URI du document définissant le vocabulaire de balisage (types d’éléments et noms d’attribut) du document SSML. L’URI en cours est https://www.w3.org/2001/10/synthesis. | Obligatoire |

## <a name="choose-a-voice-for-text-to-speech"></a>Choisir une voix de synthèse vocale

L’élément `voice` est obligatoire. Il spécifie la voix utilisée pour la synthèse vocale.

**Syntaxe**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attributs**

| Attribut | Description | Obligatoire/facultatif |
|-----------|-------------|---------------------|
| Nom | Identifie la voix utilisée pour la sortie de synthèse vocale. Pour accéder à la liste complète des voix prises en charge, voir [Prise en charge des langues](language-support.md#text-to-speech). | Obligatoire |

**Exemple**

> [!NOTE]
> Cet exemple utilise la voix `en-US-Jessa24kRUS`. Pour accéder à la liste complète des voix prises en charge, voir [Prise en charge des langues](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Utiliser plusieurs voix

Dans l’élément `speak`, vous pouvez spécifier plusieurs voix pour la sortie de synthèse vocale. Ces voix peuvent être dans différentes langues. Pour chaque voix, le texte doit être encapsulé dans un élément `voice`.

**Attributs**

| Attribut | Description | Obligatoire/facultatif |
|-----------|-------------|---------------------|
| Nom | Identifie la voix utilisée pour la sortie de synthèse vocale. Pour accéder à la liste complète des voix prises en charge, voir [Prise en charge des langues](language-support.md#text-to-speech). | Obligatoire |

**Exemple**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice  name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Ajuster les styles oraux

> [!IMPORTANT]
> Cette fonctionnalité n’opère qu’avec des voix neuronales.

Par défaut, le service de synthèse vocale synthétise le texte à l’aide d’un style oral neutre pour les voix standard et neuronales. Les voix neuronales vous permettent d’ajuster le style oral pour exprimer de la gaîté, de l’empathie ou des sentiments avec l’élément `<mstts:express-as>`. Il s’agit d’un élément facultatif spécifique des services vocaux Azure.

Actuellement, des ajustements de style oral sont pris en charge pour ces voix neuronales :
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

Des modifications sont appliquées au niveau de la phrase, et le style varie selon la voix. Si un style n’est pas pris en charge, le service renvoie la voix dans le style oral neutre par défaut.

**Syntaxe**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Attributs**

| Attribut | Description | Obligatoire/facultatif |
|-----------|-------------|---------------------|
| Type | Spécifie le style oral. Actuellement, les styles oraux sont spécifiques de la voix. | Obligatoire en cas d’ajustement du style oral pour une voix neuronale. Si vous utilisez `mstts:express-as`, le type doit être fourni. Si une valeur non valide est fournie, cet élément est ignoré. |

Reportez-vous à ce tableau pour déterminer les styles oraux pris en charge pour chaque voix neuronale.

| Voix | Type | Description |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | Exprime une émotion positive et heureuse |
| | type=`empathy` | Exprime une de la compassion et de la compréhension |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Exprime un ton formel, similaire aux journaux télévisés |
| | type=`sentiment` | Transmet un message ou récit touchant |

**Exemple**

Cet extrait de code SSML illustre la manière dont l’élément `<mstts:express-as>` est utilisé pour modifier le style oral en `cheerful`.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Ajouter ou supprimer une interruption/pause

Utilisez l’élément `break` pour insérer des pauses (ou des interruptions) entre des mots ou empêcher l’ajout automatique de pauses par le service de synthèse vocale.

> [!NOTE]
> Utilisez cet élément pour remplacer le comportement par défaut de la synthèse vocale (TTS) d’un mot ou d’une phrase dont le rendu en parole synthétisée ne semble pas naturel. Définissez `strength` sur `none` pour empêcher le service de synthèse vocale d’insérer automatiquement une interruption prosodique.

**Syntaxe**

```xml
<break strength="string" />
<break time="string" />
```

**Attributs**

| Attribut | Description | Obligatoire/facultatif |
|-----------|-------------|---------------------|
| puissance | Spécifie la durée relative d’une pause à l’aide de l’une des valeurs suivantes :<ul><li>Aucun</li><li>x-weak</li><li>weak</li><li>medium (par défaut)</li><li>strong</li><li>x-strong</li></ul> | Facultatif |
| time | Spécifie la durée absolue d’une pause en secondes ou millisecondes. Exemples de valeurs valides : 2s et 500 | Facultatif |

| Puissance | Description |
|----------|-------------|
| Aucune, ou si aucune valeur fournie | 0 ms |
| x-weak | 250 ms |
| weak | 500 ms |
| moyenne | 750 ms |
| strong | 1000 ms |
| x-strong | 1250 ms |


**Exemple**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Spécifier des paragraphes et des phrases

Les éléments `p` et `s` sont utilisés pour désigner respectivement des paragraphes et des phrases. En l’absence de ces éléments, le service de synthèse vocale détermine automatiquement la structure du document SSML.

L’élément `p` peut contenir du texte et les éléments suivants : `audio`, `break`, `phoneme`, `prosody`, `say-as`, `sub`, `mstts:express-as` et `s`.

L’élément `s` peut contenir du texte et les éléments suivants : `audio`, `break`, `phoneme`, `prosody`, `say-as`, `mstts:express-as` et `sub`.

**Syntaxe**

```XML
<p></p>
<s></s>
```

**Exemple**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Utiliser des phonèmes pour améliorer la prononciation

L’élément `ph` est utilisé pour la prononciation phonétique dans des documents SSML. L’élément `ph` ne peut rien contenir d’autre que du texte. Fournissez toujours un discours contrôlable de visu comme solution de secours.

Les alphabets phonétiques sont constitués de phonèmes composés de lettres, de chiffres ou de caractères parfois combinés. Chaque phonème décrit un son vocal unique. Cela contraste avec l’alphabet latin où chaque lettre peut représenter plusieurs sons parlés. Considérez les différentes prononciations de la lettre « c » dans les mots « casser » et « cesser », ou les différentes prononciations de la combinaison de lettres « ch » dans les mots « chose » et « almanach ».

**Syntaxe**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attributs**

| Attribut | Description | Obligatoire/facultatif |
|-----------|-------------|---------------------|
| alphabet | Spécifie l’alphabet phonétique à utiliser lors de la synthèse de la prononciation de la chaîne dans l’attribut `ph`. La chaîne spécifiant l’alphabet doit être en lettres minuscules. Les alphabets que vous pouvez spécifier sont les suivants.<ul><li>ipa &ndash; Alphabet phonétique international</li><li>sapi &ndash; Jeu de phonèmes de l’API Microsoft Speech</li><li>ups &ndash; Jeu de phonèmes universel</li></ul>L’alphabet s’applique uniquement au phonème dans l’élément. Pour plus d’informations, voir la [documentation de référence sur l’alphabet phonétique](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx). | Facultatif |
| ph | Chaîne contenant des phonèmes spécifiant la prononciation du mot figurant dans l’élément `phoneme`. Si la chaîne spécifiée contient des phonèmes non reconnus, le service de synthèse vocale rejette la totalité du document SSML et ne produit aucune des sorties vocales spécifiées dans le document. | Requis en cas d’utilisation de phonèmes. |

**Exemples**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>Ajuster la prosodie

L’élément `prosody` est utilisé pour spécifier des modifications apportées à la tonalité, au contour, à la plage, à la cadence, à la durée et au volume de la sortie de synthèse vocale. L’élément `prosody` peut contenir du texte et les éléments suivants : `audio`, `break`, `p`, `phoneme`, `prosody`, `say-as`, `sub` et `s`.

Étant donné que les valeurs d’attribut prosodique peuvent varier sur une vaste plage, le module de reconnaissance vocale interprète les valeurs affectées comme une suggestion de ce que les valeurs prosodiques réelles de la voix sélectionnée devraient être. Le service de synthèse vocale limite ou remplace les valeurs non prises en charge. Des valeurs non prises en charge sont, par exemple, une tonalité de 1 MHz ou un volume de 120.

**Syntaxe**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attributs**

| Attribut | Description | Obligatoire/facultatif |
|-----------|-------------|---------------------|
| pitch | Indique la tonalité de base pour le texte. Vous pouvez spécifier la tonalité comme suit :<ul><li>Valeur absolue, exprimée sous la forme d’un nombre suivi de « Hz » (Hertz). Par exemple, 600 Hz.</li><li>Valeur relative, exprimée sous la forme d’un nombre précédé du signe « + » ou « - » et suivi de « Hz » ou « st », qui spécifie l’importance d’un changement de tonalité. Par exemple, +80 Hz ou -2 st. « st » indique que l’unité de changement est le demi-ton, c’est-à-dire la moitié d’un ton sur l’échelle diatonique standard.</li><li>Valeur constante :<ul><li>x-low</li><li>basse</li><li>moyenne</li><li>élevée</li><li>x-high</li><li>default</li></ul></li></ul>. | Facultatif |
| contour | Le contour n’est pas pris en charge pour la voix neuronale. Le contour représente les variations de la tonalité du contenu vocal sous la forme de cibles à des positions temporelles spécifiées dans la sortie vocale. Chaque cible est définie par des ensembles de paires de paramètres. Par exemple : <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>La première valeur dans chaque paire de paramètres spécifie l’emplacement du changement de tonalité sous la forme d’un pourcentage de la durée du texte. La deuxième valeur spécifie la quantité de hausse ou de baisse de la tonalité, à l’aide d’une valeur relative ou une valeur d’énumération pour la tonalité (voir `pitch`). | Facultatif |
| range  | Valeur représentant la plage de tonalités pour le texte. Vous pouvez exprimer `range` à l’aide des mêmes valeurs absolues, relatives ou d’énumération que celles utilisées pour décrire `pitch`. | Facultatif |
| rate  | Indique la cadence d’énonciation du texte. Vous pouvez exprimer `rate` comme suit :<ul><li>Valeur relative exprimée sous forme de nombre agissant comme multiplicateur de la valeur par défaut. Par exemple, la valeur *1* n’entraîne aucun changement de cadence. La valeur *.5* entraîne une réduction de moitié de la cadence. La valeur *3* entraîne un triplement de la cadence.</li><li>Valeur constante :<ul><li>x-slow</li><li>slow</li><li>moyenne</li><li>fast</li><li>x-fast</li><li>default</li></ul></li></ul> | Facultatif |
| duration  | Période de temps qui doit s’écouler pendant que le service de synthèse vocale (TTS) lit le texte, exprimée en secondes ou millisecondes. Par exemple, *2 s* ou *1800 ms*. | Facultatif |
| volume  | Indique le niveau de volume de la voix. Vous pouvez exprimer le volume comme suit :<ul><li>Valeur absolue, exprimée sous la forme d’un nombre dans la plage de 0,0 à 100,0, du *plus bas* au *plus fort*. Par exemple, 75. La valeur par défaut est 100,0.</li><li>Valeur relative, exprimée sous la forme d’un nombre précédé du signe « + » ou « - » spécifie une quantité de changement de volume. Par exemple, + 10 ou - 5,5.</li><li>Valeur constante :<ul><li>silent</li><li>x-soft</li><li>soft</li><li>moyenne</li><li>loud</li><li>x-loud</li><li>default</li></ul></li></ul> | Facultatif |

### <a name="change-speaking-rate"></a>Modifier le débit

La cadence d’élocution peut s’appliquer aux voix standard au niveau de la phrase ou du mot. Cependant, la cadence d’élocution ne s’applique aux voix neuronales qu’au niveau de la phrase.

**Exemple**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Modifier le volume

Les modifications de volume peuvent s’appliquer aux voix standard au niveau de la phrase ou du mot. Cependant, les modifications de volume ne s’appliquent aux voix neuronales qu’au niveau de la phrase.

**Exemple**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Modifier la tonalité

Les modifications de ton peuvent s’appliquer aux voix standard au niveau de la phrase ou du mot. Cependant, les modifications de ton ne s’appliquent aux voix neuronales qu’au niveau de la phrase.

**Exemple**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Modifier le contour intonatif

> [!IMPORTANT]
> Les modifications de contour de ton ne sont pas prises en charge avec les voix neuronales.

**Exemple**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Ajouter un audio enregistré

`audio` est un élément facultatif qui vous permet d’insérer un audio MP3 dans un document SSML. Le corps de l’élément audio peut contenir du texte brut ou un balisage SSML qui est prononcé si le fichier audio n’est pas disponible ou ne peut pas être lu. De plus, l’élément `audio` peut contenir du texte et les éléments suivants : `audio`, `break`, `p`, `s`, `phoneme`, `prosody`, `say-as` et `sub`.

Tout audio inclus dans le document SSML doit respecter les exigences suivantes :

* Le MP3 doit être hébergé sur un point de terminaison HTTPS accessible via Internet. HTTPS est requis et le domaine qui héberge le fichier MP3 doit présenter un certificat SSL approuvé valide.
* Le MP3 doit être un fichier MP3 valide (MPEG v2).
* La vitesse de transmission doit être de 48 Kbits/s.
* L’échantillonnage doit être de 16 000 Hz.
* La durée totale cumulée de tous les fichiers texte et audio dans une réponse unique ne peut pas dépasser 90 secondes.
* Le MP3 ne doit pas contenir d’informations propres à un client ou d’autres informations sensibles.

**Syntaxe**

```xml
<audio src="string"/></audio>
```

**Attributs**

| Attribut | Description | Obligatoire/facultatif |
|-----------|-------------|---------------------|
| src | Spécifie l’emplacement/URL du fichier audio. | Obligatoire en cas d’utilisation de l’élément audio dans votre document SSML. |

**Exemple**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <p>
        <audio src="https://contoso.com/opinionprompt.wav"/>
        Thanks for offering your opinion. Please begin speaking after the beep.
        <audio src="https://contoso.com/beep.wav">
        Could not play the beep, please voice your opinion now. </audio>
    </p>
</speak>
```

## <a name="add-background-audio"></a>Ajouter un arrière-plan audio

L’élément `mstts:backgroundaudio` vous permet d’ajouter de l’audio en arrière-plan à vos documents SSML (ou de combiner un fichier audio avec la synthèse vocale). Avec `mstts:backgroundaudio`, vous pouvez exécuter en boucle un fichier audio en arrière-plan, l’estomper au début de la synthèse vocale et l’estomper à la fin de la synthèse vocale.

Si l’audio en arrière-plan fourni est plus court que la synthèse vocale ou la diminution du son, il est répété en boucle. S’il est plus long que la synthèse vocale, il s’arrête lorsque la diminution du son est terminée.

Un seul fichier audio en arrière-plan est autorisé par document SSML. Toutefois, vous pouvez intercaler des balises `audio` dans l’élément `voice` pour ajouter de l’audio supplémentaire à votre document SSML.

**Syntaxe**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Attributs**

| Attribut | Description | Obligatoire/facultatif |
|-----------|-------------|---------------------|
| src | Spécifie l’emplacement/URL du fichier audio en arrière-plan. | Obligatoire en cas d’utilisation de l’audio en arrière-plan dans votre document SSML. |
| volume | Spécifie le volume du fichier audio en arrière-plan. Les **valeurs acceptées** vont de `0` à `100` inclus. La valeur par défaut est `1`. | Facultatif |
| fadein | Spécifie la durée de la diminution de son du fichier audio en arrière-plan. Les **valeurs acceptées** vont de `0` à `10000` inclus.  | Facultatif |
| fadeout | Spécifie la durée de la diminution de son du fichier audio en arrière-plan. Les **valeurs acceptées** vont de `0` à `10000` inclus.  | Facultatif |

**Exemple**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Étapes suivantes

* [Prise en charge linguistique : voix, paramètres régionaux, langues](language-support.md)
