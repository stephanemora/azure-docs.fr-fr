---
title: 'Importer à partir du stockage d’objets Blob Azure : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez que cette rubrique décrit comment utiliser l’importation du module de stockage d’objets Blob Azure dans le service Azure Machine Learning pour lire les données à partir du stockage d’objets blob Azure, afin que vous puissiez utiliser les données dans une expérience d’apprentissage.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4ac98516c1a326e1ede09bbb9660113ffd0642a0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029684"
---
# <a name="import-from-azure-blob-storage-module"></a>Importer à partir du module de stockage d’objets Blob Azure

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour lire les données à partir du stockage d’objets blob Azure, afin que vous puissiez utiliser les données dans une expérience d’apprentissage.  

Le Service Blob Azure est pour stocker de grandes quantités de données, y compris les données binaires. Objets BLOB Azure sont accessible à partir de n’importe où, à l’aide de HTTP ou HTTPS. L’authentification peut être nécessaire en fonction du type de stockage d’objets blob. 

- Objets BLOB publics sont accessibles par tout le monde, ou par les utilisateurs qui ont une URL SAS.
- Objets BLOB privés requièrent une connexion et des informations d’identification.

L’importation à partir du stockage d’objets blob requiert que les données stockées dans des objets BLOB qui utilisent le **objet blob de blocs** format. Les fichiers stockés dans l’objet blob doivent utiliser séparées par des virgules (CSV) ou séparées par une tabulation (TSV) des formats. Lorsque vous lisez le fichier, les enregistrements et tous les en-têtes d’attributs applicables sont chargés sous forme de lignes en mémoire comme un jeu de données.


Nous vous recommandons vivement de profiler vos données avant l’importation, pour vous assurer que le schéma est comme prévu. Le processus d’importation analyse certains nombre de lignes principal pour déterminer le schéma, mais les lignes ultérieures peuvent contenir des colonnes supplémentaires, ou les données qui provoquent des erreurs.



## <a name="manually-set-properties-in-the-import-data-module"></a>Définir manuellement les propriétés dans le module importer des données

Les étapes suivantes décrivent comment configurer manuellement la source d’importation.

1. Ajouter le **importer des données** module à votre expérience. Vous trouverez ce module dans l’interface, dans le **d’entrée de données et de sortie**

2. Pour **source de données**, sélectionnez **stockage Blob Azure**.

3. Pour **type d’authentification**, choisissez **Public (URL SAS)** si vous savez que les informations a été fournies comme source de données public. Une URL SAS est une URL pendant une durée limitée pour l’accès public que vous pouvez générer à l’aide d’un utilitaire de stockage Azure.

    Sinon, choisissez **compte**.

4. Si vos données sont dans un **public** blob qui sont accessibles à l’aide d’une URL SAS, vous n’avez pas besoin des informations d’identification supplémentaires, car la chaîne d’URL contient toutes les informations requises pour le téléchargement et l’authentification.

    Dans le **URI** champ, tapez ou collez l’URI complet qui définit le compte et l’objet blob public.



5. Si vos données sont dans un **privé** compte, vous devez fournir les informations d’identification, y compris le nom du compte et la clé.

    - Pour **nom du compte**, tapez ou collez le nom du compte qui contient l’objet blob que vous souhaitez accéder.

        Par exemple, si l’URL complète du compte de stockage est `http://myshared.blob.core.windows.net`, vous devez taper `myshared`.

    - Pour **clé de compte**, collez la clé d’accès de stockage qui est associée au compte.

        Si vous ne connaissez pas la clé d’accès, consultez la section, « Gérer vos comptes de stockage Azure » dans cet article : [À propos des comptes Azure Storage](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

6. Pour **chemin d’accès au conteneur, répertoire ou blob**, tapez le nom de l’objet blob spécifique que vous souhaitez récupérer.

    Par exemple, si vous avez téléchargé un fichier nommé **data01.csv** au conteneur **trainingdata** dans un compte nommé **mymldata**, l’URL complète pour le fichier serait : `http://mymldata.blob.core.windows.net/trainingdata/data01.txt` .

    Par conséquent, dans le champ **chemin d’accès au conteneur, répertoire ou blob**, vous devez taper : `trainingdata/data01.csv`

    Pour importer plusieurs fichiers, vous pouvez utiliser les caractères génériques `*` (astérisque) ou `?` (point d’interrogation).

    Par exemple, en supposant que le conteneur `trainingdata` contient plusieurs fichiers d’un format compatible, vous pouvez utiliser la spécification suivante pour lire tous les fichiers commençant par `data`et les concaténer dans un seul jeu de données :

    `trainingdata/data*.csv`

    Vous ne pouvez pas utiliser des caractères génériques dans les noms de conteneur. Si vous avez besoin importer des fichiers à partir de plusieurs conteneurs, utilisez une instance distincte de la **importer les données** module pour chaque conteneur et fusionner les jeux de données à l’aide de la [Add Rows](./add-rows.md) module.

    > [!NOTE]
    > Si vous avez sélectionné l’option, **utiliser mis en cache les résultats**, les modifications que vous apportez aux fichiers dans le conteneur ne déclenchent pas une actualisation des données dans l’expérience.

7. Pour **format du fichier Blob**, sélectionnez une option qui indique le format des données qui sont stockées dans l’objet blob, afin qu’Azure Machine Learning peut traiter les données de manière appropriée. Les formats suivants sont pris en charge :

    - **CSV**: Valeurs séparées par des virgules (CSV) sont le format de stockage par défaut pour exporter et importer des fichiers dans Azure Machine Learning. Si les données contient déjà une ligne d’en-tête, veillez à sélectionner l’option, **fichier comporte une ligne d’en-tête**, ou l’en-tête sera traitée comme une ligne de données.

       

    - **TSV**: Valeurs séparées par une tabulation (TSV) sont un format utilisé par un grand nombre des outils d’apprentissage. Si les données contient déjà une ligne d’en-tête, veillez à sélectionner l’option, **fichier comporte une ligne d’en-tête**, ou l’en-tête sera traitée comme une ligne de données.

       

    - **ARFF**: Ce format prend en charge l’importation de fichiers dans le format utilisé par l’ensemble d’outils Weka. 

   

8. Exécutez l’expérience.


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 