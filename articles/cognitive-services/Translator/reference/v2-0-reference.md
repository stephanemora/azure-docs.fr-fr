---
title: API de traduction de texte Translator Text v2.0
titleSuffix: Azure Cognitive Services
description: Documentation de référence pour l’API de traduction de texte Translator Text v2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: 88503c73e2ca9cf04e64ca3a47793e9b10ca325a
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357832"
---
# <a name="translator-text-api-v20"></a>API de traduction de texte Translator Text v2.0

> [!IMPORTANT]
> Cette version de l’API de traduction de texte Translator Text est désapprouvée. [Consultez la documentation pour la version v3 de l’API de traduction de texte Translator Text](v3-0-reference.md).

Vous pouvez intégrer l’API de traduction de texte Translator Text v2 en toute transparence dans vos applications, sites web, outils ou autres solutions pour offrir une expérience utilisateur multilingue. Cette API s’appuyant sur des normes industrielles, vous pouvez l’utiliser sur toute plateforme matérielle et avec tout système d’exploitation pour effectuer la traduction et d’autres opérations liées à la langue, telles que la détection de la langue d’un texte ou la synthèse vocale. Pour plus d’informations sur l’API Microsoft Translator, cliquez ici.

## <a name="getting-started"></a>Prise en main
Pour accéder à l’API de traduction de texte Translator Text, vous devez [vous inscrire à Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authorization"></a>Authorization
Tous les appels à l’API de traduction de texte Translator Text nécessitent une clé d’abonnement pour l’authentification. L’API prend en charge trois modes d’authentification :

- Un jeton d’accès. Utilisez la clé d’abonnement référencée à l’**étape** 9 pour générer un jeton d’accès en envoyant une requête POST au service d’autorisation. Pour plus d’informations, voir la documentation du service de jetons. Transmettez le jeton d’accès au service Translator à l’aide de l’en-tête d’autorisation ou du paramètre de requête `access_token`. Chaque jeton d’accès est valide pendant 10 minutes. Obtenez un nouveau jeton d’accès toutes les 10 minutes, et utilisez-le pour des requêtes répétées pendant ces 10 minutes.
- Une clé d’abonnement utilisée directement. Transmettez votre clé d’abonnement en tant que valeur dans l’en-tête `Ocp-Apim-Subscription-Key` inclus avec votre requête adressée à l’API Translator. Dans ce mode, vous n’avez pas à appeler le service de jetons d’authentification pour générer un jeton d’accès.
- Un [abonnement multiservice Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Ce mode vous permet d’utiliser une clé secrète unique pour authentifier les requêtes de plusieurs services. <br/>
Quand vous utilisez une clé secrète multiservice, vous devez inclure deux en-têtes d’authentification avec votre requête. Le premier en-tête transmet la clé secrète. Le second en-tête spécifie la région associée à votre abonnement :
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

La région est obligatoire pour l’abonnement à l’API Texte multiservice. La région que vous sélectionnez est la seule région que vous pouvez utiliser pour la traduction de texte lors de l’utilisation de la clé d’abonnement multiservice. En outre, il doit s’agir de la même région que celle sélectionnée lors de la souscription à votre abonnement multiservice via le portail Microsoft Azure.

Les régions disponibles sont les suivantes : `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus` et `westus2`.

Considérez votre clé d’abonnement et le jeton d’accès comme des secrets qui doivent être masqués.

## <a name="profanity-handling"></a>Gestion de la vulgarité
Le service Translator conserve normalement dans la traduction toute vulgarité présente dans la source. Le degré de vulgarité et le contexte qui rend des mots vulgaires diffèrent selon les cultures. Par conséquent, le degré de vulgarité dans la langue cible peut être amplifié ou réduit.

Si vous souhaitez éviter toute vulgarité dans la traduction, indépendamment de la présence de vulgarité dans le texte source, vous disposez d’une option de filtrage de la vulgarité pour les méthodes qui la prennent en charge. Cette option vous permet de choisir si vous souhaitez voir que les termes vulgaires soient supprimés ou marqués à l’aide de balises appropriées, ou si vous préférez qu’aucune action ne soit appliquée. Les valeurs de `ProfanityAction` acceptées sont `NoAction` (par défaut), Marked et `Deleted`.


|ProfanityAction    |Action |Exemple de source (japonais)  |Exemple de traduction (français)  |
|:--|:--|:--|:--|
|NoAction   |Par défaut. Équivaut à ne pas définir d’option. La vulgarité de la source est reflétée dans la cible.        |彼はジャッカスです。     |Il est un imbécile.   |
|Marked     |Les mots vulgaires sont entourés de balises XML \<profanity> et \</profanity>.       |彼はジャッカスです。 |C’est un \<profanity>jackass\</profanity>.  |
|Deleted    |Les mots vulgaires sont retirés de la cible sans remplacement.     |彼はジャッカスです。 |Il est un.   |

    
## <a name="excluding-content-from-translation"></a>Exclusion de contenu de la traduction
Lors de la traduction de contenu contenant des balises, par exemple de type HTML (`contentType=text/html`), il est parfois utile exclure du contenu spécifique de la traduction. Vous pouvez utiliser l’attribut `class=notranslate` pour spécifier le contenu qui doit rester dans la langue d’origine. Dans l’exemple suivant, le contenu du premier élément `div` ne sera pas traduit, tandis que le contenu du deuxième élément `div` le sera.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Traduit une chaîne de texte d’une langue dans une autre.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Valeur renvoyée** : Chaîne représentant le texte traduit.

Si vous avez utilisé précédemment `AddTranslation` ou `AddTranslationArray` pour entrer une traduction notée 5 ou supérieure pour la même phrase source, `Translate` renvoie uniquement le premier choix disponible pour votre système. L’expression « même phrase source » signifie que la phrase est rigoureusement identique (correspondance à 100 %), à l’exception de la casse, des espaces, des balises et du signe de ponctuation final. S’il n’existe pas de résultat stocké avec une note égale ou supérieure à 5, le résultat renvoyé est la traduction automatique générée par Microsoft Translator.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)

string

Type de contenu de réponse : application/xml 

### <a name="parameters"></a>parameters

|Paramètre|Valeur|Description    |Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid  |(empty)    |Requis. Si l’en-tête Authorization ou Ocp-Apim-Subscription-Key est utilisé, laissez le champ appid vide. Sinon, incluez une chaîne contenant « Bearer » + « » + « access_token ».|query|string|
|text|(empty)   |Requis. Chaîne représentant le texte à traduire. La taille du texte ne peut pas dépasser 10 000 caractères.|query|string|
|from|(empty)   |facultatif. Chaîne représentant le code de langue du texte source. Par exemple, en pour l’anglais.|query|string|
|to|(empty) |Requis. Chaîne représentant le code de langue du texte cible.|query|string|
|contentType|(empty)    |facultatif. Format du texte en cours de traduction. Les formats pris en charge sont text/plain (par défaut) et text/html. Tout code HTML doit être un élément bien formé et complet.|query|string|
|category|(empty)   |facultatif. Chaîne contenant la catégorie (domaine) de la traduction. La valeur par défaut est « general ».|query|string|
|Authorization|(empty)  |Obligatoire si le champ appid ou l’en-tête Ocp-Apim-Subscription-Key ne sont pas spécifiés. Jeton d’autorisation :  « Porteur » + «» + « jeton_d’accès ».|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)  |Obligatoire si le champ appid ou l’en-tête Authorization ne sont pas spécifiés.|en-tête|string|


