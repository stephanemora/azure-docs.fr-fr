---
title: Écriture d’expressions pour les mappages d’attributs dans Azure AD
description: Découvrez comment utiliser les mappages d’expressions pour transformer des valeurs d’attributs dans un format acceptable lors de l’approvisionnement automatique des objets d’application SaaS dans Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc41a18063202bfefb9ddf7238de17fc691984af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612144"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory
Quand vous configurez l’approvisionnement pour une application SaaS, l’un des types de mappages d’attributs que vous pouvez spécifier est un mappage d’expression. Dans ce cas, vous devez écrire une expression semblable à un script qui vous permet de transformer les données des utilisateurs dans des formats plus acceptables pour l’application SaaS.

## <a name="syntax-overview"></a>Vue d’ensemble de la syntaxe
La syntaxe des expressions pour les mappages d’attributs rappelle celle des fonctions Visual Basic pour Applications (VBA).

* L’expression entière doit être définie en termes de fonctions, qui sont constituées d’un nom suivi d’arguments entre parenthèses : <br>
  *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* Vous pouvez imbriquer des fonctions dans d’autres. Par exemple : <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* Vous pouvez passer trois différents types d’arguments dans des fonctions :
  
  1. Des attributs, qui doivent être placés entre crochets. Par exemple : [nom_attribut]
  2. Des constantes de chaîne, qui doivent être placées entre des guillemets doubles. Par exemple : « États-Unis »
  3. D’autres fonctions. Par exemple : FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* Pour les constantes de chaîne, si vous avez besoin d’une barre oblique inverse (\) ou d’un guillemet (") dans la chaîne, vous devez le faire précéder du symbole de barre oblique inverse (\). Par exemple : « Nom de l’entreprise : \\"Contoso"\\ »

## <a name="list-of-functions"></a>Liste des fonctions
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [IIF](#iif) &nbsp;&nbsp;&nbsp;&nbsp;[InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>Ajouter
**Fonction :**<br> Append(source, suffixe)

**Description :**<br> prend une valeur de chaîne source et ajoute le suffixe à la fin de celle-ci.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Généralement le nom de l’attribut de l’objet source. |
| **suffix** |Obligatoire |String |Chaîne que vous souhaitez ajouter à la fin de la valeur source. |

---
### <a name="bitand"></a>BitAnd
**Fonction :**<br> BitAnd(value1, value2)

**Description :**<br> Cette fonction convertit les deux paramètres de la représentation binaire et définit un bit sur :

0 - si un des bits, ou les deux bits correspondants dans value1 et value2 ont pour valeur 0                                                  
1 - si les deux bits correspondants sont définis sur 1.                                    

En d’autres termes, elle renvoie 0 dans tous les cas, sauf si les bits correspondants de ces deux paramètres sont définis sur 1.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **value1** |Obligatoire |num |Valeur numérique qui doit être liée par AND avec value2|
| **value2** |Obligatoire |num |Valeur numérique qui doit être liée par AND avec value1|

**Exemple :**<br>
BitAnd(&HF, &HF7)                                                                                
11110111 AND 00000111 = 00000111 donc BitAnd renvoie 7, la valeur binaire de 00000111

---
### <a name="cbool"></a>CBool
**Fonction :**<br> CBool(Expression)

**Description :**<br> CBool renvoie une valeur booléenne basée sur l’expression évaluée. Si l’expression renvoie une valeur autre que zéro, CBool renvoie la valeur True, sinon elle renvoie False.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **expression** |Obligatoire | expression | Toute expression valide |

**Exemple :**<br>
CBool([attribute1] = [attribute2])                                                                    
Retourne True si les attributs ont la même valeur.

---
### <a name="coalesce"></a>Coalesce
**Fonction :**<br> Coalesce(source1, source2, ..., defaultValue)

**Description :**<br> Renvoie la première valeur source qui n’est pas NULL. Si tous les arguments ont la valeur NULL et que defaultValue est présent, defaultValue est renvoyé. Si tous les arguments ont la valeur NULL et que defaultValue n’est pas présent, Coalesce renvoie la valeur NULL.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source1  … sourceN** | Obligatoire | String |Requis, nombre de fois variable. Généralement le nom de l’attribut de l’objet source. |
| **defaultValue** | Facultatif | String | Valeur par défaut à utiliser lorsque toutes les valeurs sources sont NULL. Peut être une chaîne vide ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Fonction :**<br> ConvertToBase64(source)

**Description :**<br> La fonction ConvertToBase64 convertit une chaîne en chaîne Unicode base64.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Chaîne à convertir en base 64|

**Exemple :**<br>
ConvertToBase64("Hello world!")                                                                                                        
Renvoie « SABlAGwAbABvACAAdwBvAHIAbABkACEA ».

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Fonction :**<br> ConvertToUTF8Hex(source)

**Description :**<br> La fonction ConvertToUTF8Hex convertit une chaîne en valeur hexadécimale encodée UTF8.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Chaîne à convertir en hexadécimale UTF8|

**Exemple :**<br>
ConvertToUTF8Hex("Hello world!")                                                                                                         
Renvoie 48656C6C6F20776F726C6421.

---
### <a name="count"></a>Count
**Fonction :**<br> Count(attribute)

**Description :**<br> La fonction Count renvoie le nombre d’éléments dans un attribut à valeurs multiples.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **attribute** |Obligatoire |attribut |Attribut à valeurs multiples dont les éléments seront comptés|

---
### <a name="cstr"></a>CChaîne
**Fonction :**<br> CStr(value)

**Description :**<br> la fonction CStr convertit une valeur en un type de données de chaîne.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **value** |Obligatoire | numérique, référence ou booléenne | Peut être une valeur numérique, un attribut de référence ou une valeur booléenne. |

**Exemple :**<br>
CStr([dn])                                                            
Renvoie « cn=Joe,dc=contoso,dc=com »

---
### <a name="datefromnum"></a>DateFromNum
**Fonction :**<br> DateFromNum(value)

**Description :**<br> La fonction DateFromNum convertit une valeur au format de date AD en un type DateTime.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **value** |Obligatoire | Date | Date AD à convertir en type DateTime |

**Exemple :**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
Renvoie une valeur DateTime représentant 2012-01-01 23:00:00.

---
### <a name="formatdatetime"></a>FormatDateTime
**Fonction :**<br> FormatDateTime(source, inputFormat, outputFormat)

**Description :**<br> prend une chaîne de date dans un format et la convertit dans un autre format.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Généralement le nom de l’attribut de l’objet source. |
| **inputFormat** |Obligatoire |String |Format attendu de la valeur source. Pour connaitre les formats pris en charge, consultez [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Obligatoire |String |Format de la date de sortie. |

---
### <a name="guid"></a>Guid
**Fonction :**<br> Guid()

**Description :**<br> La fonction Guid génère un nouveau GUID aléatoire.

---
### <a name="iif"></a>IIF
**Fonction :**<br> IIF(condition,valueIfTrue,valueIfFalse)

**Description :**<br> La fonction IIF renvoie une valeur parmi un ensemble de valeurs possibles en fonction d’une condition spécifiée.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **condition** |Obligatoire |Variable ou expression |Toute valeur ou expression pouvant prendre une valeur true ou false. |
| **valueIfTrue** |Obligatoire |Variable ou chaîne | La valeur renvoyée si la condition prend la valeur true. |
| **valueIfFalse** |Obligatoire |Variable ou chaîne |La valeur renvoyée si la condition prend la valeur false.|

**Exemple :**<br>
IIF([country]="USA",[country],[department])

---
### <a name="instr"></a>InStr
**Fonction :**<br> InStr(value1,value2,start,compareType)

**Description :**<br> La fonction InStr recherche la première occurrence d’une sous-chaîne dans une chaîne.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **value1** |Obligatoire |String |Chaîne dans laquelle rechercher |
| **value2** |Obligatoire |String |Chaîne à trouver |
| **start** |Facultatif |Integer |Position de départ pour trouver la sous-chaîne|
| **compareType** |Facultatif |Enum |Peut être vbTextCompare ou vbBinaryCompare |

**Exemple :**<br>
InStr("The quick brown fox","quick")                                                                             
Prend la valeur 5.

InStr("repEated","e",3,vbBinaryCompare)                                                                                  
Prend la valeur 7.

---
### <a name="isnull"></a>IsNull
**Fonction :**<br> IsNull(Expression)

**Description :**<br> La fonction IsNull renvoie true si l’expression correspond à la valeur Null. Dans le cas d’un attribut, la valeur Null est exprimée par l’absence de ce dernier.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **expression** |Obligatoire |expression |Expression à évaluer |

**Exemple :**<br>
IsNull([displayName])                                                                                                
Renvoie True si l’attribut est absent

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Fonction :**<br> IsNullOrEmpty(Expression)

**Description :**<br> La fonction IsNullOrEmpty renvoie la valeur true si l’expression a pour valeur Null ou s’il s’agit d’une chaîne vide. Dans le cas d’un attribut, cela donne la valeur True si l’attribut est absent ou est présent mais qu’il s’agit d’une chaîne vide.
L’inverse de cette fonction est nommé IsPresent.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **expression** |Obligatoire |expression |Expression à évaluer |

**Exemple :**<br>
IsNullOrEmpty([displayName])                                               
Renvoie True si l’attribut est absent ou s’il s’agit d’une chaîne vide

---
### <a name="ispresent"></a>IsPresent
**Fonction :**<br> IsPresent(Expression)

**Description :**<br> La fonction IsPresent renvoie true si l’expression correspond à une chaîne qui n’a pas la valeur Null et n’est pas vide. L’inverse de cette fonction est appelé IsNullOrEmpty.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **expression** |Obligatoire |expression |Expression à évaluer |

**Exemple :**<br>
Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])

---
### <a name="isstring"></a>IsString
**Fonction :**<br> IsString(Expression)

**Description :**<br> La fonction IsString prend la valeur True si l’expression peut être évaluée en tant que type de chaîne.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **expression** |Obligatoire |expression |Expression à évaluer |

---
### <a name="item"></a>Élément
**Fonction :**<br> Item(attribute, index)

**Description :**<br> La fonction Item renvoie un élément à partir d’une chaîne/d’un attribut à valeurs multiples.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **attribute** |Obligatoire |Attribut |Attribut à valeurs multiples à rechercher |
| **index** |Obligatoire |Integer | Index d’un élément dans la chaîne à valeurs multiples|

**Exemple :**<br>
Item([proxyAddresses], 1)

---
### <a name="join"></a>Join
**Fonction :**<br> Join(séparateur, source1, source2, …)

**Description :**<br> Join() est similaire à Append(), mais elle peut combiner plusieurs valeurs de chaîne **sources** dans une même chaîne et chaque valeur sera séparée par une chaîne de **séparation**.

Si l’une des valeurs sources est un attribut à valeurs multiples, toutes les valeurs de cet attribut seront jointes, séparées par la valeur de séparation.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **separator** |Obligatoire |String |Chaîne utilisée pour séparer les valeurs sources quand elles sont concaténées en une seule chaîne. Peut être "" si aucun séparateur n’est requis. |
| **source1  … sourceN** |Requis, nombre de fois variable |String |Valeurs de chaîne à joindre ensemble. |

---
### <a name="left"></a>Gauche
**Fonction :**<br> Left(String,NumChars)

**Description :**<br> La fonction Left renvoie un nombre spécifié de caractères en partant de la gauche d’une chaîne. Si numChars = 0, retourne une chaîne vide.
Si numChars < 0, retourne une chaîne d’entrée.
Si la chaîne est null, retourne une chaîne vide.
Si la chaîne contient moins de caractères que le nombre spécifié dans numChars, une chaîne identique à la chaîne (c’est-à-dire, contenant tous les caractères du paramètre 1) est renvoyée.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **Chaîne** |Obligatoire |Attribut | Chaîne à partir de laquelle renvoyer des caractères |
| **NumChars** |Obligatoire |Integer | Un nombre identifiant le nombre de caractères à retourner à partir du début (gauche) de la chaîne|

**Exemple :**<br>
Left(“John Doe”, 3)                                                            
Renvoie « Joh »

---
### <a name="mid"></a>ExtracChaîne
**Fonction :**<br> Mid(source, début, longueur)

**Description :**<br> retourne une sous-chaîne de la valeur source. Une sous-chaîne est une chaîne qui ne contient que certains des caractères de la chaîne source.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Généralement le nom de l’attribut. |
| **start** |Obligatoire |entier |Index dans la chaîne **source** où la sous-chaîne doit commencer. Le premier caractère dans la chaîne aura l’index 1, le deuxième caractère aura l’index 2, et ainsi de suite. |
| **length** |Obligatoire |entier |Longueur de la sous-chaîne. Si la longueur se termine à l’extérieur de la chaîne **source**, la fonction retourne la sous-chaîne de l’index **start** jusqu’à la fin de l’index **source**. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Fonction :**<br> NormalizeDiacritics(source)

**Description :**<br> Nécessite un argument de chaîne. Retourne la chaîne, où les caractères diacritiques ont été remplacés par leurs équivalents non diacritiques. Généralement utilisé pour convertir les prénoms et les noms contenant des caractères diacritiques (accents) en valeurs autorisées pouvant être utilisées dans différents identificateurs d’utilisateurs, tels que les noms d’utilisateurs principaux, les noms de compte SAM et les adresses e-mail.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String | Généralement un attribut de nom ou de prénom. |

---
### <a name="not"></a>Not
**Fonction :**<br> Not(source)

**Description :**<br> inverse la valeur booléenne de la **source**. Si la valeur **source** est « *True* », cette fonction retourne «*False* ». Sinon, elle retourne «*True*».

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |Chaîne de type Boolean |Les valeurs **sources** attendues sont « True » ou « False ». |

---
### <a name="numfromdate"></a>NumFromDate
**Fonction :**<br> NumFromDate(value)

**Description :**<br> La fonction NumFromDate convertit une valeur DateTime au format Active Directory requis pour définir des attributs comme [accountExpires](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires). Utilisez cette fonction pour convertir les valeurs DateTime reçues d’applications cloud destinées aux ressources humaines telles que Workday et SuccessFactors en leur représentation AD équivalente. 

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **value** |Obligatoire | String | Chaîne de date et d’heure dans le format pris en charge. Pour connaitre les formats pris en charge, consultez https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx. |

**Exemple :**<br>
* Exemple Workday <br>
  En supposant que vous souhaitiez mapper l’attribut *ContractEndDate* de Workday, qui est au format *2020-12-31-08:00*, au champ *accountExpires* dans AD, voici comment vous pouvez utiliser cette fonction et modifier le décalage de fuseau horaire pour qu’il corresponde à vos paramètres régionaux. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* Exemple SuccessFactors <br>
  En supposant que vous souhaitiez mapper l’attribut *endDate* de SuccessFactors, qui est au format *M/j/aaaa hh:mm:ss tt*, au champ *accountExpires* dans AD, voici comment vous pouvez utiliser cette fonction et modifier le décalage de fuseau horaire pour qu’il corresponde à vos paramètres régionaux.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**Fonction :**<br> RemoveDuplicates(attribute)

**Description :**<br> La fonction RemoveDuplicates prend une chaîne à valeurs multiples et vérifie que chaque valeur est unique.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **attribute** |Obligatoire |Attribut à valeurs multiples |Attribut à valeurs multiples dont les doublons seront supprimés|

**Exemple :**<br>
RemoveDuplicates([proxyAddresses])                                                                                                       
Renvoie un attribut proxyAddress expurgé duquel toutes les valeurs en double ont été supprimées

---
### <a name="replace"></a>Replace
**Fonction :**<br> Remplacer (source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**Description :**<br>
Remplace les valeurs dans une chaîne. Elle fonctionne différemment selon les paramètres fournis :

* Quand **oldValue** et **replacementValue** sont fournis :
  
  * Remplace toutes les occurrences d’**oldValue** dans la **source** par  **replacementValue**.
* Quand **oldValue** et **template** sont fournis :
  
  * Remplace toutes les occurrences d’**oldValue** dans le **template** par la valeur **source**.
* Quand **regexPattern** et **replacementValue** sont fournis :

  * La fonction applique **regexPattern** à la chaîne **source** et vous pouvez utiliser les noms de groupes regex pour construire la chaîne pour **replacementValue**.
* Quand **regexPattern**, **regexGroupName** et **replacementValue** sont fournis :
  
  * La fonction applique **regexPattern** à la chaîne **source** et remplace toutes les valeurs correspondant à **regexGroupName** par **replacementValue**.
* Quand **regexPattern**, **regexGroupName** et **replacementAttributeName** sont fournis :
  
  * Si **source** n’a pas de valeur, **source** est retourné
  * Si **source** a une valeur, la fonction applique **regexPattern** à la chaîne **source** et remplace toutes les valeurs correspondant à **regexGroupName** par la valeur associée à **replacementAttributeName**.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Généralement, nom de l’attribut de l’objet **source**. |
| **oldValue** |Facultatif |String |Valeur à remplacer dans **source** ou **template**. |
| **regexPattern** |Facultatif |String |Modèle d’expression régulière pour la valeur à remplacer dans **source**. Ou, quand **replacementPropertyName** est utilisé, modèle pour extraire la valeur de **replacementPropertyName**. |
| **regexGroupName** |Facultatif |String |Nom du groupe à l’intérieur de **regexPattern**. Nous extrayons la valeur de ce groupe en tant que **replacementValue** à partir de **replacementPropertyName** uniquement quand **replacementPropertyName** est utilisé. |
| **replacementValue** |Facultatif |String |Nouvelle valeur par laquelle remplacer l’ancienne. |
| **replacementAttributeName** |Facultatif |String |Nom de l’attribut à utiliser pour la valeur de remplacement. |
| **template** |Facultatif |String |Lorsque la valeur **template** est fournie, nous recherchons la valeur **oldValue** dans le modèle et la remplaçons par la valeur **source**. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Fonction :**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**Description :**<br> Nécessite un minimum de deux arguments, qui sont définis à l’aide d’expressions de règles de génération de valeur unique. La fonction évalue chaque règle, puis vérifie la valeur générée pour l’unicité dans le répertoire/application cible. La première valeur unique trouvée est celle retournée. Si toutes les valeurs existent déjà dans la cible, l’entrée sera déposée et le motif de cette action consigné dans les journaux d’audit. Il n’existe aucune limite supérieure au nombre d’arguments qui peuvent être fournis.

> [!NOTE]
> - Il s’agit d’une fonction de niveau supérieur, vous ne pouvez donc pas l’imbriquer.
> - Cette fonction ne peut pas s’appliquer à des attributs qui ont une priorité de correspondance.  
> - Cette fonction est uniquement destinée à être utilisée pour les créations d’entrées. Lorsque vous l’utilisez avec un attribut, définissez la propriété **Appliquer le mappage** sur **Uniquement durant la création d’objet**.
> - Cette fonction est actuellement prise en charge uniquement pour le « Provisionnement d’utilisateurs de Workday vers Active Directory ». Elle ne peut pas être utilisée avec d’autres applications de provisionnement. 


**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |Au moins 2 requis, aucune limite supérieure |String | Liste des règles de génération de valeur unique à évaluer. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Fonction :**<br> SingleAppRoleAssignment([appRoleAssignments])

**Description :**<br> Retourne un appRoleAssignment unique parmi la liste de tous les appRoleAssignments affectés à un utilisateur pour une application donnée. Cette fonction est nécessaire pour convertir l’objet appRoleAssignments en une chaîne de nom de rôle unique. Notez que la bonne pratique consiste à s’assurer qu’un seul appRoleAssignment est attribué à un seul utilisateur à la fois, et si plusieurs rôles sont attribués, la chaîne de rôle retournée ne doit pas être prévisible. 

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Obligatoire |String |Objet **[appRoleAssignments]** . |

---
### <a name="split"></a>Split
**Fonction :**<br> Split (source, délimiteur)

**Description :**<br> Fractionne une chaîne en un tableau à plusieurs valeurs, en utilisant le caractère délimiteur spécifié.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |**source** à mettre à jour. |
| **délimiteur** |Obligatoire |String |Spécifie le caractère qui sera utilisé pour fractionner la chaîne (exemple : « , ») |

---
### <a name="stripspaces"></a>StripSpaces
**Fonction :**<br> StripSpaces(source)

**Description :**<br> supprime tous les caractères d’espacement (" ") de la chaîne source.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |**source** à mettre à jour. |

---
### <a name="switch"></a>Commutateur
**Fonction :**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**Description :**<br> quand la valeur **source** correspond à une **clé**, retourne la **valeur** de cette **clé**. Si la valeur **source** ne correspond à aucune clé, retourne **defaultValue**.  Les paramètres **key** et **value** doivent toujours être fournis par paires. La fonction attend toujours un nombre pair de paramètres. La fonction ne doit pas être utilisée pour les attributs référentiels tels que le gestionnaire. 

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |**Source** à mettre à jour. |
| **defaultValue** |Facultatif |String |Valeur par défaut à utiliser quand la source ne correspond à aucune clé. Peut être une chaîne vide (""). |
| **key** |Obligatoire |String |**Key** avec laquelle comparer la valeur **source**. |
| **value** |Obligatoire |String |Valeur de remplacement pour la **source** correspondant à la clé. |

---
### <a name="tolower"></a>ToLower
**Fonction :**<br> ToLower(source, culture)

**Description :**<br> prend une valeur de la chaîne *source* et la convertit en minuscules à l’aide des règles de culture spécifiées. Si aucune information de *culture* n'est spécifiée, une culture invariante est utilisée.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Généralement le nom de l’attribut de l’objet source |
| **culture** |Facultatif |String |Le format du nom de culture basé sur RFC 4646 est *languagecode2-country/regioncode2*, où *languagecode2* correspond au code de langue à deux lettres et *country/regioncode2* au code de sous-culture à deux lettres, par exemple, ja-JP pour le japonais (Japon) et en-US pour l’anglais (États-Unis). Si un code de langue à deux lettres n'est pas disponible, un code à trois lettres dérivé de la norme ISO 639-2 est utilisé.|

---
### <a name="toupper"></a>ToUpper
**Fonction :**<br> ToUpper(source, culture)

**Description :**<br> prend une valeur de la chaîne *source* et la convertit en majuscule à l’aide des règles de culture spécifiées. Si aucune information de *culture* n'est spécifiée, une culture invariante est utilisée.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |String |Généralement le nom de l’attribut de l’objet source. |
| **culture** |Facultatif |String |Le format du nom de culture basé sur RFC 4646 est *languagecode2-country/regioncode2*, où *languagecode2* correspond au code de langue à deux lettres et *country/regioncode2* au code de sous-culture à deux lettres, par exemple, ja-JP pour le japonais (Japon) et en-US pour l’anglais (États-Unis). Si un code de langue à deux lettres n'est pas disponible, un code à trois lettres dérivé de la norme ISO 639-2 est utilisé.|

---
### <a name="word"></a>Word
**Fonction :**<br> Word(String,WordNumber,Delimiters)

**Description :**<br> La fonction Word retourne un mot contenu dans une chaîne, en fonction des paramètres qui décrivent les délimiteurs à utiliser et le nombre de mots à retourner. Chaque chaîne de caractères contenue dans la chaîne séparée par l’un des caractères figurant dans delimiters est identifiée en tant que mot :

Si number < 1, retourne une chaîne vide.
Si string a la valeur null, renvoie une chaîne vide.
Si la chaîne contient moins de mots ou ne contient pas les mots identifiés par les séparateurs, une chaîne vide est renvoyée.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **Chaîne** |Obligatoire |Attribut à valeurs multiples |Chaîne à partir de laquelle renvoyer un mot.|
| **WordNumber** |Obligatoire | Integer | Nombre identifiant le nombre de mots à renvoyer|
| **delimiters** |Obligatoire |String| Une chaîne représentant le ou les délimiteurs à utiliser pour identifier les mots|

**Exemple :**<br>
Word(“The quick brown fox”,3,” “)                                                                                       
Retourne « brown ».

Word("This,string!has&many separators",3,",!&#")                                                                       
Renvoie « has »

---

## <a name="examples"></a>Exemples
### <a name="strip-known-domain-name"></a>Supprimer un nom de domaine connu
Vous devez supprimer un nom de domaine connu de l’adresse de messagerie d’un utilisateur pour obtenir un nom d’utilisateur. <br>
Par exemple, si le domaine est « contoso.com », vous pouvez utiliser l’expression suivante :

**Expression :** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Exemple d’entrée/sortie :** <br>

* **ENTRÉE** (e-mail) : "john.doe@contoso.com"
* **SORTIE** : « john.doe »

### <a name="append-constant-suffix-to-user-name"></a>Ajouter un suffixe de constante à un nom d’utilisateur
Si vous utilisez un Sandbox Salesforce, vous devrez peut-être ajouter un suffixe supplémentaire à tous les noms d’utilisateurs avant de les synchroniser.

**Expression :** <br>
`Append([userPrincipalName], ".test")`

**Exemple d’entrée/sortie :** <br>

* **ENTRÉE** : (userPrincipalName) : "John.Doe@contoso.com"
* **SORTIE**:  "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Générer des alias d’utilisateurs en concaténant des parties du prénom et du nom
Vous devez générer un alias d’utilisateur en prenant les trois premières lettres du prénom de l’utilisateur et les cinq premières lettres de son nom de famille.

**Expression :** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Exemple d’entrée/sortie :** <br>

* **ENTRÉE** (givenName) : "John"
* **ENTRÉE** (surname) : "Doe"
* **SORTIE** :  "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Supprimer les signes diacritiques d’une chaîne
Vous devez remplacer les caractères accentués par leurs équivalents non accentués.

**Expression :** <br>
NormalizeDiacritics([givenName])

**Exemple d’entrée/sortie :** <br>

* **ENTRÉE** (givenName) : "Zoë"
* **SORTIE** :  "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Fractionner une chaîne en un tableau à valeurs multiples
Vous devez prendre une liste de chaînes délimitées par des virgules, et la fractionner en un tableau pouvant être raccordé à un attribut à valeurs multiples comme l’attribut PermissionSets de Salesforce. Dans cet exemple, une liste des jeux d’autorisations a été remplie dans extensionAttribute5 dans Azure AD.

**Expression :** <br>
Split([extensionAttribute5], ",")

**Exemple d’entrée/sortie :** <br>

* **INPUT** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **SORTIE** :  ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Sortir une date sous la forme d’une chaîne dans un certain format
Vous souhaitez envoyer des dates à une application SaaS dans un format donné. <br>
Par exemple, vous souhaitez mettre en forme des dates pour ServiceNow.

**Expression :** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Exemple d’entrée/sortie :**

* **ENTRÉE** (extensionAttribute1) : "20150123105347.1Z"
* **SORTIE** :  "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Remplacer une valeur en fonction d’un ensemble d’options prédéfini

Vous devez définir le fuseau horaire de l’utilisateur en fonction du code d’état stocké dans Azure AD. <br>
Si le code d’état ne correspond à aucune des options prédéfinies, utilisez la valeur par défaut « Australia/Sydney ».

**Expression :** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Exemple d’entrée/sortie :**

* **ENTRÉE** (état) : "QLD"
* **SORTIE** : "Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Remplacer des caractères à l’aide d’une expression régulière
Vous devez rechercher des caractères correspondant à une valeur d’expression régulière et les supprimer.

**Expression :** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**Exemple d’entrée/sortie :**

* **ENTRÉE** (mailNickname: "john_doe72"
* **SORTIE** : "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Convertir la valeur userPrincipalName (UPN) générée en minuscules
Dans l’exemple ci-dessous, la valeur UPN est générée en concaténant les champs sources PreferredFirstName et PreferredLastName, et la fonction ToLower agit sur la chaîne générée pour convertir tous les caractères en minuscules. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Exemple d’entrée/sortie :**

* **ENTRÉE** (PreferredFirstName) : "John"
* **ENTRÉE** (PreferredLastName) : "Smith"
* **SORTIE** : "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Générer une valeur unique pour l’attribut userPrincipalName (UPN)
En fonction du prénom, du deuxième prénom et du nom de famille de l’utilisateur, vous devez générer une valeur pour l’attribut UPN et vérifier son caractère unique dans le répertoire AD cible avant d’attribuer la valeur à l’attribut UPN.

**Expression :** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Exemple d’entrée/sortie :**

* **ENTRÉE** (PreferredFirstName) : "John"
* **ENTRÉE** (PreferredLastName) : "Smith"
* **SORTIE** : « John.Smith@contoso.com » si la valeur UPN de John.Smith@contoso.com n’existe pas déjà dans le répertoire
* **SORTIE** : « J.Smith@contoso.com » si la valeur UPN de John.Smith@contoso.com existe déjà dans le répertoire
* **SORTIE** : « Jo.Smith@contoso.com » si les deux valeurs UPN précédentes existent déjà dans le répertoire

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Valeur de courrier dynamique si non NULL ; dans le cas contraire, transmission de userPrincipalName
Vous souhaitez transmettre l’attribut de messagerie, s’il est présent. Si ce n’est pas le cas, vous souhaitez transmettre la valeur de userPrincipalName à la place.

**Expression :** <br>
`Coalesce([mail],[userPrincipalName])`

**Exemple d’entrée/sortie :** <br>

* **ENTRÉE** (mail) : NULL
* **ENTRÉE** (userPrincipalName) : « John.Doe@contoso.com »
* **SORTIE**:  "John.Doe@contoso.com"

## <a name="related-articles"></a>Articles connexes
* [Automatiser l’approvisionnement/le déprovisionnement des utilisateurs pour les applications SaaS](../app-provisioning/user-provisioning.md)
* [Personnalisation des mappages d’attributs pour l’approvisionnement des utilisateurs](../app-provisioning/customize-application-attributes.md)
* [Filtres d’étendue pour l’approvisionnement des utilisateurs](define-conditional-rules-for-provisioning-user-accounts.md)
* [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Notifications d’approvisionnement de comptes](../app-provisioning/user-provisioning.md)
* [Liste des didacticiels sur l’intégration des applications SaaS](../saas-apps/tutorial-list.md)
