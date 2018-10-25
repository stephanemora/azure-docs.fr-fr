---
title: Informations de référence sur l’aperçu d’URL du projet
titlesuffix: Azure Cognitive Services
description: Permet d’obtenir des informations de référence sur le point de terminaison de l’aperçu d’URL du projet.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: 3416fd9bc63c48e976d0b00f42ec9f8119a40eb8
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870806"
---
# <a name="project-url-preview-v7-reference"></a>Informations de référence sur l’aperçu d’URL du projet v7

L’aperçu d’URL offre une brève description des ressources web pour les billets de blog, les discussions des forums, les pages d’aperçu, etc.  L’URL peut être n’importe quel type de ressource Internet : une page web, des actualités, une image ou une vidéo. La requête doit être une URL absolue avec un schéma de type http ou https. Les URL relatives ou autres schémas du type ftp:// ne sont pas pris en charge.

Les applications qui utilisent l’aperçu d’URL envoient des requêtes web au point de terminaison. L’URL quant à elle sert à afficher l’aperçu dans un paramètre de requête.  Cette requête doit indiquer l’en-tête *Ocp-Apim-Subscription-Key*.   

Il est possible d’analyser la réponse JSON pour rechercher les informations d’aperçu : nom, description de la ressource, indicateur *isFamilyFriendly* et liens donnant accès à une image représentative et à la ressource en ligne complète.

Vous devez utiliser les données de l’aperçu d’URL uniquement pour afficher des extraits de code et des images sous forme de vignettes (avec un lien hypertexte vers les sites sources) dans une URL créée par l’utilisateur final et partagée sur les réseaux sociaux, un chatbot ou toute offre similaire. Vous ne devez en aucun cas copier, stocker ou mettre en cache les données que vous recevez d’un projet d’aperçu d’URL. Vous devez répondre à toutes les requêtes visant à désactiver les aperçus éventuellement reçus des propriétaires de site web ou de contenu.

## <a name="endpoint"></a>Point de terminaison
Pour demander les résultats de l’aperçu d’URL, envoyez une requête au point de terminaison suivant. Utilisez les en-têtes et les paramètres d’URL pour définir d’autres spécifications.

Point de terminaison GET : 
````
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

````

La requête doit utiliser le protocole HTTPS et inclure le paramètre de requête suivant :

 q : requête identifiant l’URL dont vous voulez afficher l’aperçu 

Les sections suivantes fournissent des détails techniques sur les objets de la réponse, les paramètres de la requête et les en-têtes qui affectent les résultats de la recherche. 
  