### <a name="response-messages"></a>Messages de réponse

|Code d’état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse X-MS-Trans-Info.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Utilise la méthode `TranslateArray` pour récupérer des traductions pour plusieurs textes sources.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Le format du corps de la demande doit être le suivant :

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

Les éléments à l’intérieur de `TranslateArrayRequest` sont les suivants :


* `appid`: Requis. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ appid vide. Sinon, incluez une chaîne contenant `"Bearer" + " " + "access_token"`.
* `from`: facultatif. Chaîne représentant le code de langue du texte source. Si cette chaîne est vide, la réponse inclut le résultat de la détection automatique de la langue.
* `options`: facultatif. Objet `Options` contenant les valeurs répertoriées ci-dessous. Tous sont facultatives. Par défaut, il s’agit des paramètres les plus courants. Les éléments spécifiés doivent être répertoriés dans l’ordre alphabétique.
    - `Category`: Chaîne contenant la catégorie (domaine) de la traduction. La valeur par défaut est `general`.
    - `ContentType`: Format du texte en cours de traduction. Les formats pris en charge sont `text/plain` (par défaut), `text/xml` et `text/html`. Tout code HTML doit être un élément bien formé et complet.
    - `ProfanityAction`: Spécifie comment les vulgarités sont traitées, comme expliqué ci-dessus. Les valeurs de `ProfanityAction` acceptées sont `NoAction` (par défaut), `Marked` et `Deleted`.
    - `State`: État utilisateur pour aider à mettre en corrélation la requête et la réponse. Le même contenu sera renvoyé dans la réponse.
    - `Uri`: Filtrer les résultats sur cet URI. Par défaut : `all`.
    - `User`: Filtrer les résultats sur cet utilisateur. Par défaut : `all`.
* `texts`: Requis. Tableau contenant les textes à traduire. Toutes les chaînes doivent être dans la même langue. Les textes à traduire ne peuvent pas totaliser plus de 10 000 caractères. Le tableau ne peut pas contenir plus de 2 000 éléments.
* `to`: Requis. Chaîne représentant le code de langue du texte cible.

Les éléments facultatifs peuvent être omis. Les éléments qui sont des enfants directs de TranslateArrayRequest doivent être répertoriés dans l’ordre alphabétique.

