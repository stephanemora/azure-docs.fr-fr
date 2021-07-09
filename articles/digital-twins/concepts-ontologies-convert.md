---
title: Conversion d’ontologies
titleSuffix: Azure Digital Twins
description: Présentation du processus de conversion des modèles conformes aux standards du secteur en langage DTDL pour Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ebe6a96c88d061138eb629f50aa37ea90fe40eb4
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110475876"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Convertir des ontologies conformes aux standards du secteur en langage DTDL pour Azure Digital Twins

La plupart des [ontologies](concepts-ontologies.md) sont basées sur des standards du web sémantique tels que [OWL](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF) et [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Pour utiliser un modèle avec Azure Digital Twins, il doit être au format DTDL. Cet article fournit des conseils d’aide généraux relatifs à la conception sous la forme d’un **modèle de conversion**. Celui-ci permet de convertir des modèles RDF au format DTDL pour qu’ils puissent être utilisés avec Azure Digital Twins. 

L’article contient également un [exemple de code de convertisseur](#converter-samples) pour les convertisseurs RDF et OWL, qui peut être étendu à d’autres schémas du secteur du bâtiment.

## <a name="conversion-pattern"></a>Modèle de conversion

Il existe plusieurs bibliothèques tierces qui peuvent être utilisées lors de la conversion de modèles RDF en DTDL. Certaines de ces bibliothèques vous permettent de charger votre fichier de modèle dans un graphique. Vous pouvez parcourir le graphique en recherchant des constructions RDFS et OWL spécifiques, et les convertir en DTDL.   

Le tableau suivant est un exemple de la façon dont les constructions RDFS et OWL peuvent être mappées à DTDL. 

| Concept RDFS/OWL | Construction RDFS/OWL | Concept DTDL | Construction DTDL |
| --- | --- | --- | --- |
| Classes | `owl:Class`<br>Suffixe IRI<br>``rdfs:label``<br>``rdfs:comment`` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Sous-classes | `owl:Class`<br>Suffixe IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Propriétés DataType | `owl:DatatypeProperty`<br>`rdfs:label` ou `INode`<br>`rdfs:label`<br>`rdfs:range` | Propriétés de l’interface | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Propriétés des objets | `owl:ObjectProperty`<br>`rdfs:label` ou `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relationship | `type:Relationship`<br>`name`<br>`target` (ou omis si aucune `rdfs:range`)<br>`comment`<br>`displayName`<br>

L’extrait de code C# suivant montre comment un fichier de modèle RDF est chargé dans un graphique et converti en DTDL à l’aide de la bibliothèque [dotNetRDF](https://www.dotnetrdf.org/). 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>Exemples de convertisseurs

### <a name="rdf-converter-application"></a>Application de conversion RDF 

Un exemple d’application disponible convertit un fichier de modèle RDF en [DTDL (version 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) pour une utilisation par le service Azure Digital Twins. Il a été validé pour le schéma [Brick](https://brickschema.org/ontology/) et peut être étendu pour d’autres schémas du secteur du bâtiment (par exemple, [Building Topology Ontology (BOT)](https://w3c-lbd-cg.github.io/bot/), [Semantic Sensor Network](https://www.w3.org/TR/vocab-ssn/) ou [buildingSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

L’exemple est une [application en ligne de commande .NET Core appelée RdfToDtdlConverter](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/).

Pour télécharger le code sur votre ordinateur, sélectionnez le bouton **Parcourir le code** sous le titre sur la page de l’exemple, qui vous permet d’accéder au référentiel GitHub de l’exemple. Sélectionnez le bouton **Code** et **Télécharger le fichier ZIP** pour télécharger l’exemple sous la forme d’un fichier .zip appelé *RdfToDtdlConverter-main.zip*. Vous pouvez ensuite décompresser le fichier et explorer le code.

:::image type="content" source="media/concepts-ontologies-convert/download-repo-zip.png" alt-text="Capture d’écran du dépôt RdfToDtdlConverter sur GitHub. Le bouton Code est sélectionné, générant une boîte de dialogue, dans laquelle le bouton Télécharger le zip est en surbrillance." lightbox="media/concepts-ontologies-convert/download-repo-zip.png":::

Vous pouvez utiliser cet exemple pour voir les modèles de conversion en contexte et comme bloc de construction pour vos propres applications effectuant des conversions de modèle en fonction de vos besoins spécifiques.

### <a name="owl2dtdl-converter"></a>Convertisseur OWL2DTDL 

Le [convertisseur OWL2DTDL](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) est un exemple d’application qui traduit une ontologie OWL en un ensemble de déclarations d’interface DTDL, qui peuvent être utilisées avec le service Azure Digital Twins. Il fonctionne également pour les réseaux d’ontologies, constitués d’une ontologie racine réutilisant d’autres ontologies via des déclarations `owl:imports`.

Ce convertisseur a été utilisé pour traduire l’[ontologie RealEstateCore](https://doc.realestatecore.io/3.1/full.html) en DTDL, et peut être utilisé pour n’importe quelle ontologie basée sur OWL.

## <a name="next-steps"></a>Étapes suivantes 

* Découvrez plus en détail comment étendre les ontologies conformes aux standards du secteur pour répondre aux impératifs de vos spécifications : [Concepts : Extension des ontologies du secteur](concepts-ontologies-extend.md).

* Sinon, passez au développement de modèles à partir d’ontologies : [Utilisation de stratégies d’ontologie pour le développement de modèles](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).