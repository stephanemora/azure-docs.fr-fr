---
title: Copier des fichiers issus de plusieurs conteneurs avec Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser un modèle de solution pour copier des fichiers provenant de plusieurs conteneurs avec Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/1/2018
ms.openlocfilehash: aa5f32594c295ab6a8e60af8359370f64f75a72d
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966558"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Copier des fichiers issus de plusieurs conteneurs avec Azure Data Factory

Le modèle de solution décrit dans cet article aide à copier des fichiers provenant de plusieurs fichiers, conteneurs ou compartiments entre différents magasins de fichiers. Il est conçu pour plusieurs cas d’usage, par exemple, migrer un Data Lake d’AWS S3 vers Azure Data Lake Store ou tout répliquer d’un compte de Stockage Blob Azure vers un autre .

Si vous souhaitez copier des fichiers provenant d’un même conteneur ou compartiment, il est plus efficace d’utiliser **l’outil Copier des données** pour créer un pipeline avec une seule activité de copie. Ce modèle dépasse les besoins de ce cas d’utilisation simple.

## <a name="about-this-solution-template"></a>À propos de ce modèle de solution

Ce modèle énumère les conteneurs du magasin de stockage source, puis copie chacun d’eux dans le magasin de destination. 

Le modèle comporte trois activités :
-   une activité **GetMetadata** visant à analyser le magasin de stockage source et à dresser la liste des conteneurs ;
-   une activité **ForEach** permettant d’obtenir la liste des conteneurs auprès de l’activité **GetMetadata**, puis de transmettre chaque conteneur de la liste à l’activité Copy ;
-   une activité **Copy** servant à copier chaque conteneur du magasin de stockage source vers le magasin de destination.

Le modèle définit deux paramètres :
-   Le paramètre *SourceFilePath* est le chemin d’accès du magasin de données source, qui permet d’obtenir une liste des conteneurs ou des compartiments. Dans la plupart des cas, il s’agit du répertoire racine, qui comporte plusieurs dossiers de conteneur. La valeur par défaut de ce paramètre est `/`.
-   Le paramètre *DestinationFilePath* est le chemin d’accès où les fichiers seront copiés dans la magasin de destination. La valeur par défaut de ce paramètre est `/`.

## <a name="how-to-use-this-solution-template"></a>Utiliser ce modèle de solution

1. Accédez au modèle **Copier plusieurs conteneurs de fichiers entre différents magasins de fichiers**, puis créez une **nouvelle connexion** à votre magasin de stockage source. C’est dans ce magasin que seront copiés les fichiers provenant de plusieurs conteneurs ou compartiments.

    ![Créer une connexion à la source](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Créez une **nouvelle connexion** au magasin de stockage de destination.

    ![Créer une connexion à la destination](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Cliquez sur **Utiliser ce modèle**.

    ![Utiliser ce modèle](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Le pipeline apparaît comme disponible dans le panneau, comme le montre l’exemple suivant :

    ![Afficher le pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Cliquez sur **Déboguer**, entrez **Paramètres**, puis cliquez sur **Terminer**.

    ![Exécuter le pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Vérifiez le résultat.

    ![Vérifier le résultat](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation du service Azure Data Factory](introduction.md)