La méthode de TranslateArray accepte les valeurs `application/xml` ou `text/xml` pour `Content-Type`.

**Valeur renvoyée** : Tableau `TranslateArrayResponse`. Chaque tableau `TranslateArrayResponse` contient les éléments suivants :

* `Error`: Indique une erreur si une erreur s’est produite. Sinon, la valeur est null.
* `OriginalSentenceLengths`: Tableau d’entiers indiquant la longueur de chaque phrase du texte source d’origine. La longueur du tableau indique le nombre de phrases.
* `TranslatedText`: Texte traduit.
* `TranslatedSentenceLengths`: Tableau d’entiers indiquant la longueur de chaque phrase du texte traduit. La longueur du tableau indique le nombre de phrases.
* `State`: État utilisateur pour aider à mettre en corrélation la requête et la réponse. Renvoie le même contenu que dans la demande.

Le format du corps de la réponse est le suivant.

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

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
Une réponse correcte inclut un tableau `TranslateArrayResponse` au format décrit ci-dessus.

string

Type de contenu de réponse : application/xml

### <a name="parameters"></a>parameters

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|Authorization|(empty) |Obligatoire si le champ appid ou l’en-tête Ocp-Apim-Subscription-Key ne sont pas spécifiés. Jeton d’autorisation :  « Porteur » + «» + « jeton_d’accès ».|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)|Obligatoire si le champ appid ou l’en-tête Authorization ne sont pas spécifiés.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d’état HTTP   |Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée. Les erreurs courantes sont les suivantes : <ul><li>L’élément Array ne peut pas être vide</li><li>Catégorie non valide</li><li>La langue From (source) n’est pas valide</li><li>La langue To (cible) n’est pas valide</li><li>La demande contient trop d’éléments</li><li>La langue From (source) n’est pas prise en charge</li><li>La langue To (cible) n’est pas prise en charge</li><li>La demande de traduction contient trop de données</li><li>Le format du code HTML n’est pas correct</li><li>La demande de traduction contenait trop de chaînes</li></ul>|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse X-MS-Trans-Info.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Récupère les noms conviviaux des langues passées dans le paramètre `languageCodes`, localisés dans la langue des paramètres régionaux passée.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Le corps de la demande inclut un tableau de chaînes représentant les codes de langue ISO 639-1 dont il s’agit de récupérer les noms conviviaux. Par exemple :

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Valeur renvoyée** : Tableau de chaînes contenant les noms des langues prises en charge par le service Translator, localisées dans la langue demandée.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
Tableau de chaînes contenant les noms des langues prises en charge par le service Translator, localisées dans la langue demandée.

string

Type de contenu de réponse : application/xml
 
### <a name="parameters"></a>parameters

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid|(empty)|Requis. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ appid vide. Sinon, incluez une chaîne contenant `"Bearer" + " " + "access_token"`.|query|string|
|locale|(empty) |Requis. Chaîne représentant une combinaison d’un code de culture ISO 639 à deux lettres minuscules associé à une langue et d’un code de sous-culture ISO 3166 à deux lettres majuscules pour localiser les noms de langue, ou n code de culture en lettres minuscules ISO 639 autosuffisant.|query|string|
|Authorization|(empty)  |Obligatoire si le champ appid ou l’en-tête `Ocp-Apim-Subscription-Key` ne sont pas spécifiés. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)  |Obligatoire si le champ appid ou l’en-tête `Authorization` ne sont pas spécifiés.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d’état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse X-MS-Trans-Info.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Obtient une liste des codes de langue représentant les langues prises en charge par le service Translator.  `Translate` et `TranslateArray` peuvent traduire entre n’importe quelle paire de ces langues.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Valeur renvoyée** : Tableau de chaînes contenant les codes de langue pris en charge par le service Translator.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
Tableau de chaînes contenant les codes de langue pris en charge par le service Translator.

string

Type de contenu de réponse : application/xml
 
### <a name="parameters"></a>parameters

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid|(empty)|Requis. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ appid vide. Sinon, incluez une chaîne contenant `"Bearer" + " " + "access_token"`.|query|string|
|Authorization|(empty)  |Obligatoire si le champ `appid` ou l’en-tête `Ocp-Apim-Subscription-Key` ne sont pas spécifiés. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)|Obligatoire si le champ `appid` ou l’en-tête `Authorization` ne sont pas spécifiés.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d’état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse X-MS-Trans-Info.|
|503|Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Extrait les langues disponibles pour la synthèse vocale.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Valeur renvoyée** : Tableau de chaînes contenant les codes de langue pris en charge pour la synthèse vocale par le service Translator.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
Tableau de chaînes contenant les codes de langue pris en charge pour la synthèse vocale par le service Translator.

string

Type de contenu de réponse : application/xml

