---
title: Copier des fichiers à partir de plusieurs conteneurs à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser un modèle de solution pour copier des fichiers à partir de plusieurs conteneurs à l’aide d’Azure Data Factory.
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
ms.openlocfilehash: a52729adf8d6df3f4e44e561b45b854db433628c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635135"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Copier des fichiers issus de plusieurs conteneurs avec Azure Data Factory

Cet article décrit un modèle de solution que vous pouvez utiliser pour copier des fichiers à partir de plusieurs conteneurs entre les magasins de fichiers. Par exemple, vous pouvez l’utiliser pour migrer votre lac de données à partir d’AWS S3 à Azure Data Lake Store. Ou bien, vous pouvez utiliser le modèle pour répliquer tous les éléments à partir d’un compte de stockage d’objets Blob Azure vers un autre.

> [!NOTE]
> Si vous souhaitez copier des fichiers à partir d’un seul conteneur, il est plus efficace d’utiliser le [outil Copier des données](copy-data-tool.md) pour créer un pipeline avec une seule activité de copie. Le modèle dans cet article est plus que vous avez besoin pour ce scénario simple.

## <a name="about-this-solution-template"></a>À propos de ce modèle de solution

Ce modèle énumère les conteneurs à partir de votre magasin de stockage source. Elle copie ensuite ces conteneurs vers le magasin de destination.

Le modèle comporte trois activités :
- **GetMetadata** analyse votre magasin de stockage source et obtient la liste de conteneurs.
- **ForEach** Obtient la liste de conteneurs à partir de la **GetMetadata** activité effectue une itération sur la liste et transmet chaque conteneur à l’activité de copie.
- **Copie** copie chaque conteneur du magasin de stockage source vers le magasin de destination.

Le modèle définit deux paramètres :
- *CheminAccèsFichierSource* est le chemin d’accès de votre magasin de source de données, où vous pouvez obtenir une liste des conteneurs. Dans la plupart des cas, il s’agit du répertoire racine, qui comporte plusieurs dossiers de conteneur. La valeur par défaut de ce paramètre est `/`.
- *CheminAccèsFichierDestination* est le chemin d’accès où les fichiers seront copiés dans votre magasin de destination. La valeur par défaut de ce paramètre est `/`.

## <a name="how-to-use-this-solution-template"></a>Utiliser ce modèle de solution

1. Accédez à la **copier plusieurs conteneurs de fichiers entre les magasins de fichiers** modèle. Créer un **New** connexion à votre magasin de stockage source. Le magasin de stockage source est où vous souhaitez copier des fichiers à partir de plusieurs conteneurs à partir de.

    ![Créer une connexion à la source](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Créer un **New** connexion à votre magasin de stockage de destination.

    ![Créer une connexion à la destination](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Sélectionnez **utiliser ce modèle**.

    ![Utiliser ce modèle](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Vous verrez le pipeline, comme dans l’exemple suivant :

    ![Afficher le pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Sélectionnez **déboguer**, entrez la **paramètres**, puis sélectionnez **Terminer**.

    ![Exécuter le pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Vérifiez le résultat.

    ![Vérifier le résultat](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Étapes suivantes

- [Copie en bloc à partir d’une base de données à l’aide d’une table de contrôle avec Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Copier des fichiers à partir de plusieurs conteneurs avec Azure Data Factory](solution-template-copy-files-multiple-containers.md)