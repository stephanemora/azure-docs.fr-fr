---
title: 'Exporter des données : informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Exporter les données dans le service Azure Machine Learning pour enregistrer les résultats, les données intermédiaires et les données de travail de vos expériences dans les destinations de stockage cloud en dehors d’Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c3744803f172edf9fbf2556a12677e8faef370c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028319"
---
# <a name="export-data-module"></a>Module Exporter les données

Cet article décrit un module de l’interface visuelle (préversion) du service Azure Machine Learning.

Utilisez ce module pour enregistrer les résultats, les données intermédiaires et les données de travail de vos expériences dans les destinations de stockage cloud en dehors d’Azure Machine Learning.

Ce module prend en charge l’exportation ou de l’enregistrement de vos données dans les services de données cloud suivants :


- **Exportation vers le Stockage Blob Azure** : enregistre les données dans le service Blob d’Azure. Les données du service Blob peuvent être partagées publiquement ou enregistrées dans des magasins de données d’application sécurisés.

  
## <a name="how-to-configure-export-data"></a>Configuration de l’exportation des données

1. Ajoutez le module **Exporter des données** à votre expérience dans l’interface. Ce module figure dans la catégorie **Entrée et sortie**.

2. Connectez **Exporter des données** au module qui contient les données à exporter.

3. Double-cliquez sur **Exporter les données** pour ouvrir le volet **Propriétés**.

4. Pour la **destination des données**, sélectionnez le type de stockage cloud où vous allez enregistrer vos données. Si vous apportez des modifications à cette option, toutes les autres propriétés sont réinitialisées. Par conséquent, veillez à sélectionner cette option en premier !

5. Indiquez un nom de compte et une méthode d’authentification requis pour accéder au compte de stockage spécifié.

    L’**exportation vers le stockage Blob Azure** est la seule option disponible en préversion privée. Vous verrez ci-dessous comment configurer le module.
    1. Le service blob Azure sert à stocker de grandes quantités de données, y compris des données binaires. Il existe deux types de stockage d’objets Blob : les objets blob publics et les objets Blob qui nécessitent des informations d’identification de connexion.

    2. Pour le **type d’authentification**, choisissez **Public (SAP)** si vous savez que le stockage prend en charge l’accès via une URL SAP.

          Une URL SAP est un type spécial de l’URL qui peut être généré à l’aide d’un utilitaire de stockage Azure et qui est disponible pendant une durée limitée.  Elle contient toutes les informations nécessaires pour l’authentification et le téléchargement.

        Pour l’**URI**, saisissez ou collez l’URI complet qui définit le compte et l’objet blob public.

        Les formats de fichier CSV et TSV sont pris en charge.

    3. Pour les comptes privés, choisissez **Compte**, puis indiquez le nom du compte et la clé du compte, afin que l’expérience puisse écrire dans le compte de stockage.

         - **Nom du compte** : saisissez ou collez le nom du compte dans lequel vous souhaitez enregistrer les données. Par exemple, si l’URL complète du compte de stockage est `http://myshared.blob.core.windows.net`, vous devez saisir `myshared`.

        - **Clé du compte** : collez la clé d’accès de stockage associée au compte.

        -  **Chemin d’accès au conteneur, répertoire ou Blob** : saisissez le nom de l’objet blob où les données exportées seront stockées. Par exemple, pour enregistrer les résultats de votre expérience dans un nouvel objet blob nommé **results01.csv** du conteneur **prédictions** dans un compte nommé **mymldata**, l’URL complète de l’objet blob serait `http://mymldata.blob.core.windows.net/predictions/results01.csv`.

            Par conséquent, dans le champ **Chemin d’accès au conteneur, répertoire ou Blob**, vous devez spécifier le nom du conteneur et de l’objet blob comme suit : `predictions/results01.csv`

        - Si vous spécifiez le nom d’un objet blob qui n’existe pas encore, Azure crée l’objet blob pour vous.

       -  Lors de l’écriture dans un objet blob existant, vous pouvez spécifier que le contenu actuel de l’objet blob doit être remplacé en définissant la propriété de **mode écriture de stockage Blob Azure**. Par défaut, cette propriété est définie sur **Erreur**, ce qui signifie qu’une erreur est générée chaque fois qu’un fichier blob portant le même nom est trouvé.


    4. Pour le **format du fichier blob**, sélectionnez le format dans lequel les données doivent être stockées.

        - **CSV** : le format CSV (valeurs séparées par des virgules) est le format de stockage par défaut. Pour exporter les en-têtes de colonnes avec les données, sélectionnez l’option **Write blob header row** (Écrire la ligne d’en-tête d’objet blob).  Pour plus d’informations sur format CSV dans Azure Machine Learning, voir la [conversion au format CSV](./convert-to-csv.md).

        - **TSV** : le format TSV (valeurs séparées par des tabulations) est compatible avec de nombreux outils d’apprentissage automatique. Pour exporter les en-têtes de colonnes avec les données, sélectionnez l’option **Write blob header row** (Écrire la ligne d’en-tête d’objet blob).  

 
    5. **Utiliser les résultats mis en cache** : sélectionnez cette option si vous souhaitez éviter de réécrire les résultats dans le fichier blob chaque fois que vous exécutez l’expérience. Si aucune autre modification n’est apportée aux paramètres du module, l’expérience écrit les résultats uniquement lors de la première exécution du module, ou lorsque des données sont modifiées.

    6. Exécutez l’expérience.

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour le service Azure Machine Learning. 