### <a name="parameters"></a>parameters

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid|(empty)|Requis. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ appid vide. Sinon, incluez une chaîne contenant `"Bearer" + " " + "access_token"`.|query|string|
|Authorization|(empty)|Obligatoire si le champ `appid` ou l’en-tête `Ocp-Apim-Subscription-Key` ne sont pas spécifiés. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)|Obligatoire si le champ `appid` ou l’en-tête `Authorization` ne sont pas spécifiés.|en-tête|string|
 
### <a name="response-messages"></a>Messages de réponse

|Code d’état HTTP|Motif|
|:--|:--|
|400|Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401|Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Renvoie dans un flux WAV ou MP3 le texte passé, prononcé dans la langue souhaitée.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Valeur renvoyée** : Flux WAV ou MP3 contenant le texte passé, prononcé dans la langue souhaitée.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)

binary

Type de contenu de réponse : application/xml 

### <a name="parameters"></a>parameters

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid|(empty)|Requis. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ appid vide. Sinon, incluez une chaîne contenant `"Bearer" + " " + "access_token"`.|query|string|
|text|(empty)   |Requis. Chaîne contenant une ou plusieurs phrases dans la langue spécifiée, à prononcer pour le flux WAV. La taille du texte à prononcer ne peut pas dépasser 2 000 caractères.|query|string|
|Langage|(empty)   |Requis. Chaîne représentant le code de la langue prise en charge dans laquelle prononcer le texte. Le code doit être présent dans la liste des codes renvoyée par la méthode `GetLanguagesForSpeak`.|query|string|
|format|(empty)|facultatif. Chaîne spécifiant l’ID de type de contenu. Actuellement, `audio/wav` et `audio/mp3` sont disponibles. La valeur par défaut est `audio/wav`.|query|string|
|options|(empty)    |<ul><li>facultatif. Chaîne spécifiant les propriétés de la parole synthétisée :<li>`MaxQuality` et `MinSize` sont disponibles pour spécifier la qualité des signaux audio. Avec `MaxQuality`, vous obtenez des voix de la qualité la plus haute et avec `MinSize`, vous obtenez des voix de la qualité la plus basse. La valeur par défaut est `MinSize`.</li><li>`female` et `male` sont disponibles pour spécifier le genre souhaité de la voix. La valeur par défaut est `female`. Utilisez la barre verticale <code>\|</code> pour inclure plusieurs options. Par exemple : `MaxQuality|Male`.</li></li></ul> |query|string|
|Authorization|(empty)|Obligatoire si le champ `appid` ou l’en-tête `Ocp-Apim-Subscription-Key` ne sont pas spécifiés. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)  |Obligatoire si le champ `appid` ou l’en-tête `Authorization` ne sont pas spécifiés.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d’état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Utilise la méthode `Detect` pour identifier la langue d’une portion de texte sélectionnée.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Valeur renvoyée** : Chaîne contenant un code de langue de deux caractères pour le texte spécifié. .

### <a name="response-class-status-200"></a>Classe de réponse (état 200)

string

Type de contenu de réponse : application/xml

### <a name="parameters"></a>parameters

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid|(empty)  |Requis. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ appid vide. Sinon, incluez une chaîne contenant `"Bearer" + " " + "access_token"`.|query|string|
|text|(empty)|Requis. Chaîne contenant du texte dont la langue doit être identifiée. La taille du texte ne peut pas dépasser 10 000 caractères.|query| string|
|Authorization|(empty)|Obligatoire si le champ `appid` ou l’en-tête `Ocp-Apim-Subscription-Key` ne sont pas spécifiés. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key  |(empty)    |Obligatoire si le champ `appid` ou l’en-tête `Authorization` ne sont pas spécifiés.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d’état HTTP|Motif|
|:--|:--|
|400|Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Utilise la méthode `DetectArray` pour identifier la langue des chaînes d’un tableau. Effectue une détection indépendante de chaque élément du tableau, et renvoie un résultat pour chaque ligne de celui-ci.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Le format du corps de la demande doit être le suivant.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

La taille du texte ne peut pas dépasser 10 000 caractères.

**Valeur renvoyée** : Tableau de chaînes contenant des codes de langue de deux caractères pour chaque ligne du tableau d’entrée.

Le format du corps de la réponse est le suivant.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
La méthode DetectArray a réussi. Renvoie un tableau de chaînes contenant des codes de langue de deux caractères pour chaque ligne du tableau d’entrée.

string

Type de contenu de réponse : application/xml
 
### <a name="parameters"></a>parameters

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid|(empty)|Requis. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ appid vide. Sinon, incluez une chaîne contenant `"Bearer" + " " + "access_token"`.|query|string|
|Authorization|(empty)|Obligatoire si le champ `appid` ou l’en-tête `Ocp-Apim-Subscription-Key` ne sont pas spécifiés. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)|Obligatoire si le champ `appid` ou l’en-tête Authorization ne sont pas spécifiés.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d’état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse X-MS-Trans-Info.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation

