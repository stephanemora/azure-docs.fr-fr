---
title: 'Exemple : Créer et déployer une compétence personnalisée avec le concepteur Azure Machine Learning'
titleSuffix: Azure Cognitive Search
description: Cet exemple montre comment utiliser le concepteur Azure Machine Learning afin de créer et déployer une compétence AML personnalisée pour le pipeline d'enrichissement par IA du service Recherche cognitive Azure.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 5af4110d1092c2e0e916d1404e9177b64e650e0b
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114728107"
---
# <a name="example-build-and-deploy-a-custom-skill-with-azure-machine-learning-designer"></a>Exemple : Créer et déployer une compétence personnalisée avec le concepteur Azure Machine Learning

Le [concepteur Azure Machine Learning](../machine-learning/concept-designer.md) est un canevas interactif facile à utiliser qui permet de créer des modèles Machine Learning pour des tâches telles que la régression et la classification. L'appel du modèle créé par le concepteur dans un pipeline d'enrichissement du service Recherche cognitive nécessite quelques étapes supplémentaires. Dans cet exemple, vous allez créer un modèle de régression simple pour prédire le prix d'une automobile et appeler le point de terminaison d'inférence sous forme de compétence AML. 

Pour créer un modèle permettant de prédire le prix d'une automobile en fonction de ses différentes caractéristiques, suivez le tutoriel [Régression - Prédiction du prix des véhicules automobiles (avancé)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/regression-automobile-price-prediction-compare-algorithms.md) de la page [Exemples de pipelines et de jeux de données](../machine-learning/concept-designer.md).

> [!IMPORTANT] 
> Le déploiement du modèle en suivant le processus d'inférence en temps réel génère un point de terminaison valide, mais celui-ci ne peut pas être utilisé avec la compétence AML dans le service Recherche cognitive. 

## <a name="register-model-and-download-assets"></a>Inscrire le modèle et télécharger les ressources

Après avoir effectué l'apprentissage d'un modèle, [inscrivez-le](../machine-learning/how-to-deploy-model-designer.md) et suivez les étapes pour télécharger tous les fichiers du dossier `trained_model_outputs`, ou téléchargez uniquement les fichiers `score.py` et `conda_env.yml` à partir de la page des artefacts de modèles. Vous allez modifier le script de scoring avant que le modèle ne soit déployé en tant que point de terminaison d'inférence en temps réel.


## <a name="edit-the-scoring-script-for-use-with-cognitive-search"></a>Modifier le script de scoring à utiliser avec le service Recherche cognitive 

Les pipelines d'enrichissement du service Recherche cognitive utilisent un seul document et génèrent une requête contenant les entrées d'une seule prédiction. Le fichier `score.py` téléchargé accepte une liste d'enregistrements et renvoie une liste de prédictions sous forme de chaîne JSON sérialisée. Vous devez apporter deux modifications au fichier `score.py` :

* Modifiez le script pour qu'il fonctionne avec un seul enregistrement d'entrée, et non une liste.
* Modifiez le script pour qu'il renvoie un objet JSON contenant une seule propriété, le prix prédit.

Ouvrez le fichier `score.py` téléchargé et modifiez la fonction `run(data)`. La fonction est actuellement configurée pour attendre l'entrée suivante, comme décrit dans le fichier `_samples.json` du modèle.

```json
[
  {
    "symboling": 2,
    "make": "mitsubishi",
    "fuel-type": "gas",
    "aspiration": "std",
    "num-of-doors": "two",
    "body-style": "hatchback",
    "drive-wheels": "fwd",
    "engine-location": "front",
    "wheel-base": 93.7,
    "length": 157.3,
    "width": 64.4,
    "height": 50.8,
    "curb-weight": 1944,
    "engine-type": "ohc",
    "num-of-cylinders": "four",
    "engine-size": 92,
    "fuel-system": "2bbl",
    "bore": 2.97,
    "stroke": 3.23,
    "compression-ratio": 9.4,
    "horsepower": 68.0,
    "peak-rpm": 5500.0,
    "city-mpg": 31,
    "highway-mpg": 38,
    "price": 6189.0
  },
  {
    "symboling": 0,
    "make": "toyota",
    "fuel-type": "gas",
    "aspiration": "std",
    "num-of-doors": "four",
    "body-style": "wagon",
    "drive-wheels": "fwd",
    "engine-location": "front",
    "wheel-base": 95.7,
    "length": 169.7,
    "width": 63.6,
    "height": 59.1,
    "curb-weight": 2280,
    "engine-type": "ohc",
    "num-of-cylinders": "four",
    "engine-size": 92,
    "fuel-system": "2bbl",
    "bore": 3.05,
    "stroke": 3.03,
    "compression-ratio": 9.0,
    "horsepower": 62.0,
    "peak-rpm": 4800.0,
    "city-mpg": 31,
    "highway-mpg": 37,
    "price": 6918.0
  },
  {
    "symboling": 1,
    "make": "honda",
    "fuel-type": "gas",
    "aspiration": "std",
    "num-of-doors": "two",
    "body-style": "sedan",
    "drive-wheels": "fwd",
    "engine-location": "front",
    "wheel-base": 96.5,
    "length": 169.1,
    "width": 66.0,
    "height": 51.0,
    "curb-weight": 2293,
    "engine-type": "ohc",
    "num-of-cylinders": "four",
    "engine-size": 110,
    "fuel-system": "2bbl",
    "bore": 3.15,
    "stroke": 3.58,
    "compression-ratio": 9.1,
    "horsepower": 100.0,
    "peak-rpm": 5500.0,
    "city-mpg": 25,
    "highway-mpg": 31,
    "price": 10345.0
  }
]
```

