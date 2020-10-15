---
title: Translator v2.0
titleSuffix: Azure Cognitive Services
description: Documentation de référence pour Translator v2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: 7fa148579e7525933d388b8a93c9a3476f473cb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83588613"
---
# <a name="translator-v20"></a>Translator v2.0

> [!IMPORTANT]
> Cette version de Translator est déconseillée. [Consultez la documentation de Translator v3](v3-0-reference.md).

Vous pouvez intégrer Translator v2 en toute transparence dans vos applications, sites web, outils ou autres solutions pour offrir une expérience utilisateur multilingue. Vous pouvez l’utiliser sur toute plateforme matérielle et avec tout système d’exploitation pour effectuer la traduction et d’autres opérations liées à la langue, telles que la détection de la langue d’un texte et la synthèse vocale, en fonction des normes industrielles. Pour plus d’informations, consultez [Translator](../translator-info-overview.md).

## <a name="getting-started"></a>Prise en main
Pour accéder à Translator, vous devez [vous inscrire à Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Authentification 
Tous les appels à Translator nécessitent une clé d’abonnement pour l’authentification. L’API prend en charge trois méthodes d’authentification :

- Un jeton d’accès. Utilisez la clé d’abonnement référencée à l’étape 9 pour créer un jeton d’accès en envoyant une requête POST au service d’authentification. Pour plus d’informations, voir la documentation du service de jetons. Transmettez le jeton d’accès au service Translator à l’aide de l’en-tête `Authorization` ou du paramètre de requête `access_token`. Chaque jeton d’accès est valide pendant 10 minutes. Obtenez un nouveau jeton d’accès toutes les 10 minutes, et utilisez-le pour des requêtes répétées pendant ces 10 minutes.
- Une clé d’abonnement utilisée directement. Transmettez votre clé d’abonnement en tant que valeur dans l’en-tête `Ocp-Apim-Subscription-Key` inclus avec votre requête adressée à Translator. Quand vous utilisez directement la clé d’abonnement, vous n’êtes pas obligé d’appeler le service d’authentification de jeton pour créer un jeton d’accès.
- Un [abonnement multiservice Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Cette méthode vous permet d’utiliser une clé secrète unique pour authentifier les requêtes de plusieurs services.
Quand vous utilisez une clé secrète multiservice, vous devez inclure deux en-têtes d’authentification avec votre requête. Le premier en-tête transmet la clé secrète. Le second en-tête spécifie la région associée à votre abonnement :
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

La région est obligatoire pour l’abonnement à l’API Texte multiservice. La région que vous sélectionnez est la seule région que vous pouvez utiliser pour la traduction de texte quand vous utilisez la clé d’abonnement multiservice. Il doit s’agir de la même région que celle que vous avez sélectionnée lors de l’inscription à votre abonnement multiservice dans le portail Azure.

Les régions disponibles sont les suivantes : `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus` et `westus2`.

Vos clé d’abonnement et jeton d’accès sont des secrets qui doivent être masqués.

## <a name="profanity-handling"></a>Gestion de la vulgarité
Le service Translator conserve normalement toute vulgarité présente dans la source. Le degré de vulgarité et le contexte rendant le contenu vulgaire diffèrent en fonction des cultures. Par conséquent, le degré de vulgarité dans la langue cible peut être amplifié ou réduit.

Si vous souhaitez éviter toute vulgarité dans la traduction, même si elle est présente dans le texte source, vous disposez d’une option de filtrage de la vulgarité pour les méthodes qui la prennent en charge. Cette option vous permet de choisir si vous souhaitez supprimer les termes vulgaires, les marquer à l’aide de balises appropriées, ou si vous voulez les autoriser dans la cible. Les valeurs de `ProfanityAction` acceptées sont `NoAction` (par défaut), `Marked` et `Deleted`.


|ProfanityAction    |Action |Exemple de source (japonais)  |Exemple de traduction (français)  |
|:--|:--|:--|:--|
|NoAction   |Par défaut. Équivaut à ne pas définir d’option. La vulgarité de la source est reflétée dans la cible.        |彼はジャッカスです。     |Il est un imbécile.   |
|Marked     |Les mots vulgaires sont entourés des balises XML \<profanity> et \</profanity>.       |彼はジャッカスです。 |Il est un \<profanity>imbécile\</profanity>.  |
|Deleted    |Les mots vulgaires sont retirés de la cible sans remplacement.     |彼はジャッカスです。 |C’est un.   |

    
## <a name="excluding-content-from-translation"></a>Exclusion de contenu de la traduction
Lors de la traduction de contenu avec des balises, par exemple de type HTML (`contentType=text/html`), il est parfois utile d’exclure du contenu spécifique de la traduction. Vous pouvez utiliser l’attribut `class=notranslate` pour spécifier le contenu qui doit rester dans la langue d’origine. Dans l’exemple suivant, le contenu du premier élément `div` ne sera pas traduit, tandis que le contenu du deuxième élément `div` le sera.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Traduit une chaîne de texte d’une langue dans une autre.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Valeur renvoyée** : Chaîne qui représente le texte traduit.

Si vous avez utilisé précédemment `AddTranslation` ou `AddTranslationArray` pour entrer une traduction notée 5 ou plus pour la même phrase source, `Translate` renvoie uniquement le premier choix disponible pour votre système. L’expression « même phrase source » signifie que la phrase est rigoureusement identique (correspondance à 100 %), à l’exception de la casse, des espaces, des balises et du signe de ponctuation final. S’il n’existe pas de résultat stocké avec une note égale ou supérieure à 5, le résultat renvoyé est la traduction automatique générée par Microsoft Translator.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)

string

Type de contenu de réponse : application/xml

### <a name="parameters"></a>Paramètres

|Paramètre|Valeur|Description    |Type de paramètre|type de données|
|:--|:--|:--|:--|:--|
|appid  |(empty)    |Obligatoire. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ `appid` vide. Sinon, incluez une chaîne qui contient `"Bearer" + " " + "access_token"`.|query|string|
|text|(empty)   |Obligatoire. Chaîne qui représente le texte à traduire. Le texte ne peut pas contenir plus de 10 000 caractères.|query|string|
|de|(empty)   |facultatif. Chaîne qui représente le code de langue du texte en cours de traduction. Par exemple, en pour l’anglais.|query|string|
|to|(empty) |Obligatoire. Chaîne qui représente le code de la langue dans laquelle le texte doit être traduit.|query|string|
|contentType|(empty)    |facultatif. Format du texte en cours de traduction. Les formats pris en charge sont `text/plain` (par défaut) et `text/html`. Tous les éléments HTML doivent être des éléments complets et bien formés.|query|string|
|catégorie|(empty)   |facultatif. Chaîne qui contient la catégorie (le domaine) de la traduction. Par défaut, il s’agit de `general`.|query|string|
|Autorisation|(empty)  |Obligatoire si le champ `appid` et l’en-tête `Ocp-Apim-Subscription-Key` sont tous deux laissés vides. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)  |Obligatoire si le champ `appid` et l’en-tête `Authorization` sont tous deux laissés vides.|en-tête|string|


