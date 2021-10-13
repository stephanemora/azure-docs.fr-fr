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
ms.date: 09/24/2021
ms.author: danlep
ms.openlocfilehash: 3f16961ec4774708fa55a2a49e408a6b980cdb31
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057603"
---
# <a name="api-import-restrictions-and-known-issues"></a>Restrictions et problèmes connus relatifs à l’importation d’API

## <a name="about-this-list"></a>À propos de cette liste

Lors de l'importation d'une API, il se peut que vous rencontriez certaines restrictions ou que vous deviez identifier et rectifier des problèmes avant de pouvoir réussir l'importation. Cet article porte sur les points suivants :
* Toutes les limitations, organisées par format d'importation de l'API. 
* Fonctionnement de l’exportation OpenAPI.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Limitations relatives à l’importation OpenAPI/Swagger

Si vous recevez des erreurs lors de l’importation de votre document OpenAPI, vérifiez que vous l’avez validée à l’aide de l’une des opérations suivantes :
* À l’aide du concepteur du portail Azure (Conception > Frontale > Éditeur de spécification OpenAPI) ou 
* Avec un outil tiers, tel que <a href="https://editor.swagger.io">Swagger Editor</a>.

### <a name="general"></a><a name="open-api-general"> </a>Généralités

- Les paramètres requis pour le chemin d'accès et la requête doivent avoir des noms uniques.
    - Dans OpenAPI, un nom de paramètre doit être unique seulement dans un emplacement, par exemple, pour un chemin, une requête, un en-tête. 
    - Dans Gestion des API, nous permettons aux opérations d'être différenciées à la fois par le chemin d'accès et les paramètres de la requête. 
        - Étant donné qu’OpenAPI ne prend pas en charge cela, les noms de paramètres doivent être uniques dans l’ensemble du modèle d’URL.
- En cas d’importation incluse dans Gestion des API, une spécification OpenAPI peut avoir une taille maximale de 4 Mo. 
    - La limite de taille ne s’applique pas quand un document OpenAPI est fourni via l’URL d’un emplacement accessible à partir de votre service Gestion des API.
- Les pointeurs `\$ref` ne peuvent pas référencer des fichiers externes.
- Les seules extensions prises en charge sont :
    - `x-ms-paths` 
    - `x-servers` 
- Extensions personnalisées :
    - Ignorées lors de l’importation.
    - Non enregistrées ou conservées pour l’exportation.
- Les définitions de sécurité sont ignorées.
- `Recursion`: 
    - Gestion des API ne prend pas en charge les définitions définies de manière récursive.
    - Par exemple, les schémas qui font référence à eux-mêmes.
- L’objet `server` n’est pas pris en charge au niveau des opérations de l’API.
- Le mot clé `Produces` (qui décrit les types MIME retournés par une API) n’est pas pris en charge. 
- L’URL de fichier source disponible est appliquée aux URL de serveur relatives.
- Les définitions de schéma Inline pour les opérations d’API ne sont pas prises en charge. Définitions de schéma :
    - Définies dans l’étendue de l’API.
    - Peuvent être référencées dans les étendues de requête ou de réponse des opérations de l’API.
- Un paramètre d’URL défini doit faire partie du modèle d’URL.

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI version 2

La prise en charge d’OpenAPI version 2 est limitée au format JSON uniquement.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI version 3

-   Si plusieurs `servers` sont spécifiés, Gestion des API utilise la première URL HTTPS trouvée. 
- S’il n’y a pas d’URL HTTPS, l’URL du serveur est vide.
- `Examples` n’est pas pris en charge, contrairement à `example`.
- Les champs suivants sont inclus dans OpenAPI version 3.x, mais ne sont pas pris en charge :
    - `explode`
    - `style`
    - `allowReserved`

## <a name="openapi-import-update-and-export-mechanisms"></a>Mécanismes d’importation, de mise à jour et d’exportation OpenAPI

### <a name="general"></a><a name="open-import-export-general"> </a>Général

Les définitions d’API exportées à partir d’un service Gestion des API sont :
* Principalement destinées aux applications externes qui doivent appeler l'API hébergée dans le service Gestion des API. 
* Non destinées à être importées dans le même service Gestion des API ou dans un service différent. 

Pour la gestion de la configuration des définitions d’API dans différents services/environnements, consultez la documentation relative à l’utilisation du service Gestion des API avec Git. 

### <a name="add-new-api-via-openapi-import"></a>Ajouter une nouvelle API via l’importation OpenAPI

Pour chaque opération trouvée dans le document OpenAPI, une nouvelle opération est créée avec le nom de ressource Azure et le nom d’affichage définis sur `operationId` et `summary` respectivement. 
* La valeur `operationId` est normalisée.
    *  Si `operationId` n’est pas spécifié (non présent, `null` ou vide), la valeur du nom de ressource Azure sera générée en combinant la méthode HTTP et le modèle de chemin d’accès, par exemple, `get-foo`.
* La valeur `summary` est importée telle quelle et sa longueur est limitée à 300 caractères.
    * Si `summary` n’est pas spécifié (non présent, `null` ou vide), la valeur `display name` est définie sur `operationId`. 

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

- **Liaisons SOAP**  
    - Seules les liaisons SOAP de style d’encodage « document » et « literal » sont prises en charge.
    - Les encodages SOAP ou de style « rpc » ne sont pas pris en charge.
- **WSDL:Import**
    - Non pris en charge. Au lieu de cela, fusionnez les importations dans un document.
- **Messages avec plusieurs parties** 
    - Ce type de message n’est pas pris en charge.
- **WCF wsHttpBinding** 
    - Les services SOAP créés avec Windows Communication Foundation doivent utiliser `basicHttpBinding`.
    - `wsHttpBinding` n’est pas pris en charge.
- **MTOM** 
    - Les services utilisant `MTOM` *pourraient* fonctionner. 
    - Aucune prise en charge officielle n’est disponible pour l’instant.
- **Récursivité** 
    - Les types définis de manière récursive ne sont pas pris en charge par Gestion des API.
    - Par exemple, s’ils font référence à un tableau d’eux-mêmes.
- **Espaces de noms multiples** 
    - Si plusieurs espaces de noms peuvent être utilisés dans un schéma, seul l’espace de noms cible peut être utilisé pour définir des parties de message. 
    - Les espaces de noms autres que la cible ne sont pas préservés. 
        - Ces espaces de noms sont utilisés pour définir d’autres éléments d’entrée ou de sortie.
        - Même si un tel document WSDL peut être importé, lors de l’exportation, toutes les parties de message auront l’espace de noms cible du WSDL.
- **Tableaux** 
    - La transformation SOAP à REST prend uniquement en charge les tableaux encapsulés indiqués dans l’exemple ci-dessous :

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
