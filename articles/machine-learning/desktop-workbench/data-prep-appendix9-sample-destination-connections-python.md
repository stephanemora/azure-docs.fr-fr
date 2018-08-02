---
title: Exemples de destinations/sorties possibles avec la préparation des données Azure Machine Learning | Microsoft Docs
description: Ce document fournit un ensemble d’exemples de destinations/sorties de données personnalisées avec la préparation des données Azure Machine Learning.
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
ms.openlocfilehash: 2173ff15906940b8628aba3615f31e3f7137e3e2
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216726"
---
# <a name="sample-of-destination-connections-python"></a>Exemple de connexions de destination (Python) 
Avant de lire cette annexe, lisez la [présentation de l’extensibilité de Python](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Écriture dans Excel 


L’écriture dans Excel nécessite une bibliothèque supplémentaire. L’ajout de nouvelles bibliothèques est documenté dans la vue d’ensemble de l’extensibilité. `openpyxl` est la bibliothèque que vous devez ajouter.

Avant d’écrire dans Excel, d’autres modifications peuvent être nécessaires. Certains des types de données utilisés dans la préparation de données ne sont pas pris en charge dans certains formats de destination. Par exemple, si des objets « Erreur » existent, ils ne sont pas sérialisés correctement dans Excel. Par conséquent, avant de tenter d’écrire dans Excel, vous avez besoin d’une transformation « Remplacer les valeurs d’erreur », qui supprime les erreurs des colonnes.

Si toutes les tâches ci-dessus ont été effectuées, la ligne suivante écrit la table de données sur une même feuille dans un document Excel. Ajoutez une transformation Transformation d’un flux de données (Script). Ensuite, entrez le code suivant dans la section d’une expression.


### <a name="on-windows"></a>Sur Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>Sur macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
