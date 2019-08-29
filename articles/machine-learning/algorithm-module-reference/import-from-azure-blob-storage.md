---
title: 'Importer à partir du Stockage Blob Azure : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Cette rubrique décrit comment utiliser le module Importer du module de stockage Blob Azure dans le service Azure Machine Learning pour lire les données de Azure Blob Storage, pour pouvoir utiliser les données dans une expérience d'apprentissage automatique.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fea64070c496379351bb75f2a38aba9b4db70dcd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128722"
---
# <a name="import-from-azure-blob-storage-module"></a>Importer à partir du module de stockage Blob Azure

Cet article décrit un module de l’interface visuelle (préversion) pour le service Azure Machine Learning.

Utilisez ce module pour lire les données à partir du stockage Blob Azure, afin que vous puissiez utiliser les données dans une expérience d’apprentissage.  

Le service Blob Azure sert à stocker de grandes quantités de données, y compris des données binaires. Vous pouvez accéder aux objets Blob Azure à partir de n’importe où, à l’aide du HTTP ou du HTTPS. L’authentification peut être nécessaire en fonction du type de stockage d’objets Blob. 

- Tout le monde, notamment les utilisateurs ayant une URL SAS, peut accéder aux Blobs publics.
- Objets BLOB privés requièrent une connexion et des informations d’identification.

L’importation à partir du stockage d’objets Blob requiert que les données soient stockées dans des objets Blob qui utilisent le format **objet Blob de blocs**. Les fichiers stockés dans un objet Blob doivent utiliser le format de séparation par des virgules (CSV) ou de séparation par une tabulation (TSV). Lorsque vous lisez le fichier, les enregistrements et tous les en-têtes d’attributs applicables sont chargés sous forme de lignes en mémoire comme un jeu de données.


Nous vous recommandons vivement de profiler vos données avant l’importation, pour vous assurer que le schéma est comme prévu. Le processus d’importation analyse un certain nombre de lignes principales pour déterminer le schéma, mais les lignes ultérieures peuvent contenir des colonnes supplémentaires, ou des données qui provoquent des erreurs.



## <a name="manually-set-properties-in-the-import-data-module"></a>Définir manuellement les propriétés du module Importer des données

Les étapes suivantes décrivent la configuration manuelle de la source d’importation.

1. Ajoutez le module **Importer des données** à votre expérience. Vous trouverez ce module dans l’interface, dans la catégorie **Data Input and Output** (Entrée et sortie de données)

2. Pour **Source de données**, sélectionnez **Stockage Blob Azure**.

3. Pour **Type d’authentification**, choisissez **Public (URL SAS)** si vous savez que les informations ont été fournies en tant que source de données publique. Une URL SAS est une URL ayant une durée limitée pour l’accès public. Vous pouvez la générer à l’aide d’un utilitaire de stockage Azure.

    Sinon, choisissez un **Compte**.

4. Si vos données sont dans un objet Blob **public** qui est accessible à l’aide d’une URL SAS, vous n’avez pas besoin des informations d’identification supplémentaires, car la chaîne d’URL contient toutes les informations requises pour le téléchargement et l’authentification.

    Dans le champ **URI**, entrez ou collez l’URI complet qui définit le compte et l’objet Blob public.



5. Si vos données sont dans un **privé** compte, vous devez fournir les informations d’identification, y compris le nom du compte et la clé.

    - **Nom du compte** : entrez ou collez le nom du compte contenant l’objet Blob auquel vous voulez accéder.

        Par exemple, si l’URL complète du compte de stockage est `http://myshared.blob.core.windows.net`, vous devez saisir `myshared`.

    - Pour la **clé de compte**, collez la clé d’accès de stockage associée au compte.

        Si vous ne connaissez pas la clé d’accès, consultez la section, « Gérer vos comptes de stockage Azure » dans cet article : [À propos des comptes Azure Storage](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

6. Pour le **chemin d'accès au conteneur, le répertoire ou l’objet blob**, entrez le nom du blob spécifique que vous voulez récupérer.

    Par exemple, si vous avez téléchargé un fichier nommé **data01.csv** au conteneur **trainingdata** dans un compte nommé **mymldata**, l’URL complète pour le fichier serait : `http://mymldata.blob.core.windows.net/trainingdata/data01.txt`.

    Par conséquent, dans le champ **Chemin d’accès au conteneur, répertoire ou blob**, vous devez taper : `trainingdata/data01.csv`

    Pour importer plusieurs fichiers, vous pouvez utiliser les caractères génériques `*` (astérisque) ou `?` (point d’interrogation).

    Par exemple, en supposant que le conteneur `trainingdata` contient plusieurs fichiers d’un format compatible, vous pouvez utiliser la spécification suivante pour lire tous les fichiers commençant par `data` et les concaténer dans un seul jeu de données :

    `trainingdata/data*.csv`

    Vous ne pouvez pas utiliser de caractères génériques dans les noms de conteneur. Si vous avez besoin d’importer des fichiers à partir de plusieurs conteneurs, utilisez une instance distincte du module **Importer des données** pour chaque conteneur et, puis fusionner les jeux de données à l’aide du module [Add Rows](./add-rows.md) (Ajouter des lignes).

    > [!NOTE]
    > Si vous avez sélectionné l’option, **Utiliser des résultats mis en cache**, les modifications que vous apportez aux fichiers dans le conteneur ne déclenchent pas une actualisation des données dans l’expérience.

7. Pour le **format du fichier Blob**, sélectionnez une option qui indique le format des données qui sont stockées dans l’objet blob, afin qu’Azure Machine Learning puisse traiter les données de manière appropriée. Les formats suivants sont pris en charge :

    - **CSV** : Les valeurs séparées par des virgules (CSV) constituent le format de stockage par défaut pour l’exportation et l’importation des fichiers dans Azure Machine Learning. Si les données contiennent déjà une ligne d’en-tête, veillez à sélectionner l’option, **Le fichier comporte une ligne d’en-tête**, sans quoi, l’en-tête sera traitée comme une ligne de données.

       

    - **TSV** : Les valeurs séparées par une tabulation (TSV) constituent un format utilisé par un grand nombre des outils d’apprentissage machine. Si les données contiennent déjà une ligne d’en-tête, veillez à sélectionner l’option, **Le fichier comporte une ligne d’en-tête**, sans quoi, l’en-tête sera traitée comme une ligne de données.

       

    - **ARFF** : Ce format prend en charge l’importation de fichiers au format utilisé par l’ensemble d’outils Weka. 

   

8. Exécutez l’expérience.


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 