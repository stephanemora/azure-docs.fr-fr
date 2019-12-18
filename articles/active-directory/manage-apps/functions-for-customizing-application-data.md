---
title: Écriture d’expressions pour les mappages d’attributs dans Azure AD
description: Découvrez comment utiliser les mappages d’expressions pour transformer des valeurs d’attributs dans un format acceptable lors de l’approvisionnement automatique des objets d’application SaaS dans Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a346b264afc23e21ccf3e6d5dbf7a8f5d96518d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74842252"
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
  2. Des constantes de chaîne, qui doivent être placées entre des guillemets doubles. Par exemple :  « États-Unis »
  3. D’autres fonctions. Par exemple :  FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* Pour les constantes de chaîne, si vous avez besoin d’une barre oblique inverse (\) ou d’un guillemet (") dans la chaîne, vous devez le faire précéder du symbole de barre oblique inverse (\). Par exemple :  « Nom de l’entreprise : \\"Contoso"\\ »

## <a name="list-of-functions"></a>Liste des fonctions
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)

---
### <a name="append"></a>Append
**Fonction :**<br> Append(source, suffixe)

**Description :**<br> prend une valeur de chaîne source et ajoute le suffixe à la fin de celle-ci.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |Chaîne |Généralement le nom de l’attribut de l’objet source. |
| **suffix** |Obligatoire |Chaîne |Chaîne que vous souhaitez ajouter à la fin de la valeur source. |

---
### <a name="formatdatetime"></a>FormatDateTime
**Fonction :**<br> FormatDateTime(source, inputFormat, outputFormat)

