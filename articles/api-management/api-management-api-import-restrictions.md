---
title: Restrictions et détails de la prise en charge des formats d’API
titleSuffix: Azure API Management
description: Détails des problèmes connus et des restrictions relatifs à la prise en charge des formats Open API, WSDL ou WADL dans Gestion des API Azure.
services: api-management
documentationcenter: ''
author: dlepow
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/26/2021
ms.author: danlep
ms.openlocfilehash: 5ddb4d615dc3d1bd7c9830d11a27341baf57edb3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031809"
---
# <a name="api-import-restrictions-and-known-issues"></a>Restrictions et problèmes connus relatifs à l’importation d’API

Lors de l'importation d'une API, il se peut que vous rencontriez certaines restrictions ou que vous deviez identifier et rectifier des problèmes avant de pouvoir réussir l'importation. Cet article porte sur les points suivants :
* Comportement de Gestion des API pendant l’importation OpenAPI. 
* Limitations d’importation, organisées par le format d'importation de l'API. 
* Fonctionnement de l’exportation OpenAPI.

## <a name="api-management-during-openapi-import"></a>Gestion des API lors de l’importation OpenAPI

Lors de l’importation OpenAPI, Gestion des API :
* Vérifie spécifiquement les paramètres de chaîne de requête marqués comme obligatoires.
* Convertit les paramètres de chaîne de requête en paramètres de modèle. 

Si vous préférez un comportement différent, vous pouvez : 
* Le modifier manuellement via l’éditeur basé sur des formulaires, ou 
* Supprimer l’attribut « required » de la définition OpenAPI, pour que les paramètres ne soient pas convertis en paramètres de modèle.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Limitations relatives à l’importation OpenAPI/Swagger

Si vous recevez des erreurs lors de l’importation de votre document OpenAPI, vérifiez que vous l’avez validée à l’aide de l’une des opérations suivantes :
* À l’aide du concepteur du portail Azure (Conception > Frontale > Éditeur de spécification OpenAPI) ou 
* Avec un outil tiers, tel que <a href="https://editor.swagger.io">Swagger Editor</a>.

### <a name="general"></a><a name="open-api-general"> </a>Généralités

#### <a name="url-template-requirements"></a>Spécifications de modèle URL

| Condition requise | Description |
| ----------- | ----------- |
| **Noms uniques pour le chemin d’accès et les paramètres de requête requis** | Dans OpenAPI : <ul><li>Un nom de paramètre doit être unique seulement dans un emplacement, par exemple, pour un chemin, une requête, un en-tête.</li></ul>Dans Gestion des API :<ul><li>Nous permettons aux opérations d'être différenciées à la fois par le chemin d'accès et les paramètres de la requête.</li><li>Étant donné qu’OpenAPI ne prend pas en charge cette différenciation, les noms de paramètres doivent être uniques dans l’ensemble du modèle d’URL.</li></ul>  |
| **Paramètre d’URL défini** | Doit faire partie du modèle d’URL. |
| **URL du fichier source disponible** | Appliquée aux URL de serveur relatives. |
| **`\$ref` pointeurs** | Ne peuvent pas référencer des fichiers externes. |


#### <a name="openapi-specifications"></a>Spécifications OpenAPI

**Versions prises en charge**

Gestion des API prend en charge uniquement les éléments suivants :
* OpenAPI version 2.
* OpenAPI version 3.0.x (jusqu’à la version 3.0.3).

La version 3.1 d’OpenAPI n’est actuellement pas prise en charge dans Gestion des API.

**Limitations de taille**

| Taille maximale | Description |
| ---------- | ----------- |
| **Jusqu’à 4 Mo** | Lorsqu’une spécification OpenAPI est importée en ligne dans Gestion des API. |
| **La limite de taille ne s’applique pas** | Quand un document OpenAPI est fourni via l’URL d’un emplacement accessible à partir de votre service Gestion des API. |

#### <a name="supported-extensions"></a>Extensions prises en charge
Les seules extensions prises en charge sont :

