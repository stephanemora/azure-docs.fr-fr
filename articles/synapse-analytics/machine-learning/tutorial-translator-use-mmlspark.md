---
title: 'Tutoriel : Traducteur avec Cognitive Service'
description: Découvrez comment utiliser le Traducteur dans Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 11/02/2021
author: ruixinxu
ms.author: ruxu
ms.custom: ignite-fall-2021
ms.openlocfilehash: e0ae2aea56e7bfa255daba30c99b49bc66b5b4b5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097851"
---
# <a name="tutorial-translator-with-cognitive-service"></a>Tutoriel : Traducteur avec Cognitive Service

[Traducteur](../../cognitive-services/Translator/index.yml) est un [Azure Cognitive Service](../../cognitive-services/index.yml) qui vous permet d’effectuer la traduction de langues et d’autres opérations liées à la langue. Dans ce tutoriel, vous allez apprendre à utiliser [Traducteur](../../cognitive-services/Translator/index.yml) pour créer des solutions intelligentes multilingues sur Azure Synapse Analytics.

Ce tutoriel montre comment utiliser le traducteur avec [MMLSpark](https://github.com/Azure/mmlspark) pour :

> [!div class="checklist"]
> - Traduire le texte
> - Translittérer du texte
> - Détecter la langue
> - Arrêter la phrase
> - Recherche dans le dictionnaire
> - Exemple de dictionnaire

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit avant de commencer](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Prérequis

- [Espace de travail Azure Synapse Analytics](../get-started-create-workspace.md) avec un compte de stockage Azure Data Lake Storage Gen2 configuré comme stockage par défaut. Vous devez être le *contributeur aux données Blob du stockage* du système de fichiers Data Lake Storage Gen2 que vous utilisez.
- Pool Spark dans votre espace de travail Azure Synapse Analytics. Pour plus d’informations, consultez [Créer un pool Spark dans Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Avoir effectué les étapes de pré-configuration décrites dans le tutoriel [Configurer Cognitive Services dans Azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="get-started"></a>Bien démarrer
Ouvrez Synapse Studio et créez un nouveau notebook. Pour commencer, importez [MMLSpark](https://github.com/Azure/mmlspark). 

```python
import mmlspark
from mmlspark.cognitive import *
from notebookutils import mssparkutils
from pyspark.sql.functions import col, flatten
```

## <a name="configure-translator"></a>Configurer le traducteur

Utilisez le traducteur lié que vous avez configuré dans les [étapes de préconfiguration](tutorial-configure-cognitive-services-synapse.md). 

```python
cognitive_service_name = "<Your linked service for translator>"
```

## <a name="translate-text"></a>Traduire du texte
Traduire du texte est l’opération fondamentale du service Translator. 

```python
df = spark.createDataFrame([
  (["Hello, what is your name?", "Bye"],)
], ["text",])

translate = (Translate()
    .setLinkedService(cognitive_service_name)
    .setTextCol("text")
    .setToLanguage(["zh-Hans", "fr"])
    .setOutputCol("translation")
    .setConcurrency(5))

display(translate
      .transform(df)
      .withColumn("translation", flatten(col("translation.translations")))
      .withColumn("translation", col("translation.text"))
      .select("translation"))
```

### <a name="expected-results"></a>Résultats attendus

```json
["你好，你叫什么名字？","Bonjour, quel est votre nom?","再见","Au revoir"]
```

## <a name="transliterate-text"></a>Translittérer du texte

La translittération est le processus de conversion d’un mot ou d’une expression depuis l’écriture (l’alphabet) d’une langue vers une autre en fonction de similarités phonétiques.

```python
transliterateDf =  spark.createDataFrame([
  (["こんにちは", "さようなら"],)
], ["text",])

transliterate = (Transliterate()
    .setLinkedService(cognitive_service_name)
    .setLanguage("ja")
    .setFromScript("Jpan")
    .setToScript("Latn")
    .setTextCol("text")
    .setOutputCol("result"))

display(transliterate
    .transform(transliterateDf)
    .withColumn("text", col("result.text"))
    .withColumn("script", col("result.script"))
    .select("text", "script"))
```

### <a name="expected-results"></a>Résultats attendus

|texte|script|
|--|--|
|"["Kon'nichiwa","sayonara"]"|"["Latn","Latn"]"|

## <a name="detect-language"></a>Détecter la langue
Si vous savez que vous allez avoir besoin d’une traduction, mais que vous ne connaissez pas la langue du texte envoyé au service Translator, vous pouvez utiliser l’opération de détection de la langue. 

```python
detectDf =  spark.createDataFrame([
  (["Hello, what is your name?"],)
], ["text",])

detect = (Detect()
    .setLinkedService(cognitive_service_name)
    .setTextCol("text")
    .setOutputCol("result"))

display(detect
    .transform(detectDf)
    .withColumn("language", col("result.language"))
    .select("language"))
```

### <a name="expected-results"></a>Résultats attendus

```json
"["en"]"
```

## <a name="break-sentence"></a>Arrêter la phrase

Identifie le positionnement des limites de phrases dans du texte.

```python
bsDf =  spark.createDataFrame([
  (["Hello, what is your name?"],)
], ["text",])

breakSentence = (BreakSentence()
    .setLinkedService(cognitive_service_name)
    .setTextCol("text")
    .setOutputCol("result"))

display(breakSentence
    .transform(bsDf)
    .withColumn("sentLen", flatten(col("result.sentLen")))
    .select("sentLen"))
```

### <a name="expected-results"></a>Résultats attendus

```json
"[25]"
```

## <a name="dictionary-lookup-alternate-translations"></a>Recherche dans le dictionnaire (autres traductions)
Avec le point de terminaison, vous pouvez obtenir d’autres traductions d’un mot ou d’une expression. 

```python
dictDf = spark.createDataFrame([
  (["fly"],)
], ["text",])

dictionaryLookup = (DictionaryLookup()
    .setLinkedService(cognitive_service_name)
    .setFromLanguage("en")
    .setToLanguage("es")
    .setTextCol("text")
    .setOutputCol("result"))

display(dictionaryLookup
    .transform(dictDf)
    .withColumn("translations", flatten(col("result.translations")))
    .withColumn("normalizedTarget", col("translations.normalizedTarget"))
    .select("normalizedTarget"))
```

### <a name="expected-results"></a>Résultats attendus

|normalizedTarget|
|----|
|"["volar","mosca","operan","pilotar","moscas","marcha"]"|

## <a name="dictionary-examples-translations-in-context"></a>Exemples du dictionnaire (traductions en contexte)

Une fois que vous avez effectué une recherche dans le dictionnaire, vous pouvez passer le texte source et la traduction au point de terminaison dictionary/examples pour obtenir la liste des exemples qui utilisent les deux termes dans le contexte d’une phrase ou d’une expression.

```python
dictDf = spark.createDataFrame([
  ([("fly", "volar")],)
], ["textAndTranslation",])

dictionaryExamples = (DictionaryExamples()
    .setLinkedService(cognitive_service_name)
    .setFromLanguage("en")
    .setToLanguage("es")
    .setTextAndTranslationCol("textAndTranslation")
    .setOutputCol("result"))

display(dictionaryExamples
    .transform(dictDf)
    .withColumn("examples", flatten(col("result.examples")))
    .select("examples"))
```

### <a name="expected-results"></a>Résultats attendus

```json

[{"sourcePrefix":"I mean, for a guy who could ","sourceSuffix":".","targetPrefix":"Quiero decir, para un tipo que podía ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"Now it's time to make you ","sourceSuffix":".","targetPrefix":"Ahora es hora de que te haga ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"One happy thought will make you ","sourceSuffix":".","targetPrefix":"Uno solo te hará ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"They need machines to ","sourceSuffix":".","targetPrefix":"Necesitan máquinas para ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"That should really ","sourceSuffix":".","targetPrefix":"Eso realmente debe ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"It sure takes longer when you can't ","sourceSuffix":".","targetPrefix":"Lleva más tiempo cuando no puedes ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I have to ","sourceSuffix":" home in the morning.","targetPrefix":"Tengo que ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":" a casa por la mañana."},{"sourcePrefix":"You taught me to ","sourceSuffix":".","targetPrefix":"Me enseñaste a ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I think you should ","sourceSuffix":" with the window closed.","targetPrefix":"Creo que debemos ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":" con la ventana cerrada."},{"sourcePrefix":"They look like they could ","sourceSuffix":".","targetPrefix":"Parece que pudieran ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"But you can ","sourceSuffix":", for instance?","targetPrefix":"Que puedes ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":", por ejemplo."},{"sourcePrefix":"At least until her kids can be able to ","sourceSuffix":".","targetPrefix":"Al menos hasta que sus hijos sean capaces de ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I thought you could ","sourceSuffix":".","targetPrefix":"Pensé que podías ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I was wondering what it would be like to ","sourceSuffix":".","targetPrefix":"Me preguntaba cómo sería ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"But nobody else can ","sourceSuffix":".","targetPrefix":"Pero nadie puede ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."}]
```
## <a name="clean-up-resources"></a>Nettoyer les ressources
Pour vous assurer que l’instance Spark est arrêtée, mettez fin aux sessions connectées (notebooks). Le pool s’arrête quand la **durée d’inactivité** spécifiée dans le pool Apache Spark est atteinte. Vous pouvez également sélectionner **Arrêter la session** dans la barre d’état en haut à droite du notebook.

![screenshot-showing-stop-session](./media/tutorial-build-applications-use-mmlspark/stop-session.png)

## <a name="next-steps"></a>Étapes suivantes

* [Consultez les exemples de notebooks Synapse](https://github.com/Azure-Samples/Synapse/tree/main/MachineLearning) 
* [Dépôt GitHub MMLSpark](https://github.com/Azure/mmlspark)
