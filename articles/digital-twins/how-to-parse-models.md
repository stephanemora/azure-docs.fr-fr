---
title: Analyser et valider les modèles
titleSuffix: Azure Digital Twins
description: Découvrez comment utiliser la bibliothèque de l’analyseur pour analyser des modèles DTDL.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 12916b06dd965d7eed717df2fe71f935b8319bc4
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108772708"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>Analyser et valider des modèles avec la bibliothèque de l’analyseur DTDL

Les [modèles](concepts-models.md) dans Azure Digital Twins sont définis à l’aide du langage DTDL (Digital Twins Definition Language) basé sur JSON-LD. **Il est recommandé de valider les modèles hors connexion avant de les charger sur une instance Azure Digital Twins.**

Pour vous aider à effectuer cette opération, une bibliothèque d’analyse DTDL côté client .NET est fournie sur NuGet : [Microsoft.Azure.DigitalTwins.Parser](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). 

Vous pouvez utiliser la bibliothèque de l’analyseur directement dans votre code C#, ou utiliser l’exemple de projet de code indépendant du langage basé sur la bibliothèque de l’analyseur : [Exemple de validateur DTDL](/samples/azure-samples/dtdl-validator/dtdl-validator).

## <a name="use-the-dtdl-validator-sample"></a>Utiliser l’exemple de validateur DTDL

Le [Validateur DTDL](/samples/azure-samples/dtdl-validator/dtdl-validator) est un exemple de projet pouvant valider des documents de modèle afin de s’assurer que le DTDL est valide. Il est basé sur la bibliothèque de l’analyseur .NET et est indépendant du langage. Vous pouvez l’utiliser avec le bouton *Télécharger le fichier ZIP* sur le lien de l’exemple.

Le code source montre des exemples d’utilisation de la bibliothèque de l’analyseur. Vous pouvez utiliser l’exemple de validateur peut être utilisé comme utilitaire de ligne de commande pour valider une arborescence de répertoires de fichiers DTDL. Il fournit également un mode interactif.

Dans le dossier de l’exemple de validateur DTDL, consultez le fichier *readme.md* pour obtenir des instructions sur la façon d’empaqueter l’exemple dans un exécutable autonome.

Après avoir créé un package autonome et ajouté l’exécutable à votre chemin d’accès, vous pouvez exécuter le validateur avec cette commande dans une console sur votre ordinateur :

```cmd/sh
DTDLValidator
```

Avec les options par défaut, l’exemple recherche les fichiers .json dans le répertoire actif et tous les sous-répertoires. Vous pouvez également ajouter l’option suivante pour que l’exemple recherche dans le répertoire indiqué et dans tous les sous-répertoires des fichiers portant l’extension .dtdl :

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

Vous pouvez ajouter l’option `-i` pour que l’exemple passe en mode interactif :

```cmd/sh
DTDLValidator -i
```

Pour plus d’informations sur cet exemple, consultez le code source ou exécutez `DTDLValidator --help`.

## <a name="use-the-net-parser-library"></a>Utiliser la bibliothèque d’analyseur .NET 

La bibliothèque [Microsoft.Azure.DigitalTwins.Parser](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) fournit un accès par modèle aux définitions DTDL agissant essentiellement comme équivalent de C# pour DTDL. Vous pouvez l’utiliser indépendamment du [Kit de développement logiciel (SDK) Azure Digital Twins](concepts-apis-sdks.md), en particulier pour la validation de DTDL dans un éditeur visuel ou de texte. Elle est utile pour s’assurer que vos fichiers de définition de modèle sont valides avant d’essayer de les charger vers le service.

Pour utiliser la bibliothèque de l’analyseur, vous devez lui fournir un ensemble de documents DTDL. En règle générale, vous récupérez ces documents de modèle à partir du service, mais vous pouvez également les rendre disponibles localement si votre client était initialement responsable de les charger sur le service. 

Voici le flux de travail général pour l’utilisation de l’analyseur :
1. Récupérez tout ou partie des documents DTDL du service.
2. Transmettez les documents DTDL en mémoire retournés à l’analyseur.
3. L’analyseur valide le jeu de documents qui lui est transmis et retourne des informations détaillées sur l’erreur. Cette fonctionnalité est utile dans des scénarios d’éditeur.
4. Utilisez les API d’analyseurs pour poursuivre l’analyse des modèles inclus dans l’ensemble de documents. 

Les fonctionnalités de l’analyseur sont les suivantes :
* Obtenir toutes les interfaces de modèle implémentées (contenu de la section `extends` de l’interface).
* Obtenir l’ensemble des propriétés, de la télémétrie, des commandes, des composants et des relations déclarés dans le modèle. Cette commande obtient également toutes les métadonnées incluses dans ces définitions et prend en compte l’héritage (sections`extends`).
* Récupérer toutes les définitions de modèle complexes.
* Déterminer si un modèle peut être assigné à partir d’un autre.

> [!NOTE]
> Les appareils [IoT Plug-and-Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) utilisent une petite variante de syntaxe pour décrire leur fonctionnalité. Cette variante de syntaxe est un sous-ensemble sémantiquement compatible du langage DTDL utilisé dans Azure Digital Twins. Lorsque vous utilisez la bibliothèque de l’analyseur, vous n’avez pas besoin de savoir quelle variante de syntaxe a été utilisée pour créer le DTDL pour votre jumeau numérique. Par défaut, l’analyseur retourne toujours le même modèle pour la syntaxe PnP et Azure Digital Twins.

### <a name="code-with-the-parser-library"></a>Coder avec la bibliothèque de l’analyseur

Vous pouvez utiliser la bibliothèque de l’analyseur directement, par exemple, pour valider des modèles dans votre propre application ou pour générer une interface utilisateur, des tableaux de bord et des rapports dynamiques basés sur des modèles.

Pour prendre en charge l’exemple de code de l’analyseur ci-dessous, considérez plusieurs modèles définis dans une instance Azure Digital Twins :

:::code language="json" source="~/digital-twins-docs-samples/models/coffeeMaker-coffeeMakerInterface-coffeeBar.json":::

Le code suivant montre un exemple d’utilisation de la bibliothèque de l’analyseur pour refléter ces définitions en C# :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/parseModels.cs":::

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez fini d’écrire vos modèles, découvrez comment les télécharger (et effectuer d’autres opérations de gestion) avec les API DigitalTwinsModels :
* [Guide pratique : Gérer les modèles DTDL](how-to-manage-model.md)