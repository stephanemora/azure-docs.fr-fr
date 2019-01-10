---
title: Analyser les données à l’aide de Jupyter Notebook et Kqlmagic
description: Cette rubrique explique comment analyser les données à l’aide de Jupyter Notebook et de KQLmagic
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 7152b1d09a11d5860d52b5f73ae601422bd0f722
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2018
ms.locfileid: "53654452"
---
# <a name="analyze-data-using-jupyter-notebook-and-kqlmagic"></a>Analyser les données à l’aide de Jupyter Notebook et Kqlmagic
Jupyter Notebook est une application web open source qui vous permet de créer et de partager des documents contenant du code en temps réel, des équations, des visualisations et du texte narratif. L’utilisation inclut le nettoyage des données et la transformation, la simulation numérique, la modélisation statistique, la visualisation des données et le machine learning.
[Jupyter Notebook](https://jupyter.org/) offre des fonctions magiques qui étendent les fonctionnalités du noyau par la prise en charge de commandes supplémentaires. Kqlmagic est une fonction qui étend les fonctionnalités du noyau Python dans Jupyter Notebook pour que vous puissiez exécuter des requêtes de langage de requête Kusto en mode natif. Vous pouvez facilement combiner Python et le langage de requête Kusto pour interroger et visualiser des données à l’aide de la bibliothèque Plot.ly enrichie intégrée avec les commandes `render`. Les sources de données pour l’exécution de requêtes sont prises en charge. Ces sources de données incluent Azure Data Explorer, un service d’exploration de données rapide et hautement évolutif pour les données de journal et de télémétrie, ainsi que pour Log Analytics et Application Insights.

## <a name="prerequisites"></a>Prérequis
- Un compte e-mail professionnel qui est membre d’Azure Active Directory (AAD).
- Jupyter Notebook installé sur votre ordinateur local ou utilisation d’Azure Notebooks et clonage de l’exemple d’[Azure Notebook](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)


## <a name="install-kqlmagic-library"></a>Installer la bibliothèque Kqlmagic

1. Installer Kqlmagic :

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```

2. Charger Kqlmagic :

    ```python
    reload_ext Kqlmagic
    ```

## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Se connecter au cluster d’aide d’Azure Data Explorer

Utilisez la commande suivante pour vous connecter aux *exemples* de base de données hébergés sur le cluster d’*aide*. Pour les utilisateurs non-Microsoft AAD, remplacez le nom du locataire `Microsoft.com` par votre locataire AAD.


```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Interroger et visualiser

Interrogez les données à l’aide de l’[opérateur de rendu](/azure/kusto/query/renderoperator) et visualisez les données à l’aide de la bibliothèque ploy.ly. Cette requête et cette visualisation fournit une expérience intégrée qui utilise KQL natif. Kqlmagic prend en charge la plupart des graphiques, à l’exception de `timepivot`, `pivotchart` et `ladderchart`. Le rendu est pris en charge avec tous les attributs, à l’exception de `kind`, `ysplit` et `accumulate`. 

### <a name="query-and-render-piechart"></a>Interroger et afficher un graphique en secteurs

```python
%%kql 
StormEvents 
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Interroger et afficher un graphique temporel

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Ces graphiques sont interactifs. Sélectionnez un intervalle de temps pour zoomer sur une durée spécifique.

### <a name="customize-the-chart-colors"></a>Personnaliser les couleurs du graphique
Si vous n’aimez pas la palette de couleurs par défaut, personnalisez les graphiques à l’aide des options de palette. Les palettes disponibles sont les suivantes : [Choisissez la palette de couleurs pour les résultats de votre graphique de requête Kqlmagic](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Pour obtenir la liste des palettes :

    ```python
    %kql --palettes -popup_window
    ```

1. Sélectionnez la palette de couleurs `cool` et affichez de nouveau la requête :

    ```python
    %%kql -palette_name "cool"
    StormEvents 
    | summarize statecount=count() by State
    | sort by statecount 
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parametrize-a-query-with-python"></a>Paramétrer une requête avec Python

Kqlmagic permet l’échange simple entre le langage de requête Kusto et Python. Pour en savoir plus : [Paramétrer votre requête Kqlmagic avec Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 

### <a name="use-a-python-variable-in-your-kql-query"></a>Utiliser une variable Python dans votre requête KQL

Vous pouvez utiliser la valeur d’une variable Python dans votre requête pour filtrer les données :

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>Convertir les résultats de la requête en tramedonnées Pandas 

Vous pouvez accéder aux résultats d’une requête KQL dans les tramedonnées Pandas. Pour accéder aux résultats de la dernière requête exécutée par la variable `_kql_raw_result_` et convertir facilement les résultats en tramedonnées Pandas, procédez comme suit :

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Exemples 

Dans de nombreux scénarios d’analyse, vous souhaitez créer des notebooks réutilisables qui contiennent de nombreuses requêtes et alimentent les résultats d’une requête dans les requêtes suivantes. L’exemple ci-dessous utilise la variable Python `statefilter` pour filtrer les données.

1. Exécutez une requête pour afficher les 10 principaux états avec maximum `DamageProperty` :

    ```python
    %%kql
    StormEvents 
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Exécutez une requête pour extraire l’état principal et le définir dans une variable Python :

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Exécuter une requête en utilisant l’instruction `let` et la variable Python :

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state) 
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Exécuter la commande d’aide : 

    ```python
    %kql --help "help"
    ```

## <a name="next-steps"></a>Étapes suivantes
    
Exécutez la commande d’aide pour explorer les exemples de notebooks suivants qui contiennent toutes les fonctionnalités prises en charge :
- [Prise en main de Kqlmagic pour Azure Data Explorer](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Prise en main de Kqlmagic pour Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Prise en main de Kqlmagic pour Log Analytics](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Paramétrer votre requête Kqlmagic avec Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Choisissez la palette de couleurs pour les résultats de votre graphique de requête Kqlmagic](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)