Grâce à vos modifications, le modèle pourra accepter l'entrée générée par le service Recherche cognitive lors de l'indexation (enregistrement unique).

```json
{
    "symboling": 2,
    "make": "mitsubishi",
    "fuel-type": "gas",
    "aspiration": "std",
    "num-of-doors": "two",
    "body-style": "hatchback",
    "drive-wheels": "fwd",
    "engine-location": "front",
    "wheel-base": 93.7,
    "length": 157.3,
    "width": 64.4,
    "height": 50.8,
    "curb-weight": 1944,
    "engine-type": "ohc",
    "num-of-cylinders": "four",
    "engine-size": 92,
    "fuel-system": "2bbl",
    "bore": 2.97,
    "stroke": 3.23,
    "compression-ratio": 9.4,
    "horsepower": 68.0,
    "peak-rpm": 5500.0,
    "city-mpg": 31,
    "highway-mpg": 38,
    "price": 6189.0
}
```

Remplacez les lignes 27 à 30 par :
```python

    for key, val in data.items():
        input_entry[key].append(decode_nan(val))
```
Vous devez également modifier la sortie générée par le script, en remplaçant la chaîne de caractères par un objet JSON. Dans le fichier d'origine, remplacez l'instruction return (ligne 37) par :
```python
    output = result.data_frame.values.tolist()
    return {
        "predicted_price": output[0][-1]
    }
```

Voici la fonction `run` mise à jour avec les modifications apportées au format d'entrée et à la sortie prédite qui acceptera un enregistrement unique comme entrée et renverra un objet JSON avec le prix prédit.

```python
def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    # data is now a JSON object not a list of JSON objects
    for key, val in data.items():
        input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)
    score_module = ScoreModelModule()
    result, = score_module.run(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        append_or_result_only=True)
    #return json.dumps({"result": result.data_frame.values.tolist()})
    output = result.data_frame.values.tolist()
    # return the last column of the the first row of the dataframe
    return  {
        "predicted_price": output[0][-1]
    }
```
## <a name="register-and-deploy-the-model"></a>Inscrire et déployer le modèle

Après avoir enregistré vos modifications, vous pouvez inscrire le modèle sur le portail. Sélectionnez Inscrire le modèle et donnez-lui un nom valide. Choisissez `Other` pour le Framework de modèle, `Custom` pour le Nom du framework et `1.0` pour la Version du framework. Sélectionnez l'option `Upload folder`, puis le dossier contenant les fichiers `score.py` et `conda_env.yaml` mis à jour.

Sélectionnez le modèle, puis l'action `Deploy`. L'étape de déploiement suppose que vous disposiez d'un cluster d'inférence AKS approvisionné. Les instances de conteneur ne sont actuellement pas prises en charge dans le service Recherche cognitive.
 1. Entrez un nom de point de terminaison valide.
2. Sélectionnez le type de calcul d'`Azure Kubernetes Service`.
3. Sélectionnez le nom de calcul de votre cluster d'inférence.
4. Activez `enable authentication`.
5. Sélectionnez le type d'authentification `Key-based authentication`.
6. Sous `entry script file`, sélectionnez le fichier `score.py` mis à jour.
7. Sous `conda dependencies file`, sélectionnez le fichier `conda_env.yaml`.
8. Sélectionnez le bouton Déployer pour déployer votre nouveau point de terminaison.

## <a name="integrate-with-cognitive-search"></a>Intégrer au service Recherche cognitive

