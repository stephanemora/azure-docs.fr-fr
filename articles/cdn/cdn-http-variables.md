---
title: Variables HTTP pour le moteur de règles Azure CDN | Microsoft Docs
description: Les variables HTTP vous permettent de récupérer les métadonnées de requête et de réponse HTTP.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 53ad0c516547e17801bd57c2fd6b0d1704383797
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593812"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Variables HTTP pour le moteur de règles Azure CDN
Les variables HTTP vous permettent de récupérer les métadonnées de requête et de réponse HTTP. Ces métadonnées peuvent ensuite servir à modifier une requête ou une réponse de façon dynamique. L’utilisation de variables HTTP est limitée aux fonctionnalités de moteur de règles suivantes :

- [Cache-Key Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modify Client Request Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modify Client Response Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL Redirect](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Définitions
Le tableau suivant décrit les variables HTTP prises en charge. Une valeur vide est retournée lorsque les métadonnées géographiques (par exemple, code postal) ne sont pas disponibles pour une requête particulière.


| Nom | Variable | Description | Exemple de valeur |
| ---- | -------- | ----------- | ------------ |
| ASN (demandeur) | %{geo_asnum} | Indique le numéro de système autonome du demandeur. <br /><br />**Déprécié :** %{virt_dst_asnum}. <br />Cette variable est dépréciée au profit de %{geo_asnum}. Bien que les règles utilisant cette variable dépréciée continuent à fonctionner, vous devez les mettre à jour pour utiliser la nouvelle variable. | AS15133 |
| Ville (demandeur) | %{geo_city} | Indique la ville du demandeur. | Los Angeles |
| Continent (demandeur) | %{geo_continent} | Indique le continent du demandeur au travers de son abréviation. <br />Les valeurs autorisées sont : <br />AF : Afrique<br />AS : Asie<br />EU : Europe<br />NA : Amérique du Nord<br />OC : Océanie<br />SA : Amérique du Sud<br /><br />**Déprécié :** %{virt_dst_continent}. <br />Cette variable est dépréciée au profit de %{geo_continent}. <br />Bien que les règles utilisant cette variable dépréciée continuent à fonctionner, vous devez les mettre à jour pour utiliser la nouvelle variable.| N/A |
| Valeur du cookie | %{cookie_Cookie} | Retourne la valeur correspondant à la clé de cookie identifiée par le terme Cookie. | Exemple d’utilisation : <br />%{cookie__utma}<br /><br />Exemple de valeur :<br />111662281.2.10.1222100123 |
| Pays (demandeur) | %{geo_country} | Indique le pays d’origine du demandeur au travers de son code pays. <br />**Déprécié :** %{virt_dst_country}. <br /><br />Cette variable est dépréciée au profit de %{geo_country}. Bien que les règles utilisant cette variable dépréciée continuent à fonctionner, vous devez les mettre à jour pour utiliser la nouvelle variable. | FR |
| Designated Market Area (demandeur) | %{geo_dma_code} |Spécifie le marché des médias du demandeur au moyen de son code de région. <br /><br />Ce champ est uniquement applicable aux requêtes provenant des États-Unis.| 745 |
| Méthode de requête HTTP | %{request_method} | Indique la méthode de requête HTTP. | GET |
| Code d’état HTTP | %{status} | Indique le code d’état HTTP de la réponse. | 200 |
| Adresse IP (demandeur) | %{virt_dst_addr} | Indique l’adresse IP du demandeur. | 192.168.1.1 |
| Latitude (demandeur) | %{geo_latitude} | Indique la latitude du demandeur. | 34.0995 |
| Longitude (demandeur) | %{geo_longitude} | Indique la longitude du demandeur. | -118.4143 |
| Metropolitan Statistical Area (demandeur) | %{geo_metro_code} | Indique la zone métropolitaine du demandeur. <br /><br />Ce champ est uniquement applicable aux requêtes provenant des États-Unis.<br />| 745 |
| Port (demandeur) | %{virt_dst_port} | Indique le port éphémère du demandeur. | 55885 |
| Code postal (demandeur) | %{geo_postal_code} | Indique le code postal du demandeur. | 90210 |
| Chaîne de requête trouvée | %{is_args} | La valeur de cette variable change selon que la demande contient une chaîne de requête.<br /><br />- Chaîne de requête trouvée : ?<br />- Aucune chaîne de requête : NULL | ? |
| Paramètre de chaîne de requête trouvé | %{is_amp} | La valeur de cette variable change selon que la demande contient au moins un paramètre de chaîne de requête.<br /><br />- Paramètre trouvé : &<br />- Aucun paramètre : NULL | & |
| Valeur de paramètre de chaîne de requête | %{arg_&lt;parameter&gt;} | Retourne la valeur correspondant au paramètre de chaîne de requête identifié par le terme &lt;parameter&gt;. | Exemple d’utilisation : <br />%{arg_language}<br /><br />Exemple de paramètre de chaîne de requête : <br />?language=en<br /><br />Exemple de valeur : en |
| Valeur de chaîne de requête | %{query_string} | Indique la valeur de chaîne de requête entière définie dans l’URL de requête. |key1=val1&key2=val2&key3=val3 |
| Domaine du point d’accès | %{referring_domain} | Indique le domaine défini dans l’en-tête de requête Referrer. | <www.google.com> |
| Région (demandeur) | %{geo_region} | Indique la région du demandeur (par exemple, état ou province) au travers de son abréviation alphanumérique. | CA |
| Valeur d’en-tête de requête | %{http_RequestHeader} | Retourne la valeur correspondant à l’en-tête de requête identifié par le terme RequestHeader. <br /><br />Si le nom de l’en-tête de requête contient un tiret (par exemple, User-Agent), remplacez-le par un trait de soulignement (par exemple, User_Agent).| Exemple d’utilisation : %{http_Connection}<br /><br />Exemple de valeur : Keep-Alive | 
| Hôte de la requête | %{host} | Indique l’hôte défini dans l’URL de requête. | <www.mondomaine.com> |
| Protocole de requête | %{request_protocol} | Indique le protocole de la requête. | HTTP/1.1 |
| Modèle de requête | %{scheme} | Indique le schéma de la requête. |http |
| URI de requête (relatif) | %{request_uri} | Indique le chemin d’accès relatif, y compris la chaîne de requête, défini dans l’URI de requête. | /marketing/foo.js?loggedin=true |
| URI de requête (relatif sans chaîne de requête) | %{uri} | Indique le chemin d’accès relatif vers le contenu demandé. <br /><br/>Informations essentielles :<br />- Ce chemin d’accès relatif exclut la chaîne de requête.<br />- Ce chemin d’accès relatif reflète les réécritures d’URL. Une URL est réécrite dans les conditions suivantes :<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Fonctionnalité de réécriture d’URL : cette fonctionnalité réécrit le chemin d’accès relatif défini dans l’URI de requête.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge CNAME URL (URL du CNAME de périmètre) : ce type de requête est réécrit sur l’URL du CDN correspondant. |/800001/corigin/rewrittendir/foo.js |
| URI de demande | %{request} | Décrit la requête. <br />Syntaxe : &lt;méthode HTTP&gt; &lt;chemin d’accès relatif&gt; &lt;protocole HTTP&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Valeur d’en-tête de réponse | %{resp_&lt;ResponseHeader&gt;} | Retourne la valeur correspondant à l’en-tête de réponse identifié par le terme &lt;ResponseHeader&gt;. <br /><br />Si le nom de l’en-tête de réponse contient un tiret (par exemple, User-Agent), remplacez-le par un trait de soulignement (par exemple, User_Agent). | Exemple d’utilisation : %{resp_Content_Length}<br /><br />Exemple de valeur : 100 |

## <a name="usage"></a>Usage
Le tableau suivant décrit la syntaxe permettant de spécifier les variables HTTP.


| Syntaxe | Exemples | Description |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Utilisez la syntaxe suivante pour obtenir la valeur entière correspondant à la variable &lt;HTTPVariable&gt; spécifiée. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Utilisez la syntaxe suivante pour définir la casse de la valeur entière correspondant à la variable &lt;HTTPVariableDelimiter&gt; spécifiée. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Utilisez une expression régulière pour &lt;HTTPVariableDelimiterExpression&gt; afin de remplacer, supprimer ou manipuler la valeur d’une variable HTTP. |

Les noms de variables HTTP prennent uniquement en charge des traits de soulignement et les caractères alphabétiques. Convertissez les caractères non pris en charge en traits de soulignement.

## <a name="delimiter-reference"></a>Référence de délimiteur
Un délimiteur peut être spécifié après une variable HTTP pour apporter l’un des effets suivants :

- Transformer la valeur associée à la variable.

     Exemple : Convertit la valeur entière en minuscules.

- Supprimer la valeur associée à la variable.

- Manipuler la valeur associée à la variable.

     Exemple : Utilise des expressions régulières pour modifier la valeur associée à la variable HTTP.

Les délimiteurs sont décrits dans le tableau suivant.

| Délimiteur | Description |
| --------- | ----------- |
| := | Indique qu’une valeur par défaut sera assignée à la variable lorsqu’elle est : <br />- manquante ; <br />- définie sur Null. |
| :+ | Indique qu’une valeur par défaut sera assignée à la variable lorsqu’une valeur lui aura été assignée. |
| : | Indique qu’une sous-chaîne de la valeur assignée à la variable sera développée. |
| # | Indique que le modèle spécifié après ce délimiteur doit être supprimé lorsqu’il se trouve au début de la valeur associée à la variable. |
| % | Indique que le modèle spécifié après ce délimiteur doit être supprimé lorsqu’il se trouve à la fin de la valeur associée à la variable. <br />Cette définition s’applique uniquement lorsque le symbole % est utilisé comme délimiteur. |
| / | Délimite une variable HTTP ou un modèle. |
| // | Recherche et remplace toutes les instances du modèle spécifié. |
| /= | Recherche, copie et réécrit toutes les occurrences du modèle spécifié. |
| . | Convertit la valeur associée à la variable HTTP en minuscules. |
| ^ | Convertit la valeur associée à la variable HTTP en majuscules. |
| ,, | Convertit toutes les instances du caractère spécifié dans la valeur associée à la variable HTTP en minuscules. |
| ^^ | Convertit toutes les instances du caractère spécifié dans la valeur associée à la variable HTTP en majuscules. |

## <a name="exceptions"></a>Exceptions
Le tableau suivant décrit les circonstances dans lesquelles le texte spécifié n’est pas traité comme une variable HTTP.

| Condition | Description | Exemples |
| --------- | ----------- | --------|
| Échappement du symbole % | Le symbole de pourcentage peut faire l’objet d’un échappement au moyen d’une barre oblique inverse. <br />L’exemple de valeur à droite sera considéré comme une valeur littérale et non comme une variable HTTP.| \%{host} |
| Unknown variables | An empty string is always returned for unknown variables. | %{unknown_variable} |
| Invalid characters or syntax | Variables that contain invalid characters or syntax are treated as literal values. <br /><br />Example #1: The specified value contains an invalid character (for example, -). <br /><br />Example #2: The specified value contains a double set of curly braces. <br /><br />Example #3: The specified value is missing a closing curly brace.<br /> | Example #1: %{resp_user-agent} <br /><br />Example #2: %{{host}} <br /><br />Example #3: %{host |
| Missing variable name | A NULL value is always returned when a variable is not specified. | %{} |
| Trailing characters | Characters that trail a variable are treated as literal values. <br />The sample value to the right contains a trailing curly brace that will be treated as a literal value. | %{host}} |