### <a name="response-messages"></a>Messages de réponse

|Code d'état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Récupère des traductions pour plusieurs textes sources.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Voici le format du corps de la demande :

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

Ces éléments se trouvent dans `TranslateArrayRequest` :


* `AppId`: Obligatoire. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ `AppId` vide. Sinon, incluez une chaîne qui contient `"Bearer" + " " + "access_token"`.
* `From`: facultatif. Chaîne qui représente le code de langue du texte en cours de traduction. Si ce champ est laissé vide, la réponse inclut le résultat de la détection de langue automatique.
* `Options`: facultatif. Objet `Options` qui contient les valeurs suivantes. Tous sont facultatifs. Par défaut, il s’agit des paramètres les plus courants. Les éléments spécifiés doivent être répertoriés dans l’ordre alphabétique.
    - `Category`: Chaîne qui contient la catégorie (le domaine) de la traduction. Par défaut, il s’agit de `general`.
    - `ContentType`: Format du texte en cours de traduction. Les formats pris en charge sont `text/plain` (par défaut), `text/xml` et `text/html`. Tous les éléments HTML doivent être des éléments complets et bien formés.
    - `ProfanityAction`: Spécifie comment les vulgarités sont traitées, comme expliqué précédemment. Les valeurs acceptées sont `NoAction` (par défaut), `Marked` et `Deleted`.
    - `State`: état utilisateur pour aider à mettre en corrélation la requête et la réponse. Le même contenu sera renvoyé dans la réponse.
    - `Uri`: Filtrer les résultats sur cet URI. Par défaut : `all`.
    - `User`: Filtrer les résultats sur cet utilisateur. Par défaut : `all`.
* `Texts`: Obligatoire. Tableau qui contient le texte pour la traduction. Toutes les chaînes doivent être dans la même langue. La totalité du texte à traduire ne peut pas dépasser 10 000 caractères. Le nombre maximal d’éléments de tableau est 2 000.
* `To`: Obligatoire. Chaîne qui représente le code de la langue dans laquelle le texte doit être traduit.

Vous pouvez omettre les éléments facultatifs. Les éléments qui sont des enfants directs de `TranslateArrayRequest` doivent être listés dans l’ordre alphabétique.