Pour intégrer le point de terminaison nouvellement créé au service Recherche cognitive :
1. Ajoutez un fichier JSON contenant un seul enregistrement d'automobile à un conteneur d'objets blob.
2. Configurez un pipeline d'enrichissement par IA à l'aide du [workflow d'importation de données](cognitive-search-quickstart-blob.md). Sélectionnez `JSON` comme `parsing mode`.
3. Sous l'onglet `Add Enrichments`, sélectionnez une seule compétence `Extract people names` en tant qu'espace réservé.
4. Ajoutez un nouveau champ à l'index `predicted_price` de type `Edm.Double`, puis définissez la propriété Retrievable sur true.
5. Finalisez le processus d'importation des données.

### <a name="add-the-aml-skill-to-the-skillset"></a>Ajouter la compétence AML à l'ensemble de compétences

Dans la liste des ensembles de compétences, sélectionnez les ensembles de compétences que vous avez créés. Vous allez maintenant modifier l'ensemble de compétences pour remplacer la compétence d'identification des personnes par la compétence AML afin de prédire les prix. Sous l'onglet Définition de l'ensemble de compétences (JSON), sélectionnez `Azure Machine Learning (AML)` dans la liste déroulante des compétences. Sélectionnez l'espace de travail, pour permettre à la compétence AML de découvrir votre point de terminaison. L'espace de travail et le service de recherche doivent se trouver dans le même abonnement Azure.
Sélectionnez le point de terminaison que vous avez précédemment créé dans le cadre du tutoriel.
Vérifiez que la compétence est renseignée avec l'URI et les informations d'authentification que vous avez configurées lors du déploiement du point de terminaison. Copiez le modèle de compétence et remplacez la compétence dans l'ensemble de compétences.
Modifiez la compétence :
1. Définissez un nom valide.
2. Ajouter une description
3. Définissez degreesOfParallelism sur 1.
4. Définissez le contexte sur `/document`.
5. Définissez toutes les entrées requises (voir l'exemple de définition de compétence ci-dessous).
6. Définissez les sorties pour capturer le prix prédit renvoyé.

```json
{
      "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
      "name": "AMLdemo",
      "description": "AML Designer demo",
      "context": "/document",
      "uri": "Your AML endpoint",
      "key": "Your AML endpoint key",
      "resourceId": null,
      "region": null,
      "timeout": "PT30S",
      "degreeOfParallelism": 1,
      "inputs": [
        {
          "name": "symboling",
          "source": "/document/symboling"
        },
        {
          "name": "make",
          "source": "/document/make"
        },
        {
          "name": "fuel-type",
          "source": "/document/fuel-type"
        },
        {
          "name": "aspiration",
          "source": "/document/aspiration"
        },
        {
          "name": "num-of-doors",
          "source": "/document/num-of-doors"
        },
        {
          "name": "body-style",
          "source": "/document/body-style"
        },
        {
          "name": "drive-wheels",
          "source": "/document/drive-wheels"
        },
        {
          "name": "engine-location",
          "source": "/document/engine-location"
        },
        {
          "name": "wheel-base",
          "source": "/document/wheel-base"
        },
        {
          "name": "length",
          "source": "/document/length"
        },
        {
          "name": "width",
          "source": "/document/width"
        },
        {
          "name": "height",
          "source": "/document/height"
        },
        {
          "name": "curb-weight",
          "source": "/document/curb-weight"
        },
        {
          "name": "engine-type",
          "source": "/document/engine-type"
        },
        {
          "name": "num-of-cylinders",
          "source": "/document/num-of-cylinders"
        },
        {
          "name": "engine-size",
          "source": "/document/engine-size"
        },
        {
          "name": "fuel-system",
          "source": "/document/fuel-system"
        },
        {
          "name": "bore",
          "source": "/document/bore"
        },
        {
          "name": "stroke",
          "source": "/document/stroke"
        },
        {
          "name": "compression-ratio",
          "source": "/document/compression-ratio"
        },
        {
          "name": "horsepower",
          "source": "/document/horsepower"
        },
        {
          "name": "peak-rpm",
          "source": "/document/peak-rpm"
        },
        {
          "name": "city-mpg",
          "source": "/document/city-mpg"
        },
        {
          "name": "highway-mpg",
          "source": "/document/highway-mpg"
        },
        {
          "name": "price",
          "source": "/document/price"
        }
      ],
      "outputs": [
        {
          "name": "predicted_price",
          "targetName": "predicted_price"
        }
      ]
    }
```
### <a name="update-the-indexer-output-field-mappings"></a>Mettre à jour les mappages des champs de sortie de l'indexeur

Les mappages des champs de sortie de l'indexeur déterminent les enrichissements enregistrés dans l'index. Remplacez la section relative aux mappages des champs de sortie de l'indexeur par l’extrait de code ci-dessous :

```json
"outputFieldMappings": [
    {
      "sourceFieldName": "/document/predicted_price",
      "targetFieldName": "predicted_price"
    }
  ]
```

Vous pouvez maintenant exécuter votre indexeur et vérifier que la propriété `predicted_price` de l'index est renseignée avec le résultat de la sortie de votre compétence AML.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Examiner l'API web Compétences personnalisées](./cognitive-search-custom-skill-web-api.md)

> [En savoir plus sur l'ajout de compétences personnalisées au pipeline d'enrichissement](./cognitive-search-custom-skill-interface.md)

> [En savoir plus sur la compétence AML](./cognitive-search-tutorial-aml-custom-skill.md)