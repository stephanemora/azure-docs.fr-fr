---
title: Exemple Python pour dériver de nouvelles colonnes dans la préparation des données Azure Machine Learning | Microsoft Docs
description: Ce document fournit des exemples de code Python pour la création de colonnes dans la préparation des données Azure Machine Learning.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 43a66efde2623a848065bbde2be4b56fd6c7fc8b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969306"
---
# <a name="sample-of-custom-column-transforms-python"></a>Exemple de transformations de colonnes personnalisées (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Le nom de cette transformation dans le menu est **Add Column (Script)**.

Avant de lire cette annexe, lisez la [présentation de l’extensibilité de Python](data-prep-python-extensibility-overview.md).

## <a name="test-equivalence-and-replace-values"></a>Tester l’équivalence et remplacer les valeurs 
Si la valeur de Col1 est inférieure à 4, la nouvelle colonne doit avoir la valeur 1. Si la valeur dans Col1 est supérieure à 4, la nouvelle colonne affiche la valeur 2. 

```python
    1 if row["Col1"] < 4 else 2
```
## <a name="current-date-and-time"></a>Date et heure actuelles 

```python
    datetime.datetime.now()
```
## <a name="typecasting"></a>Cast de type 
```python
    float(row["Col1"]) / float(row["Col2"] - 1)
```
## <a name="evaluate-for-nullness"></a>Évaluer le caractère null 
Si Col1 contient une valeur null, marquer la nouvelle colonne comme étant **incorrecte**. Sinon, la marquer comme **bonne**. 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>Nouvelle colonne calculée 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Calcul de l’époque 
Nombre de secondes depuis l’époque Unix (en supposant que Col1 est déjà une date) : 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```

## <a name="hash-a-column-value-into-a-new-column"></a>Hacher une valeur de colonne dans une nouvelle colonne
```python
    import hashlib
    hash(row["MyColumnToHashCol1"])

```