La méthode `TranslateArray` accepte les valeurs `application/xml` ou `text/xml` pour `Content-Type`.

**Valeur renvoyée** : Tableau `TranslateArrayResponse`. Chaque `TranslateArrayResponse` comporte les éléments suivants :

* `Error`: Indique une erreur si une erreur s’est produite. Sinon, la valeur est null.
* `OriginalSentenceLengths`: Tableau d’entiers qui indique la longueur de chaque phrase du texte source. La longueur du tableau indique le nombre de phrases.
* `TranslatedText`: Texte traduit.
* `TranslatedSentenceLengths`: Tableau d’entiers qui indique la longueur de chaque phrase du texte traduit. La longueur du tableau indique le nombre de phrases.
* `State`: état utilisateur pour aider à mettre en corrélation la requête et la réponse. Renvoie le même contenu que la demande.

Voici le format du corps de la réponse :

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
Une réponse correcte inclut un tableau de tableaux `TranslateArrayResponse` au format décrit précédemment.

string

Type de contenu de réponse : application/xml

### <a name="parameters"></a>Paramètres

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|Autorisation|(empty)  |Obligatoire si le champ `appid` et l’en-tête `Ocp-Apim-Subscription-Key` sont tous deux laissés vides. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)|Obligatoire si le champ `appid` et l’en-tête `Authorization` sont tous deux laissés vides.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d'état HTTP   |Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée. Les erreurs courantes sont les suivantes : <ul><li>L’élément Array ne peut pas être vide.</li><li>Catégorie non valide.</li><li>La langue From (source) n’est pas valide.</li><li>La langue To (cible) n’est pas valide.</li><li>La demande contient trop d’éléments.</li><li>La langue From (source) n’est pas prise en charge.</li><li>La langue To (cible) n’est pas prise en charge.</li><li>La demande de traduction contient trop de données.</li><li>Le format du code HTML n’est pas correct.</li><li>La demande de traduction contenait trop de chaînes.</li></ul>|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Récupère les noms conviviaux des langues passées en tant que paramètre `languageCodes`, localisés dans la langue `locale` transmise.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Le corps de la demande inclut un tableau de chaînes qui représente les codes de langue ISO 639-1 dont il s’agit de récupérer les noms conviviaux. Voici un exemple :

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Valeur renvoyée** : Tableau de chaînes qui contient les noms des langues pris en charge par le service Translator, localisés dans la langue demandée.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
Tableau de chaînes qui contient les noms des langues pris en charge par le service Translator, localisés dans la langue demandée.

string

Type de contenu de réponse : application/xml
 
### <a name="parameters"></a>Paramètres

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid|(empty)|Obligatoire. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ `appid` vide. Sinon, incluez une chaîne qui contient `"Bearer" + " " + "access_token"`.|query|string|
|locale|(empty) |Obligatoire. Chaîne qui représente l’un des éléments suivants, utilisé pour localiser les noms des langues : <ul><li>Combinaison d’un code de culture ISO 639 à deux lettres minuscules associé à une langue et un code de sous-culture ISO 3166 à deux lettres majuscules. <li>Code de culture ISO 639 en lettres minuscules autosuffisant.|query|string|
|Autorisation|(empty)  |Obligatoire si le champ `appid` et l’en-tête `Ocp-Apim-Subscription-Key` sont tous deux laissés vides. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)  |Obligatoire si le champ `appid` et l’en-tête `Authorization` sont tous deux laissés vides.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d'état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Obtient une liste des codes de langue représentant les langues prises en charge par le service Translator.  `Translate` et `TranslateArray` peuvent traduire entre n’importe quelle paire de ces langues.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Valeur renvoyée** : Tableau de chaînes qui contient les codes de langue pris en charge par le service Translator.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
Tableau de chaînes qui contient les codes de langue pris en charge par le service Translator.

string

Type de contenu de réponse : application/xml
 
### <a name="parameters"></a>Paramètres

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid|(empty)|Obligatoire. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ `appid` vide. Sinon, incluez une chaîne qui contient `"Bearer" + " " + "access_token"`.|query|string|
|Autorisation|(empty)  |Obligatoire si le champ `appid` et l’en-tête `Ocp-Apim-Subscription-Key` sont tous deux laissés vides. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)|Obligatoire si le champ `appid` et l’en-tête `Authorization` sont tous deux laissés vides.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d'état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503|Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Extrait les langues disponibles pour la synthèse vocale.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Valeur renvoyée** : Tableau de chaînes qui contient les codes de langue pris en charge pour la synthèse vocale par le service Translator.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
Tableau de chaînes qui contient les codes de langue pris en charge pour la synthèse vocale par le service Translator.

string

Type de contenu de réponse : application/xml