> [!IMPORTANT]
> **AVIS DE DÉSAPPROBATION :** Depuis le 31 janvier 2018, cette méthode n’accepte plus l’envoi de nouvelles phrases, et vous recevez un message d’erreur. Reportez-vous à cette annonce sur les modifications apportées aux fonctions de traduction collaborative.

Ajoute une traduction à la mémoire de traduction.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)

string

Type de contenu de réponse : application: xml
 
### <a name="parameters"></a>parameters

|Paramètre|Valeur|Description|Type de paramètre|Type de données   |
|:--|:--|:--|:--|:--|
|appid|(empty)|Requis. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ appid vide. Sinon, incluez une chaîne contenant `"Bearer" + " " + "access_token"`.|query|string|
|originalText|(empty)|Requis. Chaîne contenant le texte à traduire. La chaîne ne peut pas contenir plus de 1 000 caractères.|query|string|
|translatedText|(empty) |Requis. Chaîne contenant le texte traduit dans la langue cible. La chaîne ne peut pas contenir plus de 2 000 caractères.|query|string|
|from|(empty)   |Requis. Chaîne représentant le code de langue du texte source. fr = français, de = allemand, etc...|query|string|
|to|(empty)|Requis. Chaîne représentant le code de langue du texte cible.|query|string|
|rating|(empty) |facultatif. Entier représentant la notation de qualité pour cette chaîne. Valeur comprise entre -10 et 10. La valeur par défaut est de 1.|query|integer|
|contentType|(empty)    |facultatif. Format du texte en cours de traduction. Les formats pris en charge sont « text/plain » et « text/html ». Tout code HTML doit être un élément bien formé et complet.   |query|string|
|category|(empty)|facultatif. Chaîne contenant la catégorie (domaine) de la traduction. La valeur par défaut est « general ».|query|string|
|user|(empty)|Requis. Chaîne utilisée pour effectuer le suivi de l’expéditeur de l’envoi.|query|string|
|URI|(empty)|facultatif. Chaîne contenant l’emplacement du contenu de cette traduction.|query|string|
|Authorization|(empty)|Obligatoire si le champ appid ou l’en-tête `Ocp-Apim-Subscription-Key` ne sont pas spécifiés. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.    |en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)|Obligatoire si le champ `appid` ou l’en-tête `Authorization` ne sont pas spécifiés.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d’état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|410|AddTranslation n’est plus pris en charge.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse X-MS-Trans-Info.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation

> [!IMPORTANT]
> **AVIS DE DÉSAPPROBATION :** Depuis le 31 janvier 2018, cette méthode n’accepte plus l’envoi de nouvelles phrases, et vous recevez un message d’erreur. Reportez-vous à cette annonce sur les modifications apportées aux fonctions de traduction collaborative.

Ajoute un tableau de traductions à ajouter à la mémoire de traduction. Il s’agit d’une version tableau de `AddTranslation`.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Le format du corps de la demande est le suivant.

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

Les éléments contenus dans l’élément AddtranslationsRequest sont les suivants :

* `AppId`: Requis. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ appid vide. Sinon, incluez une chaîne contenant `"Bearer" + " " + "access_token"`.
* `From`: Requis. Chaîne contenant le code de langue de la langue source. Doit être l’une des langues renvoyées par la méthode `GetLanguagesForTranslate`.
* `To`: Requis. Chaîne contenant le code de langue de la langue cible. Doit être l’une des langues renvoyées par la méthode `GetLanguagesForTranslate`.
* `Translations`: Requis. Tableau des traductions à ajouter à la mémoire de traduction. Chaque traduction doit contenir les éléments suivants : originalText, translatedText et rating. La taille de chaque texte (originalText et translatedText) est limitée à 1 000 caractères. Les textes (originalText et translatedText) ne peuvent pas totaliser plus de 10 000 caractères. Le nombre maximal d’éléments de tableau est 100.
* `Options`: Requis. Ensemble d’options incluant Category, ContentType, Uri et User. L’utilisateur est obligatoire. Les options Category, ContentType et Uri sont facultatives. Les éléments spécifiés doivent être répertoriés dans l’ordre alphabétique.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
La méthode AddTranslationArray a réussi. Depuis le 31 janvier 2018, les envois de phrase ne seront plus acceptés. Le service répond avec le code d’erreur 410.

string

Type de contenu de réponse : application/xml
 
### <a name="parameters"></a>parameters

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|Authorization|(empty)|Obligatoire si le champ appid ou l’en-tête Ocp-Apim-Subscription-Key ne sont pas spécifiés. Jeton d’autorisation :  « Porteur » + «» + « jeton_d’accès ».|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)|Obligatoire si le champ appid ou l’en-tête Authorization ne sont pas spécifiés.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d’état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|410    |AddTranslation n’est plus pris en charge.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503|Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Décompose une portion de texte en phrases, et renvoie un tableau contenant les longueurs des phrases.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Valeur renvoyée** : Tableau d’entiers représentant les longueurs des phrases. La longueur du tableau correspond au nombre de phrases, et les valeurs sont les longueurs des phrases.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
Tableau d’entiers représentant les longueurs des phrases. La longueur du tableau correspond au nombre de phrases, et les valeurs sont les longueurs des phrases.