## Setting default header values
A default value can be assigned to a header when it meets any of the following conditions:
- Missing/unset
- Set to NULL.

The following table describes how to define a default value.

| Condition | Syntax | Example | Description |
| --------- | ------ | --------| ----------- |
| Set a header to a default value when it meets any of the following conditions: <br /><br />- Missing Header <br /><br />- Header value is set to NULL.| %{Variable:=Value} | %{http_referrer:=unspecified} | The Referrer header will only be set to *unspecified* when it is either missing or set to NULL. No action will take place if it has been set. |
| Set a header to a default value when it is missing. | %{Variable=Value} | %{http_referrer=unspecified} | The Referrer header will only be set to *unspecified* when it is missing. No action will take place if it has been set. |
| Set the header to a default value when it does not meet any of the following conditions: <br /><br />- Missing<br /><br /> - Set to NULL. | %{Variable:+Value} | %{http_referrer:+unspecified} | The Referrer header will only be set to *unspecified* when a value has been assigned to it. No action will take place if it is either missing or set to NULL. |

## Manipulating variables
Variables can be manipulated in the following ways:

- Expanding substrings
- Removing patterns

### Substring expansion
By default, a variable will expand to its full value. Use the following syntax to only expand a substring of the variable's value.

`%<Variable>:<Offset>:<Length>}`