### <a name="parameters"></a>Paramètres

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid|(empty)|Obligatoire. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ `appid` vide. Sinon, incluez une chaîne qui contient `"Bearer" + " " + "access_token"`.|query|string|
|Autorisation|(empty)|Obligatoire si le champ `appid` et l’en-tête `Ocp-Apim-Subscription-Key` sont tous deux laissés vides. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)|Obligatoire si le champ `appid` et l’en-tête `Authorization` sont tous deux laissés vides.|en-tête|string|
 
### <a name="response-messages"></a>Messages de réponse

|Code d'état HTTP|Motif|
|:--|:--|
|400|Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401|Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Renvoie dans un flux WAV ou MP3 le texte passé, prononcé dans la langue souhaitée.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Valeur renvoyée** : Flux WAV ou MP3 du texte passé, prononcé dans la langue souhaitée.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)

binary

Type de contenu de réponse : application/xml

### <a name="parameters"></a>Paramètres

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid|(empty)|Obligatoire. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ `appid` vide. Sinon, incluez une chaîne qui contient `"Bearer" + " " + "access_token"`.|query|string|
|text|(empty)   |Obligatoire. Chaîne qui contient une ou plusieurs phrases à prononcer pour le flux, dans la langue spécifiée. Le texte ne doit pas dépasser 2 000 caractères.|query|string|
|langage|(empty)   |Obligatoire. Chaîne qui représente le code de langue pris en charge de la langue dans laquelle prononcer le texte. Le code doit être l’un des codes retournés par la méthode `GetLanguagesForSpeak`.|query|string|
|format|(empty)|facultatif. Chaîne qui spécifie l’ID de type de contenu. Actuellement, `audio/wav` et `audio/mp3` sont disponibles. La valeur par défaut est `audio/wav`.|query|string|
|options|(empty)    |facultatif. Chaîne qui spécifie les propriétés de la voix synthétisée :<ul><li>`MaxQuality` et `MinSize` indiquent la qualité du signal audio. `MaxQuality` offre la meilleure qualité. `MinSize` offre la plus petite taille de fichier. La valeur par défaut est `MinSize`.</li><li>`female` et `male` indiquent le genre souhaité de la voix. Par défaut, il s’agit de `female`. Utilisez la barre verticale (<code>\|</code>) pour inclure plusieurs options. Par exemple : `MaxQuality|Male`.</li></li></ul>  |query|string|
|Autorisation|(empty)|Obligatoire si le champ `appid` et l’en-tête `Ocp-Apim-Subscription-Key` sont tous deux laissés vides. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)  |Obligatoire si le champ `appid` et l’en-tête `Authorization` sont tous deux laissés vides.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d'état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Identifie la langue d’une section de texte.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Valeur renvoyée** : Chaîne qui contient un code de langue de deux caractères pour le texte.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)

string

Type de contenu de réponse : application/xml

### <a name="parameters"></a>Paramètres

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid|(empty)  |Obligatoire. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ `appid` vide. Sinon, incluez une chaîne qui contient `"Bearer" + " " + "access_token"`.|query|string|
|text|(empty)|Obligatoire. Chaîne qui contient du texte dont la langue doit être identifiée. Le texte ne doit pas dépasser 10 000 caractères.|query|  string|
|Autorisation|(empty)|Obligatoire si le champ `appid` et l’en-tête `Ocp-Apim-Subscription-Key` sont tous deux laissés vides. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key  |(empty)    |Obligatoire si le champ `appid` et l’en-tête `Authorization` sont tous deux laissés vides.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d'état HTTP|Motif|
|:--|:--|
|400|Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation

Identifie les langues dans un tableau de chaînes. Détecte de façon indépendante la langue de chaque élément du tableau, et renvoie un résultat pour chaque ligne de celui-ci.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Voici le format du corps de la demande :

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Le texte ne peut pas dépasser 10 000 caractères.

**Valeur renvoyée** : Tableau de chaînes qui contient un code de langue de deux caractères pour chaque ligne du tableau d’entrée.

Voici le format du corps de la réponse :

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
`DetectArray` a réussi. Retourne un tableau de chaînes qui contient un code de langue de deux caractères pour chaque ligne du tableau d’entrée.

string

Type de contenu de réponse : application/xml
 
### <a name="parameters"></a>Paramètres

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid|(empty)|Obligatoire. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ `appid` vide. Sinon, incluez une chaîne qui contient `"Bearer" + " " + "access_token"`.|query|string|
|Autorisation|(empty)|Obligatoire si le champ `appid` et l’en-tête `Ocp-Apim-Subscription-Key` sont tous deux laissés vides.  Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)|Obligatoire si le champ `appid` et l’en-tête `Authorization` sont tous deux laissés vides.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d'état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation

