---
title: Exécuter un script U-SQL Azure Data Lake sur votre ordinateur local | Microsoft Docs
description: Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio pour exécuter des travaux U-SQL sur votre ordinateur local.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: a7f43c7e17f36d9b4e0767744eee9604c2628ea8
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888964"
---
# <a name="run-u-sql-script-on-your-local-machine"></a>Exécuter un script U-SQL sur votre ordinateur local

Lorsque vous développez un script U-SQL, il est courant d’exécuter le script U-SQL localement, car cela permet d’économiser du temps et de l’argent. Azure Data Lake Tools pour Visual Studio prend en charge l’exécution de scripts U-SQL sur votre ordinateur local. 

## <a name="basic-concepts-for-local-run"></a>Concepts de base pour l’exécution locale

Le graphique ci-dessous montre les composants pour l’exécution locale et comment ces composants sont mappés sur l’exécution cloud.

|Composant|Exécution locale|Exécution cloud|
|---------|---------|---------|
|Stockage|Dossier Racine des données local|Compte Azure Data Lake Store par défaut|
|Calcul|Moteur d’exécution locale U-SQL|Service Azure Data Lake Analytics|
|Environnement d’exécution|Répertoire de travail sur l’ordinateur local|Cluster Azure Data Lake Analytics|

Explications supplémentaires pour les composants de l’exécution locale :

### <a name="local-data-root-folder"></a>Dossier Racine des données local

Ce dossier est un « magasin local » pour le compte de calcul local. N’importe quel dossier dans le système de fichiers local sur votre ordinateur local peut être un dossier Racine des données local. Il est équivalent au compte Azure Data Lake Store par défaut d’un compte Data Lake Analytics. Basculer vers un dossier Racine des données différent revient à basculer vers un autre compte de magasin par défaut. 

Le dossier Racine des données est utilisé pour :
- Stocker les métadonnées, notamment des bases de données, des tables, des fonctions table et des assemblys.
- Rechercher les chemins d’entrée et de sortie qui sont définis comme des chemins relatifs dans le script U-SQL. Les chemins relatifs facilitent le déploiement de vos scripts U-SQL dans Azure.

### <a name="u-sql-local-run-engine"></a>Moteur d’exécution locale U-SQL

