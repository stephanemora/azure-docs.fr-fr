---
title: Résolution des erreurs de module
titleSuffix: Azure Machine Learning service
description: Résolution des exceptions des modules dans Azure Machine Learning Studio à l’aide de codes d’erreur
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09a2b616e2bba93be86241c64d37daec7d6dea3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029759"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>Exceptions et codes d’erreur pour les algorithmes et informations de référence sur les modules

Découvrez les messages d’erreur et codes d’exception que vous pouvez rencontrer en utilisant des modules dans Azure Machine Learning Studio. 

Pour résoudre le problème, cherchez l’erreur dans cet article afin d’en connaître les causes courantes. Il y a deux façons d’obtenir le texte complet d’un message d’erreur dans Studio :  
 
- Cliquez sur le lien **Afficher le journal de sortie** dans le volet de droite et faites défiler vers le bas. Le message d’erreur détaillé est affiché dans les deux dernières lignes de la fenêtre.  
  
- Sélectionnez le module qui présente l’erreur et cliquez sur la croix rouge. Seul le texte d’erreur correspondant s’affiche.  
  
Si le texte du message n’est pas utile, envoyez-nous des informations sur le contexte et les ajouts ou les modifications souhaités. Vous pouvez soit envoyer vos commentaires sur l’erreur, soit visiter le [forum Azure Machine Learning STUDIO](https://aka.ms/aml-forum-studio) pour y poser une question.  


## <a name="error-0001"></a>Erreur 0001  
 Une exception se produit si une ou plusieurs colonnes spécifiées du jeu de données sont introuvables.  
  
 Vous recevez cette erreur si une sélection de colonne est effectuée pour un module, mais que la ou les colonnes sélectionnées n’existent pas dans le jeu de données d’entrée. Cette erreur peut se produire si vous avez tapé manuellement le nom d’une colonne ou si le sélecteur de colonnes a fourni une colonne suggérée qui n’existait pas dans votre jeu de données lorsque vous avez lancé l’expérience.  
  
**Résolution :** Réexaminez le module qui génère cette exception et vérifiez que le ou les noms des colonnes sont corrects et que toutes les colonnes référencées existent bien.  
  
|Messages d’exception|  
|------------------------|  
|Une ou plusieurs colonnes spécifiées sont introuvables|  
|La colonne portant le nom ou l’index « {0} » est introuvable|  
|Le colonne portant le nom ou l’index « {0} » est introuvable dans « {1} »|  
 

## <a name="error-0002"></a>Erreur 0002  
 Une exception se produit si un ou plusieurs paramètres n’ont pas pu être analysés ou convertis dans le type requis par le type de méthode cible.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous spécifiez un paramètre comme entrée et que le type de valeur est différent du type attendu et qu’une conversion implicite ne peut pas être effectuée.  
  
**Résolution :** Vérifiez les exigences du module et déterminez quel type de valeur est requis (chaîne, entier, double, etc.)  
  
|Messages d’exception|  
|------------------------|  
|Échec de l’analyse des paramètres|  
|Échec de l’analyse du paramètre « {0} ».|  
|Échec de l’analyse (conversion) du paramètre « {0} » en paramètre « {1} »|  
|Échec de la conversion du paramètre « {0} » de « {1} » en « {2} »|  
|Échec de la conversion de la valeur de paramètre « {0} » « {1} » de « {2} » en « {3} »|  
|Impossible de convertir la valeur « {0} » de la colonne « {1} » de « {2} » à « {3} » en utilisant le format « {4} » fourni|  
  

## <a name="error-0003"></a>Erreur 0003  
 Cette exception se produit si une ou plusieurs entrées sont null ou vide.  
  
 Vous recevez cette erreur dans Azure Machine Learning si des entrées ou des paramètres transmis à un module sont null ou vides.  Cette erreur peut survenir, par exemple, lorsque vous n’avez pas saisi de valeur pour un paramètre. Cela peut également se produire si vous avez sélectionné un jeu de données vide ou un jeu de données pour lequel il manque des valeurs.  
  
**Résolution :**
 
+ Ouvrez le module qui a produit l’exception et vérifiez que toutes les entrées ont été spécifiées. Assurez-vous que toutes les entrées requises sont spécifiées. 
+ Assurez-vous que les données chargées à partir du stockage Azure sont accessibles et que le nom ou la clé du compte n’a pas changé.  
+ Vérifiez que les données d’entrée ne contiennent pas de valeurs nulles et qu’elles contiennent toutes les valeurs requises.
+ Si vous utilisez une requête sur une source de données, vérifiez que les données sont renvoyées dans le format attendu. 
+ Recherchez les fautes de frappe ou d’autres modifications dans la spécification des données.
  
|Messages d’exception|  
|------------------------|  
|Une ou plusieurs des entrées sont null ou vides|  
|L’entrée « {0} » est nulle ou vide|  
  

## <a name="error-0004"></a>Erreur 0004  
 Une exception se produit si le paramètre est inférieur ou égal à une valeur spécifique.  
  
 Cette erreur s’affiche dans Azure Machine Learning si le paramètre du message est inférieur à une valeur limite requise pour que le module puisse traiter les données.  
  
**Résolution :** Réexaminez le module qui lève l’exception et modifiez le paramètre pour qu’il soit d’une valeur supérieure à la valeur spécifiée.  
  
|Messages d’exception|  
|------------------------|  
|Le paramètre doit être supérieur à la valeur limite.|  
|La valeur du paramètre « {0} » doit être supérieure à {1}.|  
|Le paramètre « {0} » contient la valeur « {1} » qui doit être supérieure à {2}.|  
  


## <a name="error-0005"></a>Erreur 0005  
 Une exception se produit si le paramètre est inférieur à une valeur spécifique.  
  
 Cette erreur s’affiche dans Azure Machine Learning si le paramètre du message est inférieur ou égal à une valeur limite requise pour que le module puisse traiter les données.  
  
**Résolution :** Réexaminez le module qui lève l’exception et modifiez le paramètre pour qu’il soit d’une valeur supérieure ou égale à la valeur spécifiée.  
  
|Messages d’exception|  
|------------------------|  
|Le paramètre doit être supérieur ou égal à la valeur limite.|  
|La valeur du paramètre « {0} » doit être supérieure ou égale à {1}.|  
|Le paramètre « {0} » contient la valeur « {1} » qui doit être supérieure ou égale à {2}.|  
  

## <a name="error-0006"></a>Erreur 0006  
 Une exception se produit si le paramètre est supérieur ou égal à la valeur spécifique.  
  
 Cette erreur s’affiche dans Azure Machine Learning si le paramètre du message est supérieur ou égal à une valeur limite requise pour que le module puisse traiter les données.  
  
**Résolution :** Réexaminez le module qui lève l’exception et modifiez le paramètre pour qu’il soit d’une valeur inférieure à la valeur spécifiée.  
  
|Messages d’exception|  
|------------------------|  
|Paramètres différents. L’un des paramètres doit être inférieur à un autre.|  
|La valeur du paramètre « {0} » doit être inférieure à celle du paramètre « {1} ».|  
|Le paramètre « {0} » contient la valeur « {1} » qui doit être inférieure à {2}.|  
  

## <a name="error-0007"></a>Erreur 0007  
 Une exception se produit si le paramètre est supérieur à une valeur spécifique.  
  
 Vous recevrez cette erreur dans Azure Machine Learning si, dans les propriétés du module, vous avez spécifié une valeur supérieure à celle autorisée. Par exemple, vous pouvez spécifier une donnée en dehors de la plage des dates prises en charge, ou vous pouvez indiquer que cinq colonnes doivent être utilisées lorsque seulement trois colonnes sont disponibles. 
 
 Vous pouvez également voir cette erreur si vous spécifiez deux jeux de données qui doivent correspondre d’une manière ou d’une autre. Par exemple, si vous renommez les colonnes et spécifiez les colonnes par index, le nombre de noms que vous fournissez doit correspondre au nombre d’index de colonne. Un autre exemple pourrait consister en une opération mathématique qui utilise deux colonnes qui doivent, l’une et l’autre, posséder le même nombre de lignes. 
  
**Résolution :**
 
 + Ouvrez le module en question et passez en revue les paramètres de propriété numérique.
 + Assurez-vous que les valeurs de paramètre se situent dans la plage de valeurs prise en charge pour cette propriété.
 + Si le module accepte plusieurs entrées, assurez-vous qu’elles sont de même taille.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Vérifiez si le jeu de données ou la source de données a subi des modifications. Il peut arriver qu’une valeur qui fonctionnait avec une version précédente des données échoue après que le nombre de colonnes, les types de données des colonnes ou la taille des données ont été modifiés.  
  
|Messages d’exception|  
|------------------------|  
|Paramètres différents. L’un des paramètres doit être inférieur ou égal à un autre.|  
|La valeur du paramètre « {0} » doit être inférieure ou égale à la valeur du paramètre « {1} ».|  
|Le paramètre « {0} » contient la valeur « {1} » qui doit être inférieure ou égale à {2}.|  
  

## <a name="error-0008"></a>Erreur 0008  
 Une exception se produit si le paramètre n’est pas dans la plage.  
  
 Cette erreur s’affiche dans Azure Machine Learning si le paramètre du message réside hors des limites requises pour que le module puisse traiter les données.  
  
 Par exemple, cette erreur s’affiche si vous essayez d’utiliser [Ajouter des lignes](add-rows.md) pour combiner deux jeux de données qui ont un nombre différent de colonnes.  
  
**Résolution :** Réexaminez le module qui lève l’exception et modifiez le paramètre pour qu’il réside dans la plage spécifiée.  
  
|Messages d’exception|  
|------------------------|  
|La valeur du paramètre n’est pas dans la plage spécifiée.|  
|La valeur du paramètre « {0} » ne figure pas dans la plage.|  
|La valeur du paramètre « {0} » doit figurer dans la plage [{1}, {2}].|  
  

## <a name="error-0009"></a>Erreur 0009  
 Une exception se produit lorsque le nom du compte de stockage Azure ou le nom du conteneur n’est pas spécifié correctement.  
  
Cette erreur se produit dans Azure Machine Learning Studio lorsque vous spécifiez des paramètres pour un compte de stockage Azure, mais que le nom ou le mot de passe ne peut pas être résolu. Les erreurs sur les mots de passe ou les noms de compte peuvent se produire pour de nombreuses raisons :
 
 + Le type du compte est incorrect. Certains nouveaux types de comptes ne sont pas pris en charge pour une utilisation avec Machine Learning Studio. Pour plus de détails, consultez [Importer des données](import-data.md).
 + Le nom de compte que vous avez entré est incorrect
 + Le compte n’existe plus
 + Le mot de passe du compte de stockage est erroné ou a changé.
 + Vous n’avez pas spécifié le nom du conteneur ou le conteneur n’existe pas
 + Vous n’avez pas complètement spécifié le chemin d’accès du fichier (chemin d’accès au blob)
   
**Résolution :**

De tels problèmes surviennent souvent lorsque vous essayez d’entrer manuellement le nom du compte, le mot de passe ou le chemin d’accès du conteneur. Nous vous recommandons d’utiliser le nouvel assistant du module [Importer des données](import-data.md), qui vous aide à rechercher et vérifier les noms.

Vérifiez également si le compte, le conteneur ou le blob a été supprimé. Choisissez un autre utilitaire de stockage Azure pour vérifier que le nom de compte et le mot de passe ont été entrés correctement et que le conteneur existe. 

Certains nouveaux types de comptes ne sont pas pris en charge par Azure Machine Learning. Par exemple, les nouveaux types de stockage « à chaud » et « à froid » ne sont pas utilisables dans le cadre de l’apprentissage automatique. Les comptes de stockage classiques et les comptes de stockage créés pour « Usage général » fonctionnent correctement.

Si le chemin d’accès complet à un blob a été spécifié, vérifiez que le chemin d’accès est spécifié en tant que **conteneur/nomblob** et que le conteneur et le blob existent dans le compte.  
  
 Le chemin d’accès ne doit pas commencer par une barre oblique. Par exemple **/conteneur/blob** est incorrect et doit être entré en tant que **conteneur/blob**.  

  
|Messages d’exception|  
|------------------------|  
|Le nom du compte de stockage Azure ou le nom du conteneur est incorrect.|  
|Le nom du compte de stockage « {0} » ou le nom du conteneur « {1} » est incorrect ; un nom de conteneur était attendu dans le format conteneur/blob.|  
  

## <a name="error-0010"></a>Erreur 0010  
 Une exception se produit si les jeux de données d’entrée possèdent des noms de colonnes qui ne correspondent pas alors qu’ils le devraient correspondre.  
  
 Vous recevrez cette erreur dans Azure Machine Learning si l’index de colonne figurant dans le message possède des noms de colonne différents dans les deux jeux de données d’entrée.  
  
**Résolution :** Utilisez [Modifier les métadonnées](edit-metadata.md) ou modifiez le jeu de données original pour avoir le même nom de colonne pour l’index de colonne spécifié.  
  
|Messages d’exception|  
|------------------------|  
|Les colonnes avec l’index correspondant dans les jeux de données d’entrée possèdent des noms différents.|  
|Les noms de colonne sont différents pour la colonne {0} (de base zéro) des jeux de données d’entrée ({1} et {2} respectivement).|  
  

## <a name="error-0011"></a>Erreur 0011  
 Une exception se produit si l’argument du jeu de colonnes transmis ne s’applique à aucune des colonnes du jeu de données.  
  
 Vous recevez cette erreur dans Azure Machine Learning si la sélection de colonnes spécifiée ne correspond à aucune des colonnes du jeu de données spécifié.  
  
 Vous pouvez également obtenir cette erreur si vous n’avez pas sélectionné de colonne et qu’au moins une colonne est nécessaire pour que le module fonctionne.  
  
**Résolution :** Modifiez la sélection de colonnes dans le module pour qu’elle s’applique aux colonnes du jeu de données.  
  
 Si le module exige que vous sélectionniez une colonne spécifique, telle qu’une colonne d’étiquette, vérifiez que la colonne de droite est sélectionnée.  
  
 Si des colonnes inappropriées sont sélectionnées, supprimez-les et renouvelez l’expérience.  
  
|Messages d’exception|  
|------------------------|  
|Le jeu de colonnes spécifié ne s’applique à aucune des colonnes du jeu de données.|  
|Le jeu de colonnes spécifié « {0} » ne s’applique à aucune des colonnes du jeu de données.|  
  

## <a name="error-0012"></a>Erreur 0012  
 Une exception se produit si l’instance de la classe n’a pas pu être créée avec le jeu d’arguments transmis.  
  
**Résolution :** Cette erreur ne peut pas être corrigée par l’utilisateur et sera déconseillée dans une version ultérieure.  
  
|Messages d’exception|  
|------------------------|  
|Modèle non formé, effectuez l’apprentissage du modèle préalablement.|  
|Modèle non formé ({0}), utilisez un modèle formé.|  
  

## <a name="error-0013"></a>Erreur 0013  
 Une exception se produit si l’apprenant transmis au module est de type non valide.  
  
 Cette erreur se produit lorsqu’un modèle formé est incompatible avec le module de notation connecté. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the experiment is run.  -->
  
**Résolution :**

Déterminez le type d’apprenant produit par le module de formation et déterminez le module de notation qui convient à l’apprenant. 

Si le modèle a été formé à l’aide de l’un des modules de formation spécialisés, connectez le modèle formé uniquement au module de notation spécialisé correspondant. 


|Type de modèle|Module de formation| Module de notation|
|----|----|----|
|n’importe quel classifieur|[Former le modèle](train-model.md) |[Noter le modèle](score-model.md)|
|n’importe quel modèle de régression|[Former le modèle](train-model.md) |[Noter le modèle](score-model.md)|
<!--| modèles de clustering| [Entraîner un modèle de clustering](train-clustering-model.md) ou [Nettoyer le clustering](sweep-clustering.md)| [Attribuer des données à des clusters](assign-data-to-clusters.md)|
| détection d’anomalies - SVM à classe unique | [Former le modèle de détection des anomalies](train-anomaly-detection-model.md) |[Noter le modèle](score-model.md)|
| détection d’anomalies - PCA |[Former le modèle](train-model.md) |[Noter le modèle](score-model.md) </br> Certaines étapes supplémentaires sont nécessaires pour évaluer le modèle. |
| détection d’anomalies - série chronologique|  [Détection des anomalies de série chronologique](time-series-anomaly-detection.md) |Le modèle s’entraîne à partir des données et produit des scores. Le module ne crée pas d’apprenant formé et aucune notation supplémentaire n’est requise. |
| modèle de recommandation| [Former la recommandation Matchbox](train-matchbox-recommender.md) | [Noter la recommandation Matchbox](score-matchbox-recommender.md) |
| classification d’image | [Classification d’image en cascade préformée](pretrained-cascade-image-classification.md) | [Noter le modèle](score-model.md) |
|Modèles Vowpal Wabbit| [Former un modèle Vowpal Wabbit Version 7-4](train-vowpal-wabbit-version-7-4-model.md) | [Noter un modèle Vowpal Wabbit Version 7-4](score-vowpal-wabbit-version-7-4-model.md) |   
|Modèles Vowpal Wabbit| [Former un modèle Vowpal Wabbit Version 7-10](train-vowpal-wabbit-version-7-10-model.md) | [Noter un modèle Vowpal Wabbit Version 7-10](score-vowpal-wabbit-version-7-10-model.md) |
|Modèles Vowpal Wabbit| [Former un modèle Vowpal Wabbit Version 8](score-vowpal-wabbit-version-8-model.md) | [Noter un modèle Vowpal Wabbit Version 8](score-vowpal-wabbit-version-8-model.md) |-->
  
|Messages d’exception|  
|------------------------|  
|L’apprenant de type non valide est transmis avec succès.|  
|L’apprenant « {0} » est de type non valide.|  


## <a name="error-0014"></a>Erreur 0014  
 Une exception se produit si le nombre de valeurs uniques de colonne est supérieur au nombre autorisé.  
  
 Cette erreur se produit lorsqu’une colonne contient trop de valeurs uniques.  Par exemple, cette erreur peut s’afficher si vous spécifiez qu’une colonne doit être gérée comme données catégorielles, mais qu’il existe trop de valeurs uniques dans la colonne pour permettre la fin du traitement. Vous pouvez également voir cette erreur s’il existe une incompatibilité entre le nombre de valeurs uniques dans les deux entrées.   
  
**Résolution :**

Ouvrez le module qui a généré l’erreur et identifiez les colonnes utilisées comme entrées. Pour certains modules, vous pouvez cliquer sur le jeu de données d’entrée et sélectionnez **Visualiser** pour obtenir des statistiques sur des colonnes individuelles, y compris le nombre de valeurs uniques et leur distribution.

Pour les colonnes que vous souhaitez utiliser pour le regroupement ou la catégorisation, prenez les mesures nécessaires pour réduire le nombre de valeurs uniques dans les colonnes. Vous pouvez réduire de différentes manières, selon le type de données de la colonne. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Impossible de trouver une résolution correspondant à votre scénario ? Vous pouvez fournir des commentaires sur cette rubrique qui inclut le nom du module qui a généré l’erreur, et le type de données et la cardinalité de la colonne. Nous allons utiliser les informations pour fournir des opérations plus ciblées pour les scénarios courants de résolution des problèmes.   
  
|Messages d’exception|  
|------------------------|  
|Le nombre de valeurs uniques de colonne est supérieur au nombre autorisé.|  
|Le nombre de valeurs uniques dans la colonne : « {0} » dépasse le nombre de tuples {1}.|  
  

## <a name="error-0015"></a>Erreur 0015  
 Une exception se produit si la connexion de base de données a échoué.  
  
 Vous recevez cette erreur si vous entrez un nom de compte SQL, un mot de passe, un serveur de base de données ou un nom de la base de données incorrect, ou si une connexion avec la base de données ne peut pas être établie en raison de problèmes avec le serveur ou la base de données.  
  
**Résolution :** Vérifiez que le nom du compte, le mot de passe, le serveur de base de données et la base de données ont été entrées correctement et que le compte spécifié dispose du niveau d’autorisations approprié. Vérifiez que la base de données est actuellement accessible.  
  
|Messages d’exception|  
|------------------------|  
|Erreur lors de l’établissement d’une connexion à la base de données.|  
|Erreur lors de l’établissement d’une connexion à la base de données : {0}.|  
  


## <a name="error-0016"></a>Erreur 0016  
 Une exception se produit si des jeux de données d’entrée transmis au module possèdent des types de colonnes incompatibles les uns avec les autres.  
  
 Vous recevez cette erreur dans Azure Machine Learning si les types de colonnes transmis dans deux ou plusieurs jeux de données ne sont pas compatibles entre eux.  
  
**Résolution :** Utilisez [Modifier les métadonnées](edit-metadata.md) ou modifiez le jeu de données d’entrée d’origine<!--, or use [Convert to Dataset](convert-to-dataset.md)--> pour vous assurer que les types des colonnes sont compatibles.  
  
|Messages d’exception|  
|------------------------|  
|Les colonnes avec l’index correspondant dans les jeux de données d’entrée possèdent des types de données incompatibles.|  
|Les colonnes {0} et {1} sont incompatibles.|  
|Les types d’éléments de colonne ne sont pas compatibles pour la colonne {0} (de base zéro) des jeux de données d’entrée ({1} et {2} respectivement).|  
  

## <a name="error-0017"></a>Erreur 0017  
 Une exception se produit si une colonne sélectionnée utilise un type de données que le module actuel ne prend pas en charge.  
  
 Par exemple, cette erreur peut se produire dans Azure Machine Learning si votre sélection de colonnes inclut une colonne dont le type de données ne peut pas être traité par le module, tel qu’une colonne chaîne pour une opération mathématique, ou une colonne de score alors qu’une colonne de fonctionnalité catégorielle est requise.  
  
**Résolution :**
 1. Identifiez la colonne qui pose problème.
 2. Consultez les conditions requises pour le module.
 3. Modifiez la colonne pour qu’elle soit conforme à ces conditions. Vous pourriez avoir besoin d’utiliser plusieurs des modules suivants pour effectuer des modifications selon la colonne et la conversion que vous essayez :
    + Utilisez [Modifier les métadonnées](edit-metadata.md) pour modifier le type de données des colonnes ou convertir des colonnes d’un type à un autre (de fonctionnalité à numérique, de catégoriel à non catégoriel, etc.) selon l’utilisation.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. En dernier recours, vous devrez peut-être modifier le jeu de données d’entrée original.

> [!TIP]
> Impossible de trouver une résolution correspondant à votre scénario ? Vous pouvez fournir des commentaires sur cette rubrique qui inclut le nom du module qui a généré l’erreur, et le type de données et la cardinalité de la colonne. Nous allons utiliser les informations pour fournir des opérations plus ciblées pour les scénarios courants de résolution des problèmes. 
  
|Messages d’exception|  
|------------------------|  
|Impossible de traiter la colonne avec le type actuel. Le type n’est pas pris en charge par le module.|  
|Impossible de traiter la colonne de type {0}. Le type n’est pas pris en charge par le module.|  
|Impossible de traiter la colonne « {1} » de type {0}. Le type n’est pas pris en charge par le module.|  
|Impossible de traiter la colonne « {1} » de type {0}. Le type n’est pas pris en charge par le module. Nom du paramètre : {2}|  
  

## <a name="error-0018"></a>Erreur 0018  
 Une exception se produit si le jeu de données d’entrée n’est pas valide.  
  
**Résolution :** Il existe plusieurs façons de résoudre cette erreur Azure Machine Learning car elle peut se produire dans de nombreux contextes différentes. En général, le message d’erreur indique que les données fournies en entrée à un module ont un nombre de colonnes incorrect ou que le type de données ne correspond pas aux exigences du module. Par exemple :  
  
-   Le module nécessite une colonne d’étiquette, mais aucune colonne n’est marquée en tant qu’étiquette ou vous n’avez pas encore sélectionné de colonne d’étiquette.  
  
-   Ce module exige que les données soient catégorielles, or vos données sont numériques.  
  
<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->
  
-   Le format des données est incorrect.  
  
-   Les données importées contiennent des caractères non valides, des valeurs incorrectes ou des valeurs hors limites.  
-   La colonne est vide ou contient trop de cellules vides.  
  
 Pour déterminer les exigences et la façon dont vos données peuvent être saisies, consultez la rubrique d’aide du module qui utilisera le jeu de données comme entrée.  
  
 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  
  
|Messages d’exception|  
|------------------------|  
|Le jeu de données n’est pas valide.|  
|{0} contient des données non valides.|  
|{0} et {1} doivent être cohérents au niveau de la colonne.|  
  

## <a name="error-0019"></a>Erreur 0019  
 Une exception se produit si la colonne contient des valeurs non triées alors que celles-ci devraient l’être.  
  
 Vous recevez cette erreur dans Azure Machine Learning si les valeurs de colonne spécifiées sont en désordre.  
  
**Résolution :** Triez les valeurs des colonnes en modifiant manuellement le jeu de données d’entrée et réexécutez le module.  
  
|Messages d’exception|  
|------------------------|  
|Les valeurs de la colonne ne sont pas triées.|  
|Les valeurs de la colonne « {0} » ne sont pas triées.|  
|Les valeurs de la colonne « {0} » du jeu de données « {1} » ne sont pas triées.|  
  

## <a name="error-0020"></a>Erreur 0020  
 Une exception se produit si le nombre de colonnes de certains jeux de données transmis au module est trop faible.  
  
 Vous recevez cette erreur dans Azure Machine Learning si le nombre de colonnes sélectionnées est insuffisant pour un module.  
  
**Résolution :** Réexaminez le module et assurez-vous que le sélecteur de colonne possède un nombre correct de colonnes sélectionnées.  
  
|Messages d’exception|  
|------------------------|  
|Le nombre de colonnes du jeu de données d’entrée est inférieur au minimum autorisé.|  
|Le nombre de colonnes du jeu de données d’entrée est inférieur au minimum autorisé de {0} colonne(s).|  
|Le nombre de colonnes du jeu de données d’entrée « {0} » est inférieur au minimum autorisé de {1} colonne(s).|

## <a name="error-0021"></a>Erreur 0021  
 Une exception se produit si le nombre de lignes de certains jeux de données transmis au module est trop faible.  
  
 Cette erreur s’affiche dans Azure Machine Learning lorsque l’opération spécifiée ne peut être exécutée en raison du nombre insuffisant de lignes dans le jeu de données. Par exemple, cette erreur peut s’afficher si le jeu de données d’entrée est vide ou si vous essayez d’effectuer une opération qui nécessite un nombre minimum de lignes pour être valide. De telles opérations peuvent inclure (sans toutefois s’y limiter) le regroupement ou la classification fondée sur des méthodes statistiques, certains types de regroupement et la formation par comptage.  
  
**Résolution :**
 
 + Ouvrez le module qui a renvoyé l’erreur et vérifiez le jeu de données d’entrée et les propriétés du module. 
 + Vérifiez que le jeu de données d’entrée n’est pas vide et qu’il y a suffisamment de lignes de données pour répondre aux exigences décrites dans l’aide du module.  
 + Si vos données sont chargées à partir d’une source externe, assurez-vous que la source de données est disponible et qu’il n’y a pas d’erreur ou de modification dans la définition des données qui entraînerait une diminution du nombre de lignes dans le processus d’importation.
 + Si vous effectuez une opération sur les données en amont du module qui pourrait affecter le type de données ou le nombre de valeurs, comme une opération de nettoyage, de fractionnement ou de jointure, vérifiez les résultats produits par une telle opération pour déterminer le nombre de lignes renvoyées.  



## <a name="error-0022"></a>Erreur 0022  
 Une exception se produit si le nombre de colonnes sélectionnées dans le jeu de données en entrée n’est pas égal au nombre attendu.  
  
 Cette erreur d’Azure Machine Learning peut se produire lorsque le module ou l’opération en aval nécessite un nombre spécifique de colonnes ou d’entrées et que vous avez fourni trop ou pas assez de colonnes ou d’entrées. Par exemple :  
  
-   Vous spécifiez une seule colonne d’étiquette ou une seule colonne clé et plusieurs colonnes sélectionnées par inadvertance.  
  
-   Vous renommez les colonnes, mais vous avez fourni plus ou moins de noms qu’il n’y a de colonnes.  
  
-   Le nombre de colonnes dans la source ou la destination a changé ou ne correspond pas au nombre de colonnes utilisé par le module.  
  
-   Vous avez fourni une liste de valeurs séparées par des virgules pour les entrées, or le nombre de valeurs ne correspond pas ou plusieurs entrées ne sont pas prises en charge.  
  
**Résolution :** Réexaminez le module et assurez-vous que la sélection de colonnes possède un nombre correct de colonnes sélectionnées. Vérifiez les sorties des modules en amont et les exigences des opérations en aval.  
  
 Si vous avez utilisé l’une des options de sélection de colonnes permettant de sélectionner plusieurs colonnes (index de colonne, toutes les fonctions, toutes les fonctions, toutes les fonctions numériques, etc.), validez le nombre exact de colonnes renvoyées par la sélection.  
  
 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->
  
 Vérifiez que le type ou le nombre de colonnes en amont n’a pas changé.  
  
 Si vous utilisez un jeu de données de recommandation pour former un modèle, n’oubliez pas que le générateur de recommandations attend un nombre limité de colonnes, correspondant à des paires utilisateur-élément ou à des classements utilisateur-élément. Supprimez les colonnes supplémentaires avant de former le modèle ou de fractionner les jeux de données de recommandation. Pour plus d’informations, voir [Fractionner les données](split-data.md).  
  
|Messages d’exception|  
|------------------------|  
|Le nombre de colonnes sélectionnées dans le jeu de données en entrée n’est pas égal au nombre attendu.|  
|Le nombre de colonnes sélectionnées dans le jeu de données en entrée n’est pas égal à {0}.|  
|Le modèle de sélection de colonnes « {0} » fournit un nombre de colonnes sélectionnées dans le jeu de données en entrée différent de {1}.|  
|Le modèle de sélection de colonnes « {0} » est censé fournir {1} colonne(s) sélectionnée(s) dans le jeu de données d’entrée mais {2} colonne(s) est (sont) fournie(s).|  



## <a name="error-0023"></a>Erreur 0023  
 Une exception se produit si la colonne cible du jeu de données en entrée n’est pas valide pour le module de formation actuel.  
  
 Cette erreur se produit dans Azure Machine Learning si la colonne cible (telle que sélectionnée dans les paramètres du module) n’est pas du type de données valide, ne contient aucune valeur ou n’est pas catégorielle comme attendu.  
  
**Résolution :** Réexaminez l’entrée du module pour inspecter le contenu de la colonne d’étiquette/cible. Assurez-vous qu’elle n’est pas totalement vide. Si le module exige que la colonne cible soit catégorielle, assurez-vous qu’elle contient plusieurs valeurs distinctes.  
  
|Messages d’exception|  
|------------------------|  
|Le jeu de données d’entrée comporte une colonne cible non prise en charge.|  
|Le jeu de données d’entrée comporte la colonne cible « {0} » qui n’est pas prise en charge.|  
|Le jeu de données d’entrée comporte la colonne cible « {0} » qui n’est pas prise en charge pour l’apprenant de type {1}.|  
 

## <a name="error-0024"></a>Erreur 0024  
Une exception se produit si le jeu de données ne contient pas de colonne d’étiquette.  

 Cette erreur se produit dans Azure Machine Learning lorsque le module nécessite une colonne d’étiquette alors que le jeu de données n’en contient pas. Par exemple, l’évaluation d’un jeu de données notées exige habituellement la présence d’une colonne d’étiquette pour calculer les métriques d’exactitude.  
 
Il peut aussi arriver qu’une colonne d’étiquette soit présente dans le jeu de données, mais pas détectée correctement par Azure Machine Learning.
  
**Résolution :**

+ Ouvrez le module qui a généré l’erreur et déterminez si une colonne d’étiquette est présente. Le nom ou le type de données de la colonne importe peut tant que la colonne contient un seul résultat (ou une variable dépendante) que vous essayez de prédire. En cas de doute sur la colonne contenant l’étiquette, recherchez un nom générique tel que *Classe* ou *Cible*. 
+  Si le jeu de données n’inclut pas de colonne d’étiquette, il est possible que la colonne d’étiquette ait été supprimée explicitement ou accidentellement en amont. Il se peut également que le jeu de données ne soit pas le résultat d’un module de classement en amont.
+ Pour marquer explicitement la colonne en tant que colonne d’étiquette, ajoutez le module [Modifier les métadonnées](edit-metadata.md) et connectez le jeu de données. Sélectionnez uniquement la colonne d’étiquette et sélectionnez **Étiquette** dans la liste déroulante  **Champs**. 
+ Si la colonne inadéquate est choisie comme étiquette, vous pouvez sélectionner **Effacer l’étiquette** dans **Champs** pour corriger les métadonnées sur la colonne. 
  
|Messages d’exception|  
|------------------------|  
|Le jeu de données ne contient pas de colonne d’étiquette.|  
|Le jeu de données « {0} » ne contient pas de colonne d’étiquette.|  
  

## <a name="error-0025"></a>Erreur 0025  
 Une exception se produit si le jeu de données ne contient pas de colonne de score.  
  
 Cette erreur se produit dans Azure Machine Learning si l’entrée du modèle d’évaluation ne contient pas de colonnes de score valides. Par exemple, l’utilisateur tente d’évaluer un jeu de données avant qu’il ne soit évalué à l’aide d’un modèle correctement formé ou que la colonne de score ne soit explicitement supprimée en amont. Cette exception s’applique également si les colonnes de score des deux jeux de données sont incompatibles. Par exemple, vous pouvez essayer de comparer la précision d’un régresseur linéaire à celle d’un classifieur binaire.  
  
**Résolution :** Réexaminez l’entrée du modèle d’évaluation pour déterminer celui-ci contient une ou plusieurs colonnes de score. Si ce n’est pas le cas, c’est que le jeu de données n’a pas été noté ou que les colonnes de score ont été supprimées dans un module en amont.  
  
|Messages d’exception|  
|------------------------|  
|Il n’y a pas de colonne de score dans le jeu de données.|  
|Il n’y a pas de colonne de score dans « {0} ».|  
|Il n’y a pas de colonne de score dans « {0} » produite par un « {1} ». Notez le jeu de données en utilisant le bon type d’apprenant.|  
  

## <a name="error-0026"></a>Erreur 0026  
 Une exception se produit si les colonnes portant le même nom ne sont pas autorisées.  
  
 Cette erreur se produit dans Azure Machine Learning si plusieurs colonnes portent le même nom. Par exemple, cette erreur se produit car le jeu de données n’a pas de ligne d’en-tête et les noms de colonne sont attribués automatiquement : Col0, Col1, etc.  
  
**Résolution :** Si les colonnes portent le même nom, insérez un module [Modifier les métadonnées](edit-metadata.md) entre le jeu de données d’entrée et le module. Utilisez le sélecteur de colonnes dans [Modifier les métadonnées](edit-metadata.md) pour sélectionner les colonnes à renommer, et tapez les nouveaux noms de colonnes dans la zone de texte **Nouveaux noms de colonnes**.  
  
|Messages d’exception|  
|------------------------|  
|Les noms de colonnes Equal (Égal à) sont spécifiés dans les arguments. Les noms de colonnes Equal (Égal à) ne sont pas autorisés par le module.|  
|Les noms de colonnes Equal (Égal à) ne sont pas autorisés dans les arguments « {0} » et « {1} ». Spécifiez des noms différents.|  
  

## <a name="error-0027"></a>Erreur 0027  
 Une exception se produit dans le cas où deux objets doivent être de tailles identiques mais ne le sont pas.  
  
 C’est une erreur fréquente d’Azure Machine Learning pouvant être causée par de nombreuses conditions.  
  
**Résolution :** Il n’existe pas une solution en particulier. Toutefois, vous pouvez vérifier les conditions telles que les suivantes :  
  
-   Si vous renommez des colonnes, assurez-vous que chaque liste (les colonnes d’entrée et la liste des nouveaux noms) comporte le même nombre d’éléments.  
  
-   Si vous joignez ou concaténez deux jeux de données, assurez-vous qu’ils s’appuient sur le même schéma.  
  
-   Si vous joignez deux jeux de données qui ont plusieurs colonnes, assurez-vous que les colonnes clés sont définies avec le même type de données et sélectionnez l’option **Allow duplicates and preserve column order in selection** (Autoriser les doublons et préserver l’ordre des colonnes dans la sélection).  
  
|Messages d’exception|  
|------------------------|  
|La taille des objets transmis est incohérente.|  
|La taille de « {0} » est incompatible avec la taille de « {1} ».|  
  

## <a name="error-0028"></a>Erreur 0028  
 Une exception se produit dans le cas où le jeu de colonnes contient des noms de colonnes en double, ce qui n’est pas autorisé.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque plusieurs noms de colonnes portent le même nom.  
  
**Résolution :** Si des colonnes portent le même nom, ajoutez une instance de [Modifier les métadonnées](edit-metadata.md) entre le jeu de données d’entrée et le module qui provoque l’erreur. Utilisez le Sélecteur de colonnes dans [Modifier les métadonnées](edit-metadata.md) pour sélectionner les colonnes à renommer, et tapez les nouveaux noms de colonnes dans la zone de texte **Nouveaux noms de colonnes**. Si vous renommez plusieurs colonnes, assurez-vous que les valeurs que vous saisissez dans **New column names** (Nouveaux noms de colonne) sont uniques.  
  
|Messages d’exception|  
|------------------------|  
|Le jeu de colonnes contient des noms de colonne en double.|  
|Le nom « {0} » est dupliqué.|  
|Le nom « {0} » est dupliqué dans « {1} ».|  
  

## <a name="error-0029"></a>Erreur 0029  
 Une exception se produit dans le cas où une URI non valide est transmise.  
  
 Cette erreur se produit dans Azure Machine Learning dans le cas où un URI non valide est transmis.  Vous recevez cette erreur si l’une des conditions suivantes est vraie.  
  
-   Une erreur est identifiée dans l’URI public ou SAS fourni pour la lecture ou l’écriture de données dans Stockage Blob Azure.  
  
-   La fenêtre de temps a expiré pour la SAP.  
  
-   L’URL web via la source HTTP représente un fichier ou un URI de bouclage.  
  
-   Le format de l’URL web via HTTP est incorrect.  
  
-   L’URL ne peut pas être résolue par la source distante.  
  
**Résolution :** Réexaminez le module et vérifiez le format de l’URI. Si la source de données est une URL web via HTTP, vérifiez que la source prévue n’est pas un fichier ou une URI de bouclage (localhost).  
  
|Messages d’exception|  
|------------------------|  
|Un URI non valide est transmis.|  
  

## <a name="error-0030"></a>Erreur 0030  
 Une exception se produit dans le cas où il n’est pas possible de télécharger un fichier.  
  
 Cette exception se produit dans Azure Machine Learning lorsqu’il n’est pas possible de télécharger un fichier. Vous recevez cette exception lorsqu’une tentative de lecture effectuée à partir d’une source HTTP a échoué au bout de trois (3) tentatives.  
  
**Résolution :** Vérifiez que l’URI de la source HTTP est correct et que le site est actuellement accessible via Internet.  
  
|Messages d’exception|  
|------------------------|  
|Impossible de télécharger un fichier.|  
|Erreur lors du téléchargement du fichier : {0}.|  
  

## <a name="error-0031"></a>Erreur 0031  
 Une exception se produit si le nombre de colonnes du jeu de colonnes est inférieur au nombre requis.  
  
 Cette erreur se produit dans Azure Machine Learning si le nombre de colonnes sélectionnées est inférieur au nombre requis.  Vous recevez cette erreur si le nombre minimum de colonnes requis n’est pas sélectionné.  
  
**Résolution :** Ajoutez des colonnes supplémentaires à la sélection de colonnes en utilisant le **Sélecteur de colonnes**.  
  
|Messages d’exception|  
|------------------------|  
|Le nombre de colonnes du jeu de colonnes est inférieur au nombre requis.|  
|{0} colonne(s) doit (doivent) être spécifiée(s). Le nombre effectif de colonnes est de {1}.|  

## <a name="error-0032"></a>Erreur 0032  
 Une exception se produit si l’argument n’est pas numérique.  
  
 Vous recevez cette erreur dans Azure Machine Learning si l’argument est une valeur double ou NaN.  
  
**Résolution :** Modifiez l’argument spécifié pour utiliser une valeur valide.  
  
|Messages d’exception|  
|------------------------|  
|L’argument n’est pas numérique.|  
|« {0} » n’est pas un nombre.|  
  

## <a name="error-0033"></a>Erreur 0033  
 Une exception se produit si l’argument est une valeur infinie (Infinity).  
  
 Cette erreur se produit dans Azure Machine Learning si l’argument est infini. Vous recevez ce message d’erreur si l’argument est `double.NegativeInfinity` ou `double.PositiveInfinity`.  
  
**Résolution :** Corrigez la valeur de l’argument spécifié.  
  
|Messages d’exception|  
|------------------------|  
|L’argument doit être fini.|  
|« {0} » n’est pas fini.|  
  

## <a name="error-0034"></a>Erreur 0034  
 Une exception se produit s’il existe plusieurs classements pour une paire utilisateur-élément donnée.  
  
 Cette erreur se produit dans Azure Machine Learning au niveau de la recommandation si une paire utilisateur-élément possède plusieurs valeurs de classement.  
  
**Résolution :** Assurez-vous que la paire utilisateur-élément possède une seule valeur de classement.  
  
|Messages d’exception|  
|------------------------|  
|Il existe plusieurs classements pour la ou les valeurs du jeu de données.|  
|Plusieurs classements pour l’utilisateur {0} et l’élément {1} dans le tableau des données de prévision des classements.|  
  

## <a name="error-0035"></a>Erreur 0035  
 Une exception se produit si aucune fonctionnalité n’est fournie pour un utilisateur ou un élément donné.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous essayez d’utiliser un modèle de recommandation pour le scoring mais qu’un vecteur de fonctionnalité est introuvable.  
  
**Résolution :**

Selon la recommandation matchbox, certaines exigences doivent être satisfaites lors de l’utilisation de fonctionnalités d’élément ou de fonctionnalités d’utilisateur.  Cette erreur indique qu’il manque un vecteur de fonctionnalité pour un utilisateur ou un élément que vous avez fourni en entrée.  Vous devez vous assurer qu’un vecteur de fonctionnalité est disponible dans les données pour chaque utilisateur ou chaque élément.  
  
 Par exemple, si vous avez formé un modèle de recommandation à l’aide de fonctionnalités telles que l’âge, l’emplacement ou le revenu de l’utilisateur, mais que vous souhaitez à présent créer des scores pour les nouveaux utilisateurs qui n’ont pas été vus pendant la formation, vous devez fournir un ensemble équivalent de fonctionnalités (à savoir l’âge, l’emplacement et les valeurs de revenu) pour les nouveaux utilisateurs afin de définir des prévisions appropriées pour ces derniers. 
 
 Si vous ne disposez d’aucune fonctionnalité pour ces utilisateurs, vous pouvez implémenter vos compétences techniques pour développer des fonctionnalités appropriées.  Par exemple, si vous n’avez pas de valeurs individuelles d’âge ou de revenu pour les utilisateurs, vous pouvez générer des valeurs approximatives applicables à un groupe d’utilisateurs. 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > La résolution ne s’applique pas à votre cas ? Vous êtes invité à envoyer vos commentaires sur cet élément et à fournir des informations sur le scénario, notamment le module et le nombre de lignes dans la colonne. À l’avenir, nous utiliserons ces informations pour fournir des opérations plus ciblées de résolution des problèmes.
   
|Messages d’exception|  
|------------------------|  
|Aucune fonctionnalité n’a été fournie pour un utilisateur ou un élément requis.|  
|Les fonctionnalités pour {0} ne sont pas fournies alors qu’elles sont requises.|  
  

## <a name="error-0036"></a>Erreur 0036  
 Une exception se produit si plusieurs vecteurs de fonctionnalité ont été fournis pour un utilisateur ou un élément donné.  
  
 Cette erreur se produit dans Azure Machine Learning si un vecteur de fonctionnalité est défini plusieurs fois.  
  
**Résolution :** Assurez-vous que le vecteur de fonctionnalité n’est pas défini plusieurs fois.  
  
|Messages d’exception|  
|------------------------|  
|Définition de fonctionnalité en double pour un utilisateur ou un élément.|  
|Définition de fonctionnalité en double pour {0}.|  
  

## <a name="error-0037"></a>Erreur 0037  
 Une exception se produit si plusieurs colonnes d’étiquette sont spécifiées alors qu’une seule colonne d’étiquette est autorisée.  
  
 Cette erreur se produit dans Azure Machine Learning si vous avez sélectionné plusieurs colonnes en tant que nouvelle colonne d’étiquette. La plupart des algorithmes d’apprentissage supervisés exigent qu’une seule colonne soit marquée comme cible ou étiquette.  
  
**Résolution :** Assurez-vous de sélectionner une seule colonne comme nouvelle colonne d’étiquette.  
  
|Messages d’exception|  
|------------------------|  
|Plusieurs colonnes d’étiquette sont spécifiées.|  
  

## <a name="error-0038"></a>Erreur 0038  
 Une exception se produit si le nombre d’éléments attendus doit être une valeur exacte mais ne l’est pas.  
  
 Cette erreur se produit dans Azure Machine Learning si le nombre d’éléments attendus doit être une valeur exacte mais ne l’est pas.  Vous recevez ce message d’erreur si le nombre d’éléments n’est pas égal à la valeur attendue valide.  
  
**Résolution :** Modifiez l’entrée pour obtenir un nombre correct d’éléments.  
  
|Messages d’exception|  
|------------------------|  
|Le nombre d’éléments n’est pas valide.|  
|Le nombre d’éléments de « {0} » n’est pas valide.|  
|Le nombre d’éléments de « {0} » n’est pas égal au nombre valide d’éléments ({1}).|  
  

## <a name="error-0039"></a>Erreur 0039  
 Une exception se produit si une opération a échoué.  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’une opération interne ne peut pas s’achever.  
  
**Résolution :** Cette erreur est due à de nombreuses conditions différentes : c’est pourquoi il n’existe pas une seule solution au problème.  
 Le tableau suivant contient des messages génériques pour cette erreur, qui sont suivis d’une description spécifique de la condition. 
 
 Si aucun détail n’est disponible, [envoyez des commentaires](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) dans lesquelles vous fournirez des informations sur les modules qui ont généré l’erreur et sur les conditions associées.
  
|Messages d’exception|  
|------------------------|  
|L’opération a échoué.|  
|Erreur lors de l’exécution de l’opération : {0}.|  
  

## <a name="error-0040"></a>Erreur 0040  
 Une exception se produit lors de l’appel d’un module dont l’utilisation est déconseillée.  
  
 Cette erreur se produit dans Azure Machine Learning lors de l’appel d’un module dont l’utilisation est déconseillée.  
  
**Résolution :** Remplacez le module déconseillé par un module supporté. Consultez le journal de sortie du module pour déterminer quel module utiliser à la place.  
  
|Messages d’exception|  
|------------------------|  
|Accès à un module déconseillé.|  
|Le module « {0} » est déconseillé. Utilisez le module « {1} » à la place.|  
 

## <a name="error-0041"></a>Erreur 0041  
 Une exception se produit lors de l’appel d’un module dont l’utilisation est déconseillée.  
  
 Cette erreur se produit dans Azure Machine Learning lors de l’appel d’un module dont l’utilisation est déconseillée.  
  
**Résolution :** Remplacez le module déconseillé par un jeu de modules pris en charge. Ces informations doivent être visibles dans le journal de sortie du module.  
  
|Messages d’exception|  
|------------------------|  
|Accès à un module déconseillé.|  
|Le module « {0} » est déconseillé. Utilisez les modules « {1} » pour les fonctionnalités demandées.|  
 

## <a name="error-0042"></a>Erreur 0042  
 Une exception se produit lorsqu’il n’est pas possible de convertir une colonne en un autre type.  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’il n’est pas possible de convertir une colonne vers le type spécifié.  Vous recevez cette erreur si un module nécessite un type de données particulier, comme la date, l’heure, du texte, un nombre à virgule flottante ou un nombre entier, mais qu’il n’est pas possible de convertir une colonne existante vers le type requis.  
 
Par exemple, vous pouvez sélectionner une colonne et essayer de la convertir en un type de données numérique pour une opération mathématique, et obtenir cette erreur si la colonne contient des données non valides. 

Cette erreur peut également se produire si vous essayez d’utiliser une colonne contenant des nombres à virgule flottante ou plusieurs valeurs uniques comme colonne catégorielle. 
  
**Résolution :**

+ Ouvrez la page d’aide du module qui a généré l’erreur et vérifiez le type de données requis.
+ Examinez les types de données des colonnes du jeu de données d’entrée.
+ Inspectez les données provenant de sources de données dites sans schéma.
+ Vérifiez le jeu de données pour détecter les valeurs manquantes ou les caractères spéciaux qui pourraient bloquer la conversion vers le type de données souhaité. 
    + Les types de données numériques doivent être cohérents : par exemple, vérifiez la présence de nombres à virgule flottante dans une colonne d’entiers.
    + Recherchez les chaînes de texte ou les valeurs NA dans une colonne numérique. 
    + Les valeurs booléennes peuvent être converties en une représentation appropriée en fonction du type de données requis.
    + Examinez les colonnes de texte à la recherche de caractères non unicode, de caractères de tabulation ou de caractères de contrôle.
    + Les données DateHeure doivent être cohérentes pour éviter les erreurs de modélisation, mais le nettoyage peut être complexe en raison des formats nombreux. Envisagez d’utiliser <!--the [Execute R Script](execute-r-script.md) or -->[Exécutez les modules Python Script](execute-python-script.md) pour effectuer le nettoyage.  
+ Si nécessaire, modifiez les valeurs du jeu de données d’entrée pour que la colonne puisse être convertie avec succès. La modification peut comprendre des opérations de regroupement, de troncation ou d’arrondi, l’élimination des valeurs aberrantes ou l’imputation des valeurs manquantes. Consultez les articles suivants pour quelques scénarios courants de transformation de données en apprentissage automatique :
    + [Nettoyer les données manquantes](clean-missing-data.md)
    + [Normaliser les données](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->
 
> [!TIP]
> La résolution n’est pas claire ou ne s’applique pas à votre cas ? Vous êtes invités à envoyer vos commentaires sur cet élément et à fournir des informations sur le scénario, notamment le module et le type de données de la colonne. À l’avenir, nous utiliserons ces informations pour fournir des opérations plus ciblées de résolution des problèmes.  
  
|Messages d’exception|  
|------------------------|  
|La conversion n’est pas autorisée.|  
|Impossible de convertir une colonne de type {0} en colonne de type {1}.|  
|Impossible de convertir la colonne « {2} » de type {0} en colonne de type {1}.|  
|Impossible de convertir la colonne « {2} » de type {0} en colonne « {3} » de type {1}.|  
  

## <a name="error-0043"></a>Erreur 0043  
 Une exception se produit lorsque le type d’élément n’implémente pas explicitement Equals.  
  
 Cette erreur dans Azure Machine Learning n’est pas utilisée et deviendra obsolète.  
  
**Résolution :** Aucune.  
  
|Messages d’exception|  
|------------------------|  
|Aucune méthode Equals explicite accessible n’a été trouvée.|  
|Impossible de comparer les valeurs de la colonne \\» {0}\\ » de type {1}. Aucune méthode Equals explicite accessible n’a été trouvée.|  


## <a name="error-0044"></a>Erreur 0044  
 Une exception se produit lorsqu’il n’est pas possible de dériver un type d’élément de colonne à partir de valeurs existantes.  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’il n’est pas possible de déduire le type d’une ou de plusieurs colonnes dans un jeu de données. Cela se produit généralement lors de la concaténation de deux ou plusieurs jeux de données avec différents types d’éléments. Azure Machine Learning génère cette erreur s’il n’est pas en mesure de déterminer un type commun capable de représenter toutes les valeurs dans une ou plusieurs colonnes sans perte d’information.  
  
**Résolution :** Assurez-vous que toutes les valeurs d’une colonne spécifique dans les deux jeux de données combinés sont du même type (numérique, booléen, catégoriel, chaîne, date, etc.) ou peuvent être forcés sur le même type.  
  
|Messages d’exception|  
|------------------------|  
|Impossible de dériver le type d’élément de la colonne.|  
|Impossible de dériver le type d’élément pour la colonne « {0} »--tous les éléments sont des références null.|  
|Impossible de dériver le type d’élément pour la colonne « {0} » du jeu de données « {1} » --tous les éléments sont des références null.|  
  

## <a name="error-0045"></a>Erreur 0045  
 Une exception se produit lorsqu’il n’est pas possible de créer une colonne en raison de types d’éléments mixtes dans la source.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque les types d’éléments de deux jeux de données combinés sont différents.  
  
**Résolution :** Assurez-vous que toutes les valeurs d’une colonne spécifique dans les deux jeux de données combinés sont du même type (numérique, booléen, catégoriel, chaîne, date, etc.).  
  
|Messages d’exception|  
|------------------------|  
|Impossible de créer la colonne avec des types d’éléments mixtes.|  
|Impossible de créer une colonne avec l’ID « {0} » comportant les types d’élément combinés suivants :\n\tType de données[{1}, {0}] : {2}\n\tType de données [{3}, {0}] : {4}.|  
  

## <a name="error-0046"></a>Erreur 0046  
 Une exception se produit lorsqu’il n’est pas possible de créer un répertoire sur le chemin spécifié.  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’il n’est pas possible de créer un répertoire dans le chemin d’accès spécifié. Vous recevez cette erreur si une partie du chemin d’accès au répertoire de sortie d’une requête du répertoire Hive est incorrecte ou inaccessible.  
  
**Résolution :** Réexaminez le module et vérifiez que le chemin d’accès du répertoire est correctement formaté et qu’il est accessible avec les informations d’identification actuelles.  
  
|Messages d’exception|  
|------------------------|  
|Spécifiez un répertoire de sortie valide.|  
|Répertoire : {0} ne peut pas être créé. Spécifiez un chemin d’accès valide.|  
  

## <a name="error-0047"></a>Erreur 0047  
 Une exception se produit si le nombre de colonnes de fonctionnalités de certains jeux de données transmis au module est trop faible.  
  
 Cette erreur se produit dans Azure Machine Learning si le jeu de données d’entrée pour l’apprentissage ne contient pas le nombre minimal de colonnes requis par l’algorithme. En règle générale, soit le jeu de données est vide, soit il ne contient que des colonnes de formation.  
  
**Résolution :** Réexaminez le jeu de données d’entrée pour vous qu’il contient une ou plusieurs colonnes en plus de la colonne d’étiquette.  
  
|Messages d’exception|  
|------------------------|  
|Le nombre de colonnes de fonctionnalité du jeu de données d’entrée est inférieur au minimum autorisé.|  
|Le nombre de colonnes de fonctionnalités du jeu de données d’entrée est inférieur au minimum autorisé de {0} colonne(s).|  
|Le nombre de colonnes de fonctionnalités du jeu de données d’entrée « {0} » est inférieur au minimum autorisé de {1} colonne(s).|  
  

## <a name="error-0048"></a>Erreur 0048  
 Une exception se produit dans le cas où il n’est pas possible d’ouvrir un fichier.  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’il n’est pas possible d’ouvrir un fichier en vue de la lecture ou de l’écriture de données. Vous pourriez recevoir cette erreur pour les raisons suivantes :  
  
-   Le conteneur ou le fichier (blob) n’existe pas  
  
-   Le niveau d’accès du fichier ou du conteneur ne vous permet pas d’accéder au fichier  
  
-   Le fichier est trop volumineux pour être lu ou bien le format est incorrect  
  
**Résolution :** Réexaminez le module et le fichier que vous essayez de lire.  
  
 Vérifiez que les noms du conteneur et du fichier sont corrects.  
  
 Utilisez le Portail Azure classique ou un outil de stockage Azure pour vérifier que vous avez l’autorisation d’accéder au fichier.  
  
  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->
  
|Messages d’exception|  
|------------------------|  
|Impossible d’ouvrir un fichier.|  
|Erreur lors de l’ouverture du fichier : {0}.|  


## <a name="error-0049"></a>Erreur 0049  
 Une exception se produit dans le cas où il n’est pas possible d’analyser un fichier.  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’il n’est pas possible d’analyser un fichier. Vous recevez cette erreur si le format de fichier sélectionné dans le module [Importer des données](import-data.md) ne correspond pas au format réel du fichier, ou si le fichier contient un caractère non reconnaissable.  
  
**Résolution :** Réexaminez le module et corrigez la sélection du format de fichier s’il ne correspond pas au format du fichier. Si possible, inspectez le fichier pour confirmer qu’il ne contient aucun caractère non autorisé.  
  
|Messages d’exception|  
|------------------------|  
|Impossible d’analyser un fichier.|  
|Erreur lors de l’analyse du fichier : {0}.|  
  

## <a name="error-0050"></a>Erreur 0050  
 Une exception se produit dans le cas où les fichiers d’entrée et de sortie sont identiques.  
  
**Résolution :** Cette erreur dans Azure Machine Learning n’est pas utilisée et deviendra obsolète.  
  
|Messages d’exception|  
|------------------------|  
|Les fichiers spécifiés pour l’entrée et la sortie doivent être différents.|


## <a name="error-0051"></a>Erreur 0051  
 Une exception se produit dans le cas où plusieurs fichiers de sortie sont identiques.  
  
**Résolution :** Cette erreur dans Azure Machine Learning n’est pas utilisée et deviendra obsolète.  
  
|Messages d’exception|  
|------------------------|  
|Les fichiers spécifiés pour les sorties ne peuvent pas être identiques.|


## <a name="error-0052"></a>Erreur 0052  
 Une exception se produit si la clé de compte de stockage Azure n’est pas spécifiée correctement.  
  
 Cette erreur se produit dans Azure Machine Learning si la clé utilisée pour accéder au compte de stockage Azure est incorrecte. Par exemple, vous pouvez voir cette erreur si la clé de stockage Azure a été tronquée lors de l’opération de copier-coller ou si la clé incorrecte a été utilisée.  
  
 Pour plus d’informations sur la façon d’obtenir la clé d’un compte de stockage Azure, consultez la section consacrée à [l’affichage, la copie et la régénération des clés d’accès au stockage](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  
  
**Résolution :** Réexaminez le module et vérifiez que la clé de stockage Azure est correcte pour le compte ; copiez à nouveau la clé du Portail Azure classique si nécessaire.  
  
|Messages d’exception|  
|------------------------|  
|La clé de compte de stockage Azure est incorrecte.|  
  

## <a name="error-0053"></a>Erreur 0053  
 Une exception se produit dans le cas où il n’y a pas de fonctionnalités utilisateur ou d’éléments pour les recommandations matchbox.  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’un vecteur de fonctionnalité est introuvable.  
  
**Résolution :** Assurez-vous qu’un vecteur de fonctionnalité est présent dans le jeu de données d’entrée.  
  
|Messages d’exception|  
|------------------------|  
|Les fonctionnalités et/ou éléments utilisateur sont requis mais ne sont pas fournis.|  

## <a name="error-0054"></a>Erreur 0054  
 Une exception se produit s’il y a trop peu de valeurs distinctes dans la colonne pour terminer l’opération.  
  
**Résolution :** Cette erreur dans Azure Machine Learning n’est pas utilisée et deviendra obsolète.  
  
|Messages d’exception|  
|------------------------|  
|Les données possèdent trop peu de valeurs distinctes dans la colonne spécifiée pour terminer l’opération.|  
|Les données possèdent trop peu de valeurs distinctes dans la colonne spécifiée pour terminer l’opération. Le minimum requis est de {0} éléments.|  
|Les données possèdent trop peu de valeurs distinctes dans la colonne « {1} » spécifiée pour terminer l’opération. Le minimum requis est de {0} éléments.|  
  

## <a name="error-0055"></a>Erreur 0055  
 Une exception se produit lors de l’appel d’un module dont l’utilisation est déconseillée.  Cette erreur d’Azure Machine Learning apparaît si vous essayez d’appeler un module dont l’utilisation est déconseillée.
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Accès à un module déconseillé.|  
|Le module « {0} » est déconseillé.|  

## <a name="error-0056"></a>Erreur 0056  
 Une exception se produit si les colonnes que vous avez sélectionnées pour une opération ne respectent pas les exigences.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous choisissez des colonnes pour une opération qui nécessite que la colonne soit d’un type de données particulier. 
 
 Cette erreur peut également se produire si la colonne est du type de données approprié, mais que le module que vous utilisez exige que la colonne soit également marquée comme une fonctionnalité, une étiquette ou une colonne catégorielle.  
  
  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->
  
**Résolution :**
  
1.  Passez en revue le type de données des colonnes actuellement sélectionnées. 

2. Déterminez si les colonnes sélectionnées sont des colonnes catégorielles, d’étiquette ou de fonctionnalités.  
  
3.  Examinez la rubrique d’aide du module dans lequel vous avez effectué la sélection de colonnes afin de déterminer s’il existe des exigences spécifiques pour le type de données ou l’utilisation des colonnes.  
  
3.  Utilisez [Modifier les métadonnées](edit-metadata.md) pour modifier le type de colonne pour la durée de cette opération. Assurez-vous rétablir la valeur d’origine du type de colonne, en utilisant une autre instance de [Modifier les métadonnées](edit-metadata.md), si vous en avez besoin pour les opérations en aval.  
  
|Messages d’exception|  
|------------------------|  
|Une ou plusieurs colonnes sélectionnées n’appartenaient pas à une catégorie autorisée.|  
|La colonne portant le nom « {0} » n’appartient pas à une catégorie autorisée.|  
  

## <a name="error-0057"></a>Erreur 0057  
 Une exception se produit lorsque vous tentez de créer un fichier ou un blob qui existe déjà.  
  
 Cette exception se produit lorsque vous utilisez le module [Exporter des données](export-data.md) ou un autre module pour enregistrer les résultats d’une expérience Azure Machine Learning dans le stockage blob Azure, mais que vous essayez de créer un fichier ou un blob qui existe déjà.   
  
**Résolution :**
 
 Vous ne recevez cette erreur que si vous avez préalablement défini la propriété **Azure blob storage write mode** (Mode écriture du stockage Blob Azure) sur **Error**. De par sa conception, ce module génère une erreur si vous essayez d’écrire un jeu de données dans un blob qui existe déjà.
 
 - Ouvrez les propriétés du module et définissez la propriété **Azure blob storage write mode** sur **Overwrite** (Remplacer).
 - Vous pouvez également saisir le nom d’un fichier ou d’un blob de destination différent et vous assurer de spécifier un blob qui n’existe pas déjà.  
  
|Messages d’exception|  
|------------------------|  
|Le fichier ou le blob existe déjà.|  
|Le fichier ou le blob « {0} » existe déjà.|  
  

## <a name="error-0058"></a>Erreur 0058  
 Cette erreur se produit dans Azure Machine Learning si le jeu de données ne contient pas la colonne d’étiquette attendue.  
  
 Cette exception peut également se produire lorsque la colonne d’étiquette fournie ne correspond pas aux données ou au type de données attendu par l’apprenant ou qu’elle contient des valeurs erronées. Par exemple, cette exception est produite lors de l’utilisation d’une colonne d’étiquette à valeur réelle lors de la formation d’un classifieur binaire.  
  
**Résolution :** La résolution dépend de l’apprenant ou du formateur que vous utilisez et des types de données des colonnes de votre jeu de données. Tout d’abord, vérifiez les exigences de l’algorithme d’apprentissage automatique ou du module de formation.  
  
 Examinez le jeu de données d’entrée. Vérifiez que la colonne que vous vous attendez à traiter telle que l’étiquette possède le type de données approprié pour le modèle que vous créez.  
  
 Vérifiez les entrées pour les valeurs manquantes et supprimez-les ou remplacez-les si nécessaire.  
  
 Si nécessaire, ajoutez le module [Modifier les métadonnées](edit-metadata.md) et assurez-vous que la colonne d’étiquette est marquée en tant qu’étiquette.  
  
|Messages d’exception|  
|------------------------|  
|La colonne d’étiquette n’est pas spécifiée comme prévu|  
|La colonne d’étiquette n’est pas spécifiée comme prévu dans « {0} ».|  
|La colonne « {0} » n’est pas spécifiée comme prévu dans « {1} ».|  
  

## <a name="error-0059"></a>Erreur 0059  
 Une exception se produit si un index de colonne spécifié dans un sélecteur de colonnes ne peut pas être analysé.  
  
 Cette erreur se produit dans Azure Machine Learning si un index de colonne spécifié lors de l’utilisation du sélecteur de colonnes ne peut pas être analysé.  Vous recevez cette erreur lorsque l’index de colonne est dans un format non valide qui ne peut pas être analysé.  
  
**Résolution :** Modifiez l’index de colonne pour utiliser une valeur d’index valide.  
  
|Messages d’exception|  
|------------------------|  
|Un ou plusieurs index de colonne ou plages d’index spécifiés n’ont pas pu être analysés.|  
|L’index de colonne ou la plage « {0} » n’a pas pu être analysé.|  
  

## <a name="error-0060"></a>Erreur 0060  
 Une exception se produit lorsqu’une plage de colonnes hors limite est spécifiée dans un sélecteur de colonnes.  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’une plage de colonnes hors limites est spécifiée dans le sélecteur de colonnes. Vous recevez cette erreur si la plage de colonnes figurant dans le sélecteur de colonnes ne correspond pas aux colonnes du jeu de données.  
  
**Résolution :** Modifiez la plage de colonnes figurant dans le sélecteur de colonnes de sorte qu’elle corresponde aux colonnes du jeu de données.  
  
|Messages d’exception|  
|------------------------|  
|Plage d’index de colonnes non valide ou hors limites spécifiée.|  
|La plage de colonnes « {0} » est non valide ou hors limites.|  
  

## <a name="error-0061"></a>Erreur 0061  
 Une exception se produit lorsque vous tentez d’ajouter une ligne à un DataTable dont le nombre de colonnes est différent de celui de la table.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous essayez d’ajouter une ligne à un jeu de données dont le nombre de colonnes est différent de celui du jeu de données.  Vous recevez cette erreur si la ligne qui est ajoutée au jeu de données possède un nombre de colonnes différent de celui du jeu de données d’entrée.  La ligne ne peut pas être ajoutée au jeu de données si le nombre de colonnes est différent.  
  
**Résolution :** Modifiez le jeu de données d’entrée pour qu’il possède le même nombre de colonnes que la ligne ajoutée, ou modifiez la ligne ajoutée pour qu’elle possède le même nombre de colonnes que le jeu de données.  
  
|Messages d’exception|  
|------------------------|  
|Toutes les tables doivent avoir le même nombre de colonnes.|  
  

## <a name="error-0062"></a>Erreur 0062  
 Une exception se produit lorsque l’on tente de comparer deux modèles avec des types d’apprenants différents.  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’il n’est pas possible de comparer les métriques d’évaluation de deux jeux de données notés différents. Dans ce cas, il n’est pas possible de comparer l’efficacité des modèles utilisés pour produire les deux jeux de données notés.  
  
**Résolution :** Vérifiez que les résultats notés sont produits par le même type de modèle d’apprentissage automatique (classification binaire, régression, classification multi-classe, recommandation, clustering, détection des anomalies, etc.) Tous les modèles que vous comparez doivent avoir le même type d’apprenant.  
  
|Messages d’exception|  
|------------------------|  
|Tous les modèles doivent avoir le même type d’apprenant.|  
  

 <!--## Error 0063  
 This exception is raised when R script evaluation fails with an error.  
  
 This error occurs when you have provided an R script in one of the [R language modules](r-language-modules.md) in Azure Machine Learning, and the R code contains internal syntax errors. The exception can also occur if you provide the wrong inputs to the R script. 
 
 The error can also occur if the script is too large to execute in the workspace. The maximum script size for the **Execute R Script** module is 1,000 lines or 32 KB of work space, whichever is lesser.
  
**Resolution:**

1. In Azure Machine Learning Studio, right-click the module that has the error, and select **View Log**.
2. Examine the standard error log of the module, which contains the stack trace.
    + Lines beginning with [ModuleOutput] indicate output from R.
    + Messages from R marked as **warnings** typically do not cause the experiment to fail.
3. Resolve script issues.  
    + Check for R syntax errors. Check for variables that are defined but never populated.
    + Review the input data and the script to determine if either the data or variables in the script use characters not supported by Azure Machine Learning.
    + Check whether all package dependencies are installed.
    + Check whether your code loads required libraries that are not loaded by default.
    + Check whether the required packages are the correct version.
    + Make sure that any dataset that you want to output is converted to a data frame.  
4.  Resubmit the experiment.

 <!--
> [!NOTE]
> These topics contains examples of R code that you can use, as well as links to experiments in the [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) that use R script.
> + [Execute R Script](execute-r-script.md)
> + [Create R Model](create-r-model.md)
-->  
|Messages d’exception|  
|------------------------|  
|Erreur lors de l’évaluation du script R.|  
|L’erreur suivante s’est produite pendant l’évaluation du script R : ---------- Début du message d’erreur de R ---------- {0} ----------- Fin du message d’erreur de R-----------|  
|Lors de l’évaluation du script R « {1} », l’erreur suivante s’est produite : : ---------- Début du message d’erreur de R ---------- {0} ----------- Fin du message d’erreur de R-----------|  
  


## <a name="error-0064"></a>Erreur 0064  
 Une exception se produit si le nom du compte de stockage Azure ou la clé de stockage n’est pas spécifiée correctement.  
  
 Cette erreur se produit dans Azure Machine Learning si le nom du compte de stockage Azure ou la clé de stockage n’est pas spécifiée correctement. Vous recevez cette erreur si vous entrez un nom de compte ou un mot de passe incorrect pour le compte de stockage. Cela peut se produire si vous entrez manuellement le nom de compte ou le mot de passe. Cela peut également se produire si le compte a été supprimé.  
  
**Résolution :** Vérifiez que le nom de compte et le mot de passe ont été saisis correctement et que le compte existe.  
  
|Messages d’exception|  
|------------------------|  
|Le nom du compte de stockage Azure ou la clé de stockage est incorrect.|  
|Le nom de compte de stockage Azur « {0} » ou la clé de stockage du nom de compte est incorrect.|  
  

## <a name="error-0065"></a>Erreur 0065  
 Une exception se produit si le nom du bob Azure n’est pas spécifié correctement.  
  
 Cette erreur se produit dans Azure Machine Learning si le nom du blob Azure n’est pas spécifié correctement.  Vous recevez l’erreur si :  
  
-   Le blob est introuvable dans le conteneur spécifié.  
  
 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->
  
-   Seul le conteneur a été spécifié comme source dans une requête [Importer des données](import-data.md) lorsque le format était Excel ou CSV avec encodage ; la concaténation du contenu de tous les blobs dans un conteneur n’est pas autorisée avec ces formats.  
  
-   Une URI SAS ne contient pas le nom d’un blob valide.  
  
**Résolution :** Réexaminez le module qui engendre l’exception. Vérifiez que le blob spécifié existe bien dans le conteneur du compte de stockage et que les autorisations vous permettent de voir le blob. Vérifiez que l’entrée est définie sous la forme **nomconteneur/nomfichier** si vous avez Excel ou CSV avec des formats d’encodage. Vérifiez qu’une URI SAS contient le nom d’un blob valide.  
  
|Messages d’exception|  
|------------------------|  
|Le blob de stockage Azure est incorrect.|  
|Le blob de stockage Azure « {0} » est incorrect.|  
  

## <a name="error-0066"></a>Erreur 0066  
 Une exception se produit si une ressource n’a pas pu être téléchargée vers un blob Azure.  
  
 Cette erreur se produit dans Azure Machine Learning si une ressource n’a pas pu être téléchargée dans un blob Azure.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Les deux sont sauvegardés sur le même compte de stockage Azure que le compte contenant le fichier d’entrée.  
  
**Résolution :** Réexaminez le module. Vérifiez que le nom du compte Azure, la clé de stockage et le conteneur sont corrects et que le compte est autorisé à écrire dans le conteneur.  
  
|Messages d’exception|  
|------------------------|  
|La ressource n’a pas pu être chargée vers le stockage Azure.|  
|Le fichier « {0} » n’a pas pu être chargé vers le stockage Azure en tant que {1}.|  
  

## <a name="error-0067"></a>Erreur 0067  
 Une exception se produit si un jeu de données possède un nombre de colonnes différent du nombre de colonnes prévu.  
  
 Cette erreur se produit dans Azure Machine Learning si un jeu de données possède un nombre de colonnes différent du nombre de colonnes prévu.  Vous recevrez cette erreur lorsque le nombre de colonnes du jeu de données est différent du nombre de colonnes attendu par le module pendant l’exécution.  
  
**Résolution :** Modifiez le jeu de données d’entrée ou les paramètres.  
  
|Messages d’exception|  
|------------------------|  
|Nombre de colonnes inattendu dans la table de données.|  
|« {0} » colonnes attendues mais « {1} » colonnes trouvées à la place.|  
  

## <a name="error-0068"></a>Erreur 0068  
 Une exception se produit si le script Hive spécifié n’est pas correct.  
  
 Cette erreur se produit dans Azure Machine Learning si un script Hive SQL contient des erreurs de syntaxe ou si l’interpréteur Hive rencontre une erreur lors de l’exécution de la requête ou du script.  
  
**Résolution :**

Le message d’erreur Hive est normalement rapporté dans le journal des erreurs afin que vous puissiez prendre des mesures en fonction de l’erreur spécifique. 

+ Ouvrez le module et vérifiez si la requête contient des erreurs.  
+ Vérifiez que la requête fonctionne correctement en dehors d’Azure Machine Learning en vous connectant à la console Hive de votre cluster Hadoop et en exécutant la requête.  
+ Veillez à placer les commentaires dans votre script Hive sur une ligne séparée au lieu de mélanger les instructions exécutables et les commentaires sur une seule ligne.  

### <a name="resources"></a>Ressources

Consultez les articles suivants pour obtenir de l’aide sur les requêtes Hive dans le cadre de l’apprentissage automatique :

+ [Créer des tables Hive et charger des données à partir de Stockage Blob Azure](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Explorer les données dans des tables avec des requêtes Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Créer des fonctionnalités pour les données dans un cluster Hadoop à l’aide de requêtes Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive).
+ [Aide-mémoire pour les utilisateurs Hive pour SQL (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) (en anglais)

  
|Messages d’exception|  
|------------------------|  
|Le script Hive est incorrect.|  
|Le script Hive {0} est incorrect.|  
  

## <a name="error-0069"></a>Erreur 0069  
 Une exception se produit si le script SQL spécifié n’est pas correct.  
  
 Cette erreur se produit dans Azure Machine Learning si le script SQL spécifié comporte des problèmes de syntaxe ou si les colonnes ou le tableau spécifié dans le script n’est pas valide. 
 
 Vous recevrez cette erreur si le moteur SQL rencontre une erreur lors de l’exécution de la requête ou du script. Le message d’erreur SQL est normalement rapporté dans le journal des erreurs afin que vous puissiez prendre des mesures en fonction de l’erreur spécifique.  
  
**Résolution :** Réexaminez le module et vérifiez si la requête SQL contient des erreurs.  
  
 Vérifiez que la requête fonctionne correctement en dehors d’Azure ML en vous connectant directement au serveur de base de données et en exécutant la requête.  
  
 Si un message SQL est généré par l’exception du module, prenez les mesures en fonction de l’erreur signalée. Par exemple, les messages d’erreur contiennent parfois des indications spécifiques sur l’erreur probable :
+ *Aucune colonne ou base de données manquante*, indiquant que vous avez peut-être tapé un nom de colonne incorrect. Si vous êtes sûr que le nom de la colonne est correct, essayez d’utiliser des crochets ou des guillemets pour inclure l’identificateur de colonne.
+ *Erreur logique SQL près de \<mot-clé SQL\>* , indiquant une erreur de syntaxe probable avant le mot-clé spécifié

  
|Messages d’exception|  
|------------------------|  
|Le script SQL est incorrect.|  
|La requête SQL « {0} » n’est pas correcte.|  
|La requête SQL « {0} » n’est pas correcte : {1}|  
  

## <a name="error-0070"></a>Erreur 0070  
 Une exception se produit lorsque l’on tente d’accéder à une table Azure inexistante.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous essayez d’accéder à une table Azure inexistante. Vous recevrez cette erreur si vous spécifiez une table dans le stockage Azure qui n’existe pas lors de la lecture ou de l’écriture dans Stockage Table Azure. Cela peut se produire si le nom de la table souhaitée que vous avez saisi est erroné ou si le nom cible et le type de stockage sont différents. Par exemple, vous aviez l’intention d’effectuer une lecture dans une table mais vous avez entré le nom d’un blob.  
  
**Résolution :** Réexaminez le module pour vérifier que le nom de la table est correct.  
  
|Messages d’exception|  
|------------------------|  
|La table Azure n’existe pas.|  
|La table Azure « {0} » n’existe pas.|  
  
## <a name="error-0071"></a>Erreur 0071  
 Une exception se produit si les informations d’identification fournies sont incorrectes.  
  
 Cette erreur se produit dans Azure Machine Learning si les informations d’identification fournies sont incorrectes.  
  
 Cette erreur peut également se produire si le module ne peut pas se connecter à un cluster HDInsight.  
  
**Résolution :** Examinez les entrées du module et vérifiez le nom du compte et le mot de passe.  
  
 Vérifiez si les problèmes suivants peuvent provoquer une erreur :  
  
-   Le schéma du jeu de données ne correspond pas au schéma de la table de données de destination.  
  
-   Les noms des colonnes sont manquants ou mal orthographiés  
  
-   Vous écrivez dans une table dont certains noms de colonne comportent des caractères non autorisés. Normalement, vous pouvez mettre ces noms de colonnes entre crochets, mais si cela ne fonctionne pas, modifiez les noms de colonne pour n’utiliser que des lettres et des caractères de soulignement (_)  
  
-   Les chaînes que vous essayez d’écrire contiennent des guillemets simples  
  
 Si vous essayez de vous connecter à un cluster HDInsight, vérifiez que le cluster cible est accessible avec les identifiants fournis.  
  
|Messages d’exception|  
|------------------------|  
|Des informations d’identification incorrectes sont transmises.|  
|Un nom d’utilisateur « {0} » ou un mot de passe incorrect est transmis|  
  

## <a name="error-0072"></a>Erreur 0072  
 Une exception se produit dans le cas d’un délai d’attente de connexion.  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’une connexion arrive à expiration. Vous recevez cette erreur s’il existe actuellement des problèmes de connectivité avec la source ou la destination de données, comme une connexion Internet lente, ou si le jeu de données est volumineux et/ou si la requête SQL à lire dans les données effectue un traitement complexe.  
  
**Résolution :** Déterminez s’il existe actuellement des problèmes de lenteur de connexion au stockage Azure ou à Internet.  
  
|Messages d’exception|  
|------------------------|  
|Le délai d’expiration de la connexion s’est écoulé.|  
  

## <a name="error-0073"></a>Erreur 0073  
 Une exception se produit si une erreur se produit lors de la conversion d’une colonne vers un autre type.  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’il n’est pas possible de convertir une colonne en un autre type.  Vous recevez cette erreur si un module nécessite un type particulier et qu’il n’est pas possible de convertir la colonne vers le nouveau type.  
  
**Résolution :** Modifiez le jeu de données d’entrée pour que la colonne puisse être convertie en fonction de l’exception interne.  
  
|Messages d’exception|  
|------------------------|  
|Échec de conversion de colonne.|  
|Échec de conversion de colonne en {0}.|  
  

## <a name="error-0074"></a>Erreur 0074  
 Une exception se produit lorsque [Modifier les métadonnées](edit-metadata.md) tente de convertir une colonne éparse en colonne catégorielle.  
  
 Cette erreur d’Azure Machine Learning lorsque [Modifier les métadonnées](edit-metadata.md) tente de convertir une colonne éparse en colonne catégorielle.  Vous recevez cette erreur lorsque vous tentez de convertir des colonnes éparses en colonnes catégorielles avec l’option **Make Categorical** (Rendre catégorielle).  Le module échoue car Azure machine Learning ne prend pas en charge les tableaux catégoriels épars.  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|Messages d’exception|  
|------------------------|  
|Les colonnes éparses ne peuvent pas être converties en colonnes catégorielles.|  
  

## <a name="error-0075"></a>Erreur 0075  
Une exception se produit lorsqu’une fonction de regroupement non valide est utilisée lors de la quantification d’un jeu de données.  
  
Cette erreur se produit dans Azure Machine Learning lorsque vous essayez de lier des données à l’aide d’une méthode non prise en charge ou lorsque les combinaisons de paramètres ne sont pas valides.  
  
**Résolution :**

La gestion des erreurs pour cet événement a été introduite dans une version antérieure d’Azure Machine Learning qui permettait une plus grande personnalisation des méthodes de compartimentage. Actuellement, toutes les méthodes de compartimentage sont basées sur une sélection dans une liste déroulante, donc techniquement cette erreur ne devrait plus se produire.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|Messages d’exception|  
|------------------------|  
|La fonction de compartimentage n’est pas valide.|  
  

## <a name="error-0077"></a>Erreur 0077  
 Une exception se produit en cas de transmission du mode écriture d’un fichier blob inconnu.  
  
 Cette erreur se produit dans Azure Machine Learning si un argument non valide est transmis dans les spécifications pour une destination ou une source de fichier blob.  
  
**Résolution :** Dans presque tous les modules qui importent ou exportent des données vers et depuis le Stockage blob Azure, les valeurs des paramètres contrôlant le mode d’écriture sont assignées à partir d’une liste déroulante ; il n’est donc pas possible de transmettre une valeur non valide, et cette erreur ne devrait pas survenir. Cette erreur ne sera plus possible dans une version ultérieure.  
  
|Messages d’exception|  
|------------------------|  
|Mode écriture Blob non pris en charge.|  
|Mode écriture Blob non pris en charge : {0}.|  
  

## <a name="error-0078"></a>Erreur 0078  
 Une exception se produit lorsque l’option HTTP associée à [Importer des données](import-data.md) reçoit un code d’état 3xx indiquant une redirection.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque l’option HTTP associée à [Importer des données](import-data.md) reçoit un code d’état 3xx (301, 302, 304, etc.) indiquant une redirection. Vous recevez cette erreur si vous tentez de vous connecter à une source HTTP qui redirige le navigateur vers une autre page. Pour des raisons de sécurité, la redirection de sites web n’est pas autorisée comme source de données pour Azure Machine Learning.  
  
**Résolution :** Si le site web est un site web de confiance, entrez directement l’URL redirigée.  
  
|Messages d’exception|  
|------------------------|  
|La redirection HTTP n’est pas autorisée|  
  

## <a name="error-0079"></a>Erreur 0079  
 Une exception se produit si le nom du conteneur de stockage Azure n’est pas spécifié correctement.  
  
 Cette erreur se produit dans Azure Machine Learning si le nom du conteneur de stockage Azure n’est pas spécifié correctement. Vous recevez cette erreur si vous n’avez pas spécifié à la fois le conteneur et le nom du blob (fichier) en utilisant l’option **Path to blob beginning with container** (Chemin d’accès vers le blob en commençant par le conteneur) lorsque vous écrivez dans Stockage Blob Azure.  
  
**Résolution :** Réexaminez le module [Exporter des données](export-data.md) et vérifiez que le chemin d’accès au blob contient à la fois le conteneur et le nom du fichier, dans le format **conteneur/nomfichier**.  
  
|Messages d’exception|  
|------------------------|  
|Le nom du conteneur de stockage Azure est incorrect.|  
|Le nom de conteneur de stockage « {0} » est incorrect ; un nom de conteneur était attendu dans le format nomconteneur/nomblob.|  
  

## <a name="error-0080"></a>Erreur 0080  
 Une exception se produit lorsque le module n’autorise pas la présence de colonnes sans valeurs.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque aucune valeur ne figure dans une ou plusieurs des colonnes utilisées par le module. Par exemple, si un module calcule des statistiques globales pour chaque colonne, il ne peut pas fonctionner sur une colonne qui ne contient aucune donnée. Dans ce cas, l’exécution du module est interrompue avec cette exception.  
  
**Résolution :** Réexaminez le jeu de données d’entrée et supprimez toutes les colonnes vides.  
  
|Messages d’exception|  
|------------------------|  
|Les colonnes ne comportant aucune valeur ne sont pas autorisées.|  
|Aucune valeur ne figure dans la colonne {0}.|  
  

## <a name="error-0081"></a>Erreur 0081  
 Une exception se produit dans le module PCA si le nombre de dimensions à réduire est égal au nombre de colonnes de fonctionnalités dans le jeu de données d’entrée, lequel contient au moins une colonne de fonctionnalités éparses.  
  
 Cette erreur se produit dans Azure Machine Learning si les conditions suivantes sont remplies : (a) le jeu de données d’entrée comporte au moins une colonne éparse et (b) le nombre final de dimensions demandées est égal au nombre de dimensions d’entrée.  
  
**Résolution :** Réduisez le nombre de dimensions dans la sortie pour qu’il soit inférieur au nombre de dimensions dans l’entrée. Ce cas est courant dans les applications de PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|Messages d’exception|  
|------------------------|  
|Pour les jeux de données contenant des colonnes d’éléments épars, le nombre de dimensions à réduire doit être inférieur au nombre de colonnes d’éléments.|  
 

## <a name="error-0082"></a>Erreur 0082  
 Une exception se produit lorsque la désérialisation d’un modèle n’aboutit pas.  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’un modèle ou une transformation d’apprentissage automatique enregistré ne peut pas être chargé par une version plus récente du runtime Azure Machine Learning à la suite d’un changement cassant.  
  
**Résolution :** L’expérience de formation qui a produit le modèle ou la transformation doit être exécutée de nouveau, et le modèle ou la transformation doit être enregistré de nouveau également.  
  
|Messages d’exception|  
|------------------------|  
|Le modèle n’a pas pu être désérialisé parce qu’il est probablement sérialisé dans un format de sérialisation plus ancien. Formez de nouveau le modèle, puis enregistrez-le.|  
  

## <a name="error-0083"></a>Erreur 0083  
 Une exception se produit si le jeu de données utilisé pour la formation n’est pas compatible avec un type concret d’apprenant.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque le jeu de données est incompatible avec l’apprenant en formation. Par exemple, au moins une valeur est manquante sur chaque ligne du jeu de données, ce qui signifie que celui-ci peut être ignoré pendant la formation. Dans d’autres cas, certains algorithmes d’apprentissage automatique tels que la détection d’anomalies ne s’attendent pas à la présence d’étiquettes et peuvent générer cette exception si le jeu de données contient des étiquettes.  
  
**Résolution :** Consulter la documentation de l’apprenant utilisé pour vérifier les exigences du jeu de données d’entrée. Examinez les colonnes pour vérifier si toutes les colonnes requises sont présentes.  
  
|Messages d’exception|  
|------------------------|  
|Le jeu de données utilisé pour la formation n’est pas valide.|  
|{0} contient des données non valides pour l’apprentissage.|  
|{0} contient des données non valides pour l’apprentissage. Type d’apprenant : {1}.|  
  

## <a name="error-0084"></a>Erreur 0084  
 Une exception se produit lorsque les scores produits à partir d’un script R sont évalués. Cela n’est actuellement pas pris en charge.  
  
 Cette erreur se produit dans Azure Machine Learning si vous essayez d’utiliser l’un des modules pour évaluer un modèle avec sortie à partir d’un script R qui contient des scores.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|L’évaluation des scores produites par R n’est actuellement pas prise en charge.|  
  

## <a name="error-0085"></a>Erreur 0085  
 Une exception se produit lorsque l’évaluation du script échoue avec une erreur.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous exécutez un script personnalisé qui contient des erreurs de syntaxe.  
  
**Résolution :** Dans un éditeur externe, vérifiez si votre code contient des erreurs.  
  
|Messages d’exception|  
|------------------------|  
|Erreur lors de l’évaluation du script.|  
|L’erreur suivante s’est produite lors de l’évaluation du script. Consultez le journal de sortie pour plus d’informations : ---------- Début du message d’erreur de l’interpréteur {0} ---------- {1} ----------Fin du message d’erreur de l’interpréteur {0} ----------|  
  

## <a name="error-0086"></a>Erreur 0086  
 Une exception se produit lorsqu’une transformation de comptage n’est pas valide.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous sélectionnez une transformation basée sur une table de comptage mais que la transformation sélectionnée est incompatible avec les données actuelles ou avec la nouvelle table de comptage.  
  
**Résolution :** Le module prend en charge l’enregistrement des comptages et des règles qui composent la transformation dans deux formats différents. Si vous fusionnez des tables de comptage, vérifiez que les deux tables que vous avez l’intention de fusionner utilisent le même format.  
  
En général, une transformation basée sur le comptage ne peut être appliquée qu’aux jeux de données qui ont le même schéma que le jeu de données sur lequel la transformation a été initialement créée.  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Messages d’exception|  
|------------------------|  
|Transformation de comptage non valide spécifiée.|  
|La transformation de comptage n’est pas valide au niveau du port d’entrée « {0} ».|  
|La transformation de comptage du port d’entrée ’{0}’ ne peut pas être fusionnée avec celle du port d’entrée’{1}’. Vérifiez les métadonnées utilisées pour le comptage des correspondances.|  
  

## <a name="error-0087"></a>Erreur 0087  
 Une exception se produit lorsqu’un type de table de comptage non valide est spécifié pour l’apprentissage avec les modules de comptage.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous essayez d’importer une table de comptage existante mais que celle-ci est incompatible avec les données actuelles ou avec la nouvelle table de comptage.  
  
**Résolution :** Il existe des formats différents pour l’enregistrement des comptages et des règles qui composent la transformation. Si vous fusionnez des tables de comptage, vérifiez qu’elles utilisent toutes deux le même format.  
  
 Généralement, une transformation basée sur le comptage ne peut être appliquée qu’aux jeux de données qui ont le même schéma que le jeu de données sur lequel la transformation a été initialement créée.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>Erreur 0088  
 Une exception se produit lorsqu’un type de comptage non valide est spécifié pour l’apprentissage avec les modules de comptage.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous essayez d’utiliser une méthode de comptage différente qui est prise en charge pour la fonctionnalité de comptage.  
  
**Résolution :** En général, les méthodes de comptage sont choisies dans une liste déroulante, ce qui signifie que cette erreur ne doit normalement pas se produire.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Messages d’exception|  
|------------------------|  
|Un type de comptage non valide est spécifié.|  
|Le type de comptage spécifié « {0} » n’est pas un type de comptage valide.|  
  

## <a name="error-0089"></a>Erreur 0089  
 Une exception se produit lorsque le nombre spécifié de classes est inférieur au nombre effectif de classes dans un jeu de données utilisé pour le comptage.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous créez une table de comptage et que la colonne d’étiquette contient un nombre de classes différent de celui spécifié dans les paramètres du module.  
  
**Résolution :** Vérifiez votre jeu de données et dénombrez les valeurs distinctes (classes possibles) dans la colonne d’étiquette. Lorsque vous créez la table de comptage, vous devez spécifier au moins ce nombre de classes.  
  
 La table de comptage ne peut pas déterminer automatiquement le nombre de classes disponibles.  
  
 Lorsque vous créez la table de comptage, vous ne pouvez pas spécifier 0 ou tout nombre inférieur au nombre effectif de classes dans la colonne d’étiquette.  
  
|Messages d’exception|  
|------------------------|  
|Le nombre de classes est incorrect. Assurez-vous que le nombre de classes que vous spécifiez dans le volet de paramètres est supérieur ou égal au nombre de classes dans la colonne d’étiquette.|  
|Le nombre de classes spécifié est « {0} », qui n’est pas supérieur à une valeur d’étiquette « {1} » dans le jeu de données utilisé pour le comptage. Assurez-vous que le nombre de classes que vous spécifiez dans le volet de paramètres est supérieur ou égal au nombre de classes dans la colonne d’étiquette.|  
  

## <a name="error-0090"></a>Erreur 0090  
 Une exception se produit lorsque la création de table Hive échoue.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous utilisez [Exporter des données](export-data.md) ou une autre option pour enregistrer les données dans un cluster HDInsight et que la table Hive spécifiée ne peut pas être créée.  
  
**Résolution :** Vérifiez le nom du compte de stockage Azure associé au cluster et vérifiez que vous utilisez le même compte dans les propriétés du module.  
  
|Messages d’exception|  
|------------------------|  
|Impossible de créer la table Hive. Pour un cluster HDInsight, vérifiez que le nom du compte de stockage Azure associé au cluster est identique à la valeur transmise par le paramètre de module.|  
|Impossible de créer la table Hive « {0} ». Pour un cluster HDInsight, vérifiez que le nom du compte de stockage Azure associé au cluster est identique à la valeur transmise par le paramètre de module.|  
|Impossible de créer la table Hive « {0} ». Pour un cluster HDInsight, vérifiez que le nom du compte de stockage Azure associé au cluster est « {1} ».|  
 

## <a name="error-0100"></a>Erreur 0100  
 Une exception se produit lorsqu’un langage non pris en charge est spécifié pour un module personnalisé.  
  
 Cette erreur se produit dans Azure Machine Learning si, lors de la génération d’un module personnalisé, la propriété du nom de l’élément **Language** dans un fichier de définition xml de module personnalisé possède une valeur non valide. Actuellement, la seule valeur possible est `R` pour cette propriété. Par exemple :  
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Résolution :** Vérifiez que la propriété du nom de l’élément **Language** dans le fichier de définition xml du module personnalisé est définie sur `R`. Enregistrez le fichier, mettez à jour le package zip du module personnalisé et essayez d’ajouter à nouveau le module personnalisé.  
  
|Messages d’exception|  
|------------------------|  
|Langage du module personnalisé non prise en charge spécifiée|  
  

## <a name="error-0101"></a>Erreur 0101  
 Tous les ID de port et de paramètre doivent être uniques  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’un ou plusieurs ports ou paramètres se voient recevoir la même valeur d’ID dans un fichier de définition XML de module personnalisé.  
  
**Résolution :** Vérifiez que les valeurs d’ID sont uniques pour tous les ports et paramètres. Enregistrez le fichier xml, mettez à jour le package zip du module personnalisé et essayez d’ajouter à nouveau le module personnalisé.  
  
|Messages d’exception|  
|------------------------|  
|Tous les ID de port et de paramètre doivent être uniques pour un module|  
|Le module « {0} » possède des ID de port et d’argument en double. Tous les ID de port/de paramètre doivent être uniques pour un module.|  
  

## <a name="error-0102"></a>Erreur 0102  
 Une exception se produit lorsqu’un fichier ZIP ne peut pas être extrait.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous importez un package compressé possédant l’extension.zip mais que celui-ci n’est pas un fichier zip ou que le fichier zip est enregistré dans un format zip qui n’est pas en charge.  
  
**Résolution :** Assurez-vous que le fichier sélectionné est un fichier .zip valide et qu’il a été compressé à l’aide d’un algorithme de compression pris en charge.  
  
 Si vous obtenez cette erreur lors de l’importation de jeux de données au format compressé, vérifiez que tous les fichiers contenus utilisent l’un des formats de fichiers pris en charge et qu’ils sont au format Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 Essayez de rajouter les fichiers souhaités dans un nouveau dossier compressé au format zip et essayez d’ajouter de nouveau le module personnalisé.  
  
|Messages d’exception|  
|------------------------|  
|Le fichier ZIP donné n’est pas dans le format correct|  


## <a name="error-0103"></a>Erreur 0103  
 Une exception se produit lorsqu’un fichier ZIP ne contient pas de fichiers .xml  
  
 Cette erreur se produit dans Azure Machine Learning lorsque le package zip du module personnalisé ne contient aucun fichier de définition de module (.xml). Ces fichiers doivent se trouver à la racine du package zip (par exemple, pas dans un sous-dossier).  
  
**Résolution :** Vérifiez qu’un ou plusieurs fichiers de définition de module xml résident dans le dossier racine du package zip en l’extrayant dans un dossier temporaire sur votre lecteur de disque. Tous les fichiers xml doivent aller directement dans le dossier où vous avez extrait le package zip. Lorsque vous créez le package zip, assurez-vous de ne pas sélectionner un dossier contenant des fichiers xml à compresser, car cela créera un sous-dossier dans le package zip avec le même nom que le dossier que vous avez choisi de compresser.  
  
|Messages d’exception|  
|------------------------|  
|Le fichier ZIP donné ne contient aucun fichier de définition de module (fichiers .xml)|  


## <a name="error-0104"></a>Erreur 0104  
 Une exception se produit lorsqu’un fichier de définition de module fait référence à un script qui est introuvable  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’un fichier de définition xml d’un module personnalisé fait référence à un fichier script dans l’élément **Language** qui n’existe pas dans le package zip. Le chemin d’accès du fichier de script est défini dans la propriété **sourceFile** de l’élément **Language**. Le chemin d’accès au fichier source est relatif à la racine du package zip (même emplacement que les fichiers de définition xml du module). Si le fichier de script réside dans un sous-dossier, vous devez spécifier le chemin d’accès relatif à ce fichier. Par exemple, si tous les scripts étaient stockés dans un dossier **myScripts** dans le package zip, l’élément **Language** devrait ajouter ce chemin d’accès à la propriété **sourceFile** comme ci-dessous. Par exemple :  
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Résolution :** Assurez-vous que la valeur de la propriété **sourceFile** dans l’élément **Language** de la définition xml du module personnalisé est correcte et que le fichier source existe dans le chemin d’accès relatif du package zip.  
  
|Messages d’exception|  
|------------------------|  
|Le fichier de script R référencé n’existe pas.|  
|Le fichier de script R référencé « {0} » est introuvable. Vérifiez que le chemin d’accès relatif au fichier est correct à partir de l’emplacement des définitions.|  


## <a name="error-0105"></a>Erreur 0105  
 Cette erreur s’affiche lorsqu’un fichier de définition de module contient un type de paramètre non pris en charge  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous créez une définition xml de module personnalisé et que le type d’un paramètre ou d’un argument dans la définition ne correspond pas à un type pris en charge.  
  
**Résolution :** Assurez-vous que la propriété de type de n’importe quel élément **Arg** du fichier de définition xml du module personnalisé est un type pris en charge.  
  
|Messages d’exception|  
|------------------------|  
|Type de paramètre non pris en charge.|  
|Le type de paramètre « {0} » non pris en charge est spécifié.|  


## <a name="error-0106"></a>Erreur 0106  
 Une exception se produit lorsqu’un fichier de définition de module définit un type d’entrée non pris en charge  
  
 Cette erreur se produit dans Azure Machine Learning lorsque le type d’un port d’entrée d’une définition XML de module personnalisé ne correspond pas à un type pris en charge.  
  
**Résolution :** Assurez-vous que la propriété de type d’un élément Input du fichier de définition xml du module personnalisé est un type pris en charge.  
  
|Messages d’exception|  
|------------------------|  
|Type d’entrée non pris en charge.|  
|Type d’entrée « {0} » non pris en charge spécifié.|  


## <a name="error-0107"></a>Erreur 0107  
 Une exception se produit lorsqu’un fichier de définition de module définit un type de sortie non pris en charge  
  
 Cette erreur se produit dans Azure Machine Learning lorsque le type d’un port de sortie dans une définition xml de module personnalisé ne correspond pas à un type pris en charge.  
  
**Résolution :** Assurez-vous que la propriété de type d’un élément Output du fichier de définition xml du module personnalisé est un type pris en charge.  
  
|Messages d’exception|  
|------------------------|  
|Type de sortie non pris en charge.|  
|Type de sortie « {0} » non pris en charge spécifié.|  


## <a name="error-0108"></a>Erreur 0108  
 Une exception se produit lorsqu’un fichier de définition de module définit plus de ports d’entrée ou de sortie qu’il n’est possible de gérer  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’un fichier de définition de module personnalisé comporte un nombre excessif de ports d’entrée ou de sortie.  
  
**Résolution :** Assurez-vous que le nombre maximal de ports d’entrée et de sortie définis dans la définition xml du module personnalisé ne dépasse pas le nombre maximal de ports pris en charge.  
  
|Messages d’exception|  
|------------------------|  
|Dépassement du nombre de ports d’entrée ou de sortie pris en charge.|  
|Dépassement du nombre de ports « {0} » pris en charge. Le nombre maximal de ports {0} est de {1}.| 

## <a name="error-0109"></a>Erreur 0109  
 Une exception se produit lorsqu’un fichier de définition de module définit un sélecteur de colonnes de façon incorrecte  
  
 Cette erreur se produit dans Azure Machine Learning lorsque la syntaxe d’un argument de sélecteur de colonnes est erronée dans une définition xml de module personnalisé.  
  
**Résolution :** Cette erreur se produit lorsque la syntaxe d’un argument de sélecteur de colonnes est erronée dans une définition xml de module personnalisé.  
  
|Messages d’exception|  
|------------------------|  
|Syntaxe non prise en charge pour le sélecteur de colonnes.|  
  

## <a name="error-0110"></a>Erreur 0110  
 Une exception se produit lorsqu’un fichier de définition de module définit un sélecteur de colonnes qui fait référence à un ID de port d’entrée inexistant  
  
 Cette erreur se produit dans Azure Machine Learning lorsque la propriété *portId* de l’élément Propriétés d’un argument de type ColumnPicker ne correspond pas à la valeur d’ID d’un port d’entrée.  
  
**Résolution :** Assurez-vous que la propriété portId correspond à la valeur d’ID d’un port d’entrée défini dans la définition xml du module personnalisé.  
  
|Messages d’exception|  
|------------------------|  
|Le sélecteur de colonnes fait référence à un ID de port d’entrée inexistant|  
|Le sélecteur de colonnes fait référence à un ID de port d’entrée inexistant ’{0}’.|  
  

## <a name="error-0111"></a>Erreur 0111  
 Une exception se produit lorsqu’un fichier de définition de module définit une propriété non valide  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’une propriété non valide est assignée à un élément de la définition de module personnalisé.  
  
**Résolution :** Assurez-vous que la propriété est prise en charge par l’élément de module personnalisé.  
  
|Messages d’exception|  
|------------------------|  
|La définition de la propriété n’est pas valide.|  
|La définition de la propriété « {0} » n’est pas valide.|  
  

## <a name="error-0112"></a>Erreur 0112  
 Une exception se produit lorsqu’un fichier de définition de module ne peut pas être analysé  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’une erreur de format XML empêche l’analyse de la définition XML du module personnalisé en tant que fichier XML valide.  
  
**Résolution :** Assurez-vous que chaque élément est ouvert et fermé correctement. Assurez-vous que le formatage XML ne comporte pas d’erreur.  
  
|Messages d’exception|  
|------------------------|  
|Impossible d’analyser le fichier de définition de module.|  
|Impossible d’analyser le fichier de définition de module « {0} ».|  
  

## <a name="error-0113"></a>Erreur 0113  
 Une exception se produit lorsqu’un fichier de définition de module contient des erreurs.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque le fichier XML de définition de module personnalisé est analysable mais contient des erreurs, tels que la définition d’éléments non pris en charge par les modules personnalisés.  
  
**Résolution :** Assurez-vous que le fichier de définition de module personnalisé définit les éléments et les propriétés qui sont pris en charge par les modules personnalisés.  
  
|Messages d’exception|  
|------------------------|  
|Le fichier de définition de module contient des erreurs.|  
|Le fichier de définition de module « {0} » contient des erreurs.|  
|Le fichier de définition de module « {0} » contient des erreurs. {1}|  
  

## <a name="error-0114"></a>Erreur 0114  
 Une exception se produit en cas d’échec de la génération d’un module personnalisé.  
  
 Cette erreur se produit dans Azure Machine Learning en cas d’échec de la génération d’un module personnalisé. Cela se produit lorsqu’une ou plusieurs erreurs liées aux modules personnalisés sont identifiées lors de l’ajout du module personnalisé. Les erreurs supplémentaires sont signalées au sein de ce message d’erreur.  
  
**Résolution :** Résolvez les erreurs signalées dans ce message d’exception.  
  
|Messages d’exception|  
|------------------------|  
|Impossible de générer le module personnalisé.|  
|Les générations du module personnalisé ont échoué avec la (ou les) erreur(s) : {0}|  
  

## <a name="error-0115"></a>Erreur 0115  
 Une exception se produit lorsqu’un script par défaut du module personnalisé possède une extension qui n’est pas prise en charge.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous fournissez un script pour un module personnalisé qui utilise une extension de nom de fichier inconnu.  
  
**Résolution :** Vérifiez le format de fichier et l’extension de nom de fichier pour les fichiers script inclus dans le module personnalisé.  
  
|Messages d’exception|  
|------------------------|  
|Extension non prise en charge pour le script par défaut.|  
|Extension de fichier {0} non prise en charge pour le script par défaut.|  
  

## <a name="error-0121"></a>Erreur 0121  
 Une exception se produit lors le script SQL ne parvient pas à écrire des données dans la table car celle-ci n’est pas inscriptible  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous utilisez le module [Exporter des données](export-data.md) pour enregistrer les résultats dans une table d’une base de données SQL ; or la table ne peut pas être écrite. En règle générale, cette erreur se produit si le module [Exporter des données](export-data.md) parvient à établir une connexion avec l’instance SQL Server, mais est alors incapable d’écrire le contenu du jeu de données Azure ML dans la table.  
  
**Résolution :**
 - Ouvrez le volet Propriétés du module [Exporter des données](export-data.md) et vérifiez que les noms de base de données et de table sont entrés correctement. 
 - Révisez le schéma du jeu de données que vous exportez et assurez-vous que les données sont compatibles avec la table de destination.
 - Vérifiez que la connexion SQL associée au nom d’utilisateur et au mot de passe est autorisée à écrire dans la table. 
 - Si l’exception contient des informations d’erreur supplémentaires à partir de SQL Server, utilisez-les informations pour apporter les corrections.  
  
|Messages d’exception|  
|------------------------|  
|Connecté au serveur, impossible d’écrire dans la table.|  
|Impossible d’écrire dans la table SQL : {0}|  


## <a name="error-0122"></a>Erreur 0122  
 Une exception se produit si plusieurs colonnes de pondération sont spécifiées alors qu’une seule colonne d’étiquette est autorisée.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque les colonnes sélectionnées comme colonnes de pondération sont trop nombreuses.  
  
**Résolution :** Révisez le jeu de données d’entrée et de ses métadonnées. Assurez-vous qu’une seule colonne contient les pondérations.  
  
|Messages d’exception|  
|------------------------|  
|Plusieurs colonnes de pondération sont spécifiées.|  


## <a name="error-0123"></a>Erreur 0123  
 Une exception se produit si la colonne de vecteurs est spécifiée en tant que colonne d’étiquette.  
  
 Cette erreur se produit dans Azure Machine Learning si vous utilisez un vecteur comme colonne d’étiquette.  
  
**Résolution :** Modifiez le format de données de la colonne si nécessaire ou sélectionnez une autre colonne.  
  
|Messages d’exception|  
|------------------------|  
|Une colonne de vecteurs est spécifiée en tant que colonne d’étiquette.|  


## <a name="error-0124"></a>Erreur 0124  
 Une exception se produit si des colonnes non numériques sont spécifiées en tant que colonne de pondération.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Une colonne non numérique est spécifiée en tant que colonne de pondération.|  
  


## <a name="error-0125"></a>Erreur 0125  
 Une exception se produit lorsque les schémas de plusieurs jeux de données ne correspondent pas.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Le schéma du jeu de données ne correspond pas.|  


## <a name="error-0126"></a>Erreur 0126  
 Une exception se produit si l’utilisateur spécifie un domaine SQL qui n’est pas pris en charge dans Azure ML.  
  
 Cette erreur se produit lorsque l’utilisateur spécifie un domaine SQL qui n’est pas pris en charge dans Azure Machine Learning. Vous recevez cette erreur si vous essayez de vous connecter à un serveur de base de données dans un domaine qui ne figure pas dans la liste verte. Actuellement, les domaines SQL autorisés sont : « .database.windows.net », « .cloudapp.net » et « .database.secure.windows.net ». Autrement dit, le serveur doit être un serveur SQL Azure ou un serveur dans une machine virtuelle sur Azure.  
  
**Résolution :** Réexaminez le module. Vérifiez que le serveur de base de données SQL appartient à un des domaines acceptés :  
  
-   .database.windows.net  
  
-   .cloudapp.net  
  
-   .database.secure.windows.net  
  
|Messages d’exception|  
|------------------------|  
|Domaine SQL non pris en charge.|  
|Le domaine SQL {0} n’est pas actuellement pris en charge dans Azure ML|  
  

## <a name="error-0127"></a>Erreur 0127  
 La taille en pixels de l’image dépasse la limite autorisée  
  
 Cette erreur se produit si vous lisez des images d’un jeu de données d’images à des fins de classification et que le modèle ne peut pas traiter ces images car leur taille est trop importante.  
  
 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  
  
-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->
  
|Messages d’exception|  
|------------------------|  
|La taille en pixels de l’image dépasse la limite autorisée.|  
|La taille en pixels de l’image dans le fichier « {0} » dépasse la limite autorisée : « {1} »|  


## <a name="error-0128"></a>Erreur 0128  
 Le nombre de probabilités conditionnelles dépasse la limite pour les colonnes catégorielles.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Le nombre de probabilités conditionnelles dépasse la limite pour les colonnes catégorielles.|  
|Le nombre de probabilités conditionnelles dépasse la limite pour les colonnes catégorielles. Les colonnes « {0} » et « {1} » posent problème.|  


## <a name="error-0129"></a>Erreur 0129  
 Le nombre de colonnes du jeu de données dépasse la limite autorisée.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Le nombre de colonnes du jeu de données dépasse la limite autorisée.|  
|Le nombre de colonnes du jeu de données « {0} » dépasse la limite autorisée.|  
|Le nombre de colonnes du jeu de données « {0} » dépasse la limite autorisée ({1}).|  
|Le nombre de colonnes du jeu de données « {0} » dépasse la limite autorisée « {1} » de « {2} ».|  
## <a name="error-0130"></a>Erreur 0130  
 Une exception se produit lorsque toutes les lignes du jeu de données de formation contiennent des valeurs manquantes.  
  
 Cette exception se produit lorsque certaines colonnes du jeu de données de formation sont vides.  
  
**Résolution :** Utilisez le module [Nettoyage des données manquantes](clean-missing-data.md) pour supprimer les colonnes dans lesquelles il manque des valeurs.  
  
|Messages d’exception|  
|------------------------|  
|Toutes les lignes du jeu de données de formation contiennent des valeurs manquantes.  Utilisez le module Nettoyage des données manquantes pour corriger les valeurs manquantes.|  
 

## <a name="error-0131"></a>Erreur 0131  
 Une exception se produit si un ou plusieurs jeux de données d’un fichier zip ne sont pas décompressés et inscrits correctement  
  
 Cette erreur se produit lorsqu’un ou plusieurs jeux de données d’un fichier zip ne sont pas décompressés et lus correctement. Vous recevez cette erreur si le décompression échoue parce que le fichier zip lui-même ou l’un des fichiers qu’il contient est endommagé ou si une erreur système survient lors de la décompression et du développement d’un fichier.  
  
**Résolution :** Appuyez-vous sur le message d’erreur pour déterminer comment procéder.  
  
|Messages d’exception|  
|------------------------|  
|Le chargement de jeux de données compressés a échoué|  
|Le jeu de données compressé {0} a échoué avec le message suivant : {1}|  
|Le jeu de données compressé {0} a échoué avec l’exception {1} et le message suivant : {2}|  
  

## <a name="error-0132"></a>Erreur 0132  
 Aucun nom de fichier n’a été spécifié pour la décompression ; plusieurs fichiers ont été trouvés dans le fichier zip.  
  
 Cette erreur se produit lorsque aucun nom de fichier n’a été spécifié pour la décompression ; plusieurs fichiers ont été trouvés dans le fichier zip. Vous recevez cette erreur si le fichier .zip contient plusieurs fichiers compressé alors que vous n’avez pas spécifié de fichier à extraire dans la zone de texte **Dataset to Unpack** (Jeu de données à extraire), dans le volet **Property** (Propriété) du module. Actuellement, un seul fichier peut être extrait à chaque exécution du module.  
  
**Résolution :** Le message d’erreur fournit la liste des fichiers trouvés dans le fichier .zip. Copiez le nom du fichier souhaité et collez-le dans la zone de texte **Dataset to Unpack** (Jeu de données à extraire).  
  
|Messages d’exception|  
|------------------------|  
|Le fichier zip contient plusieurs fichiers ; vous devez spécifier le fichier à développer.|  
|Le fichier contient plusieurs fichiers. Spécifiez le fichier à développer. Les fichiers suivants ont été trouvés : {0}|  
  

## <a name="error-0133"></a>Erreur 0133  
 Le fichier spécifié est introuvable dans le fichier zip  
  
 Cette erreur se produit lorsque le nom de fichier saisi dans le champ **Dataset to Unpack** (Jeu de données à extraire) du volet **Property** (Propriété) ne correspond au nom d’aucun fichier trouvé dans le fichier .zip. Les causes les plus courantes de cette erreur sont une erreur de frappe ou la recherche du fichier d’archive incorrect pour le fichier à développer.  
  
**Résolution :** Réexaminez le module. Si le nom du fichier que vous souhaitez décompresser apparaît dans la liste des fichiers trouvés, copiez le nom de fichier et collez-le dans la zone de propriétés **Dataset to Unpack**  (Jeu de données à extraire). Si le nom du fichier souhaité ne figure pas dans la liste, assurez-vous de disposer du fichier .zip correct et que celui-ci porte le nom attendu.  
  
|Messages d’exception|  
|------------------------|  
|Le fichier spécifié n’a pas été trouvé dans le fichier zip.|  
|Le fichier spécifié est introuvable. Fichier(s) suivant(s) trouvé(s) : {0}|  
  

## <a name="error-0134"></a>Erreur 0134
Une exception se produit lorsqu’une colonne d’étiquette est manquante ou possède un nombre insuffisant de lignes étiquetées.  
  
Cette erreur se produit lorsque le module nécessite une colonne d’étiquette mais que vous n’en avez pas inclus dans la sélection de colonnes ou que le nombre de valeurs est trop faible dans la colonne d’étiquette.

Cette erreur peut également se produire lorsqu’une opération précédente modifie le jeu de données de sorte qu’un nombre insuffisant de lignes est disponible pour une opération en aval. Par exemple, supposons que vous utilisiez une expression dans le module **Partition and Sample** (Partitionner et échantillonner) pour diviser un jeu de données par valeurs. Si aucune correspondance n’est trouvée pour votre expression, l’un des jeux de données résultant de la partition sera vide.

Résolution : 

 Si vous incluez une colonne d’étiquette dans la sélection de colonnes mais qu’elle n’est pas reconnue, utilisez le module [Modifier les métadonnées](edit-metadata.md) pour la marquer en tant que colonne d’étiquette.
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->Ensuite, vous pouvez utiliser le module [Clean Missing Data](clean-missing-data.md) (Nettoyer les données manquantes) pour supprimer les lignes vides dans la colonne d’étiquette. 

 Vérifiez vos jeux de données d’entrée pour vous assurer qu’ils contiennent des données valides et suffisamment de lignes pour satisfaire aux exigences de l’opération. De nombreux algorithmes génèrent un message d’erreur s’ils nécessitent un nombre minimal de lignes de données ; or, les données ne contiennent que quelques lignes ou seulement un en-tête.
  
|Messages d’exception|
|------------------------|
|Une exception se produit lorsqu’une colonne d’étiquette est manquante ou possède un nombre insuffisant de lignes étiquetées.|  
|Une exception se produit lorsqu’une colonne d’étiquette est manquante ou possède moins de {0} lignes étiquetées.|  
  

## <a name="error-0135"></a>Erreur 0135  
 Seul un cluster basé sur un centroïde est pris en charge.  
  
**Résolution :** Cette erreur peut s’afficher si vous avez essayé d’évaluer un modèle de clustering basé sur un algorithme de clustering personnalisé qui n’utilise pas de centroïdes pour initialiser le cluster.  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|Messages d’exception|  
|------------------------|  
|Seul un cluster basé sur un centroïde est pris en charge.|  
  

## <a name="error-0136"></a>Erreur 0136  
 Aucun nom de fichier renvoyé, ce qui empêche de traiter le fichier.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Aucun nom de fichier renvoyé, ce qui empêche de traiter le fichier.|  
  

## <a name="error-0137"></a>Erreur 0137  
 Le kit de développement logiciel (SDK) Stockage Microsoft Azure a rencontré une erreur de conversion entre les propriétés des tables et les colonnes des jeux de données en lecture ou en écriture.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Erreur de conversion entre la propriété du stockage de table Azure et la colonne du jeu de données.|  
|Erreur de conversion entre la propriété du stockage de table Azure et la colonne du jeu de données. Informations supplémentaires : {0}|  

## <a name="error-0138"></a>Erreur 0138  
 Mémoire insuffisante, ce qui empêche l’exécution complète du module. Le sous-échantillonnage du jeu de données peut aider à atténuer le problème.  
  
 Cette erreur se produit lorsque le module en cours d’exécution nécessite plus de mémoire que la mémoire disponible dans le conteneur Azure. Cela peut se produire si vous travaillez avec un jeu de données volumineux et que l’opération en cours ne peut pas tenir en mémoire.  
  
**Résolution :** Si vous essayez de lire un jeu de données volumineux et que l’opération n’aboutit pas, vous pouvez recourir au sous-échantillonnage du jeu de données pour y remédier.  
  
  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  
  
 Sometimes transient loads can lead to this error. Machine support also changes over time. 
  
 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->
  
|Messages d’exception|  
|------------------------|  
|Mémoire insuffisante, ce qui empêche l’exécution complète du module.|  
  

## <a name="error-0139"></a>Erreur 0139  
 Une exception se produit lorsqu’il n’est pas possible de convertir une colonne en un autre type.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous essayez de convertir une colonne vers un type de données différent, mais que ce type n’est pas pris en charge par l’opération actuelle ou par le module.  
  
 L’erreur peut également survenir lorsqu’un module tente de convertir implicitement des données pour répondre aux exigences du module actuelle mais que la conversion n’est pas possible.  
  
**Résolution :**

1. Examinez vos données d’entrée et déterminez le type de données exact de la colonne que vous voulez utiliser et le type de données de la colonne qui produit l’erreur. Parfois, vous pouvez considérer que le type de données est correct, mais constatez qu’une opération en amont a modifié le type de données ou l’utilisation d’une colonne. Utilisez le module [Modifier les métadonnées](edit-metadata.md) pour réinitialiser les métadonnées des colonnes à leur état original. 
2. Consultez la page d’aide du module afin de vérifier la configuration requise pour l’opération spécifiée. Déterminez quels types de données sont pris en charge par le module actuel et quelle plage de valeurs est prise en charge. 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. Déterminez s’il est possible de convertir ou de caster la colonne vers un autre type de données. Les modules suivants offrent tous une flexibilité et une puissance considérables pour la modification des données : 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [Exécutez le script Python](execute-python-script.md).  

> [!NOTE]
> Cela ne fonctionne toujours pas ? Envisagez de fournir des commentaires supplémentaires sur le problème afin de nous aider à rédiger des instructions plus efficaces pour la résolution des problèmes. Il suffit de soumettre vos commentaires sur cette page et d’indiquer le nom du module qui a généré l’erreur, ainsi que le type de conversion de données qui a échoué.
  
|Messages d’exception|  
|------------------------|  
|La conversion n’est pas autorisée.|  
|Impossible de convertir : {0}.|  
|Impossible de convertir : {0}, à la ligne {1}.|  
|Impossible de convertir la colonne de type {0} en colonne de type {1} sur la ligne {2}.|  
|Impossible de convertir la colonne « {2} » de type {0}en colonne de type {1} sur la ligne {3}.|  
|Impossible de convertir la colonne « {2} » de type {0} en colonne « {3} » de type {1} sur la ligne {4}.| 

## <a name="error-0140"></a>Erreur 0140  
 Une exception se produit si l’argument du jeu de colonnes passé ne contient d’autres colonnes hormis la colonne d’étiquette.  
  
 Cette erreur se produit si vous avez connecté un jeu de données à un module qui nécessite plusieurs colonnes, notamment des fonctionnalités, mais que vous n’avez fourni que la colonne d’étiquette.  
  
**Résolution :** Choisissez au moins une colonne de fonctionnalités à inclure dans le jeu de données.  
  
|Messages d’exception|  
|------------------------|  
|Le jeu de colonnes passé ne contient pas d’autres colonnes hormis la colonne d’étiquette.|  
  

## <a name="error-0141"></a>Erreur 0141  
 Une exception se produit si le nombre de colonnes numériques sélectionnées est trop faible et si les valeurs uniques dans les colonnes catégorielles et de chaînes sont trop petites.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque la colonne sélectionnée ne possède pas assez de valeurs uniques pour effectuer l’opération.  
  
**Résolution :** Certaines opérations effectuent des opérations statistiques sur les colonnes de fonctionnalités et de catégories, et si le nombre de valeurs est insuffisant, l’opération peut échouer ou retourner un résultat non valide. Vérifiez votre ensemble de données pour compter les valeurs dans les colonnes de fonctionnalités et d’étiquette et déterminez si l’opération que vous essayez d’effectuer est statistiquement valide.  
  
 Si le jeu de données source est valide, vous pouvez également vérifier si une opération de métadonnées ou de manipulation de données en amont a modifié les données et supprimé certaines valeurs.  
  
 Si les opérations en amont incluent un fractionnement, un échantillonnage ou un rééchantillonnage, vérifiez que le résultat contient le nombre attendu de lignes et de valeurs.  
  
|Messages d’exception|  
|------------------------|  
|Le nombre de colonnes numériques sélectionnées est trop faible et les valeurs uniques dans les colonnes catégorielles et de chaînes sont trop petites.|  
|Le nombre de colonnes numériques sélectionnées est trop faible et les valeurs uniques dans les colonnes catégorielles et de chaînes (actuellement {0}) doivent être au moins égales à {1}|  
  

## <a name="error-0142"></a>Erreur 0142  
 Une exception se produit lorsque le système ne peut pas charger le certificat nécessaire à l’authentification.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Impossible de charger le certificat.|  
|Impossible de charger le certificat {0}. Son empreinte est {1}.|  
  

## <a name="error-0143"></a>Erreur 0143  
 Impossible d’analyser l’URL fournie par l’utilisateur qui est supposée provenir de GitHub.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous spécifiez une URL non valide et que le module nécessite une URL GitHub valide.  
  
**Résolution :** Vérifiez que l’URL fait référence à un référentiel GitHub valide. Les autres types de site ne sont pas pris en charge.  
  
|Messages d’exception|  
|------------------------|  
|L’URL n’émane pas de github.com.|  
|L’URL n’émane pas de github.com : {0}|  

## <a name="error-0144"></a>Erreur 0144  
 La partie attendue est manquante dans l’URL GitHub fournie par l’utilisateur.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous spécifiez une source de fichier GitHub utilisant un format URL non valide.  
  
**Résolution :** Vérifiez que l’URL du référentiel GitHub est valide et qu’elle se termine par \blob\ ou \tree\\.  
  
|Messages d’exception|  
|------------------------|  
|Impossible d’analyser l’URL GitHub.|  
|Impossible d’analyser l’URL GitHub (« \blob\\ » ou « \tree\\ » doit figurer après le nom du référentiel) : {0}|  

## <a name="error-0145"></a>Erreur 0145  
 Impossible de créer le répertoire de réplication pour une raison quelconque.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque le module ne parvient pas à créer le répertoire spécifié.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Impossible de créer le répertoire de réplication.|  
  

## <a name="error-0146"></a>Erreur 0146  
 Lorsque les fichiers utilisateur sont décompressés dans le répertoire local, il arrive que le chemin d’accès combiné soit trop long.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous extrayez des fichiers mais que certains noms de fichier sont trop longs une fois décompressés.  
  
**Résolution :** Modifiez les noms de fichier de manière à ce que le chemin d’accès et le nom de fichier combinés ne dépassent pas 248 caractères.  
  
|Messages d’exception|  
|------------------------|  
|Le chemin d’accès de réplication dépasse 248 caractères, raccourcissez le nom ou le chemin d’accès du script.|  

## <a name="error-0147"></a>Erreur 0147  
 Impossible de télécharger les ressources depuis GitHub pour une raison quelconque  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous ne pouvez pas lire ou télécharger les fichiers spécifiés depuis GitHub.  
  
**Résolution :** Le problème peut être temporaire. Vous pouvez essayer d’accéder aux fichiers ultérieurement. Vous pouvez également vérifier que vous disposez des autorisations nécessaires et que la source est valide.  
  
|Messages d’exception|  
|------------------------|  
|Erreur d’accès à GitHub.|  
|Erreur d’accès à GitHub. {0}|  
  

## <a name="error-0148"></a>Erreur 0148  
 Problèmes d’accès non autorisé lors de l’extraction de données ou de la création du répertoire.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque vous essayez de créer un répertoire ou de lire des données stockées mais que vous ne possédez pas les autorisations nécessaires.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Exception d’accès non autorisé lors de l’extraction des données.|  
  

## <a name="error-0149"></a>Erreur 0149  
 Le fichier utilisateur n’existe pas dans l’offre groupée GitHub.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque le module ne parvient pas à créer le répertoire spécifié.  
  
Résolution : 
  
|Messages d’exception|  
|------------------------|  
|Le fichier GitHub est introuvable.|  
|Le fichier GitHub est introuvable : {0}|  
  

## <a name="error-0150"></a>Erreur 0150  
 Les scripts qui proviennent du package utilisateur n’ont pas pu être décompressés, probablement à cause d’une collision avec des fichiers GitHub.  
  
 Cette erreur se produit dans Azure Machine Learning lorsqu’un script ne peut pas être extrait, en général, lorsqu’il existe un fichier existant de même nom.  
  
Résolution :
  
|Messages d’exception|  
|------------------------|  
|Impossible de décompresser l’offre groupée ; collision de noms possible avec les fichiers GitHub.|  
  

## <a name="error-0151"></a>Erreur 0151  
 Une erreur d’écriture dans le stockage cloud s’est produite. Vérifiez l’URL.  
  
 Cette erreur se produit dans Azure Machine Learning lorsque le module tente d’écrire des données dans un stockage cloud, mais que l’URL est indisponible ou n’est pas valide.  
  
Résolution : Vérifiez l’URL et assurez-vous qu’elle est accessible en écriture.  
  
|Messages d’exception|  
|------------------------|  
|Erreur d’écriture dans le stockage cloud (éventuellement due à une URL incorrecte).|  
|Erreur d’écriture dans le stockage cloud : {0}. Vérifiez l’URL.|  
  
## <a name="error-0152"></a>Erreur 0152  
 Le type de cloud Azure a été spécifié de manière incorrecte dans le contexte du module.  
  
|Messages d’exception|  
|------------------------|  
|Type de cloud Azure incorrect|  
|Type de cloud Azure incorrect : {0}|  
  
## <a name="error-0153"></a>Erreur 0153  
 Le point de terminaison de stockage spécifié n’est pas valide.  
  
|Messages d’exception|  
|------------------------|  
|Type de cloud Azure incorrect|  
|Point de terminaison de stockage incorrect : {0}|  

## <a name="error-0154"></a>Erreur 0154  
 Impossible de résoudre le nom de serveur spécifié  
  
|Messages d’exception|  
|------------------------|  
|Impossible de résoudre le nom de serveur spécifié|  
|Impossible de résoudre le serveur {0}.documents.azure.com spécifié|

## <a name="error-0155"></a>Erreur 0155  
 Le client DocDb a généré une exception  
  
|Messages d’exception|  
|------------------------|  
|Le client DocDb a généré une exception|  
|Client DocDb : {0}|

## <a name="error-0156"></a>Erreur 0156  
 Réponse incorrecte du serveur HCatalog.  
  
|Messages d’exception|  
|------------------------|  
|Réponse incorrecte du serveur HCatalog. Vérifiez que tous les services sont en cours d’exécution.|  
|Réponse incorrecte du serveur HCatalog. Vérifiez que tous les services sont en cours d’exécution. Détails de l’erreur : {0}|

## <a name="error-0157"></a>Erreur 0157  
 Une erreur de lecture s’est produite Azure Cosmos DB en raison de schémas de documents incohérents ou différents. Lecteur nécessite que tous les documents aient le même schéma.  
  
|Messages d’exception|  
|------------------------|  
|Documents détectés avec des schémas différents. Assurez-vous que tous les documents possèdent le même schéma|

## <a name="error-1000"></a>Erreur 1000  
Exception de la bibliothèque interne.  
  
Cette erreur est fournie pour capturer les erreurs internes du moteur qui n’auraient pas été gérées autrement. Par conséquent, la cause de cette erreur peut être différente selon le module qui a généré l’erreur.  
  
Pour obtenir plus d’aide, nous vous recommandons de publier le message détaillé qui accompagne l’erreur sur le forum Azure Machine Learning, en indiquant une description du scénario, y compris les données utilisées comme entrées. Ces commentaires nous aideront à hiérarchiser les erreurs et à identifier les problèmes les plus importants qui feront l’objet de travaux ultérieurs.  
  
|Messages d’exception|  
|------------------------|  
|Exception de la bibliothèque.|  
|Exception de la bibliothèque : {0}|  
|Exception de la bibliothèque {0} : {1}|  