> [!IMPORTANT]
> **Avis de dépréciation :** depuis le 31 janvier 2018, cette méthode n’accepte plus l’envoi de nouvelles phrases. Vous recevez un message d’erreur. Consultez l’annonce sur les modifications apportées à l’infrastructure CTF (Collaborative Translation Framework).

Ajoute une traduction à la mémoire de traduction.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)

string

Type de contenu de réponse : application: xml
 
### <a name="parameters"></a>Paramètres

|Paramètre|Valeur|Description|Type de paramètre|Type de données   |
|:--|:--|:--|:--|:--|
|appid|(empty)|Obligatoire. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ `appid` vide. Sinon, incluez une chaîne qui contient `"Bearer" + " " + "access_token"`.|query|string|
|originalText|(empty)|Obligatoire. Chaîne qui contient le texte à traduire. La longueur maximale de la chaîne est de 1 000 caractères.|query|string|
|translatedText|(empty) |Obligatoire. Chaîne qui contient le texte traduit dans la langue cible. La longueur maximale de la chaîne est de 2 000 caractères.|query|string|
|de|(empty)   |Obligatoire. Chaîne qui représente le code de la langue d’origine du texte. Par exemple, en pour l’anglais et de pour l’allemand.|query|string|
|to|(empty)|Obligatoire. Chaîne qui représente le code de la langue dans laquelle le texte doit être traduit.|query|string|
|rating|(empty) |facultatif. Entier qui représente la notation de qualité pour la chaîne. La valeur est comprise entre -10 et 10. La valeur par défaut est 1.|query|entier|
|contentType|(empty)    |facultatif. Format du texte en cours de traduction. Les formats pris en charge sont `text/plain` et `text/html`. Tous les éléments HTML doivent être des éléments complets et bien formés.    |query|string|
|catégorie|(empty)|facultatif. Chaîne qui contient la catégorie (le domaine) de la traduction. Par défaut, il s’agit de `general`.|query|string|
|utilisateur|(empty)|Obligatoire. Chaîne utilisée pour effectuer le suivi de l’expéditeur de l’envoi.|query|string|
|URI|(empty)|facultatif. Chaîne qui contient l’emplacement du contenu de la traduction.|query|string|
|Autorisation|(empty)|Obligatoire si le champ `appid` et l’en-tête `Ocp-Apim-Subscription-Key` sont tous deux laissés vides.  Jeton d’autorisation : `"Bearer" + " " + "access_token"`.  |en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)|Obligatoire si le champ `appid` et l’en-tête `Authorization` sont tous deux laissés vides.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d'état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|410|`AddTranslation` n’est plus pris en charge.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation

> [!IMPORTANT]
> **Avis de dépréciation :** depuis le 31 janvier 2018, cette méthode n’accepte plus l’envoi de nouvelles phrases. Vous recevez un message d’erreur. Consultez l’annonce sur les modifications apportées à l’infrastructure CTF (Collaborative Translation Framework).

Ajoute un tableau de traductions à la mémoire de traduction. Cette méthode est une version de tableau de `AddTranslation`.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Voici le format du corps de la demande :

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

Ces éléments se trouvent dans `AddtranslationsRequest` :

* `AppId`: Obligatoire. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ `AppId` vide. Sinon, incluez une chaîne qui contient `"Bearer" + " " + "access_token"`.
* `From`: Obligatoire. Chaîne qui contient le code de la langue source. Doit être l’une des langues renvoyées par la méthode `GetLanguagesForTranslate`.
* `To`: Obligatoire. Chaîne qui contient le code de la langue cible. Doit être l’une des langues renvoyées par la méthode `GetLanguagesForTranslate`.
* `Translations`: Obligatoire. Tableau des traductions à ajouter à la mémoire de traduction. Chaque traduction doit contenir `OriginalText`, `TranslatedText` et `Rating`. La taille maximale de chaque `OriginalText` et `TranslatedText` est de 1 000 caractères. Le total de tous les éléments `OriginalText` et `TranslatedText` ne peut pas dépasser 10 000 caractères. Le nombre maximal d’éléments de tableau est 100.
* `Options`: Obligatoire. Ensemble d’options, notamment `Category`, `ContentType`, `Uri` et `User`. `User` est obligatoire. `Category`, `ContentType` et `Uri` sont facultatifs. Les éléments spécifiés doivent être répertoriés dans l’ordre alphabétique.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
La méthode `AddTranslationArray` a réussi. 

Depuis le 31 janvier 2018, les envois de phrases ne sont plus acceptés. Le service répond avec le code d’erreur 410.

string

Type de contenu de réponse : application/xml
 
