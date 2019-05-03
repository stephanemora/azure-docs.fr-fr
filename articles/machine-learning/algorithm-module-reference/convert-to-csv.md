---
title: 'Convertir en fichier CSV : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser la convertir en module de volume partagé de cluster dans le service Azure Machine Learning pour convertir un jeu de données dans un fichier CSV qui peut être téléchargé, exporté ou partagé avec les modules de script R ou Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 8b8b6758cc2df7a092ce36e9507f84ac534d0e3d
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028724"
---
# <a name="convert-to-csv-module"></a>Convertir en module de volume partagé de cluster

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour convertir un jeu de données dans un fichier CSV qui peut être téléchargé, exporté ou partagé avec les modules de script R ou Python.

### <a name="more-about-the-csv-format"></a>Plus d’informations sur le format CSV 

Le format CSV, ce qui signifie « valeurs séparées par des virgules », est un format de fichier utilisé par nombreux outils d’apprentissage externe. CSV est un format d’échange courantes lorsque vous travaillez avec des langages open source comme R ou Python.

Même si vous effectuez la majeure partie de votre travail dans Azure Machine Learning, voici les heures lorsque il peut s’avérer pratique pour convertir votre jeu de données au format CSV à utiliser dans les outils externes. Par exemple : 

+ Téléchargez le fichier CSV pour l’ouvrir avec Excel, ou l’importer dans une base de données relationnelle.  
+ Enregistrez le fichier CSV pour le stockage cloud et vous y connecter depuis Power BI pour créer des visualisations.  
+ Utilisez le format CSV pour préparer des données pour une utilisation dans R et Python. Simplement avec le bouton droit de la sortie du module pour générer le code nécessaire pour accéder aux données directement à partir de Python ou un bloc-notes Jupyter. 

Lorsque vous convertissez un jeu de données au format CSV, le fichier est enregistré dans votre espace de travail Azure ML. Vous pouvez utiliser un utilitaire de stockage Azure pour ouvrir et utiliser le fichier directement, ou vous pouvez avec le bouton droit de la sortie du module et télécharger le fichier CSV sur votre ordinateur ou l’utiliser dans le code R ou Python.  

## <a name="how-to-configure-convert-to-csv"></a>Comment configurer les convertir au format CSV

1.  Ajouter le [Convert to CSV](./convert-to-csv.md) module à votre expérience. Vous trouverez ce module dans le **les Conversions de Format de données** groupe dans l’interface. 

2. Connecter à n’importe quel module qui génère un jeu de données.   
  
3.  Exécutez l’expérience.

### <a name="results"></a>Résultats
  

Double-cliquez sur la sortie de [Convert to CSV](./convert-to-csv.md), puis sélectionnez une de ces options.  

 + **Jeu de données de résultat -> téléchargement**: Ouvre une copie des données au format CSV que vous pouvez enregistrer dans un dossier local. Si vous ne spécifiez pas un dossier, un nom de fichier par défaut est appliqué et le fichier CSV est enregistré dans local **télécharge** bibliothèque.


 + **Jeu de données de résultat -> Enregistrer en tant que jeu de données**: Enregistre le fichier CSV dans l’espace de travail Azure ML comme un jeu de données distinct.

 + **Générer le Code d’accès aux données**: Azure ML génère deux ensembles de code pour vous permettre d’accéder aux données, à l’aide de Python ou à l’aide de R. Pour accéder aux données, copiez l’extrait de code dans votre application. (*Générer un Code d’accès aux données seront bientôt.* )

## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 