integer

Type de contenu de réponse : application/xml 

### <a name="parameters"></a>parameters

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid|(empty)  |Requis. Si l’en-tête Authorization ou Ocp-Apim-Subscription-Key est utilisé, laissez le champ appid vide. Sinon, incluez une chaîne contenant « Bearer » + « » + « access_token ».|query| string|
|text|(empty)   |Requis. Chaîne représentant le texte à fractionner en phrases. La taille du texte ne peut pas dépasser 10 000 caractères.|query|string|
|Langage   |(empty)    |Requis. Chaîne représentant le code de langue du texte d’entrée.|query|string|
|Authorization|(empty)|Obligatoire si le champ appid ou l’en-tête Ocp-Apim-Subscription-Key ne sont pas spécifiés. Jeton d’autorisation :  « Porteur » + «» + « jeton_d’accès ».    |en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)|Obligatoire si le champ appid ou l’en-tête Authorization ne sont pas spécifiés.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d’état HTTP|Motif|
|:--|:--|
|400|Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401|Informations d’identification non valides.|
|500|Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse X-MS-Trans-Info.|
|503|Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Récupère un tableau de traductions pour une paire de langues donnée à partir de la banque et du moteur de traduction automatique. La méthode GetTranslations diffère de Translate, car elle renvoie toutes les traductions disponibles.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

Le corps de la demande inclut l’objet TranslationOptions facultatif, dont le format est le suivant.

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

L’objet `TranslateOptions` contient les valeurs répertoriées ci-dessous. Tous sont facultatives. Par défaut, il s’agit des paramètres les plus courants. Les éléments spécifiés doivent être répertoriés dans l’ordre alphabétique.

* `Category`: Chaîne contenant la catégorie (domaine) de la traduction. La valeur par défaut est « general ».
* `ContentType`: La seule option prise en charge (et donc par défaut) est text/plain.
* `IncludeMultipleMTAlternatives` : indicateur booléen pour déterminer si plusieurs alternatives doivent être renvoyées à partir du moteur de traduction automatique. Les valeurs valides sont true et false (respecte la casse). Par défaut, la valeur est false et n’offre qu’une seule alternative. Définir l’indicateur sur true permet de générer des alternatives artificielles de traduction, entièrement intégrées avec l’infrastructure de traduction collaborative (CTF). La fonctionnalité permet de renvoyer des alternatives de traduction pour des phrases qui n’ont pas d’alternatives dans l’infrastructure de traduction collaborative, en ajoutant des alternatives artificielles à partir de la liste n-meilleures traductions du décodeur.
    - Les notes sont appliquées comme suit : (1) La meilleure traduction automatique obtient une note de 5. (2) Les alternatives de l’infrastructure de traduction collaborative reflètent l’autorité de leur auteur, de -10 à +10. (3) Les alternatives de traduction générées automatiquement (n-meilleures) ont la note 0, et leur degré de correspondance est 100.
    - Nombre d’Alternatives Le nombre d’alternatives renvoyé peut atteindre la valeur maxTranslations, mais peut être inférieur.
    - Paires de langues Cette fonctionnalité n’est pas disponible pour les traductions entre le chinois simplifié et le chinois traditionnel, dans les deux sens. Elle est disponible pour toutes les autres paires de langues que Microsoft Translator prend en charge.
* `State`: État utilisateur pour aider à mettre en corrélation la requête et la réponse. Le même contenu sera renvoyé dans la réponse.
* `Uri`: Filtrer les résultats sur cet URI. Si aucune valeur n’est définie, la valeur par défaut est all.
* `User`: Filtrer les résultats sur cet utilisateur. Si aucune valeur n’est définie, la valeur par défaut est all.

La demande `Content-Type` doit être `text/xml`.

**Valeur renvoyée** : Le format de la réponse est le suivant.

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

Cela inclut un élément `GetTranslationsResponse` contenant les valeurs suivantes :

* `Translations`: Tableau des correspondances trouvées, stockées dans des objets TranslationMatch (voir ci-dessous). Les traductions peuvent inclure de légères variantes du texte d’origine (correspondances approximatives). Les traductions seront triées : Correspondances à 100 % en premier, correspondances approximatives ensuite.
* `From`: Si la méthode ne spécifie pas de langue From (source), cette langue est le résultat de la détection automatique de la langue. Sinon, il s’agit de la langue source spécifiée.
* `State`: État utilisateur pour aider à mettre en corrélation la requête et la réponse. Contient la même valeur que celle fournie dans le paramètre TranslateOptions.