Pour plus d’informations sur les en-têtes que doivent comporter les demandes, voir [En-têtes](#headers).  
  
Pour plus d’informations sur les paramètres de requête que doivent comporter les demandes, voir [Paramètres de la requête](#query-parameters).  
  
Pour plus d’informations sur les objets JSON que comporte la réponse, voir [Objets de la réponse](#response-objects).

La longueur maximale de l’URL de la requête est de 2 048 caractères. Pour que votre URL ne dépasse pas la limite, la longueur maximale de vos paramètres de requête doit être inférieure à 1 500 caractères. Si l’URL dépasse 2 048 caractères, le serveur retourne une erreur 404 (Introuvable).  

Pour plus d’informations sur l’utilisation autorisée et l’affichage des résultats, voir [Conditions d’utilisation et d’affichage](use-display-requirements.md). 

> [!NOTE]
> Certains en-têtes de demande pertinents pour d’autres API de recherche n’affectent pas l’aperçu d’URL :
> - Pragma : l’appelant n’a aucun contrôle sur l’utilisation éventuelle d’un cache par l’aperçu d’URL.
> - User-Agent : pour l’instant, l’API d’aperçu d’URL ne fournit pas des réponses différentes selon que les appels proviennent de PC, d’ordinateurs portables ou d’appareils mobiles.

> De même, certains paramètres ne sont actuellement pas pertinents pour l’API d’aperçu d’URL, mais ils pourront être utilisés à l’avenir dans le but d’améliorer la globalisation. 
 
## <a name="headers"></a>headers  
Voici les en-têtes possibles d’une demande et d’une réponse.  
  
|En-tête|Description|  
|------------|-----------------|   
|<a name="market" />BingAPIs-Market|En-tête de réponse.<br /><br /> Marché utilisé par la demande. Le format est \<code de langue\>-\<code du pays\>. Par exemple, en-US.|  
|<a name="traceid" />BingAPIs-TraceId|En-tête de réponse.<br /><br /> ID de l’entrée du journal contenant les détails de la demande. Lorsqu’une erreur se produit, capturez cet ID. Si vous ne parvenez pas à identifier ou à résoudre le problème, précisez cet ID avec les autres informations envoyées à l’équipe de support.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|En-tête de demande requis.<br /><br /> Clé d’abonnement que vous avez reçue lorsque vous vous êtes inscrit à ce service dans [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="clientid" />X-MSEdge-ClientID|En-tête de demande et de réponse facultatif.<br /><br /> Bing utilise cet en-tête pour garantir aux utilisateurs un comportement cohérent d’un appel d’API Bing à l’autre. Bing propose souvent de nouvelles fonctionnalités et améliorations, et se sert de l’ID client comme d’une clé pour attribuer le trafic aux différentes versions d’évaluation. Si vous n’assignez pas le même ID client à un utilisateur d’une demande à l’autre, Bing est susceptible d’affecter cet utilisateur à plusieurs versions d’évaluation en conflit, ce qui risque de nuire à l’expérience utilisateur. Par exemple, si la deuxième demande comporte une attribution de version d’évaluation différente de la première, l’expérience se révélera peut-être inattendue. De même, Bing peut utiliser l’ID client pour personnaliser les résultats web d’après l’historique de recherche correspondant à cet ID et ainsi proposer à l’utilisateur une expérience plus riche.<br /><br /> Bing utilise également cet en-tête pour aider à améliorer le classement des résultats en analysant l’activité générée par un ID client. Les améliorations de la pertinence vous permettent d’obtenir des résultats d’API Bing de meilleure qualité et en retour, des taux de clic plus élevés pour le consommateur de l’API.<br /><br />Voici les règles d’utilisation de base qui s’appliquent à cet en-tête.<br /><ul><li>Chaque utilisateur de votre application sur l’appareil doit avoir un ID client unique, généré par Bing.<br /><br/>Si vous n’insérez pas cet en-tête dans la demande, Bing génère un ID et le retourne dans l’en-tête de réponse X-MSEdge-ClientID. La première fois que l’utilisateur utilise votre application sur cet appareil est la seule fois où vous ne devez PAS inclure cet en-tête dans la demande.<br /><br/></li><li>Utilisez l’ID client pour chaque demande d’API Bing que votre application effectue pour cet utilisateur sur l’appareil.<br /><br/></li><li>**ATTENTION :** Vérifiez que cet ID client ne peut pas être associé à des informations authentifiables sur le compte d’utilisateur.</li><br/><li>Conservez l’ID client. Pour conserver l’identifiant dans une application de navigateur, utilisez un cookie HTTP persistant qui garantit l’utilisation de cet identifiant dans toutes les sessions. N’utilisez pas de cookie de session. Dans le cas d’autres applications, comme des applications mobiles, utilisez le stockage persistant de l’appareil pour conserver cet identifiant.<br /><br/>La prochaine fois que l’utilisateur utilisera votre application sur cet appareil, vous récupérerez l’ID client que vous aurez conservé.</li></ul><br /> **REMARQUE :** Les réponses de Bing ne comportent pas forcément cet en-tête. Si elles l’incluent, capturez l’ID client et utilisez-le pour toutes les demandes Bing suivantes concernant l’utilisateur sur cet appareil.<br /><br /> **REMARQUE :** Si vous insérez l’en-tête X-MSEdge-ClientID, n’incluez pas les cookies dans la demande.|  
|<a name="clientip" />X-MSEdge-ClientIP|En-tête de demande facultatif.<br /><br /> Adresse IPv4 ou IPv6 de l’appareil client. L’adresse IP est utilisée pour découvrir l’emplacement de l’utilisateur. Bing utilise les informations d’emplacement pour déterminer le comportement de recherche approprié.<br /><br />  N’obfusquez pas l’adresse (par exemple, en donnant au dernier octet la valeur 0). En obfusquant l’adresse, vous provoquez un éloignement de l’appareil, soit le renvoi possible par Bing de résultats erronés.|  
<br /><br /></li></ul>   

## <a name="query-parameters"></a>Paramètres de requête  
La demande peut comporter les paramètres de requête suivants. Consultez la colonne Requis pour savoir lesquels sont obligatoires. Vous devez coder au format URL les paramètres de la requête. La requête doit être une URL absolue avec un schéma de type http ou https. Nous ne prenons pas en charge les URL relatives ou autres schémas du type ftp://.
  
  
|NOM|Valeur|type|Obligatoire|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Marché d’où proviennent les résultats. <br /><br />Pour obtenir une liste des valeurs de marché possibles, consultez la section [Codes de marché](#market-codes).<br /><br /> **REMARQUE :** actuellement, l’API d’aperçu d’URL prend uniquement en charge la région des États-Unis et la langue anglaise.<br /><br />|Chaîne|Oui|  
|<a name="query" />q|URL servant à afficher l’aperçu.|Chaîne|Oui|  
|<a name="responseformat" />responseFormat|Type de média à utiliser pour la réponse. Voici les valeurs possibles. Elles ne sont pas sensibles à la casse.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> La valeur par défaut est JSON. Pour plus d’informations sur les objets JSON que contient la réponse, voir [Objets de la réponse](#response-objects).<br /><br />  Si vous spécifiez JsonLd, le corps de la réponse comporte les objets JSON-LD contenant les résultats de la recherche. Pour plus d’informations sur la spécification JSON-LD, voir [JSON-LD](http://json-ld.org/).|Chaîne|Non |
|<a name="safesearch"/>safeSearch|Le contenu pour adultes jugé non conforme, ou le contenu piraté, est bloqué avec pour code d’erreur 400 et aucun indicateur *isFamilyFriendly* n’est renvoyé. <p>Voici par contre le comportement concernant le contenu pour adultes considéré comme légal. Le système renvoie le code d’état 200 et l’indicateur *isFamilyFriendly* est défini sur false.<ul><li>safeSearch=strict : le titre, la description, l’URL et l’image ne seront pas renvoyés.</li><li>safeSearch=moderate : vous obtenez le titre, l’URL et la description, mais pas l’image descriptive.</li><li>safeSearch=off : vous obtenez tous les objets/éléments de la réponse (titre, URL, description et image).</li></ul> |Chaîne|Non requis. </br> La valeur par défaut est safeSearch=strict.| 

## <a name="response-objects"></a>Objets de la réponse  
Le schéma de réponse est soit [WebPage] soit ErrorResponse, comme dans l’API Recherche Web. Si la demande échoue, l’objet de niveau supérieur est l’objet [ErrorResponse](#errorresponse).


|Object|Description|  
|------------|-----------------|  
|[WebPage](#webpage)|Objet JSON de niveau supérieur contenant les attributs de l’aperçu.|  

  
### <a name="error"></a>Error  
Définit l’erreur qui s’est produite.  
  
|Élément|Description|type|  
|-------------|-----------------|----------|  
|<a name="error-code" />code|Code d’erreur identifiant la catégorie de l’erreur. Pour connaître la liste des codes possibles, voir [Codes d’erreur](#error-codes).|Chaîne|  
|<a name="error-message" />message|Description de l’erreur.|Chaîne|  
|<a name="error-moredetails" />moreDetails|Description de l’erreur comportant des informations supplémentaires.|Chaîne|  
|<a name="error-parameter" />parameter|Paramètre de requête de la demande ayant provoqué l’erreur.|Chaîne|  
|<a name="error-subcode" />subCode|Code identifiant l’erreur. Par exemple, si `code` est InvalidRequest, `subCode` peut être ParameterInvalid ou ParameterInvalidValue. |Chaîne|  
|<a name="error-value" />value|Valeur du paramètre de requête qui n’était pas valide.|Chaîne|  
  

### <a name="errorresponse"></a>ErrorResponse  
Objet de niveau supérieur figurant dans la réponse en cas d’échec de la demande.  
  
|NOM|Valeur|type|  
|----------|-----------|----------|  
|_type|Indicateur de type.|Chaîne|  
|<a name="errors" />errors|Liste des erreurs qui décrivent les raisons pour lesquelles la demande a échoué.|[Error](#error)[]|   
  

### <a name="webpage"></a>WebPage  
Définit les informations concernant la page web dans l’aperçu.  
  
|NOM|Valeur|type|  
|----------|-----------|----------|
|Nom|Titre de la page (pas nécessairement le titre HTML).|Chaîne|
|url|URL réellement analysée (potentiellement avec redirections de la demande).|Chaîne|  
|description|Brève description de la page et de son contenu.|Chaîne|  
|isFamilyFriendly|Précision maximale pour les éléments figurant dans l’index web ; les extractions en temps réel effectuent cette détection d’après l’URL uniquement, et non d’après le contenu de la page.|booléenne|
|primaryImageOfPage/contentUrl|URL d’une image représentative à inclure dans l’aperçu.|Chaîne| 


### <a name="identifiable"></a>Identifiable
|NOM|Valeur|type|  
|-------------|-----------------|----------|
|id|Identificateur de ressource.|Chaîne|
 

## <a name="error-codes"></a>Codes d’erreur

Voici les codes d’état HTTP qu’une demande peut retourner.  
  
|Code d’état|Description|  
|-----------------|-----------------|  
|200|Vous avez réussi !|  
|400|L’un des paramètres de requête est manquant ou non valide.| 
|400|ServerError, subCode ResourceError : l’URL demandée n’a pas pu être atteinte.|
|400|ServerError, subCode ResourceError : l’URL demandée n’a pas renvoyé de code attestant de la réussite (y compris si elle renvoie une erreur HTTP 404).|
|400|InvalidRequest, subCode Blocked : l’URL demandée contient peut-être du contenu réservé aux adultes et a été bloquée.| 
|401|La clé d’abonnement est manquante ou non valide.|  
|403|L’utilisateur est authentifié (par exemple, il a utilisé une clé d’abonnement valide), mais il n’est pas autorisé à accéder à la ressource demandée.<br /><br /> Bing peut également retourner cet état si l’appelant a dépassé son quota mensuel de requêtes.|  
|410|La demande a utilisé le protocole HTTP au lieu du protocole HTTPS. HTTPS est le seul protocole pris en charge.|  
|429|L’appelant a dépassé son quota de requêtes par seconde.|  
|500|Événement serveur inattendu.|

Si la demande échoue, la réponse comporte un objet [ErrorResponse](#errorresponse) qui contient une liste d’objets [Error](#error) décrivant l’origine de l’erreur. Si l’erreur est liée à un paramètre, le champ `parameter` identifie celui qui pose problème. De même, si l’erreur est liée à une valeur de paramètre, le champ `value` identifie la valeur non valide.

```json
{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidRequest", 
      "subCode": "ParameterMissing", 
      "message": "Required parameter is missing.", 
      "parameter": "q" 
    }
  ]
}

{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidAuthorization", 
      "subCode": "AuthorizationMissing", 
      "message": "Authorization is required.", 
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

Voici les valeurs possibles de code d’erreur et de sous-code d’erreur.

|Code|SubCode|Description
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Le code d’état HTTP est 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqué|Bing retourne InvalidRequest à chaque fois que la demande comporte une partie non valide. Par exemple, un paramètre obligatoire est manquant ou une valeur de paramètre n’est pas valide.<br/><br/>Si l’erreur est ParameterMissing ou ParameterInvalidValue, le code d’état HTTP est 400.<br/><br/>Si vous utilisez le protocole HTTP au lieu du protocole HTTPS, Bing retourne HttpNotAllowed, et le code d’état HTTP est 410.
|RateLimitExceeded|Aucun sous-code|Bing retourne RateLimitExceeded chaque fois que vous dépassez votre quota de requêtes par seconde (QPS) ou par mois (QPM).<br/><br/>Si vous dépassez votre QPS, Bing retourne le code d’état HTTP 429 ; si vous dépassez votre QPM, Bing retourne le code d’état 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing retourne InvalidAuthorization s’il ne parvient pas à identifier l’appelant. Par exemple, l’en-tête `Ocp-Apim-Subscription-Key` est manquant ou la clé d’abonnement n’est pas valide.<br/><br/>Il y a redondance si plusieurs méthodes d’authentification sont spécifiées.<br/><br/>Si l’erreur est InvalidAuthorization, le code d’état HTTP est 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing retourne InsufficientAuthorization lorsque l’appelant n’est pas autorisé à accéder à la ressource. Cela peut se produire si la clé d’abonnement a été désactivée ou a expiré. <br/><br/>Si l’erreur est InsufficientAuthorization, le code d’état HTTP est 403.

## <a name="next-steps"></a>Étapes suivantes
- [Démarrage rapide C#](csharp.md)
- [Démarrage rapide Java](java-quickstart.md)
- [Démarrage rapide JavaScript](javascript.md)
- [Démarrage rapide Node](node-quickstart.md)
- [Démarrage rapide Python](python-quickstart.md)

