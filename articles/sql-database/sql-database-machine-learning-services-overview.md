---
title: Azure SQL Database Machine Learning Services avec vue d’ensemble de R (version préliminaire)
description: Cet article décrit la base de données de SQL Azure Machine Learning Services (avec R) et explique son fonctionnement.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: b33561ad80207a5d649ac1fd497c9d6c7add78c6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723988"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>SQL Database Services Azure Machine Learning avec R (version préliminaire)

Machine Learning Services est une fonctionnalité d’Azure SQL Database pour l’exécution de scripts R dans les bases de données. La fonctionnalité inclut les packages Microsoft R pour l’analyse prédictive haute performance et le machine learning. Les données relationnelles peuvent être utilisées dans des scripts R au moyen de procédures stockées, de scripts T-SQL contenant des instructions R ou de code R contenant T-SQL.

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services (avec R) est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> La version préliminaire publique est disponible pour les bases de données uniques et des pools élastiques à l’aide du modèle d’achat vCore dans le **à usage général** et **critique pour l’entreprise** niveaux de service. Dans cette préversion publique initiale, le niveau de service **Hyperscale** et l'option de déploiement **Instance gérée** ne sont pas pris en charge. Actuellement, R est le seul langage pris en charge. Il n’existe aucune prise en charge de Python pour l’instant.
>
> La version préliminaire est actuellement disponible dans les régions suivantes : Europe de l’ouest, Europe du Nord, ouest des États-Unis 2, est des États-Unis, Sud-Centre des États-Unis, Nord du centre des États-Unis, centre du Canada, Asie du Sud-est, Inde-Sud et sud-est de l’Australie.
>
> [Inscrivez-vous à la préversion](#signup) ci-après.

## <a name="what-you-can-do-with-r"></a>Fonctionnalités de R

Utilisez la puissance du langage R pour fournir des fonctionnalités avancées d’analytique et de machine learning en base de données. Les calculs et le traitement sont effectués là où les données résident, éliminant ainsi la nécessité de tirer (pull) les données sur le réseau. En outre, vous pouvez exploiter la puissance de packages R d’entreprise pour fournir une analytique avancée à l’échelle.

Machine Learning Services inclut une distribution de base de R à laquelle Microsoft superpose des packages R d’entreprise. Les fonctions et algorithmes R de Microsoft sont conçus pour combiner mise à l’échelle et utilité dans les domaines suivants : analytique prédictive, modélisation statistique, visualisation des données et algorithmes de machine learning de pointe.

### <a name="r-packages"></a>Packages R

Les packages R open source courants sont préinstallés dans Machine Learning Services. Les packages R suivants de Microsoft sont également inclus :

| Package R | Description|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open est la distribution améliorée de Microsoft R. Il est une plateforme open source complète de science de données et d’analyse statistique. Il est basé sur R et totalement compatible avec ce langage, et il inclut des capacités supplémentaires pour améliorer les performances et la reproductibilité. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR est la bibliothèque principale de R évolutive. Les fonctions de cette bibliothèque sont parmi les plus utilisées. La transformation et la manipulation des données, le résumé statistique, la visualisation et de nombreuses formes de modélisation et d’analyse sont rendues possibles avec ces bibliothèques. De plus, les fonctions de ces bibliothèques répartissent automatiquement les charges de travail entre les cœurs disponibles pour un traitement parallèle, avec la possibilité de travailler sur des blocs de données qui sont coordonnés et gérés par le moteur de calcul. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML ajoute des algorithmes de Machine Learning pour créer des modèles personnalisés pour l’analyse de texte, l’analyse d’images et l’analyse de sentiments. |

<!-- Add this back when the new package-related article is written
In addition to the pre-installed packages, you can [install additional packages](sql-database-quickstart-r-create-script.md#add-a-package).
-->

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>S’inscrire à la version préliminaire

Pour vous inscrire à la préversion publique, procédez comme suit :

1. Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/) avant de commencer.

2. Envoyez un e-mail à Microsoft sur [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) pour vous inscrire à la préversion publique. La préversion publique de Machine Learning Services (avec R) dans SQL Database n’est pas activée par défaut.

Une fois que vous êtes inscrit au programme, Microsoft vous intégrerons à la version préliminaire publique et activer R pour votre existante ou nouvelle base de données.

Services machine Learning avec R n’est pas recommandée pour la charge de travail de production pendant la préversion publique.

## <a name="next-steps"></a>Étapes suivantes

- Consultez le [principales différences à partir de SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- Pour savoir comment utiliser R pour interroger Azure SQL Database Machine Learning Services (version préliminaire), consultez le [guide de démarrage rapide](sql-database-connect-query-r.md).
- Pour commencer avec des scripts R simples, consultez [créer et exécuter des scripts R simples dans la base de données de SQL Azure Machine Learning Services (version préliminaire)](sql-database-quickstart-r-create-script.md).
