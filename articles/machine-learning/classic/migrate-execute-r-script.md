---
title: 'ML Studio (classique) : Migrer vers Azure Machine Learning – Exécuter un script R'
description: Reconstruisez les modules Exécuter un script R de Studio (classique) à exécuter sur Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: ac9ad296029451d624345d8b3bb365d881ba9a84
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565565"
---
# <a name="migrate-execute-r-script-modules-in-studio-classic"></a>Migrer les modules Exécuter un script R dans Studio (classique)

Dans cet article, vous allez apprendre à reconstruire un module **Exécuter un script R** de Studio (classique) dans Azure Machine Learning.

Pour plus d’informations sur la migration à partir de Studio (classique), consultez [l’article de présentation sur la migration](migrate-overview.md).

## <a name="execute-r-script"></a>Exécuter un script R

Le concepteur d’Azure Machine Learning s’exécute désormais sous Linux. Studio (classique) s’exécute sous Windows. En raison du changement de plateforme, vous devez ajuster votre module **Exécuter un script R** pendant la migration, sinon le pipeline échouera.

Pour migrer un module **Exécuter un script R** à partir de Studio (classique), vous devez remplacer les interfaces `maml.mapInputPort` `maml.mapOutputPort` par des fonctions standard.

Le tableau suivant récapitule les modifications apportées au module Exécuter un script R :

|Fonctionnalité|Studio (classique)|Concepteur Azure Machine Learning|
|---|---|---|
|Interface de script|`maml.mapInputPort` et `maml.mapOutputPort`|Interface de fonction|
|Plateforme|Windows|Linux|
|Accès à l’Internet |Non|Oui|
|Mémoire|14 Go|Dépend du SKU de calcul|

### <a name="how-to-update-the-r-script-interface"></a>Comment mettre à jour l’interface de script R

Voici le contenu d’un exemple de module **Exécuter un script R** dans Studio (classique) :
```r
# Map 1-based optional input ports to variables 
dataset1 <- maml.mapInputPort(1) # class: data.frame 
dataset2 <- maml.mapInputPort(2) # class: data.frame 

# Contents of optional Zip port are in ./src/ 
# source("src/yourfile.R"); 
# load("src/yourData.rdata"); 

# Sample operation 
data.set = rbind(dataset1, dataset2); 

 
# You'll see this output in the R Device port. 
# It'll have your stdout, stderr and PNG graphics device(s). 

plot(data.set); 

# Select data.frame to be sent to the output Dataset port 
maml.mapOutputPort("data.set"); 
```

Voici le contenu mis à jour dans le concepteur. Notez que `maml.mapInputPort` et `maml.mapOutputPort` ont été remplacées par l’interface de fonction standard `azureml_main`. 
```r
azureml_main <- function(dataframe1, dataframe2){ 
    # Use the parameters dataframe1 and dataframe2 directly 
    dataset1 <- dataframe1 
    dataset2 <- dataframe2 

    # Contents of optional Zip port are in ./src/ 
    # source("src/yourfile.R"); 
    # load("src/yourData.rdata"); 

    # Sample operation 
    data.set = rbind(dataset1, dataset2); 


    # You'll see this output in the R Device port. 
    # It'll have your stdout, stderr and PNG graphics device(s). 
    plot(data.set); 

  # Return datasets as a Named List 

  return(list(dataset1=data.set)) 
} 
```
Pour plus d’informations, consultez la [référence du module Exécuter un script R](../algorithm-module-reference/execute-r-script.md) du concepteur.

### <a name="install-r-packages-from-the-internet"></a>Installer des packages R à partir d’Internet

Le concepteur d’Azure Machine Learning vous permet d’installer des packages directement à partir de CRAN.

Il s’agit d’une amélioration par rapport à Studio (classique). Dans la mesure où Studio (classique) s’exécute dans un environnement de bac à sable sans accès Internet, vous deviez charger des scripts dans un pack zip pour installer d’autres packages. 

Utilisez le code suivant pour installer les packages CRAN dans le module **Exécuter un script R** du concepteur :
```r
  if(!require(zoo)) { 
      install.packages("zoo",repos = "http://cran.us.r-project.org") 
  } 
  library(zoo) 
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à migrer des modules Exécuter un script R vers Azure Machine Learning.

Consultez les autres articles de la série sur la migration Studio (classique) :

1. [Vue d’ensemble de la migration](migrate-overview.md).
1. [Migrer un jeu de données](migrate-register-dataset.md).
1. [Reconstruire un pipeline de formation Studio (classique)](migrate-rebuild-experiment.md).
1. [Reconstruire un service web Studio (classique)](migrate-rebuild-web-service.md).
1. [Intégrer un service web Azure Machine Learning à des applications clientes](migrate-rebuild-integrate-with-client-app.md).
1. **Migrer des modules Exécuter un script R**.