L’objet TranslationMatch comprend les éléments suivants :

* `Error`: Si une erreur s’est produite pour une chaîne d’entrée spécifique, le code d’erreur est stocké. Sinon, le champ est vide.
* `MatchDegree`: Le système établit des correspondances entre les phrases entrées et celles contenues dans le magasin, y compris des correspondances inexactes.  MatchDegree indique à quel point le texte d’entrée correspond au texte d’origine trouvé dans le magasin. La valeur renvoyée est comprise entre 0 et 100, où 0 est indique l’absence totale de similarité, et 100 une correspondance exacte respectant la casse.
MatchedOriginalText : Texte d’origine mis en correspondance pour ce résultat. Renvoyé uniquement si le texte d’origine mis en correspondance diffère du texte d’entrée. Utilisé pour renvoyer le texte source d’une correspondance approximative. Non renvoyé pour les résultats de Microsoft Translator.
* `Rating`: Indique l’autorité de la personne décidant de la qualité. Les résultats de la traduction automatique ont la note 5. Les traductions d’origine anonyme ont généralement une note de 1 à 4, tandis que les traductions provenant de personnes ayant un degré d’autorité ont généralement une note de 6 à 10.
* `Count`: Nombre de fois où cette traduction avec cette note a été sélectionnée. La valeur est 0 pour la réponse traduite automatiquement.
* `TranslatedText`: Texte traduit.

### <a name="response-class-status-200"></a>Classe de réponse (état 200)
Objet `GetTranslationsResponse` au format décrit ci-dessus.

string

Type de contenu de réponse : application/xml
 
### <a name="parameters"></a>parameters

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|appid|(empty)|Requis. Si l’en-tête `Authorization` ou `Ocp-Apim-Subscription-Key` est utilisé, laissez le champ appid vide. Sinon, incluez une chaîne contenant `"Bearer" + " " + "access_token"`.|query|string|
|text|(empty)|Requis. Chaîne représentant le texte à traduire. La taille du texte ne peut pas dépasser 10 000 caractères.|query|string|
|from|(empty)|Requis. Chaîne représentant le code de langue du texte source.|query|string|
|to |(empty)    |Requis. Chaîne représentant le code de langue du texte cible.|query|string|
|maxTranslations|(empty)|Requis. Entier représentant le nombre maximal de traductions à renvoyer.|query|integer|
|Authorization| (empty)|Obligatoire si le champ `appid` ou l’en-tête `Ocp-Apim-Subscription-Key` ne sont pas spécifiés. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|string| en-tête|
|Ocp-Apim-Subscription-Key|(empty)  |Obligatoire si le champ `appid` ou l’en-tête `Authorization` ne sont pas spécifiés.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d’état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503|Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Remarques relatives à l’implémentation
Utilise la méthode `GetTranslationsArray` pour récupérer plusieurs traductions candidates pour plusieurs textes sources.