### <a name="parameters"></a>Paramètres

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|Autorisation|(empty)|Obligatoire si le champ `appid` et l’en-tête `Ocp-Apim-Subscription-Key` sont tous deux laissés vides.  Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)|Obligatoire si le champ `appid` et l’en-tête `Authorization` sont tous deux laissés vides.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d'état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|410    |`AddTranslation` n’est plus pris en charge.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503|Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Décompose une section de texte en phrases, et renvoie un tableau qui contient les longueurs de chaque phrase.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Valeur renvoyée** : Tableau d’entiers qui représente les longueurs des phrases. La longueur du tableau représente le nombre de phrases. Les valeurs représentent la longueur de chaque phrase.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
Tableau d’entiers qui représente les longueurs des phrases. La longueur du tableau représente le nombre de phrases. Les valeurs représentent la longueur de chaque phrase.

entier

Type de contenu de réponse : application/xml

### <a name="parameters"></a>Paramètres

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid|(empty)  |Obligatoire. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ `appid` vide. Sinon, incluez une chaîne qui contient `"Bearer" + " " + "access_token"`.|query| string|
|text|(empty)   |Obligatoire. Chaîne qui représente le texte à fractionner en phrases. La taille maximale du texte est de 10 000 caractères.|query|string|
|langage   |(empty)    |Obligatoire. Chaîne qui représente le code de langue du texte d’entrée.|query|string|
|Autorisation|(empty)|Obligatoire si le champ `appid` et l’en-tête `Ocp-Apim-Subscription-Key` sont tous deux laissés vides. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.   |en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)|Obligatoire si le champ `appid` et l’en-tête `Authorization` sont tous deux laissés vides.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d'état HTTP|Motif|
|:--|:--|
|400|Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401|Informations d’identification non valides.|
|500|Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503|Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Récupère un tableau de traductions pour une paire de langues donnée à partir de la banque et du moteur de traduction automatique. `GetTranslations` diffère de `Translate` par le fait qu’il retourne toutes les traductions disponibles.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

Le corps de la demande inclut l’objet `TranslationOptions` facultatif, dont le format est le suivant :

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

L’objet `TranslateOptions` contient les valeurs de la liste suivante. Tous sont facultatifs. Par défaut, il s’agit des paramètres les plus courants. Les éléments spécifiés doivent être répertoriés dans l’ordre alphabétique.

* `Category`: Chaîne qui contient la catégorie (le domaine) de la traduction. Par défaut, il s’agit de `general`.
* `ContentType`: la seule option prise en charge (et donc par défaut) est `text/plain`.
* `IncludeMultipleMTAlternatives`: indicateur booléen permettant de spécifier si plusieurs alternatives doivent être renvoyées à partir du moteur de traduction automatique. Les valeurs valides sont `true` et `false` (avec respect de la casse). La valeur par défaut est `false`, qui ne retourne qu’une seule alternative. L’affectation de la valeur `true` à l’indicateur permet la création d’alternatives artificielles, entièrement intégrées à l’infrastructure CTF (Collaborative Translation Framework). La fonctionnalité permet de retourner des alternatives pour les phrases qui n’ont pas de traduction dans l’infrastructure CTF en ajoutant des alternatives artificielles à partir de la liste *n*-meilleures valeurs du décodeur.
    - Notation. Les notes sont appliquées comme suit : 
         - La meilleure traduction automatique obtient une note de 5.
       - Les alternatives de l’infrastructure CTF reflètent l’autorité du réviseur. Elles sont comprises entre -10 et +10.
       - Les alternatives de traduction générées automatiquement (*n*-meilleures valeurs) ont la note 0 et un degré de correspondance de 100.
    - Nombre d’alternatives. Le nombre d’alternatives retournées peut être aussi élevé que la valeur spécifiée dans `maxTranslations`, mais il peut aussi être inférieur.
    - Paires de langues. Cette fonctionnalité n’est pas disponible pour les traductions entre le chinois simplifié et le chinois traditionnel, dans les deux sens. Elle est disponible pour toutes les autres paires de langues que Microsoft Translator prend en charge.
* `State`: état utilisateur pour aider à mettre en corrélation la requête et la réponse. Le même contenu sera renvoyé dans la réponse.
* `Uri`: Filtrer les résultats sur cet URI. Si aucune valeur n’est définie, la valeur par défaut est `all`.
* `User`: Filtrer les résultats sur cet utilisateur. Si aucune valeur n’est définie, la valeur par défaut est `all`.

La demande `Content-Type` doit être `text/xml`.

**Valeur renvoyée** : Voici le format de la réponse :

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

Cette réponse inclut un élément `GetTranslationsResponse` qui contient les valeurs suivantes :