| Extension | Description |
| ----------- | ----------- |
| **`x-ms-paths`** | <ul><li>Vous permet de définir des chemins qui se différencient par des paramètres de requête dans l’URL.</li><li>Sujets abordés dans les [documents AutoRest](https://github.com/Azure/autorest/tree/main/docs/extensions#x-ms-paths).</li></ul> |
| **`x-servers`** | Un rétroportage de l’objet [OpenAPI 3 `servers` ](https://swagger.io/docs/specification/api-host-and-base-path/) pour OpenAPI 2. |

#### <a name="unsupported-extensions"></a>Extensions non prises en charge
| Extension | Description |
| ----------- | ----------- |
| **`Recursion`** | Gestion des API ne prend pas en charge les définitions définies de manière récursive.<br />Par exemple, les schémas qui font référence à eux-mêmes. |
| **`Server` objet** | Non pris en charge au niveau des opérations de l’API. |
| **`Produces` mot clé** | Décrit les types MIME retournés par une API. <br />Non pris en charge. |

#### <a name="custom-extensions"></a>Extensions personnalisées
- Ignorées lors de l’importation.
- Non enregistrées ou conservées pour l’exportation.

#### <a name="unsupported-definitions"></a>Définitions non prises en charge 
Les définitions de schéma Inline pour les opérations d’API ne sont pas prises en charge. Définitions de schéma :
- Définies dans l’étendue de l’API.
- Peuvent être référencées dans les étendues de requête ou de réponse des opérations de l’API.

#### <a name="ignored-definitions"></a>Définitions ignorées
Les définitions de sécurité sont ignorées.

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI version 2

La prise en charge d’OpenAPI version 2 est limitée au format JSON uniquement.

### <a name="openapi-version-30x"></a><a name="open-api-v3"> </a>OpenAPI version 3.0.x

La dernière version d’OpenAPI 3.0 prise en charge est 3.0.3.

#### <a name="https-urls"></a>URL HTTPS
- Si plusieurs `servers` sont spécifiés, Gestion des API utilise la première URL HTTPS trouvée. 
- S’il n’y a pas d’URL HTTPS, l’URL du serveur est vide.

#### <a name="supported"></a>Prise en charge
- `example`

#### <a name="unsupported"></a>Non pris en charge
Les champs suivants sont inclus dans [OpenAPI version 3.0.x](https://swagger.io/specification/), mais ne sont pas pris en charge :

| Object | Champ |
| ----------- | ----------- |
| **OpenAPI** | `externalDocs` |
| **Composants** | <ul><li>`responses`</li><li>`parameters`</li><li>`examples`</li><li>`requestBodies`</li><li>`headers`</li><li>`securitySchemes`</li><li>`links`</li><li>`callbacks`</li></ul> |
| **PathItem** | <ul><li>`trace`</li><li>`servers`</li></ul> |
| **opération** | <ul><li>`externalDocs`</li><li>`callbacks`</li><li>`security`</li><li>`servers`</li></ul> |
| **Paramètre** | <ul><li>`allowEmptyValue`</li><li>`style`</li><li>`explode`</li><li>`allowReserved`</li></ul> |

## <a name="openapi-import-update-and-export-mechanisms"></a>Mécanismes d’importation, de mise à jour et d’exportation OpenAPI

### <a name="general"></a><a name="open-import-export-general"> </a>Général

Les définitions d’API exportées à partir d’un service Gestion des API sont :
* Principalement destinées aux applications externes qui doivent appeler l'API hébergée dans le service Gestion des API. 
* Non destinées à être importées dans le même service Gestion des API ou dans un service différent. 

Pour la gestion de la configuration des définitions d’API dans différents services/environnements, consultez la documentation relative à l’utilisation du service Gestion des API avec Git. 

### <a name="add-new-api-via-openapi-import"></a>Ajouter une nouvelle API via l’importation OpenAPI

Pour chaque opération trouvée dans le document OpenAPI, une nouvelle opération est créée avec :
* Nom de la ressource Azure définie sur `operationId`.
    * La valeur `operationId` est normalisée.
    *  Si `operationId` n’est pas spécifié (non présent, `null` ou vide), la valeur du nom de la ressource Azure sera générée en combinant la méthode HTTP et le modèle de chemin d’accès.
        * Par exemple : `get-foo`.

* Nom complet défini sur `summary`. 
    * valeur `summary` :
        * Importée telle quelle.
        * La longueur est limitée à 300 caractères.
    * Si `summary` n’est pas spécifié (non présent, `null` ou vide), la valeur du nom complet est définie sur `operationId`. 

### <a name="update-an-existing-api-via-openapi-import"></a>Mettre à jour une API existante via l’importation OpenAPI

Lors de l’importation, l’opération d’API existante :
* Change pour correspondre à l’API décrite dans le document OpenAPI. 
* Effectue une correspondance sur une opération dans le document OpenAPI en comparant sa valeur `operationId` au nom de ressource Azure de l’opération existante. 
    * Si une correspondance est trouvée, les propriétés de l’opération existante sont mises à jour « sur place ».
    * Si aucune correspondance n’est trouvée :
        * Une nouvelle opération est créée en combinant la méthode HTTP et le modèle de chemin, par exemple `get-foo`. 
        * Pour chaque nouvelle opération, l’importation tente de copier les stratégies à partir d’une opération existante avec la même méthode HTTP et le même modèle de chemin d’accès.

Toutes les opérations sans correspondance existantes sont supprimées.

Pour que l’importation soit plus prévisible, suivez les instructions ci-dessous :

- Spécifiez la propriété `operationId` pour chaque opération.
- Évitez de modifier `operationId` après l’importation initiale.
- Ne modifiez jamais `operationId` et la méthode HTTP ou le modèle de chemin d’accès en même temps.

### <a name="export-api-as-openapi"></a>Exporter l’API comme OpenAPI

Pour chaque opération :
* Le nom de la ressource Azure sera exporté en tant que `operationId`.
* Le nom d’affichage sera exporté en tant que `summary`.

**Règles de normalisation pour `operationId`**
- Convertit les caractères en minuscules.
- Remplacez chaque séquence de caractères non alphanumériques par un tiret unique.
    - Par exemple, `GET-/foo/{bar}?buzz={quix}` sera transformé en `get-foo-bar-buzz-quix-`.
- Coupez les tirets des deux côtés.
    - Par exemple, `get-foo-bar-buzz-quix-` deviendra `get-foo-bar-buzz-quix`
- Tronquez pour obtenir 76 caractères, quatre caractères de moins que la limite maximale pour un nom de ressource.
- Utilisez les quatre caractères restants pour un suffixe de déduplication, si nécessaire, sous la forme `-1, -2, ..., -999`.

## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

Vous pouvez créer des API SOAP pass-through et SOAP-à-REST avec des fichiers WSDL.

### <a name="soap-bindings"></a>Liaisons SOAP 
- Seules les liaisons SOAP de style d’encodage « document » et « literal » sont prises en charge.
- Les encodages SOAP ou de style « rpc » ne sont pas pris en charge.

### <a name="wsdlimport"></a>WSDL:Import
Non pris en charge. Au lieu de cela, fusionnez les importations dans un document.

### <a name="messages-with-multiple-parts"></a>Messages avec plusieurs parties 
Ce type de message n’est pas pris en charge.

### <a name="wcf-wshttpbinding"></a>WCF wsHttpBinding 
- Les services SOAP créés avec Windows Communication Foundation doivent utiliser `basicHttpBinding`.
- `wsHttpBinding` n’est pas pris en charge.

### <a name="mtom"></a>MTOM 
- Les services utilisant `MTOM` *pourraient* fonctionner. 
- Aucune prise en charge officielle n’est disponible pour l’instant.

### <a name="recursion"></a>Récursivité
- Les types définis de manière récursive ne sont pas pris en charge par Gestion des API.
- Par exemple, s’ils font référence à un tableau d’eux-mêmes.

### <a name="multiple-namespaces"></a>Espaces de noms multiples
Si plusieurs espaces de noms peuvent être utilisés dans un schéma, seul l’espace de noms cible peut être utilisé pour définir des parties de message. Ces espaces de noms sont utilisés pour définir d’autres éléments d’entrée ou de sortie.

Les espaces de noms autres que la cible ne sont pas préservés lors de l’exportation. Bien que vous puissiez importer un document WSDL qui définit des parties de message avec d’autres espaces de noms, toutes les parties de message disposent de l’espace de noms cible WSDL lors de l’exportation.

### <a name="arrays"></a>Tableaux 
La transformation SOAP à REST prend uniquement en charge les tableaux encapsulés indiqués dans l’exemple ci-dessous :

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

Il n’existe aucun problème connu relatif à l’importation WADL.