**Description :**<br> prend une chaîne de date dans un format et la convertit dans un autre format.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |Chaîne |Généralement le nom de l’attribut de l’objet source. |
| **inputFormat** |Obligatoire |Chaîne |Format attendu de la valeur source. Pour connaitre les formats pris en charge, consultez [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Obligatoire |Chaîne |Format de la date de sortie. |

---
### <a name="join"></a>Join
**Fonction :**<br> Join(séparateur, source1, source2, …)

**Description :**<br> Join() est similaire à Append(), mais elle peut combiner plusieurs valeurs de chaîne **sources** dans une même chaîne et chaque valeur sera séparée par une chaîne de **séparation**.

Si l’une des valeurs sources est un attribut à valeurs multiples, toutes les valeurs de cet attribut seront jointes, séparées par la valeur de séparation.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **separator** |Obligatoire |Chaîne |Chaîne utilisée pour séparer les valeurs sources quand elles sont concaténées en une seule chaîne. Peut être "" si aucun séparateur n’est requis. |
| **source1  … sourceN** |Requis, nombre de fois variable |Chaîne |Valeurs de chaîne à joindre ensemble. |

---
### <a name="mid"></a>Mid
**Fonction :**<br> Mid(source, début, longueur)

**Description :**<br> retourne une sous-chaîne de la valeur source. Une sous-chaîne est une chaîne qui ne contient que certains des caractères de la chaîne source.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |Chaîne |Généralement le nom de l’attribut. |
| **start** |Obligatoire |integer |Index dans la chaîne **source** où la sous-chaîne doit commencer. Le premier caractère dans la chaîne aura l’index 1, le deuxième caractère aura l’index 2, et ainsi de suite. |
| **length** |Obligatoire |integer |Longueur de la sous-chaîne. Si la longueur se termine à l’extérieur de la chaîne **source**, la fonction retourne la sous-chaîne de l’index **start** jusqu’à la fin de l’index **source**. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Fonction :**<br> NormalizeDiacritics(source)

**Description :**<br> Nécessite un argument de chaîne. Retourne la chaîne, où les caractères diacritiques ont été remplacés par leurs équivalents non diacritiques. Généralement utilisé pour convertir les prénoms et les noms contenant des caractères diacritiques (accents) en valeurs autorisées pouvant être utilisées dans différents identificateurs d’utilisateurs, tels que les noms d’utilisateurs principaux, les noms de compte SAM et les adresses e-mail.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |Chaîne | Généralement un attribut de nom ou de prénom. |

---
### <a name="not"></a>not
**Fonction :**<br> Not(source)

**Description :**<br> inverse la valeur booléenne de la **source**. Si la valeur **source** est « *True* », cette fonction retourne «*False* ». Sinon, elle retourne «*True*».

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |Chaîne de type Boolean |Les valeurs **sources** attendues sont « True » ou « False ». |

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
| **source** |Obligatoire |Chaîne |Généralement, nom de l’attribut de l’objet **source**. |
| **oldValue** |Facultatif |Chaîne |Valeur à remplacer dans **source** ou **template**. |
| **regexPattern** |Facultatif |Chaîne |Modèle d’expression régulière pour la valeur à remplacer dans **source**. Ou, quand **replacementPropertyName** est utilisé, modèle pour extraire la valeur de **replacementPropertyName**. |
| **regexGroupName** |Facultatif |Chaîne |Nom du groupe à l’intérieur de **regexPattern**. Nous extrayons la valeur de ce groupe en tant que **replacementValue** à partir de **replacementPropertyName** uniquement quand **replacementPropertyName** est utilisé. |
| **replacementValue** |Facultatif |Chaîne |Nouvelle valeur par laquelle remplacer l’ancienne. |
| **replacementAttributeName** |Facultatif |Chaîne |Nom de l’attribut à utiliser pour la valeur de remplacement. |
| **template** |Facultatif |Chaîne |Lorsque la valeur **template** est fournie, nous recherchons la valeur **oldValue** dans le modèle et la remplaçons par la valeur **source**. |

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
| **uniqueValueRule1  … uniqueValueRuleN** |Au moins 2 requis, aucune limite supérieure |Chaîne | Liste des règles de génération de valeur unique à évaluer. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Fonction :**<br> SingleAppRoleAssignment([appRoleAssignments])

**Description :**<br> Retourne un appRoleAssignment unique parmi la liste de tous les appRoleAssignments affectés à un utilisateur pour une application donnée. Cette fonction est nécessaire pour convertir l’objet appRoleAssignments en une chaîne de nom de rôle unique. Notez que la bonne pratique consiste à s’assurer qu’un seul appRoleAssignment est attribué à un seul utilisateur à la fois, et si plusieurs rôles sont attribués, la chaîne de rôle retournée ne doit pas être prévisible. 

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Obligatoire |Chaîne |Objet **[appRoleAssignments]** . |

---
### <a name="split"></a>Split
**Fonction :**<br> Split (source, délimiteur)

**Description :**<br> Fractionne une chaîne en un tableau à plusieurs valeurs, en utilisant le caractère délimiteur spécifié.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |Chaîne |**source** à mettre à jour. |
| **délimiteur** |Obligatoire |Chaîne |Spécifie le caractère qui sera utilisé pour fractionner la chaîne (exemple : « , ») |

---
### <a name="stripspaces"></a>StripSpaces
**Fonction :**<br> StripSpaces(source)

**Description :**<br> supprime tous les caractères d’espacement (" ") de la chaîne source.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |Chaîne |**source** à mettre à jour. |

---
### <a name="switch"></a>Switch
**Fonction :**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**Description :**<br> quand la valeur **source** correspond à une **clé**, retourne la **valeur** de cette **clé**. Si la valeur **source** ne correspond à aucune clé, retourne **defaultValue**.  Les paramètres **key** et **value** doivent toujours être fournis par paires. La fonction attend toujours un nombre pair de paramètres. La fonction ne doit pas être utilisée pour les attributs référentiels tels que le gestionnaire. 

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |Chaîne |**Source** à mettre à jour. |
| **defaultValue** |Facultatif |Chaîne |Valeur par défaut à utiliser quand la source ne correspond à aucune clé. Peut être une chaîne vide (""). |
| **key** |Obligatoire |Chaîne |**Key** avec laquelle comparer la valeur **source**. |
| **value** |Obligatoire |Chaîne |Valeur de remplacement pour la **source** correspondant à la clé. |

---
### <a name="tolower"></a>ToLower
**Fonction :**<br> ToLower(source, culture)

**Description :**<br> prend une valeur de la chaîne *source* et la convertit en minuscules à l’aide des règles de culture spécifiées. Si aucune information de *culture* n'est spécifiée, une culture invariante est utilisée.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |Chaîne |Généralement le nom de l’attribut de l’objet source |
| **culture** |Facultatif |Chaîne |Le format du nom de culture basé sur RFC 4646 est *languagecode2-country/regioncode2*, où *languagecode2* correspond au code de langue à deux lettres et *country/regioncode2* au code de sous-culture à deux lettres, par exemple, ja-JP pour le japonais (Japon) et en-US pour l’anglais (États-Unis). Si un code de langue à deux lettres n'est pas disponible, un code à trois lettres dérivé de la norme ISO 639-2 est utilisé.|

---
### <a name="toupper"></a>ToUpper
**Fonction :**<br> ToUpper(source, culture)

**Description :**<br> prend une valeur de la chaîne *source* et la convertit en majuscule à l’aide des règles de culture spécifiées. Si aucune information de *culture* n'est spécifiée, une culture invariante est utilisée.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Notes |
| --- | --- | --- | --- |
| **source** |Obligatoire |Chaîne |Généralement le nom de l’attribut de l’objet source. |
| **culture** |Facultatif |Chaîne |Le format du nom de culture basé sur RFC 4646 est *languagecode2-country/regioncode2*, où *languagecode2* correspond au code de langue à deux lettres et *country/regioncode2* au code de sous-culture à deux lettres, par exemple, ja-JP pour le japonais (Japon) et en-US pour l’anglais (États-Unis). Si un code de langue à deux lettres n'est pas disponible, un code à trois lettres dérivé de la norme ISO 639-2 est utilisé.|

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

## <a name="related-articles"></a>Articles connexes
* [Automatiser l’approvisionnement/le déprovisionnement des utilisateurs pour les applications SaaS](user-provisioning.md)
* [Personnalisation des mappages d’attributs pour l’approvisionnement des utilisateurs](customize-application-attributes.md)
* [Filtres d’étendue pour l’approvisionnement des utilisateurs](define-conditional-rules-for-provisioning-user-accounts.md)
* [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](use-scim-to-provision-users-and-groups.md)
* [Notifications d’approvisionnement de comptes](user-provisioning.md)
* [Liste des didacticiels sur l’intégration des applications SaaS](../saas-apps/tutorial-list.md)