* `Translations`: Tableau des correspondances trouvées, stockées dans les objets `TranslationMatch` (décrits dans la section suivante). Les traductions peuvent inclure de légères variantes du texte d’origine (correspondances approximatives). Les traductions seront triées : Correspondances à 100 % en premier, correspondances approximatives ensuite.
* `From`: Si la méthode ne spécifie pas de langue `From`, cette valeur provient de la détection de langue automatique. Sinon, il s’agit de la langue `From` spécifiée.
* `State`: état utilisateur pour aider à mettre en corrélation la requête et la réponse. Contient la valeur fournie dans le paramètre `TranslateOptions`.

L’objet `TranslationMatch` est constitué des valeurs suivantes :

* `Error`: Code d’erreur, si une erreur se produit pour une chaîne d’entrée spécifique. Sinon, ce champ est vide.
* `MatchDegree`: indique à quel point le texte d’entrée correspond au texte d’origine trouvé dans le magasin. Le système établit des correspondances entre les phrases entrées et celles contenues dans le magasin, y compris des correspondances inexactes. La valeur renvoyée est comprise entre 0 et 100, où 0 indique l’absence totale de similarité, et 100 une correspondance exacte respectant la casse.
* `MatchedOriginalText`: Texte d’origine mis en correspondance pour ce résultat. Cette valeur est retournée uniquement si le texte d’origine mis en correspondance était différent du texte d’entrée. Elle est utilisée pour retourner le texte source d’une correspondance approximative. Cette valeur n’est pas renvoyée pour les résultats de Microsoft Translator.
* `Rating`: Indique l’autorité de la personne décidant de la qualité. Les résultats de la traduction automatique ont la note 5. Les traductions fournies de manière anonyme ont généralement une note comprise entre 1 et 4. Les traductions provenant de personnes ayant un degré d’autorité ont généralement une note comprise entre 6 et 10.
* `Count`: Nombre de fois où cette traduction avec cette note a été sélectionnée. La valeur est 0 pour la réponse traduite automatiquement.
* `TranslatedText`: Texte traduit.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
Objet `GetTranslationsResponse` au format décrit précédemment.

string

Type de contenu de réponse : application/xml
 