L’URI de demande est `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Le format du corps de la demande est le suivant.

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

`GetTranslationsArrayRequest` contient les éléments suivants :

* `AppId`: Requis. Si l’en-tête Authorization est utilisé, laissez le champ appid vide. Sinon, incluez une chaîne contenant `"Bearer" + " " + "access_token"`.
* `From`: Requis. Chaîne représentant le code de langue du texte source.
* `MaxTranslations`: Requis. Entier représentant le nombre maximal de traductions à renvoyer.
* `Options`: facultatif. Objet Options contenant les valeurs répertoriées ci-dessous. Tous sont facultatives. Par défaut, il s’agit des paramètres les plus courants. Les éléments spécifiés doivent être répertoriés dans l’ordre alphabétique.
    - Catégorie : Chaîne contenant la catégorie (domaine) de la traduction. La valeur par défaut est general.
    - `ContentType`: La seule option prise en charge (et donc par défaut) est text/plain.
    - `IncludeMultipleMTAlternatives` : indicateur booléen pour déterminer si plusieurs alternatives doivent être renvoyées à partir du moteur de traduction automatique. Les valeurs valides sont true et false (respecte la casse). Par défaut, la valeur est false et n’offre qu’une seule alternative. Définir l’indicateur sur true permet de générer des alternatives artificielles de traduction, entièrement intégrées avec l’infrastructure de traduction collaborative (CTF). La fonctionnalité permet de renvoyer des alternatives de traduction pour des phrases qui n’ont pas d’alternatives dans l’infrastructure de traduction collaborative, en ajoutant des alternatives artificielles à partir de la liste n-meilleures traductions du décodeur.
        - Les notes sont appliquées comme suit : (1) La meilleure traduction automatique obtient une note de 5. (2) Les alternatives de l’infrastructure de traduction collaborative reflètent l’autorité de leur auteur, de -10 à +10. (3) Les alternatives de traduction générées automatiquement (n-meilleures) ont la note 0, et leur degré de correspondance est 100.
        - Nombre d’Alternatives Le nombre d’alternatives renvoyé peut atteindre la valeur maxTranslations, mais peut être inférieur.
        - Paires de langues Cette fonctionnalité n’est pas disponible pour les traductions entre le chinois simplifié et le chinois traditionnel, dans les deux sens. Elle est disponible pour toutes les autres paires de langues que Microsoft Translator prend en charge.
* `State`: État utilisateur pour aider à mettre en corrélation la requête et la réponse. Le même contenu sera renvoyé dans la réponse.
* `Uri`: Filtrer les résultats sur cet URI. Si aucune valeur n’est définie, la valeur par défaut est all.
* `User`: Filtrer les résultats sur cet utilisateur. Si aucune valeur n’est définie, la valeur par défaut est all.
* `Texts`: Requis. Tableau contenant les textes à traduire. Toutes les chaînes doivent être dans la même langue. Les textes à traduire ne peuvent pas totaliser plus de 10 000 caractères. Le nombre maximal d’éléments de tableau est 10.
* `To`: Requis. Chaîne représentant le code de langue du texte cible.

Les éléments facultatifs peuvent être omis. Les éléments qui sont des enfants directs de `GetTranslationsArrayRequest` doivent être répertoriés dans l’ordre alphabétique.

La demande `Content-Type` doit être `text/xml`.

**Valeur renvoyée** : Le format de la réponse est le suivant.

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

* `Translations`: Tableau des correspondances trouvées, stockées dans des objets `TranslationMatch` (voir ci-dessous). Les traductions peuvent inclure de légères variantes du texte d’origine (correspondances approximatives). Les traductions seront triées : Correspondances à 100 % en premier, correspondances approximatives ensuite.
* `From`: Si la méthode ne spécifie pas de langue `From`, cette langue est le résultat de la détection automatique de la langue. Sinon, il s’agit de la langue source spécifiée.
* `State`: État utilisateur pour aider à mettre en corrélation la requête et la réponse. Contient la même valeur que celle fournie dans le paramètre `TranslateOptions`.

Un objet `TranslationMatch` comprend les éléments suivants :
* `Error`: Si une erreur s’est produite pour une chaîne d’entrée spécifique, le code d’erreur est stocké. Sinon, le champ est vide.
* `MatchDegree`: Le système établit des correspondances entre les phrases entrées et celles contenues dans le magasin, y compris des correspondances inexactes.  `MatchDegree` indique à quel point le texte d’entrée correspond au texte d’origine trouvé dans le magasin. La valeur renvoyée est comprise entre 0 et 100, où 0 est indique l’absence totale de similarité, et 100 une correspondance exacte respectant la casse.
* `MatchedOriginalText`: Texte d’origine mis en correspondance pour ce résultat. Renvoyé uniquement si le texte d’origine mis en correspondance diffère du texte d’entrée. Utilisé pour renvoyer le texte source d’une correspondance approximative. Non renvoyé pour les résultats de Microsoft Translator.
* `Rating`: Indique l’autorité de la personne décidant de la qualité. Les résultats de la traduction automatique ont la note 5. Les traductions d’origine anonyme ont généralement une note de 1 à 4, tandis que les traductions provenant de personnes ayant un degré d’autorité ont généralement une note de 6 à 10.
* `Count`: Nombre de fois où cette traduction avec cette note a été sélectionnée. La valeur est 0 pour la réponse traduite automatiquement.
* `TranslatedText`: Texte traduit.


### <a name="response-class-status-200"></a>Classe de réponse (état 200)

string

Type de contenu de réponse : application/xml
 
### <a name="parameters"></a>parameters

|Paramètre|Valeur|Description|Type de paramètre|Type de données|
|:--|:--|:--|:--|:--|
|Authorization  |(empty)    |Obligatoire si le champ `appid` ou l’en-tête `Ocp-Apim-Subscription-Key` ne sont pas spécifiés. Jeton d’autorisation : `"Bearer" + " " + "access_token"`.|en-tête|string|
|Ocp-Apim-Subscription-Key|(empty)  |Obligatoire si le champ `appid` ou l’en-tête `Authorization` ne sont pas spécifiés.|en-tête|string|

### <a name="response-messages"></a>Messages de réponse

|Code d’état HTTP|Motif|
|:--|:--|
|400    |Demande incorrecte. Vérifiez les paramètres d’entrée et la réponse d’erreur détaillée.|
|401    |Informations d’identification non valides.|
|500    |Erreur de serveur. Si l’erreur persiste, faites-le nous savoir. Veuillez nous fournir la date et l’heure approximatives de la demande, ainsi que l’ID de demande, incluses dans l’en-tête de réponse `X-MS-Trans-Info`.|
|503    |Service temporairement indisponible. Veuillez réessayer et faites-nous savoir si l’erreur persiste.|

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Migrer vers l’API de traduction de texte Translator Text v3](../migrate-to-v3.md)