Le moteur d’exécution locale U-SQL est un « compte compute local » pour les travaux U-SQL. Les utilisateurs peuvent exécuter des travaux U-SQL localement via Azure Data Lake Tools pour Visual Studio. L’exécution locale est également prise en charge via les interfaces de programmation et de ligne de commande du kit SDK U-SQL Azure Data Lake. [En savoir plus sur le kit SDK U-SQL Azure Data Lake](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directory"></a>Répertoire de travail

Lorsque vous exécutez un script U-SQL, un dossier de répertoire de travail est nécessaire pour la mise en cache des résultats de la compilation, des journaux d’exécution et ainsi de suite. Dans Azure Data Lake Tools pour Visual Studio, le répertoire de travail est le répertoire de travail du projet U-SQL (généralement situé sous `<U-SQL project root path>/bin/debug>`). Le répertoire de travail est nettoyé chaque fois qu’une nouvelle exécution est déclenchée.

## <a name="local-run-in-visual-studio"></a>Exécution locale dans Visual Studio

Azure Data Lake Tools pour Visual Studio a un moteur d’exécution locale intégré et l’utilise en tant que compte compute local. Pour exécuter un script U-SQL localement, sélectionnez le compte (Ordinateur-local) ou (Projet-local) dans la liste déroulante de marge de l’éditeur de script et cliquez sur **Envoyer**.

![Data Lake Tools pour Visual Studio, envoyer le script au compte local](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-run-with-local-machine-account"></a>Exécution locale avec le compte (Ordinateur-local)

Le compte (Ordinateur-local) est un compte compute local partagé avec un seul dossier Racine des données local en tant que compte de magasin local. Le dossier Racine des données est par défaut situé dans « C:\Users\<username>\AppData\Local\USQLDataRoot » et il est configurable via **Outils > Data Lake > Options et paramètres**.

![Data Lake Tools pour Visual Studio, configurer la racine des données locale](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Un projet U-SQL est requis pour l’exécution locale. Le répertoire de travail du projet U-SQL est utilisé pour le répertoire de travail de l’exécution locale U-SQL. Les résultats de la compilation, les journaux d’exécution et les autres fichiers liés à l’exécution du travail sont générés et stockés sous le dossier de répertoire de travail pendant l’exécution locale. Notez qu’à chaque fois que vous réexécutez le script, tous ces fichiers dans le répertoire de travail seront nettoyés et régénérés.

## <a name="local-run-with-local-project-account"></a>Exécution locale avec le compte (Projet-local)

Le compte (Projet-local) est un compte compute local isolé du projet pour chaque projet avec un dossier Racine des données local isolé. Chaque projet U-SQL actif s’ouvrant dans l’Explorateur de solutions est associé à un compte `(Local-project: <project name>)` répertorié à la fois dans l’Explorateur de serveurs et dans la marge de l’éditeur de script U-SQL. 

Le compte (Projet-local) fournit un environnement de développement concis et isolé pour les développeurs. Contrairement au compte (Ordinateur-local) qui dispose d’un dossier Racine des données local partagé stockant les métadonnées et les données d’entrée/sortie pour tous les travaux locaux, le compte (Projet-local) crée un dossier Racine des données local temporaire sous le répertoire de travail du projet U-SQL à chaque fois qu’un script U-SQL est exécuté. Ce dossier Racine des données temporaire est nettoyé en cas de reconstruction ou de réexécution. 

Le projet U-SQL fournit une bonne expérience pour gérer cet environnement d’exécution local isolé via la propriété et la référence de projet. Vous pouvez configurer les sources de données d’entrée pour les scripts U-SQL dans le projet, ainsi que les environnements de bases de données référencés.

### <a name="manage-input-data-source-for-local-project-account"></a>Gérer la source des données d’entrée pour le compte (Projet-local)

Le projet U-SQL prend en charge la création et la configuration des données du dossier Racine des données local pour le compte (Projet-local). Un dossier Racine des données temporaire est nettoyé et recréé sous le répertoire de travail du projet U-SQL à chaque fois que l’exécution locale et la reconstruction se produisent. Toutes les sources de données configurées par le projet U-SQL sont copiées dans ce dossier Racine des données local temporaire avant l’exécution locale des travaux. 

Vous pouvez configurer le dossier racine de vos sources de données en **cliquant avec le bouton droit sur le projet U-SQL > Propriétés > Source des données de test**. Lors de l’exécution d’un script U-SQL sur le compte (Projet-local), tous les fichiers et sous-dossiers (notamment les fichiers dans les sous-dossiers) dans le dossier **Source des données de test** sont copiés dans le dossier Racine des données local temporaire. Après l’exécution du travail local, les résultats de la sortie figurent également dans le dossier Racine des données local temporaire dans le répertoire de travail du projet. Notez que toutes ces sorties seront supprimées et nettoyées quand le projet sera regénéré et nettoyé. 

![Data Lake Tools pour Visual Studio, configurer la source des données de test du projet](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-referenced-database-environment-for-local-project-account"></a>Gérer l’environnement de base de données référencé pour le compte (Projet-local) 

Si une requête U-SQL utilise ou interroge avec des objets de base de données U-SQL, vous devez préparer les environnements de base de données localement avant d’exécuter ce script U-SQL localement. Pour le compte (Projet-local), les dépendances de base de données U-SQL peuvent être gérées par des références au projet U-SQL. Vous pouvez ajouter des références au projet de base de données U-SQL à votre projet U-SQL. Avant d’exécuter des scripts U-SQL sur le compte (Projet-local), toutes les bases de données référencées sont déployées dans le dossier Racine des données local temporaire. Et pour chaque exécution, le dossier Racine des données temporaire est alors nettoyé comme un nouvel environnement isolé.

Articles connexes :
* [Découvrez comment gérer la définition de base de données U-SQL via le projet de base de données U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)
* [Découvrez comment gérer la référence de base de données U-SQL dans le projet U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

## <a name="difference-between-local-machine-and-local-project-account"></a>Différences entre le compte (Ordinateur-local) et le compte (Projet-local)

Le compte (Ordinateur-local) vise à simuler un compte Azure Data Lake Analytics sur l’ordinateur local des utilisateurs. Il partage la même expérience que le compte Azure Data Lake Analytics. Le compte (Projet-local) vise à fournir un environnement de développement local convivial qui permet aux utilisateurs de déployer des références de bases de données et les données d’entrée avant d’exécuter le script localement. Le compte (Ordinateur-local) fournit un environnement permanent partagé, accessible via tous les projets. Le compte (Projet-local) fournit un environnement de développement isolé pour chaque projet, et il est actualisé pour chaque exécution. Selon les éléments ci-dessus, le compte (Projet-local) offre une expérience de développement plus rapide en appliquant rapidement les nouvelles modifications.

Vous trouverez plus de différences entre le compte (Ordinateur-local) et le compte (Projet-local) dans le graphique ci-dessous :

|Différence|(Ordinateur-local)|(Projet-local)|
|----------------|---------------|---------------|
|Accès local|Accessible par tous les projets|Seul le projet correspondant peut accéder à ce compte|
|Dossier Racine des données local|Dossier local permanent. Configuré via **Outils > Data Lake > Options et paramètres**|Un dossier temporaire créé pour chaque exécution locale sous le répertoire de travail du projet U-SQL. Le dossier est nettoyé en cas de reconstruction ou de réexécution|
|Données d’entrée pour le script U-SQL|Chemin d’accès relatif sous le dossier Racine des données local permanent|Défini via **Propriété du projet U-SQL > Source des données de test**. Tous les fichiers et les sous-dossiers sont copiés dans le dossier Racine des données temporaire avant l’exécution locale|
|Données de sortie pour le script U-SQL|Chemin d’accès relatif sous le dossier Racine des données local permanent|Dirigées vers le dossier Racine des données temporaire. Les résultats sont nettoyés en cas de reconstruction ou de réexécution.|
|Déploiement de base de données référencée|Les bases de données référencées ne sont pas déployées automatiquement lors de l’exécution sur le compte (Ordinateur-local). Il en va de même pour l’envoi au compte Azure Data Lake Analytics.|Les bases de données référencées sont déployées sur le compte (Projet-local) automatiquement avant l’exécution locale. Tous les environnements de base de données sont nettoyés et redéployés en cas de reconstruction ou de réexécution.|

## <a name="local-run-with-u-sql-sdk"></a>Exécution locale avec le kit SDK U-SQL

Outre l’exécution locale de scripts U-SQL dans Visual Studio, vous pouvez également utiliser le kit SDK U-SQL Azure Data Lake pour exécuter les scripts U-SQL en local, avec la ligne de commande et les interfaces de programmation. Grâce à ces interfaces, vous pouvez automatiser l’exécution locale et le test de U-SQL.

[En savoir plus sur le kit SDK U-SQL Azure Data Lake](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Étapes suivantes

- [Kit SDK Azure Data Lake U-SQL](data-lake-analytics-u-sql-sdk.md)
- [Comment configurer un pipeline CI/CD pour Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Utiliser un projet de base de données U-SQL afin de développer une base de données U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)
- [Comment tester votre code Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