### <a name="parameters"></a>Paramètres

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid|(empty)|Obligatoire. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ `appid` vide. Sinon, incluez une chaîne qui contient `"Bearer" + " " + "access_token"`.|query|string|
|text|(empty)|Obligatoire. Chaîne qui représente le texte à traduire. La taille maximale du texte est de 10 000 caractères.|query|string|
|de|(empty)|Obligatoire. Chaîne qui représente le code de langue du texte en cours de traduction.|query|string|
|to |(empty)    |Obligatoire. Chaîne qui représente le code de la langue dans laquelle le texte doit être traduit.|query|string|
|maxTranslations|(empty)|Obligatoire. Entier qui représente le nombre maximal de traductions à renvoyer.|query|entier|
|Autorisation| (empty)|Obligatoire si le champ `appid` et l’en-tête `Ocp-Apim-Subscription-Key` sont tous deux laissés vides. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|string|  en-tête|
|Ocp-Apim-Subscription-Key|(empty)  |Obligatoire si le champ `appid` et l’en-tête `Authorization` sont tous deux laissés vides.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d'état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503|Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Récupère plusieurs traductions candidates pour plusieurs textes sources.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Voici le format du corps de la demande :

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` comprend les éléments suivants :

* `AppId`: Obligatoire. Si l’en-tête `Authorization` est utilisé, laissez le champ `AppId` vide. Sinon, incluez une chaîne qui contient `"Bearer" + " " + "access_token"`.
* `From`: Obligatoire. Chaîne qui représente le code de langue du texte en cours de traduction.
* `MaxTranslations`: Obligatoire. Entier qui représente le nombre maximal de traductions à renvoyer.
* `Options`: facultatif. Objet `Options` qui contient les valeurs suivantes. Tous sont facultatifs. Par défaut, il s’agit des paramètres les plus courants. Les éléments spécifiés doivent être répertoriés dans l’ordre alphabétique.
    - `Category`: Chaîne qui contient la catégorie (le domaine) de la traduction. Par défaut, il s’agit de `general`.
    - `ContentType`: la seule option prise en charge (et donc par défaut) est `text/plain`.
    - `IncludeMultipleMTAlternatives`: indicateur booléen permettant de spécifier si plusieurs alternatives doivent être renvoyées à partir du moteur de traduction automatique. Les valeurs valides sont `true` et `false` (avec respect de la casse). La valeur par défaut est `false`, qui ne retourne qu’une seule alternative. L’affectation de la valeur `true` à l’indicateur permet la génération d’alternatives artificielles de traduction, entièrement intégrées à l’infrastructure CTF (Collaborative Translation Framework). La fonctionnalité permet de retourner des alternatives pour les phrases qui n’en ont pas dans l’infrastructure CTF en ajoutant des alternatives artificielles à partir de la liste *n*-meilleures valeurs du décodeur.
        - Notation. Les notes sont appliquées comme suit :
          - La meilleure traduction automatique obtient une note de 5.
          - Les alternatives de l’infrastructure CTF reflètent l’autorité du réviseur. Elles sont comprises entre -10 et +10.
          - Les alternatives de traduction générées automatiquement (*n*-meilleures valeurs) ont la note 0 et un degré de correspondance de 100.
        - Nombre d’alternatives. Le nombre d’alternatives retournées peut être aussi élevé que la valeur spécifiée dans `maxTranslations`, mais il peut aussi être inférieur.
        - Paires de langues. Cette fonctionnalité n’est pas disponible pour les traductions entre le chinois simplifié et le chinois traditionnel, dans les deux sens. Elle est disponible pour toutes les autres paires de langues que Microsoft Translator prend en charge.
* `State`: état utilisateur pour aider à mettre en corrélation la requête et la réponse. Le même contenu sera renvoyé dans la réponse.
* `Uri`: Filtrer les résultats sur cet URI. Si aucune valeur n’est définie, la valeur par défaut est `all`.
* `User`: Filtrer les résultats sur cet utilisateur. Si aucune valeur n’est définie, la valeur par défaut est `all`.
* `Texts`: Obligatoire. Tableau qui contient le texte pour la traduction. Toutes les chaînes doivent être dans la même langue. La totalité du texte à traduire ne peut pas dépasser 10 000 caractères. Le nombre maximal d’éléments de tableau est 10.
* `To`: Obligatoire. Chaîne qui représente le code de la langue dans laquelle le texte doit être traduit.

Vous pouvez omettre les éléments facultatifs. Les éléments qui sont des enfants directs de `GetTranslationsArrayRequest` doivent être listés dans l’ordre alphabétique.

La demande `Content-Type` doit être `text/xml`.

**Valeur renvoyée** : Voici le format de la réponse :

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

Chaque élément `GetTranslationsResponse` contient les valeurs suivantes :

* `Translations`: Tableau des correspondances trouvées, stockées dans les objets `TranslationMatch` (décrits dans la section suivante). Les traductions peuvent inclure de légères variantes du texte d’origine (correspondances approximatives). Les traductions seront triées : Correspondances à 100 % en premier, correspondances approximatives ensuite.
* `From`: Si la méthode ne spécifie pas de langue `From`, cette valeur provient de la détection de langue automatique. Sinon, il s’agit de la langue `From` spécifiée.
* `State`: état utilisateur pour aider à mettre en corrélation la requête et la réponse. Contient la valeur fournie dans le paramètre `TranslateOptions`.

L’objet `TranslationMatch` contient les valeurs suivantes :
* `Error`: Code d’erreur, si une erreur se produit pour une chaîne d’entrée spécifique. Sinon, ce champ est vide.
* `MatchDegree`: indique à quel point le texte d’entrée correspond au texte d’origine trouvé dans le magasin. Le système établit des correspondances entre les phrases entrées et celles contenues dans le magasin, y compris des correspondances inexactes. La valeur renvoyée est comprise entre 0 et 100, où 0 indique l’absence totale de similarité, et 100 une correspondance exacte respectant la casse.
* `MatchedOriginalText`: Texte d’origine mis en correspondance pour ce résultat. Cette valeur est retournée uniquement si le texte d’origine mis en correspondance était différent du texte d’entrée. Elle est utilisée pour retourner le texte source d’une correspondance approximative. Cette valeur n’est pas renvoyée pour les résultats de Microsoft Translator.
* `Rating`: Indique l’autorité de la personne décidant de la qualité. Les résultats de la traduction automatique ont la note 5. Les traductions fournies de manière anonyme ont généralement une note comprise entre 1 et 4. Les traductions provenant de personnes ayant un degré d’autorité ont généralement une note comprise entre 6 et 10.
* `Count`: Nombre de fois où cette traduction avec cette note a été sélectionnée. La valeur est 0 pour la réponse traduite automatiquement.
* `TranslatedText`: Texte traduit.


### <a name="response-class-status-200"></a>Classe de réponse (état 200)

string

Type de contenu de réponse : application/xml
 
### <a name="parameters"></a>Paramètres

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|Autorisation  |(empty)    |Obligatoire si le champ `appid` et l’en-tête `Ocp-Apim-Subscription-Key` sont tous deux laissés vides.  Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)  |Obligatoire si le champ `appid` et l’en-tête `Authorization` sont tous deux laissés vides.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d'état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Migrer vers Translator v3](../migrate-to-v3.md)


