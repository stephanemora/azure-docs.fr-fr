---
title: Copie de fichiers à partir de plusieurs conteneurs
description: Découvrez comment utiliser un modèle de solution pour copier des fichiers provenant de plusieurs conteneurs à l’aide d’Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: 73560c49e10ab96c934d4dd3cea9395093a26420
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82629046"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Copier des fichiers issus de plusieurs conteneurs avec Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cet article décrit un modèle de solution que vous pouvez utiliser pour copier des fichiers à partir de plusieurs conteneurs entre des magasins de fichiers. Vous pouvez par exemple l’utiliser pour migrer votre lac de données d’AWS S3 vers Azure Data Lake Store, ou pour tout répliquer d’un compte Stockage Blob Azure vers un autre.

> [!NOTE]
> Si vous souhaitez copier des fichiers provenant d’un même conteneur, il est plus efficace d’utiliser l’[outil Copier des données](copy-data-tool.md) pour créer un pipeline avec une seule activité de copie. Le modèle dans cet article suffit amplement pour ce scénario simple.

## <a name="about-this-solution-template"></a>À propos de ce modèle de solution

Ce modèle énumère les conteneurs à partir de votre magasin de stockage source. Il copie ensuite ces conteneurs vers le magasin de destination.

Le modèle comporte trois activités :
- **GetMetadata** analyse votre magasin de stockage source et obtient la liste des conteneurs.
- **ForEach** obtient la liste des conteneurs auprès de l’activité **GetMetadata**, puis itère au sein de la liste et transmet chaque conteneur à l’activité Copy.
- **Copy** copie chaque conteneur du magasin de stockage source vers le magasin de destination.

Le modèle définit les paramètres suivants :
- *SourceFileFolder* est le chemin de dossier de votre magasin de source de données, où vous pouvez obtenir la liste des conteneurs. Il s’agit du répertoire racine, qui comporte plusieurs dossiers de conteneur. La valeur par défaut de ce paramètre est `sourcefolder`.
- *SourceFileDirectory* est le chemin de sous-dossier sous le répertoire racine de votre magasin de source de données. La valeur par défaut de ce paramètre est `subfolder`.
- *DestinationFileFolder* est le chemin de dossier où les fichiers seront copiés dans votre magasin de destination. La valeur par défaut de ce paramètre est `destinationfolder`.
- *DestinationFileDirectory* est le chemin de sous-dossier où les fichiers seront copiés dans votre magasin de destination. La valeur par défaut de ce paramètre est `subfolder`.

## <a name="how-to-use-this-solution-template"></a>Utiliser ce modèle de solution

1. Accédez au modèle **Copier plusieurs conteneurs de fichiers entre des magasins de fichiers**. Créez une **nouvelle** connexion à votre magasin de stockage source. C’est dans ce magasin que seront copiés les fichiers provenant de plusieurs conteneurs.

    ![Créer une connexion à la source](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Créez une **nouvelle** connexion à votre magasin de stockage de destination.

    ![Créer une connexion à la destination](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Sélectionnez **Utiliser ce modèle**.

    ![Utiliser ce modèle](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Vous verrez le pipeline, comme indiqué dans l’exemple suivant :

    ![Afficher le pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Sélectionnez **Déboguer**, entrez les **Paramètres**, puis sélectionnez **Terminer**.

    ![Exécuter le pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Vérifiez le résultat.

    ![Vérifier le résultat](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Étapes suivantes

- [Copier en bloc à partir d’une base de données à l’aide d’une table de contrôle avec Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Copier des fichiers issus de plusieurs conteneurs avec Azure Data Factory](solution-template-copy-files-multiple-containers.md)