Key information:

- The value assigned to the Offset term determines the starting character of the substring:

     - Positive: The starting character of the substring is calculated from the first character in the string.
     - Zero: The starting character of the substring is the first character in the string.
     - Negative: The starting character of the substring is calculated from the last character in the string.

- The length of the substring is determined by the *Length* term:

     - Omitted: Omitting the Length term allows the substring to include all characters between the starting character and the end of the string.
     - Positive: Determines the length of the substring from the starting character to the right.
     - Negative: Determines the length of the substring from the starting character to the left.

#### Example:

The following example relies on the following sample request URL:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

The following string demonstrates various methods for manipulating variables:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Based on the sample request URL, the above variable manipulation will produce the following value:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### Pattern removal
Text that matches a specific pattern can be removed from either the beginning or the end of a variable's value.

| Syntax | Action |
| ------ | ------ |
| %{Variable#Pattern} | Remove text when the specified pattern is found at the beginning of a variable's value. |
| %{Variable%Pattern} | Remove text when the specified pattern is found at the end of a variable's value. |

#### Example:

In this sample scenario, the *request_uri* variable is set to:

`/800001/myorigin/marketing/product.html?language=en-US`

The following table demonstrates how this syntax works.

| Sample syntax | Results | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Because the variable starts with the pattern, it was replaced. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Because the variable doesn't end with the pattern, there was no change.|

### Find and replace
The find and replace syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/Find/Replace} | Find and replace first occurrence of the specified pattern. |
| %{Variable//Find/Replace} | Find and replace all occurrences of the specified pattern. |
| %{Variable^} |Convert the entire value to uppercase. |
| %{Variable^Find} | Convert the first occurrence of the specified pattern to uppercase. |
| %{Variable,} | Convert the entire value to lowercase. |
| %{Variable,Find} | Convert the first occurrence of the specified pattern to lowercase. |

### Find and rewrite
For a variation on find and replace, use the text that matches the specified pattern when rewriting it. The find and rewrite syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Find, copy, and rewrite all occurrences of the specified pattern. |
| %{Variable/^Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the start of the variable. |
| %{Variable/$Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the end of the variable. |
| %{Variable/Find} | Find and delete all occurrences of the specified pattern. |

Key information:

- Expand text that matches the specified pattern by specifying a dollar sign followed by a whole integer (for example, $1).

- Multiple patterns can be specified. The order in which the pattern is specified determines the integer that will be assigned to it. In the following example, the first pattern matches "www.," the second pattern matches the second-level domain, and the third pattern matches the top-level domain:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- The rewritten value can consist of any combination of text and these placeholders.

    In the previous example, the hostname is rewritten to `cdn.$2.$3:80` (for example, cdn.mydomain.com:80).

- The case of a pattern placeholder (for example, $1) can be modified through the following flags:
     - U: Uppercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Lowercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- An operator must be specified before the pattern. The specified operator determines the pattern-capturing behavior:

     - `=`: Indicates that all occurrences of the specified pattern must be captured and rewritten.
     - `^`: Indicates that only text that starts with the specified pattern will be captured.
     - `$`: Indicates that only text that ends with the specified pattern will be capture.
 
- If you omit the */Rewrite* value, the text that matches the pattern is deleted.

\`{host}le: HTTP variables for Azure CDN rules engine | Microsoft Docs
description: HTTP variables allow you to retrieve HTTP request and response metadata.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''

ms.assetid: 
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus


---
# HTTP variables for Azure CDN rules engine
HTTP variables provide the means through which you can retrieve HTTP request and response metadata. This metadata can then be used to dynamically alter a request or a response. The use of HTTP variables is restricted to the following rules engine features:

- [Cache-Key Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modify Client Request Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modify Client Response Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL Redirect](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## Definitions
The following table describes the supported HTTP variables. A blank value is returned when GEO metadata (for example, postal code) is unavailable for a particular request.


| Name | Variable | Description | Sample value |
| ---- | -------- | ----------- | ------------ |
| ASN (Requester) | %{geo_asnum} | Indicates the requester's AS number. <br /><br />**Deprecated:** %{virt_dst_asnum}. <br />This variable has been deprecated in favor of %{geo_asnum}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | AS15133 |
| City (Requester) | %{geo_city} | Indicates the requester's city. | Los Angeles |
| Continent (Requester) | %{geo_continent} | Indicates the requester's continent through its abbreviation. <br />Valid values are: <br />AF: Africa<br />AS: Asia<br />EU: Europe<br />NA: North America<br />OC: Oceania<br />SA: South America<br /><br />**Deprecated:** %{virt_dst_continent}. <br />This variable has been deprecated in favor of %{geo_continent}. <br />Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable.| N/A |
| Cookie Value | %{cookie_Cookie} | Returns the value corresponding to the cookie key identified by the Cookie term. | Sample Usage: <br />%{cookie__utma}<br /><br />Sample Value:<br />111662281.2.10.1222100123 |
| Country (Requester) | %{geo_country} | Indicates the requester's country of origin through its country code. <br />**Deprecated:** %{virt_dst_country}. <br /><br />This variable has been deprecated in favor of %{geo_country}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | US |
| Designated Market Area (Requester) | %{geo_dma_code} |Indicates the requester's media market by its region code. <br /><br />This field is only applicable to requests that originate from the United States.| 745 |
| HTTP Request Method | %{request_method} | Indicates the HTTP request method. | GET |
| HTTP Status Code | %{status} | Indicates the HTTP status code for the response. | 200 |
| IP Address (Requester) | %{virt_dst_addr} | Indicates the requester's IP address. | 192.168.1.1 |
| Latitude (Requester) | %{geo_latitude} | Indicates the requester's latitude. | 34.0995 |
| Longitude (Requester) | %{geo_longitude} | Indicates the requester's longitude. | -118.4143 |
| Metropolitan Statistical Area (Requester) | %{geo_metro_code} | Indicates the requester's metropolitan area. <br /><br />This field is only applicable to requests that originate from the United States.<br />| 745 |
| Port (Requester) | %{virt_dst_port} | Indicates the requester's ephemeral port. | 55885 |
| Postal Code (Requester) | %{geo_postal_code} | Indicates the requester's postal code. | 90210 |
| Query String Found | %{is_args} | The value for this variable varies according to whether the request contains a query string.<br /><br />- Query String Found: ?<br />- No Query String: NULL | ? |
| Query String Parameter Found | %{is_amp} | The value for this variable varies according to whether the request contains at least one query string parameter.<br /><br />- Parameter Found: &<br />- No Parameters: NULL | & |
| Query String Parameter Value | %{arg_&lt;parameter&gt;} | Returns the value corresponding to the query string parameter identified by the &lt;parameter&gt; term. | Sample Usage: <br />%{arg_language}<br /><br />Sample Query String Parameter: <br />?language=en<br /><br />Sample Value: en |
| Query String Value | %{query_string} | Indicates the entire query string value defined in the request URL. |key1=val1&key2=val2&key3=val3 |
| Referrer Domain | %{referring_domain} | Indicates the domain defined in the Referrer request header. | <www.google.com> |
| Region (Requester) | %{geo_region} | Indicates the requester's region (for example, state or province) through its alphanumeric abbreviation. | CA |
| Request Header Value | %{http_RequestHeader} | Returns the value corresponding to the request header identified by the RequestHeader term. <br /><br />If the name of the request header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent).| Sample Usage: %{http_Connection}<br /><br />Sample Value: Keep-Alive | 
| Request Host | %{host} | Indicates the host defined in the request URL. | <www.mydomain.com> |
| Request Protocol | %{request_protocol} | Indicates the request protocol. | HTTP/1.1 |
| Request Scheme | %{scheme} | Indicates the request scheme. |http |
| Request URI (Relative) | %{request_uri} | Indicates the relative path, including the query string, defined in the request URI. | /marketing/foo.js?loggedin=true |
| Request URI (Relative without query string) | %{uri} | Indicates the relative path to the requested content. <br /><br/>Key information:<br />- This relative path excludes the query string.<br />- This relative path reflects URL rewrites. A URL will be rewritten under the following conditions:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL Rewrite Feature: This feature rewrites the relative path defined in the request URI.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge CNAME URL: This type of request is rewritten to the corresponding CDN URL. |/800001/corigin/rewrittendir/foo.js |
| Request URI | %{request} | Describes the request. <br />Syntax: &lt;HTTP method&gt; &lt;relative path&gt; &lt;HTTP protocol&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Response Header Value | %{resp_&lt;ResponseHeader&gt;} | Returns the value corresponding to the response header identified by the &lt;ResponseHeader&gt; term. <br /><br />If the name of the response header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent). | Sample Usage: %{resp_Content_Length}<br /><br />Sample Value: 100 |

## Usage
The following table describes the proper syntax for specifying an HTTP variable.


| Syntax | Example | Description |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Use this syntax to get the entire value corresponding to the specified &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Use this syntax to set the case for the entire value corresponding to the specified  &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Use a regular expression for &lt;HTTPVariableDelimiterExpression&gt; to replace, delete, or manipulate an HTTP variable's value. |

HTTP variable names only support alphabetic characters and underscores. Convert unsupported characters to underscores.

## Delimiter reference
A delimiter can be specified after an HTTP variable to achieve any of the following effects:

- Transform the value associated with the variable.

     Example: Convert the entire value to lowercase.

- Delete the value associated with the variable.

- Manipulate the value associated with the variable.

     Example: Use regular expressions to change the value associated with the HTTP variable.

The delimiters are described in the following table.

| Delimiter | Description |
| --------- | ----------- |
| := | Indicates that a default value will be assigned to the variable when it is either: <br />- Missing <br />- Set to NULL. |
| :+ | Indicates that a default value will be assigned to the variable when a value has been assigned to it. |
| : | Indicates that a substring of the value assigned to the variable will be expanded. |
| # | Indicates that the pattern specified after this delimiter should be deleted when it is found at the beginning of the value associated with the variable. |
| % | Indicates that the pattern specified after this delimiter should be deleted when it is found at the end of the value associated with the variable. <br />This definition is only applicable when the % symbol is used as a delimiter. |
| / | Delimits an HTTP variable or a pattern. |
| // | Find and replace all instances of the specified pattern. |
| /= | Find, copy, and rewrite all occurrences of the specified pattern. |
| , | Convert the value associated with the HTTP variable to lowercase. |
| ^ | Convert the value associated with the HTTP variable to uppercase. |
| ,, | Convert all instances of the specified character in the value associated with the HTTP variable to lowercase. |
| ^^ | Convert all instances of the specified character in the value associated with the HTTP variable to uppercase. |

## Exceptions
The following table describes circumstances under which the specified text isn't treated as an HTTP variable.

| Condition | Description | Example |
| --------- | ----------- | --------|
| Escaping % symbol | The percentage symbol can be escaped through the use of a backslash. <br />The sample value to the right will be treated as a literal value and not as an HTTP variable.| \%{host} |
| Variables inconnues | Une chaîne vide est toujours retournée pour les variables inconnues. | % {unknown_variable} |
| Caractères ou syntaxe non valides | Les variables qui contiennent des caractères ou une syntaxe non valides sont traitées comme des valeurs littérales. <br /><br />Exemple 1 : la valeur spécifiée contient un caractère non valide (par exemple, -). <br /><br />Exemple 2 : la valeur spécifiée contient un double ensemble d’accolades. <br /><br />Exemple 3 : il manque une accolade fermante à la valeur spécifiée.<br /> | Exemple 1 : %{resp_user-agent} <br /><br />Exemple 2 : %{{host}} <br /><br />Exemple 3 : %{host |
| Nom de la variable manquant | Une valeur Null est toujours retournée lorsqu’une variable n’est pas spécifiée. | %{} |
| Caractères de fin | Les caractères de fin d’une variable sont traités comme des valeurs littérales. <br />L’exemple de valeur à droite contient une accolade de fin qui sera traitée comme une valeur littérale. | %{host}} |

## <a name="setting-default-header-values"></a>Paramétrage des valeurs d’en-tête par défaut
Une valeur par défaut peut être assignée à un en-tête dans les conditions suivantes :
- en-tête manquant/non défini ;
- valeur de l’en-tête définie sur Null.

Le tableau suivant décrit comment définir une valeur par défaut.

| Condition | Syntaxe | Exemples | Description |
| --------- | ------ | --------| ----------- |
| Définition d’une valeur par défaut pour un en-tête dans les conditions suivantes : <br /><br />- en-tête manquant ; <br /><br />- valeur d’en-tête définie sur Null.| %{Variable:=Value} | %{http_referrer:=unspecified} | L’en-tête Referrer est uniquement défini sur *unspecified* quand il est manquant ou défini avec la valeur Null. Aucune action n’est effectuée s’il a été défini. |
| Définition d’un en-tête à une valeur par défaut lorsqu’il est manquant. | %{Variable=Value} | %{http_referrer=unspecified} | L’en-tête Referrer est uniquement défini sur *unspecified* quand il est manquant. Aucune action n’est effectuée s’il a été défini. |
| Définition d’une valeur par défaut pour un en-tête quand il ne satisfait aucune des conditions suivantes : <br /><br />- en-tête manquante ;<br /><br /> - valeur de l’en-tête définie sur Null. | %{Variable:+Value} | %{http_referrer:+unspecified} | L’en-tête Referrer est uniquement défini sur *unspecified* lorsqu’une valeur lui a été assignée. Aucune action n’est effectuée s’il est manquant ou défini avec la valeur Null. |

## <a name="manipulating-variables"></a>Manipulation de variables
Les variables peuvent être manipulées comme suit :

- Développement de sous-chaînes
- Suppression de modèles

### <a name="substring-expansion"></a>Développement de sous-chaîne
Par défaut, une variable se développe à sa valeur complète. Utilisez la syntaxe suivante pour développer uniquement une sous-chaîne de la valeur de la variable.

`%<Variable>:<Offset>:<Length>}`

Informations essentielles :

- La valeur assignée au terme Offset détermine le caractère de début de la sous-chaîne :

     - Positive : le caractère de début de la sous-chaîne est calculé à partir du premier caractère de la chaîne.
     - Zéro : le caractère de début de la sous-chaîne est le premier caractère de la chaîne.
     - Négatif : le caractère de début de la sous-chaîne est calculé à partir du dernier caractère de la chaîne.

- La longueur de la sous-chaîne est déterminée par le terme *Length* :

     - Omise : l’omission du terme Length permet à la sous-chaîne d’inclure tous les caractères entre le caractère de début et la fin de la chaîne.
     - Positive : détermine la longueur de la sous-chaîne à partir du premier caractère vers la droite.
     - Négatif : détermine la longueur de la sous-chaîne à partir du premier caractère vers la gauche.

#### <a name="example"></a>Exemple :

L’exemple suivant s’appuie sur l’exemple d’URL de requête suivant :

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

La chaîne suivante illustre différentes méthodes de manipulation des variables :

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

En fonction de l’exemple d’URL de requête, la manipulation de variables ci-dessus produit la valeur suivante :

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Suppression du modèle
Le texte correspondant à un modèle spécifique peut être supprimé du début ou de la fin de la valeur d’une variable.

| Syntaxe | Action |
| ------ | ------ |
| %{Variable#Pattern} | Supprime le texte lorsque le modèle spécifié est localisé au début de la valeur d’une variable. |
| %{Variable%Pattern} | Supprime le texte lorsque le modèle spécifié est localisé à la fin de la valeur d’une variable. |

#### <a name="example"></a>Exemple :

Dans cet exemple de scénario, la variable *request_uri* est définie sur :

`/800001/myorigin/marketing/product.html?language=en-US`

Le tableau suivant illustre le fonctionnement de cette syntaxe.

| Exemple de syntaxe | Résultats | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Étant donné que la variable commence par le modèle, ce dernier a été remplacé. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Étant donné que la variable ne se termine pas par le modèle, il n’y a aucune modification.|

### <a name="find-and-replace"></a>Rechercher et remplacer
La syntaxe de recherche et de remplacement est décrite dans le tableau suivant.

| Syntaxe | Action |
| ------ | ------ |
| %{Variable/Find/Replace} | Recherche et remplace la première occurrence du modèle spécifié. |
| %{Variable//Find/Replace} | Recherche et remplace toutes les occurrences du modèle spécifié. |
| %{Variable^} |Convertit la valeur entière en majuscules. |
| %{Variable^Find} | Convertit la première occurrence du modèle spécifié en majuscules. |
| %{Variable,} | Convertit la valeur entière en minuscules. |
| %{Variable,Find} | Convertit la première occurrence du modèle spécifié en minuscules. |

### <a name="find-and-rewrite"></a>Rechercher et réécrire
Pour une variante de la recherche et du remplacement, utilisez le texte correspondant au modèle spécifié lors de sa réécriture. La syntaxe de recherche et de réécriture est décrite dans le tableau suivant.

| Syntaxe | Action |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Recherche, copie et réécrit toutes les occurrences du modèle spécifié. |
| %{Variable/^Find/Rewrite} | Recherche, copie et réécrit le modèle spécifié lorsqu’il apparaît au début de la variable. |
| %{Variable/$Find/Rewrite} | Recherche, copie et réécrit le modèle spécifié lorsqu’il apparaît à la fin de la variable. |
| %{Variable/Find} | Recherche et supprime toutes les occurrences du modèle spécifié. |

Informations essentielles :

- Développez le texte correspondant au modèle spécifié en indiquant un signe dollar suivi d’un nombre entier (par exemple, $1).

- Plusieurs modèles peuvent être spécifiés. L’ordre dans lequel le modèle est spécifié détermine l’entier qui lui sera assigné. Dans l’exemple suivant, le premier modèle correspond à « www. », le deuxième modèle correspond au domaine de second niveau, et le troisième modèle correspond au domaine de niveau supérieur :

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- La valeur réécrite peut se composer de n’importe quelle combinaison de texte et de ces espaces réservés.

    Dans l’exemple précédent, le nom d’hôte est réécrit de la manière suivante : `cdn.$2.$3:80` (par exemple, cdn.mydomain.com:80).

- La casse d’un espace réservé de modèle (par exemple, $1) peut être modifiée au moyen des indicateurs suivants :
     - U : majuscules pour la valeur développée.

         Exemple de syntaxe :

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L : minuscules pour la valeur développée.

         Exemple de syntaxe :

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Un opérateur doit être spécifié avant le modèle. L’opérateur spécifié détermine le comportement de capture de modèle :

     - `=`: indique que toutes les occurrences du modèle spécifié doivent être capturées et réécrites.
     - `^`: indique que seul le texte commençant par le modèle spécifié est capturé.
     - `$`: indique que seul le texte se terminant par le modèle spécifié est capturé.
 
- Si vous omettez la valeur */Rewrite* valeur, le texte correspondant au modèle est supprimé.


