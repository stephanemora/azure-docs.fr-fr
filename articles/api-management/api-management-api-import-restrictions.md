---
title: Restrictions et problèmes connus relatifs à l’importation d’API dans la gestion des API Azure | Microsoft Docs
description: Détails des problèmes connus et des restrictions relatifs à l’importation dans la gestion des API Azure à l’aide des formats Open API, WSDL ou WADL.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: 251caa840446e75ff13d9b4dcebcbae3a36473c8
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265445"
---
# <a name="api-import-restrictions-and-known-issues"></a>Restrictions et problèmes connus relatifs à l’importation d’API
## <a name="about-this-list"></a>À propos de cette liste
Quand vous importez une API, vous pouvez rencontrer certaines restrictions ou identifier des problèmes que vous devez résoudre au préalable. Cet article documente ces restrictions et problèmes connus, organisés par format d’importation de l’API.

## <a name="open-api"> </a>OpenAPI/Swagger

Si vous recevez des erreurs durant l’importation de votre document OpenAPI, vérifiez que vous l’avez validé au préalable. Vous pouvez le faire avec le concepteur dans le portail Azure (Conception - Front End - Éditeur de spécification OpenAPI) ou à l’aide d’un outil tiers comme <a href="https://editor.swagger.io">Swagger Editor</a>.

### <a name="open-api-general"> </a>Général

* Les paramètres requis pour le chemin d'accès et la requête doivent avoir des noms uniques. (Dans OpenAPI, un nom de paramètre doit être unique seulement dans un emplacement, par exemple, pour un chemin, une requête, un en-tête. Toutefois, dans Gestion des API nous autorisons la discrimination des opérations dans les paramètres de chemin et de requête (non pris en charge par OpenAPI). C’est pourquoi nous demandons à ce que les noms de paramètre soient uniques dans l’ensemble du modèle d’URL.)
* Les pointeurs **$ref** ne peuvent pas référencer des fichiers externes.
* **x-ms-paths** et **x-servers** sont les seules extensions prises en charge.
* Les extensions personnalisées sont ignorées à l’importation et ne sont pas enregistrées ni conservées pour l’exportation.
* **Récursivité** : Gestion des API ne prend pas en charge les définitions spécifiées de manière récursive (par exemple, les schémas qui se référencent eux-mêmes).
* L’URL de fichier source (si disponible) est appliquée aux URL de serveur relatives.

### <a name="open-api-v2"> </a>OpenAPI version 2

* Seul le format JSON est pris en charge.

### <a name="open-api-v3"> </a>OpenAPI version 3

* Si de nombreux **serveurs** sont spécifiés, Gestion des API tente de sélectionner la première URL HTTPs. En cas d’absence d’URL HTTPs, elle sélectionne la première URL HTTP. En cas d’absence d’URL HTTP, l’URL de serveur est vide.
* Le terme **Examples** n’est pas pris en charge, contrairement à **example**.
* **Multipart/form-data** n’est pas pris en charge.

> [!IMPORTANT]
> Consultez ce [document](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) pour obtenir des informations importantes et des conseils liés à l’importation OpenAPI.

## <a name="wsdl"></a>WSDL
Les fichiers WSDL servent à générer des API Pass-through SOAP ou constituent le backend d’une API SOAP à REST.
* **Liaisons SOAP** : seules les liaisons SOAP avec encodage de style « document » et « littéral » sont prises en charge. Les encodages SOAP ou de style « rpc » ne sont pas pris en charge.
* **WSDL:Import** : Cet attribut n’est pas pris en charge. Les clients doivent fusionner les importations dans un seul document.
* **Messages en plusieurs parties** : Ces types de messages ne sont pas pris en charge.
* **WCF wsHttpBinding** : Les services SOAP créés avec Windows Communication Foundation doivent utiliser basicHttpBinding. wsHttpBinding n’est pas pris en charge.
* **MTOM** : les services utilisant MTOM <em>peuvent</em> fonctionner. Aucune prise en charge officielle n’est disponible pour l’instant.
* **Récursivité** : les types qui sont définis de manière récursive (par exemple, qui font référence à leur propre tableau) ne sont pas pris en charge par APIM.
* **Plusieurs espaces de noms** : plusieurs espaces de noms peuvent être utilisés dans un schéma, mais seul l’espace de noms cible peut être utilisé pour définir des parties de message. Les espaces de noms autre que la cible qui permettent de définir d’autres éléments d’entrée ou de sortie ne sont pas conservés. Même si un tel document WSDL peut être importé, lors de l’exportation, toutes les parties de message auront l’espace de noms cible du WSDL.

## <a name="wadl"></a>WADL
Il n’existe aucun problème connu relatif à l’importation WADL.
