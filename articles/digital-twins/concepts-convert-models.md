---
title: Conversion de modèles standard
titleSuffix: Azure Digital Twins
description: Comprendre le modèle de conversion de modèles standard (RDF/OWL) en DTDL
author: baanders
ms.author: baanders
ms.date: 9/28/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: bf39bd0c787ed64d573cc2a257442b4d3bb8ab7d
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015112"
---
# <a name="convert-industry-standard-models-to-dtdl-for-azure-digital-twins"></a>Convertir des modèles standard en DTDL pour Azure Digital Twins

Les modèles dans Azure Digital Twins sont représentés à l’aide du [**langage DTDL (Digital Twins Definition Language)** ](concepts-models.md) basé sur JSON-LD. Si vous avez des modèles existants en dehors d’Azure Digital Twins qui sont basés sur une norme du secteur, comme RDF ou OWL, vous devez **les convertir en DTDL** afin de les utiliser avec Azure Digital Twins. La version DTDL devient alors la source de vérité pour le modèle dans Azure Digital Twins.

Cet article décrit un modèle pour la conversion de modèles industriels ou personnalisés basés sur RDF en DTDL. Il inclut :
* Des **conseils de niveau de stratégie** qui peuvent être appliqués à une variété de normes et de types de modèles
* [**Exemple de code** pour un convertisseur spécifique à RDF](#sample-converter-application)

## <a name="industry-models"></a>Modèles sectoriels  

L’utilisation de modèles sectoriels fournit un point de départ riche lors de la conception de votre modèle Azure Digital Twins. L’utilisation de modèles sectoriels facilite également la normalisation et le partage d’informations. 

Voici quelques exemples de modèles sectoriels courants :  

| Secteur vertical | Modèle |
| --- | --- | 
| Gestion des bâtiments et des installations | [RealEstateCore](https://www.realestatecore.io/)<br>[Schéma BRICK](https://brickschema.org/ontology/1.1/)<br>[Ontologie de topologie de construction (BOT)](https://w3c-lbd-cg.github.io/bot/)<br>[Réseau de capteurs sémantiques](https://www.w3.org/TR/vocab-ssn/)<br>[Industry Foundation Classes (IFC) buildingSmart](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/) |
| Villes intelligentes | [ETSI NGSI-LD](https://www.etsi.org/deliver/etsi_gr/CIM/001_099/008/01.01.01_60/gr_CIM008v010101p.pdf)<br>[Informations de référence sur les applications intelligentes (Smart Applications REFerence - SAREF)](https://saref.etsi.org/)<br>[Fiware](https://www.fiware.org/)<br>[Villes intelligentes ouvertes et agiles (Open & Agile Smart Cities - OASC)](https://oascities.org/) |
| Réseau électrique | [CIM](https://cimug.ucaiug.org/)/[IEC 61968](https://en.wikipedia.org/wiki/IEC_61968)<br>[Modèles ADRM](http://www.adrm.com/) pour les échanges d’énergie et de marchandises | 

En fonction de vos besoins, vous pouvez également utiliser DTDL pour personnaliser ou étendre des modèles sectoriels, ou développer votre propre modèle personnalisé à partir de zéro. 

## <a name="create-and-edit-models"></a>Créer et modifier des modèles

La première étape de la modélisation consiste à créer vos modèles. Vous pouvez créer et terminer la modification de vos modèles standard avant de les convertir en DTDL, ou vous pouvez les convertir en DTDL au préalable et poursuivre leur modification en tant que documents DTDL.

### <a name="with-industry-standards"></a>Avec les normes sectorielles

La plupart des modèles sectoriels (également appelés **ontologies**) sont basés sur des normes web sémantiques comme [OWL](https://www.w3.org/OWL/[), [RDF](https://www.w3.org/2001/sw/wiki/RDF) et [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Dans certains cas, vous pouvez créer ou modifier un modèle à l’aide des outils de modèle basés sur OWL. Vous pouvez utiliser un nombre quelconque d’outils de création de modèles pour concevoir un modèle basé sur OWL, y compris les suivants.
* [WebProtégé](https://protege.stanford.edu/products.php#web-protege) et [Protégé Desktop](https://protege.stanford.edu/products.php#desktop-protege) sont des exemples populaires. Vous pouvez importer des modèles sectoriels dans plusieurs formats, modifier ou étendre un modèle, et exporter le modèle. 
* [WebVOWL](http://www.visualdataweb.de/webvowl/) est un autre outil populaire pour la visualisation des modèles. 

Si vous créez un modèle à l’aide d’une norme sectorielle qui n’est pas DTDL, vous devez la convertir en DTDL et la charger sur Azure Digital Twins. 

#### <a name="common-model-file-formats"></a>Formats de fichiers de modèles communs 

RDF, OWL et RDFS sont les blocs de construction de base du web sémantique. 

**RDF** fournit une structure conceptuelle pour décrire les éléments, sous la forme de **triplets**. Les triplets se composent de trois parties : **objet**, **prédicat** et **objet**. Les objets peuvent être constitués d’URI. 

Voici quelques exemples de triplets RDF :

```
<LogicalDevice> <hasCapabiity> <Temperature>
<Chiller> <locatedIn> <Level1>
<Asset> <isPartOf> <Asset>  
```

Ces exemples sont tous des RDF valides, mais la dernière instruction n’est pas sémantiquement valide. C’est dans cette situation que la spécification OWL entre en jeu. **OWL** définit ce que vous pouvez écrire avec RDF afin d’avoir une ontologie valide.

Voici un exemple utilisant OWL : 

```
<Asset> <isPartOf> <Building>
<TemperatureSensor> <isType> <Sensor>
```

**RDFS** fournit des sémantiques de vocabulaire supplémentaires qui vous aident à définir et décrire des classes. Par exemple, une classe de ce type est `rdfs:subClassOf` :

```
<Equipment> <subClassOf> <Asset>
```

Les modèles peuvent être enregistrés, importés et exportés dans de nombreux formats de fichiers, notamment :  
* RDF/XML 
* Turtle (TTL) 
* OWL/XML 

### <a name="with-dtdl"></a>Avec DTDL

Azure Digital Twins utilise le langage  **Digital Twin Definition Language (DTDL)** basé sur JSON-LD pour la modélisation. Tout modèle qui sera utilisé avec Azure Digital Twins devra être écrit à l’origine ou converti en DTDL.

Lorsque vous utilisez des modèles dans DTDL, vous pouvez utiliser [**l’extension DTDL**](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) disponible pour [Visual Studio Code](https://code.visualstudio.com/), qui fournit la validation de la syntaxe et d’autres fonctionnalités pour faciliter l’écriture de modèles DTDL.

Vous pouvez en apprendre davantage sur les modèles Azure Digital Twins et leurs composants dans [*Concepts : Modèles personnalisés*](concepts-models.md) et [*Guide pratique : Gérer des modèles personnalisés*](how-to-manage-model.md).

## <a name="convert-models-to-dtdl"></a>Convertir des modèles en DTDL

Pour utiliser un modèle avec Azure Digital Twins, il doit être au format DTDL. Cette section explique comment convertir des modèles RDF en DTDL afin qu’ils puissent être utilisés avec Azure Digital Twins.

Il existe plusieurs bibliothèques tierces qui peuvent être utilisées lors de la conversion de modèles RDF en DTDL. Certaines de ces bibliothèques vous permettent de charger votre fichier de modèle dans un graphique. Vous pouvez parcourir le graphique en recherchant des constructions RDFS et OWL spécifiques, et les convertir en DTDL.   

Le tableau suivant est un exemple de la façon dont les constructions RDFS et OWL peuvent être mappées à DTDL. 

| Concept RDFS/OWL | Construction RDFS/OWL | Concept DTDL | Construction DTDL |
| --- | --- | --- | --- |
| Classes | `owl:Class`<br>Suffixe IRI<br>``rdfs:label``<br>``rdfs:comment`` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Sous-classes | `owl:Class`<br>Suffixe IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Propriétés DataType | `owl:DatatypeProperty`<br>`rdfs:label` ou `INode`<br>`rdfs:label`<br>`rdfs:range` | Propriétés de l’interface | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Propriétés des objets | `owl:ObjectProperty`<br>`rdfs:label` ou `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relationship | `type:Relationship`<br>`name`<br>`target` (ou omis si aucune `rdfs:range`)<br>`comment`<br>`displayName`<br>

L’extrait de code C# suivant montre comment un fichier de modèle RDF est chargé dans un graphique et converti en DTDL à l’aide de la bibliothèque [**dotNetRDF**](https://www.dotnetrdf.org/). 

```csharp
using VDS.RDF.Ontology; 
using VDS.RDF.Parsing; 
using Microsoft.Azure.DigitalTwins.Parser; 

//...

Console.WriteLine("Reading file..."); 

FileLoader.Load(_ontologyGraph, rdfFile.FullName); 

// Start looping through for each owl:Class 
foreach (OntologyClass owlClass in _ontologyGraph.OwlClasses) 
{ 

    // Generate a DTMI for the owl:Class 
    string Id = GenerateDTMI(owlClass); 

    if (!String.IsNullOrEmpty(Id)) 
    { 

        Console.WriteLine($"{owlClass.ToString()} -> {Id}"); 

        // Create Interface
        DtdlInterface dtdlInterface = new DtdlInterface 
        { 
            Id = Id, 
            Type = "Interface", 
            DisplayName = GetInterfaceDisplayName(owlClass), 
            Comment = GetInterfaceComment(owlClass), 
            Contents = new List<DtdlContents>() 
        }; 

        // Use DTDL 'extends' for super classes 
        IEnumerable<OntologyClass> foundSuperClasses = owlClass.DirectSuperClasses; 

        //... 
     }

     // Add interface to the list of interfaces 
     _interfaceList.Add(dtdlInterface); 
} 

// Serialize to JSON 
var json = JsonConvert.SerializeObject(_interfaceList); 

//...
``` 

## <a name="validate-and-upload-dtdl-models"></a>Valider et charger des modèles DTDL

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

Une fois qu’un modèle est converti et validé, vous pouvez **Charger le modèle dans l’instance Azure Digital Twins**. Pour plus d’informations sur ce processus, consultez la section [*Charger des modèles*](how-to-manage-model.md#upload-models) de *Guide pratique : Gérer les modèles personnalisés*.

## <a name="sample-converter-application"></a>Exemple d’application de convertisseur 

Un exemple d’application disponible convertit un fichier de modèle RDF en [DTDL (version 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) pour une utilisation par le service Azure Digital Twins. L’exemple est une application en ligne de commande .NET Core appelée **RdfToDtdlConverter**.

Vous pouvez vous procurer l’exemple ici : [**RdfToDtdlConverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Pour télécharger le code sur votre ordinateur, cliquez sur le bouton *Télécharger ZIP* sous le titre de la page d’accueil de l’exemple. Cela permet de télécharger un fichier *ZIP* sous le nom *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*, que vous pouvez ensuite décompresser et explorer.

Vous pouvez utiliser cet exemple pour voir les modèles de conversion en contexte et comme bloc de construction pour vos propres applications effectuant des conversions de modèle en fonction de vos besoins spécifiques.

## <a name="next-steps"></a>Étapes suivantes 

Découvrez plus d’informations sur la conception et la gestion de modèles de jumeaux numériques :
 
* [*Concepts : Modèles personnalisés*](concepts-models.md)
* [*Guide pratique : Gérer des modèles personnalisés*](how-to-manage-model.md)
* [*Guide pratique : Analyser et valider les modèles*](how-to-parse-models.md)