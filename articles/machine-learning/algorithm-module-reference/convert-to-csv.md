---
title: 'Convertir au format CSV : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Convertir au format CSV dans Azure Machine Learning service pour convertir un jeu de données dans un fichier au format CSV qui peut être téléchargé, exporté ou partagé avec des modules de script R ou Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 37c1653b31049dc0142cf8c0c6f6d405338b1d1f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693191"
---
# <a name="convert-to-csv-module"></a>Module Convertir au format CSV

Cet article décrit un module de l’interface visuelle (préversion) d’Azure Machine Learning service.

Utilisez ce module pour convertir un jeu de données dans un fichier au format CSV qui peut être téléchargé, exporté ou partagé avec des modules de script R ou Python.

### <a name="more-about-the-csv-format"></a>Plus d’informations sur le format CSV 

Le format CSV, qui signifie « valeurs séparées par des virgules », est un format de fichier utilisé par de nombreux outils de Machine Learning externes. CSV est un format d’échange courant lorsque vous utilisez des langages open source tels que R ou Python.

Même si vous effectuez la majeure partie de votre travail dans Azure Machine Learning, il peut parfois s’avérer pratique de convertir votre jeu de données au format CSV pour l’utiliser dans des outils externes. Par exemple :

+ Téléchargez le fichier CSV pour l’ouvrir avec Excel, ou importez-le dans une base de données relationnelle.  
+ Enregistrez le fichier CSV dans le stockage cloud et connectez-vous à celui-ci depuis Power BI pour créer des visualisations.  
+ Utilisez le format CSV pour préparer des données pour une utilisation dans R et Python. Cliquez simplement avec le bouton droit sur la sortie du module pour générer le code nécessaire pour accéder aux données directement depuis Python ou un notebook Jupyter. 

Lorsque vous convertissez un jeu de données au format CSV, le fichier est enregistré dans votre espace de travail Azure ML. Vous pouvez utiliser un utilitaire de stockage Azure pour ouvrir et utiliser le fichier directement, ou vous pouvez cliquer avec le bouton droit sur la sortie du module et télécharger le fichier CSV sur votre ordinateur ou l’utiliser dans le code R ou Python.  

## <a name="how-to-configure-convert-to-csv"></a>Comment configurer Convertir au format CSV

1.  Ajoutez le module [Convertir au format CSV](./convert-to-csv.md) à votre pipeline. Vous trouverez ce module dans le groupe **Conversion des formats de données** de l’interface. 

2. Connectez-le à n’importe quel module qui génère un jeu de données.   
  
3.  Exécuter le pipeline.

### <a name="results"></a>Résultats
  

Double-cliquez sur la sortie de [Convertir au format CSV](./convert-to-csv.md), puis sélectionnez une de ces options.  

 + **Jeu de données de résultat -> Télécharger** : Ouvre immédiatement une copie des données au format CSV que vous pouvez enregistrer dans un dossier local. Si vous ne spécifiez pas de dossier, un nom de fichier par défaut est appliqué et le fichier CSV est enregistré dans la bibliothèque **Téléchargements** locale.


 + **Jeu de données de résultat -> Enregistrer comme jeu de données** : Enregistre le fichier CSV dans l’espace de travail Azure ML sous un autre jeu de données.

 + **Generate Data Access Code** (Générer le code d’accès aux données) : Azure ML génère deux jeux de code pour vous permettre d’accéder aux données, à l’aide de Python ou à l’aide de R. Pour accéder aux données, copiez l’extrait de code dans votre application. (*Generate Data Access Code [Générer le code d’accès aux données] sera bientôt disponible.* )

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 