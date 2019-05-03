---
title: 'Exporter des données : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module d’exporter des données dans le service Azure Machine Learning pour enregistrer les résultats, les données intermédiaires et les données de travail de vos expériences dans les destinations de stockage cloud en dehors d’Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c3744803f172edf9fbf2556a12677e8faef370c2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028319"
---
# <a name="export-data-module"></a>Exporter le module de données

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour enregistrer les résultats, les données intermédiaires et les données de travail de vos expériences dans les destinations de stockage cloud en dehors d’Azure Machine Learning.

Ce module prend en charge l’exportation ou de l’enregistrement de vos données dans les services de données cloud suivants :


- **Exporter vers le stockage Blob Azure**: Enregistre les données dans le service Blob dans Azure. Les données dans le service Blob peuvent être partagées publiquement ou enregistrées dans les magasins de données de sécurisation de l’application.

  
## <a name="how-to-configure-export-data"></a>Comment exporter des données de configuration

1. Ajouter le **exporter des données** module à votre expérience dans l’interface. Vous trouverez ce module dans le **d’entrée et sortie** catégorie.

2. Se connecter **exporter des données** au module qui contient les données que vous voulez exporter.

3. Double-cliquez sur **exporter des données** pour ouvrir le **propriétés** volet.

4. Pour **destination de données**, sélectionnez le type de stockage cloud où vous allez enregistrer vos données. Si vous apportez des modifications à cette option, toutes les autres propriétés sont réinitialisées. Par conséquent, veillez à sélectionner cette option tout d’abord !

5. Fournir une méthode d’authentification et le nom de compte requise pour accéder au compte de stockage spécifié.

    **Exporter vers le stockage Blob Azure** est la seule option disponible en version préliminaire privée. Ci-dessous montre comment configurer le module.
    1. Le service blob Azure est pour stocker de grandes quantités de données, y compris les données binaires. Il existe deux types de stockage d’objets blob : objets BLOB publics et les objets BLOB qui nécessitent des informations d’identification de connexion.

    2. Pour **type d’authentification**, choisissez **Public (SAP)** si vous savez que le stockage prend en charge l’accès via une URL SAS.

          Une URL SAS est un type spécial de l’URL qui peut être généré à l’aide d’un utilitaire de stockage Azure et est disponible que pendant une durée limitée.  Il contient toutes les informations qui sont nécessaire pour l’authentification et le téléchargement.

        Pour **URI**, tapez ou collez l’URI complet qui définit le compte et l’objet blob public.

        Format de fichier CSV et TSV sont prises en charge.

    3. Pour les comptes privés, choisissez **compte**et fournir le nom du compte et la clé du compte, afin que l’expérience peut écrire dans le compte de stockage.

         - **Nom du compte** : Tapez ou collez le nom du compte dans lequel vous souhaitez enregistrer les données. Par exemple, si l’URL complète du compte de stockage est `http://myshared.blob.core.windows.net`, vous devez taper `myshared`.

        - **Clé du compte** : Collez la clé d’accès de stockage qui est associée au compte.

        -  **Chemin d’accès au conteneur, répertoire ou blob**: Tapez le nom de l’objet blob de stockage pour les données exportées. Par exemple, pour enregistrer les résultats de votre expérience dans un nouvel objet blob nommé **results01.csv** dans le conteneur **prédictions** dans un compte nommé **mymldata**, l’URL complète de la objet BLOB serait `http://mymldata.blob.core.windows.net/predictions/results01.csv`.

            Par conséquent, dans le champ **chemin d’accès au conteneur, répertoire ou blob**, vous devez spécifier le conteneur et le nom d’objet blob comme suit : `predictions/results01.csv`

        - Si vous spécifiez le nom d’un objet blob qui ne sont pas déjà exister, Azure crée l’objet blob pour vous.

       -  Lors de l’écriture à un objet blob existant, vous pouvez spécifier que le contenu actuel de l’objet blob être remplacée en définissant la propriété, **stockage blob Azure en mode écriture**. Par défaut, cette propriété est définie **erreur**, ce qui signifie qu’une erreur est générée chaque fois qu’un fichier blob existant portant le même nom est trouvé.


    4. Pour **format de fichier pour le fichier blob**, sélectionnez le format dans lequel les données doivent être stockées.

        - **CSV**: Valeurs séparées par des virgules (CSV) sont le format de stockage par défaut. Pour exporter des en-têtes de colonnes avec les données, sélectionnez l’option **ligne d’en-tête écriture objet blob**.  Pour plus d’informations sur le virgules - format délimité dans Azure Machine Learning, consultez [Convert to CSV](./convert-to-csv.md).

        - **TSV**: Format de valeurs séparées par une tabulation (TSV) est compatible avec de nombreux outils d’apprentissage machine. Pour exporter des en-têtes de colonnes avec les données, sélectionnez l’option **ligne d’en-tête écriture objet blob**.  

 
    5. **Utiliser les résultats mis en cache**: Sélectionnez cette option si vous souhaitez éviter de réécrire les résultats dans le fichier blob chaque fois que vous exécutez l’expérience. S’il n’y a aucune autre modification aux paramètres du module, l’expérience écrit les résultats de la première fois le module est exécuté, ou lorsque des modifications sont apportées aux données.

    6. Exécutez l’expérience.

## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 