---
title: Résoudre les erreurs de module
titleSuffix: Azure Machine Learning service
description: Dépannage des exceptions de module dans Azure Machine Learning Studio à l’aide des codes d’erreur
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09a2b616e2bba93be86241c64d37daec7d6dea3b
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029759"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>Exceptions et codes d’erreur pour l’algorithme de & référence de Module

En savoir plus sur les messages d’erreur et les codes d’exception que vous pouvez rencontrer à l’aide de modules dans Azure Machine Learning Studio. 

Pour résoudre ce problème, recherchez l’erreur dans cet article pour en savoir plus sur les causes courantes. Il existe deux façons d’obtenir le texte intégral d’un message d’erreur dans Studio :  
 
- Cliquez sur le lien, **afficher le journal de sortie**, dans le volet droit, faites défiler vers le bas. Le message d’erreur détaillé s’affiche dans les deux dernières lignes de la fenêtre.  
  
- Sélectionnez le module qui a l’erreur, puis cliquez sur le X rouge. Seul le texte d’erreur pertinentes s’affiche.  
  
Si le texte du message n’est pas utile, envoyez-nous des informations sur le contexte et les ajouts souhaitées ou les modifications. Vous pouvez envoyer vos commentaires sur la rubrique de l’erreur, ou visitez le [forum d’Azure Machine Learning STUDIO](https://aka.ms/aml-forum-studio) et publier une question.  


## <a name="error-0001"></a>Erreur 0001  
 Exception se produit si une ou plusieurs colonnes spécifiées du jeu de données n’a pas pu être trouvés.  
  
 Vous recevez cette erreur si une sélection de colonne est effectuée pour un module, mais les ou les colonnes sélectionnées n’existent pas dans le jeu de données d’entrée. Cette erreur peut se produire si vous avez tapé manuellement dans un nom de colonne ou si le sélecteur de colonne a fourni une colonne suggérée n’existait pas dans votre jeu de données lorsque vous avez exécuté l’expérience.  
  
**Résolution :** Réexaminez le module lever cette exception et valider que l’ou les noms de colonne sont corrects et que toutes les colonnes référencées existent.  
  
|Messages d’exception|  
|------------------------|  
|Une ou plusieurs colonnes spécifiées sont introuvables.|  
|Colonne avec l’index ou le nom «{0}» introuvable|  
|Colonne avec le nom ou index «{0}« n’existe pas dans »{1}»|  
 

## <a name="error-0002"></a>Erreur 0002  
 Cette exception est levée si un ou plusieurs paramètres n’a pas pu être analysé ou converti à partir de type spécifié en requis par le type de méthode cible.  
  
 Cette erreur se produit dans Azure Machine Learning quand vous spécifiez un paramètre en tant qu’entrée et le type de valeur est différent du type qui est attendu, et la conversion implicite ne peut pas être effectuée.  
  
**Résolution :** Vérifiez les spécifications des modules et de déterminer quel type de valeur est requis (chaîne, entier, double, etc..)  
  
|Messages d’exception|  
|------------------------|  
|Impossible d’analyser le paramètre|  
|Impossible d’analyser «{0}« paramètre|  
|Impossible d’analyser (convertir) «{0}« paramètre pour »{1}»|  
|Impossible de convertir «{0}« paramètre à partir de »{1}« à »{2}»|  
|Impossible de convertir «{0}« valeur de paramètre »{1}« from »{2}« à »{3}»|  
|Impossible de convertir la valeur «{0}« dans la colonne »{1}« à partir de »{2}« vers »{3}« avec l’utilisation du format »{4}» fournie|  
  

## <a name="error-0003"></a>Erreur 0003  
 Cette exception est levée si une ou plusieurs entrées sont null ou vide.  
  
 Vous recevez cette erreur dans Azure Machine Learning si des entrées ou des paramètres à un module sont null ou vide.  Cette erreur peut se produire, par exemple, lorsque vous n’avez pas tapé dans n’importe quelle valeur pour un paramètre. Il peut également se produire si vous avez choisi un jeu de données qui a des valeurs manquantes, ou un dataset vide.  
  
**Résolution :**
 
+ Ouvrez le module qui a généré l’exception et vérifiez que toutes les entrées ont été spécifiées. Assurez-vous que toutes requises entrées sont spécifiées. 
+ Assurez-vous que les données chargées à partir du stockage Azure sont accessibles et que le nom du compte ou la clé n’a pas changé.  
+ Vérifiez les données d’entrée pour les valeurs manquantes ou null.
+ Si vous utilisez une requête sur une source de données, vérifiez que les données a été renvoyées dans le format prévu. 
+ Recherchez les fautes de frappe ou d’autres modifications dans la spécification de données.
  
|Messages d’exception|  
|------------------------|  
|Un ou plusieurs des entrées sont null ou vide|  
|Entrée «{0}» est null ou vide|  
  

## <a name="error-0004"></a>Erreur 0004  
 Exception se produit si le paramètre est inférieur ou égal à une valeur spécifique.  
  
 Vous recevez cette erreur dans Azure Machine Learning si le paramètre dans le message est inférieure à une valeur de limite requise pour le module traiter les données.  
  
**Résolution :** Réexaminez le module qui lève l’exception et modifiez le paramètre une valeur supérieure à la valeur spécifiée.  
  
|Messages d’exception|  
|------------------------|  
|Paramètre doit être supérieure à la valeur limite.|  
|Paramètre «{0}» valeur doit être supérieure à {1}.|  
|Paramètre «{0}« a la valeur »{1}» qui doit être supérieur à {2}|  
  


## <a name="error-0005"></a>Erreur 0005  
 Exception se produit si le paramètre est inférieur à une valeur spécifique.  
  
 Vous recevez cette erreur dans Azure Machine Learning, si le paramètre dans le message est inférieur ou égal à une valeur de limite requise pour le module traiter les données.  
  
**Résolution :** Réexaminez le module qui lève l’exception et modifiez le paramètre pour être supérieure ou égale à la valeur spécifiée.  
  
|Messages d’exception|  
|------------------------|  
|Paramètre doit être supérieure ou égale à la valeur limite.|  
|Paramètre «{0}» valeur doit être supérieure ou égale à {1}.|  
|Paramètre «{0}« a la valeur »{1}» qui doit être supérieur ou égal à {2}.|  
  

## <a name="error-0006"></a>Erreur 0006  
 Exception se produit si le paramètre est supérieur ou égal à la valeur spécifiée.  
  
 Vous recevez cette erreur dans Azure Machine Learning si le paramètre dans le message est supérieure ou égale à une valeur de limite requise pour le module traiter les données.  
  
**Résolution :** Réexaminez le module qui lève l’exception et modifiez le paramètre pour être inférieure à la valeur spécifiée.  
  
|Messages d’exception|  
|------------------------|  
|Non concordance des paramètres. L’un des paramètres doit être inférieur à un autre.|  
|Paramètre «{0}« la valeur doit être inférieur au paramètre »{1}» valeur.|  
|Paramètre «{0}« a la valeur »{1}» qui doit être inférieure à {2}.|  
  

## <a name="error-0007"></a>Erreur 0007  
 Exception se produit si le paramètre est supérieur à une valeur spécifique.  
  
 Vous recevez cette erreur dans Azure Machine Learning si, dans les propriétés pour le module, vous avez spécifié une valeur qui est supérieure à celle est autorisée. Par exemple, vous pouvez spécifier les données d’un qui est en dehors de la plage de dates prise en charge, ou vous pouvez indiquer que cinq colonnes utilisées lorsque trois colonnes seulement sont disponibles. 
 
 Vous pouvez également voir cette erreur si vous spécifiez deux jeux de données qui doivent correspondre d’une certaine façon. Par exemple, si vous renommez les colonnes et spécifiez les colonnes par index, le nombre de noms que vous fournissez doit correspondre au nombre d’index de colonne. Un autre exemple peut être une opération mathématique qui utilise deux colonnes, où les colonnes doivent avoir le même nombre de lignes. 
  
**Résolution :**
 
 + Ouvrez le module en question et passez en revue les paramètres de propriété numérique.
 + Assurez-vous que les valeurs de paramètre se situent dans la plage de valeurs prise en charge pour cette propriété.
 + Si le module prend plusieurs entrées, assurez-vous que les entrées sont de la même taille.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Vérifiez si la jeu de données ou source de données a changé. Parfois, une valeur qui fonctionnaient avec une version précédente des données échoue après que le nombre de colonnes, les types de données de colonne ou la taille des données a changé.  
  
|Messages d’exception|  
|------------------------|  
|Non concordance des paramètres. L’un des paramètres doit être inférieure ou égale à un autre.|  
|Paramètre «{0}« la valeur doit être inférieure ou égale au paramètre »{1}» valeur.|  
|Paramètre «{0}« a la valeur »{1}» qui doit être inférieure ou égale à {2}.|  
  

## <a name="error-0008"></a>Erreur 0008  
 Exception se produit si le paramètre n’est pas dans la plage.  
  
 Vous recevez cette erreur dans Azure Machine Learning si le paramètre dans le message est en dehors des limites requises pour le module traiter les données.  
  
 Par exemple, cette erreur s’affiche si vous essayez d’utiliser [Add Rows](add-rows.md) pour combiner deux jeux de données qui ont un nombre différent de colonnes.  
  
**Résolution :** Réexaminez le module qui lève l’exception et modifiez le paramètre pour être dans la plage spécifiée.  
  
|Messages d’exception|  
|------------------------|  
|Valeur du paramètre n’est pas dans la plage spécifiée.|  
|Paramètre «{0}» valeur n’est pas dans la plage.|  
|Paramètre «{0}» valeur doit être dans la plage [{1}, {2}].|  
  

## <a name="error-0009"></a>Erreur 0009  
 Exception se produit lorsque le nom de compte de stockage Azure ou le nom du conteneur spécifié est incorrect.  
  
Cette erreur se produit dans Azure Machine Learning Studio lorsque vous spécifiez des paramètres pour un compte de stockage Azure, mais le nom ou le mot de passe ne peut pas être résolu. Erreurs sur les mots de passe ou les noms de compte peuvent se produire pour de nombreuses raisons :
 
 + Le compte est un type incorrect. Certains nouveaux types de comptes ne sont pas pris en charge pour une utilisation avec Machine Learning Studio. Consultez [importer des données](import-data.md) pour plus d’informations.
 + Vous avez entré le nom de compte incorrect
 + Le compte n’existe plus
 + Le mot de passe du compte de stockage est incorrect ou a changé
 + Vous n’avez pas spécifié le nom du conteneur ou le conteneur n’existe pas
 + Vous n’avez pas spécifient le chemin d’accès de fichier (chemin d’accès à l’objet blob)
   
**Résolution :**

Ces problèmes se produisent souvent lorsque vous essayez d’entrer manuellement le chemin d’accès compte nom, mot de passe ou conteneur. Nous vous recommandons d’utiliser l’Assistant Nouvelle pour le [importer des données](import-data.md) module, ce qui vous permet de rechercher et de vérifier les noms.

Vérifiez également si le compte, conteneur ou objet blob a été supprimé. Utilisez un autre utilitaire de stockage Azure pour vérifier que le nom de compte et le mot de passe sont entrés correctement, et que le conteneur existe. 

Certains types de compte plus récente ne sont pas pris en charge par Azure Machine Learning. Par exemple, le nouveau « chaud » ou les types de stockage « froid » ne peut pas être utilisés pour l’apprentissage. Comptes de stockage classiques et comptes de stockage créés comme « Usage général » fonctionne correctement.

Si le chemin d’accès complet à un objet blob a été spécifié, vérifiez que le chemin d’accès est spécifié comme **conteneur/blobname**, et le conteneur et l’objet blob existant dans le compte.  
  
 Le chemin d’accès ne doit pas contenir une barre oblique. Par exemple **/conteneur/blob** est incorrecte et doit être entré en tant que **conteneur/blob**.  

  
|Messages d’exception|  
|------------------------|  
|Le nom de compte de stockage Azure ou le nom du conteneur est incorrect.|  
|Le nom de compte de stockage Azure «{0}« ou un nom de conteneur »{1}» est incorrect ; un nom de conteneur du format conteneur/blob était attendu.|  
  

## <a name="error-0010"></a>Erreur 0010  
 Exception se produit si des jeux de données d’entrée ont des noms de colonne doivent correspondre, mais ne le faites pas.  
  
 Vous recevez cette erreur dans Azure Machine Learning si l’index de colonne dans le message a des noms de colonne différents dans les deux jeux de données d’entrée.  
  
**Résolution :** Utilisez [modifier les métadonnées](edit-metadata.md) ou modifier le jeu de données d’origine pour avoir le même nom de colonne pour l’index de colonne spécifiée.  
  
|Messages d’exception|  
|------------------------|  
|Colonnes avec des index correspondants dans les jeux de données d’entrée ont des noms différents.|  
|Noms de colonnes ne sont pas les mêmes pour la colonne {0} (de base zéro) des jeux de données d’entrée ({1} et {2} respectivement).|  
  

## <a name="error-0011"></a>Erreur 0011  
 Exception se produit si passé argument défini de colonne ne s’applique pas à une des colonnes de jeu de données.  
  
 Vous recevez cette erreur dans Azure Machine Learning si la sélection de la colonne spécifiée ne correspond pas aux colonnes du jeu de données donné.  
  
 Vous pouvez également obtenir cette erreur si vous n’avez pas sélectionné une colonne et au moins une colonne est requise pour le module puisse fonctionner.  
  
**Résolution :** Modifiez la sélection des colonnes dans le module afin qu’elle s’applique aux colonnes du jeu de données.  
  
 Si le module requiert que vous sélectionnez une colonne spécifique, par exemple, une colonne d’étiquette, vérifiez que la colonne de droite est sélectionnée.  
  
 Si les colonnes inappropriés sont sélectionnées, supprimez-les et réexécuter l’expérience.  
  
|Messages d’exception|  
|------------------------|  
|Jeu de colonnes spécifié ne s’applique pas à une des colonnes de jeu de données.|  
|Jeu de colonnes spécifié «{0}» ne s’applique pas aux colonnes du jeu de.|  
  

## <a name="error-0012"></a>Erreur 0012  
 Exception est levée si l’instance de classe n’a pas pu être créée avec le jeu d’arguments transmis.  
  
**Résolution :** Cette erreur n’est pas exploitable par l’utilisateur et sera déconseillée dans une version ultérieure.  
  
|Messages d’exception|  
|------------------------|  
|Non formé du modèle, de former le modèle tout d’abord.|  
|Modèle non formé ({0}), utilisez le modèle formé.|  
  

## <a name="error-0013"></a>Erreur 0013  
 Exception est levée si l’apprenant transmis au module est un type non valide.  
  
 Cette erreur se produit chaque fois qu’un modèle formé est incompatible avec le module de notation connecté. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the experiment is run.  -->
  
**Résolution :**

Déterminer le type d’apprenant qui est généré par le module de formation et déterminer le module de notation qui convient à l’apprenant. 

Si le modèle a été formé à l’aide d’un des modules de formation spécialisée, connectez le modèle formé uniquement pour le module de notation spécialisé correspondant. 


|Type de modèle|Module de formation| Module de notation|
|----|----|----|
|n’importe quel classifieur|[Former le modèle](train-model.md) |[Noter le modèle](score-model.md)|
|n’importe quel modèle de régression|[Former le modèle](train-model.md) |[Noter le modèle](score-model.md)|
<!--| Modèles de clustering| [Modèle de Clustering de train](train-clustering-model.md) ou [Clustering de balayage](sweep-clustering.md)| [Affecter des données aux Clusters](assign-data-to-clusters.md)|
| détection d’anomalie - One-Class SVM | [Modèle de détection d’anomalie de train](train-anomaly-detection-model.md) |[Noter le modèle](score-model.md)|
| détection d’anomalie - PCA |[Former le modèle](train-model.md) |[Noter le modèle](score-model.md) </br> Certaines étapes supplémentaires sont nécessaires pour évaluer le modèle. |
| détection d’anomalie - série chronologique|  [Détection des anomalies de série chronologique](time-series-anomaly-detection.md) |Modèle effectue l’apprentissage à partir des données et génère les scores. Le module ne crée pas un apprenant formé et aucune évaluation supplémentaire n’est nécessaire. |
| modèle de recommandation| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Noter la recommandation Matchbox](score-matchbox-recommender.md) |
| classification d’images | [Classification d’images en Cascade PRÉFORMÉE](pretrained-cascade-image-classification.md) | [Noter le modèle](score-model.md) |
|Modèles Vowpal Wabbit| [Former Vowpal Wabbit Version 7-4 modèle](train-vowpal-wabbit-version-7-4-model.md) | [Score de Vowpal Wabbit Version 7-4 modèle](score-vowpal-wabbit-version-7-4-model.md) |   
|Modèles Vowpal Wabbit| [Former Vowpal Wabbit Version 7 à 10 modèle](train-vowpal-wabbit-version-7-10-model.md) | [Score de Vowpal Wabbit Version 7 à 10 modèle](score-vowpal-wabbit-version-7-10-model.md) |
|Modèles Vowpal Wabbit| [Former le modèle de Version 8 Vowpal Wabbit](score-vowpal-wabbit-version-8-model.md) | [Modèle de score Vowpal Wabbit Version 8](score-vowpal-wabbit-version-8-model.md) |-->
  
|Messages d’exception|  
|------------------------|  
|Apprenant de type non valide est passé.|  
|Apprenant «{0}» a le type non valide.|  


## <a name="error-0014"></a>Erreur 0014  
 Exception se produit si le nombre de valeurs uniques de colonne est supérieur à celle autorisée.  
  
 Cette erreur se produit lorsqu’une colonne contient trop de valeurs uniques.  Par exemple, cette erreur peut s’afficher si vous spécifiez qu’une colonne être gérée comme données catégoriques, mais il existe trop de valeurs uniques dans la colonne pour permettre la fin du traitement. Vous pouvez également voir cette erreur s’il existe une incompatibilité entre le nombre de valeurs uniques dans les deux entrées.   
  
**Résolution :**

Ouvrez le module qui a généré l’erreur et identifier les colonnes utilisées comme entrées. Pour certains modules, vous pouvez cliquer sur le jeu de données d’entrée et sélectionnez **visualiser** pour obtenir des statistiques sur des colonnes individuelles, y compris le nombre de valeurs uniques et leur distribution.

Pour les colonnes que vous souhaitez utiliser pour le regroupement ou la catégorisation, prendre des mesures pour réduire le nombre de valeurs uniques dans les colonnes. Vous pouvez réduire de différentes manières, selon le type de données de la colonne. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Impossible de trouver une résolution correspondant à votre scénario ? Vous pouvez fournir des commentaires sur cette rubrique qui inclut le nom du module qui a généré l’erreur et le type de données et la cardinalité de la colonne. Nous allons utiliser les informations pour fournir plus ciblés étapes pour les scénarios courants de dépannage.   
  
|Messages d’exception|  
|------------------------|  
|Nombre de valeurs uniques de colonne est supérieure à celle autorisée.|  
|Nombre de valeurs uniques dans la colonne : «{0}» dépasse le nombre de tuples {1}.|  
  

## <a name="error-0015"></a>Erreur 0015  
 Exception se produit si la connexion de base de données a échoué.  
  
 Vous recevez cette erreur si vous entrez un nom de compte SQL incorrect, mot de passe, serveur de base de données ou nom de la base de données, ou si une connexion avec la base de données ne peut pas être établie en raison de problèmes avec le serveur ou la base de données.  
  
**Résolution :** Vérifiez que le nom du compte, mot de passe, serveur de base de données et base de données ont été entrées correctement, et que le compte spécifié dispose du niveau d’autorisations approprié. Vérifiez que la base de données est actuellement accessible.  
  
|Messages d’exception|  
|------------------------|  
|Erreur lors de la connexion de base de données.|  
|Erreur lors de la connexion de base de données : {0}.|  
  


## <a name="error-0016"></a>Erreur 0016  
 Exception se produit si des jeux de données d’entrée transmis au module doit-elle avoir des types de colonnes compatibles mais ne le faites pas.  
  
 Vous recevez cette erreur dans Azure Machine Learning si les types de colonnes passés dans deux ou plusieurs jeux de données ne sont pas compatibles entre eux.  
  
**Résolution :** Utilisez [modifier les métadonnées](edit-metadata.md) ou modifier le jeu de données d’entrée d’origine<!--, or use [Convert to Dataset](convert-to-dataset.md)--> Pour vous assurer que les types des colonnes sont compatibles.  
  
|Messages d’exception|  
|------------------------|  
|Colonnes avec des index correspondants dans les jeux de données d’entrée ont des types incompatibles.|  
|Colonnes {0} et {1} ne sont pas compatibles.|  
|Types d’éléments de colonne ne sont pas compatibles pour la colonne {0} (de base zéro) des jeux de données d’entrée ({1} et {2} respectivement).|  
  

## <a name="error-0017"></a>Erreur 0017  
 Exception se produit si une colonne sélectionnée utilise un type de données qui n’est pas pris en charge par le module actuel.  
  
 Par exemple, vous pouvez recevoir cette erreur dans Azure Machine Learning si votre sélection de colonne contient une colonne avec un type de données qui ne peut pas être traité par le module, comme une colonne de chaîne pour une opération mathématique ou une colonne de score où une colonne de caractéristique catégorielle Obligatoire.  
  
**Résolution :**
 1. Identifie la colonne qui est le problème.
 2. Passez en revue la configuration requise du module.
 3. Modifier la colonne pour le rendre conforme aux exigences. Vous devrez peut-être utiliser plusieurs des modules suivants pour apporter des modifications, en fonction de la colonne et la conversion que vous essayez de :
    + Utilisez [modifier les métadonnées](edit-metadata.md) pour modifier le type de données de colonnes, ou pour modifier l’utilisation des colonnes à partir de fonctionnalité numérique, catégoriel à non catégorielles, et ainsi de suite.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. En dernier recours, vous devrez peut-être modifier le jeu de données d’entrée d’origine.

> [!TIP]
> Impossible de trouver une résolution correspondant à votre scénario ? Vous pouvez fournir des commentaires sur cette rubrique qui inclut le nom du module qui a généré l’erreur et le type de données et la cardinalité de la colonne. Nous allons utiliser les informations pour fournir plus ciblés étapes pour les scénarios courants de dépannage. 
  
|Messages d’exception|  
|------------------------|  
|Impossible de traiter la colonne du type actuel. Le type n’est pas pris en charge par le module.|  
|Impossible de traiter la colonne de type {0}. Le type n’est pas pris en charge par le module.|  
|Impossible de traiter la colonne «{1}» de type {0}. Le type n’est pas pris en charge par le module.|  
|Impossible de traiter la colonne «{1}» de type {0}. Le type n’est pas pris en charge par le module. Nom du paramètre : {2}|  
  

## <a name="error-0018"></a>Erreur 0018  
 Exception est levée si le jeu de données d’entrée n’est pas valide.  
  
**Résolution :** Cette erreur dans Azure Machine Learning peut apparaître dans de nombreux contextes, donc il est pas une solution unique. En règle générale, l’erreur indique que les données fournies en tant qu’entrée à un module ont un nombre incorrect de colonnes, ou que le type de données ne correspond pas les exigences du module. Par exemple :   
  
-   Le module a besoin d’une colonne d’étiquette, mais aucune colonne n’est marquée comme une étiquette, ou vous n’avez pas encore sélectionné une colonne d’étiquette.  
  
-   Le module nécessite que les données être catégorielles mais vos données sont numériques.  
  
<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->
  
-   Les données sont dans un format incorrect.  
  
-   Données importées contient des caractères non valides, des valeurs incorrectes, ou valeurs hors limites.  
-   La colonne est vide ou contient trop de valeurs manquantes.  
  
 Pour déterminer la configuration requise et la façon dont vos données peuvent, consultez la rubrique d’aide pour le module qui utilise le jeu de données en tant qu’entrée.  
  
 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  
  
|Messages d’exception|  
|------------------------|  
|Jeu de données n’est pas valide.|  
|{0} contient des données non valides.|  
|{0} et {1} doit être cohérente des colonnes en termes.|  
  

## <a name="error-0019"></a>Erreur 0019  
 Exception se produit si la colonne doit contenir des valeurs triées, mais il n’existe pas.  
  
 Vous recevez cette erreur dans Azure Machine Learning si les valeurs de colonne spécifiée sont en désordre.  
  
**Résolution :** Trier les valeurs de colonne en modifiant manuellement le jeu de données d’entrée, puis réexécutez le module.  
  
|Messages d’exception|  
|------------------------|  
|Valeurs de colonne ne sont pas triées.|  
|Valeurs de colonne «{0}» ne sont pas triés.|  
|Valeurs de colonne «{0}« du jeu de données »{1}» ne sont pas triés.|  
  

## <a name="error-0020"></a>Erreur 0020  
 Exception se produit si le nombre de colonnes dans certains jeux de données transmis au module est trop petit.  
  
 Vous recevez cette erreur dans Azure Machine Learning si ce n’est pas assez de colonnes ont été sélectionnés pour un module.  
  
**Résolution :** Réexaminez le module et vous assurer que le sélecteur de colonne a un nombre correct de colonnes sélectionnées.  
  
|Messages d’exception|  
|------------------------|  
|Nombre de colonnes dans le jeu de données d’entrée est inférieur au minimum autorisé.|  
|Nombre de colonnes dans le jeu de données d’entrée est inférieur au minimum autorisé de {0} ou les colonnes.|  
|Nombre de colonnes dans le jeu de données d’entrée «{0}» est inférieur au minimum autorisé de {1} ou les colonnes.|

## <a name="error-0021"></a>Erreur 0021  
 Exception se produit si le nombre de lignes dans certains jeux de données transmis au module est trop petit.  
  
 Cette erreur dans vu dans Azure Machine Learning lorsqu’il y a pas suffisamment de lignes dans le jeu de données pour effectuer l’opération spécifiée. Par exemple, vous pouvez voir cette erreur si le jeu de données d’entrée est vide, ou si vous essayez d’effectuer une opération qui requiert un nombre minimal de lignes soit valide. Ces opérations peuvent inclure (mais ne sont pas limitées à) regroupement ou la classification basée sur des méthodes statistiques, certains types de compartimentage et de formation avec les nombres.  
  
**Résolution :**
 
 + Ouvrez le module qui a renvoyé l’erreur et vérifiez les propriétés de jeu de données et le module d’entrée. 
 + Vérifiez que le jeu de données d’entrée n’est pas vide et qu’il n’y a suffisamment de lignes de données pour répondre aux exigences décrites dans l’aide du module.  
 + Si vos données sont chargées à partir d’une source externe, assurez-vous que la source de données est disponible et qu’il n’existe aucune erreur ou modifier dans la définition de données qui provoque le processus d’importation obtenir moins de lignes.
 + Si vous effectuez une opération sur les données en amont du module susceptibles d’affecter le type de données ou le nombre de valeurs, telles que le nettoyage, fractionnement, ou d’opérations de jointure, vérifier les résultats de ces opérations pour déterminer le nombre de lignes retournées.  



## <a name="error-0022"></a>Erreur 0022  
 Exception se produit si le nombre de colonnes sélectionnées dans le jeu de données d’entrée n’est pas égal au nombre attendu.  
  
 Cette erreur dans Azure Machine Learning peut se produire lorsque le module en aval ou une opération nécessite un nombre spécifique d’entrées ou de colonnes, et vous avez fourni trop ou trop peu des colonnes ou des entrées. Par exemple :   
  
-   Vous spécifiez une colonne d’étiquette unique ou d’une colonne clé et accidentellement sélectionné plusieurs colonnes.  
  
-   Vous renommez les colonnes, mais fourni des noms plus ou moins qu’il existe de colonnes.  
  
-   Le nombre de colonnes dans la source ou la destination a changé ou ne correspond pas au nombre de colonnes utilisées par le module.  
  
-   Vous avez fourni une liste séparée par des virgules de valeurs pour les entrées, mais le nombre de valeurs ne correspond pas, ou plusieurs entrées ne sont pas pris en charge.  
  
**Résolution :** Réexaminez le module et vérifiez la sélection de colonne pour vous assurer que le nombre correct de colonnes est sélectionné. Vérifiez les sorties des modules en amont et les exigences des opérations en aval.  
  
 Si vous avez utilisé une des options de sélection de colonne que vous peuvent sélectionner plusieurs colonnes (index de colonne, toutes les fonctionnalités, le tout numériques, etc.), valider le nombre exact de colonnes retournées par la sélection.  
  
 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->
  
 Vérifiez que le nombre ou le type de colonnes en amont n’a pas changé.  
  
 Si vous utilisez un jeu de données de recommandation pour former un modèle, souvenez-vous que le Générateur de recommandations attend un nombre limité de colonnes, correspondant aux paires utilisateur-élément ou utilisateur-élément-classements. Supprimer des colonnes supplémentaires avant l’apprentissage du modèle ou en fractionnant les jeux de données de recommandation. Pour plus d’informations, consultez [fractionner les données](split-data.md).  
  
|Messages d’exception|  
|------------------------|  
|Nombre de colonnes sélectionnées dans le jeu de données d’entrée n’est pas égal au nombre attendu.|  
|Nombre de colonnes sélectionnées dans le jeu de données d’entrée n’est pas égale à {0}.|  
|Modèle de sélection de colonne «{0}» fournit le nombre de colonnes sélectionnées dans le jeu de données d’entrée non égal à {1}.|  
|Modèle de sélection de colonne «{0}» est censé fournir {1} ou les colonnes sélectionnées dans le jeu de données d’entrée, mais {2} ou les colonnes est/sont fournis.|  



## <a name="error-0023"></a>Erreur 0023  
 Exception se produit si la colonne cible du jeu de données d’entrée n’est pas valide pour le module de formateur actuel.  
  
 Cette erreur dans Azure Machine Learning se produit si la colonne cible (comme sélectionné dans les paramètres du module) n’est pas du type de données valid, contenant toutes les valeurs manquantes ou non par catégorie comme prévu.  
  
**Résolution :** Réexaminez le module d’entrée pour inspecter le contenu de la colonne étiquette/cible. Assurez-vous qu’il n’a pas de toutes les valeurs manquantes. Si le module attend colonne cible comme catégorique, assurez-vous qu’il existe plusieurs valeurs distinctes dans la colonne cible.  
  
|Messages d’exception|  
|------------------------|  
|Jeu de données d’entrée a pris en charge la colonne cible.|  
|Colonne cible du jeu de données d’entrée a non pris en charge «{0}».|  
|Colonne cible du jeu de données d’entrée a non pris en charge «{0}» pour l’apprenant de type {1}.|  
 

## <a name="error-0024"></a>Erreur 0024  
Exception est levée si le jeu de données ne contient pas de colonne d’étiquette.  

 Cette erreur dans Azure Machine Learning se produit lorsque le module a besoin d’une colonne d’étiquette et le jeu de données n’a pas de colonne d’étiquette. Par exemple, évaluation d’un jeu de données évalué nécessite généralement que la colonne d’étiquette est présente pour calculer les mesures de précision.  
 
Il peut également se produire qu’une colonne d’étiquette n’est présente dans le jeu de données, mais pas correctement détecté par Azure Machine Learning.
  
**Résolution :**

+ Ouvrez le module qui a généré l’erreur et déterminer si une colonne d’étiquette est présente. Le type de données ou de nom de la colonne importe peu, tant que la colonne contient un seul résultat (ou variable dépendante) que vous tentez de prédire. Si vous n’êtes pas sûr de la colonne qui a l’étiquette, recherchez un nom générique comme *classe* ou *cible*. 
+  Si le jeu de données n’inclut pas de colonne d’étiquette, il est possible que la colonne d’étiquette a été explicitement ou accidentellement supprimée en amont. Cela peut également être que le jeu de données n’est pas la sortie d’un module de notation en amont.
+ Pour marquer explicitement la colonne comme colonne d’étiquette, ajoutez le [modifier les métadonnées](edit-metadata.md) module et connectez le jeu de données. Sélectionnez la colonne d’étiquette, puis **étiquette** à partir de la **champs** liste déroulante. 
+ Si la mauvaise colonne est choisie comme l’étiquette, vous pouvez sélectionner **effacer l’étiquette** à partir de la **champs** pour corriger les métadonnées sur la colonne. 
  
|Messages d’exception|  
|------------------------|  
|Il n’existe aucune colonne d’étiquette dans le jeu de données.|  
|Il n’existe aucune colonne d’étiquette dans «{0}».|  
  

## <a name="error-0025"></a>Erreur 0025  
 Exception est levée si le jeu de données ne contient pas de colonne de score.  
  
 Cette erreur dans Azure Machine Learning se produit si l’entrée au modèle evaluate ne contient-elle pas de score valide colonnes. Par exemple, l’utilisateur tente d’évaluer un jeu de données avant qu’il a été noté avec un modèle formé correct, ou la colonne de score a été supprimée explicitement en amont. Cette exception se produit également si les colonnes de score sur deux jeux de données ne sont pas compatibles. Par exemple, vous pouvez essayer comparer la précision de régresseur linéaire avec celui d’un classifieur binaire.  
  
**Résolution :** Revoir l’entrée dans le modèle d’évaluer et examiner si elle contient une ou plusieurs colonnes de score. Si ce n’est pas le cas, le jeu de données n’était pas noté ou les colonnes de score, ont été supprimés dans un module en amont.  
  
|Messages d’exception|  
|------------------------|  
|Il n’existe aucune colonne de score dans le jeu de données.|  
|Il n’existe aucune colonne de score dans «{0}».|  
|Il n’existe aucune colonne de score dans «{0}» qui est généré par un «{1}». Noter le jeu de données à l’aide du type correct d’apprenant.|  
  

## <a name="error-0026"></a>Erreur 0026  
 Exception se produit si les colonnes portant le même nom ne sont pas autorisées.  
  
 Cette erreur dans Azure Machine Learning se produit si plusieurs colonnes portent le même nom. Une façon, vous pouvez recevoir cette erreur est si le jeu de données ne dispose pas d’une ligne d’en-tête et les noms de colonne sont automatiquement attribués : Col0, Col1, etc.  
  
**Résolution :** Si les colonnes portent le même nom, insérez un [modifier les métadonnées](edit-metadata.md) module entre le jeu de données d’entrée et le module. Utilisez le sélecteur de colonne dans [modifier les métadonnées](edit-metadata.md) pour sélectionner les colonnes à renommer en tapant le nouveau nom dans la **nouveaux noms de colonnes** zone de texte.  
  
|Messages d’exception|  
|------------------------|  
|Noms de colonnes identiques sont spécifiés dans des arguments. Noms de colonnes identiques ne sont pas autorisés par le module.|  
|Les noms de colonnes dans les arguments identiques »{0}« et »{1}» ne sont pas autorisés. Spécifier des noms différents.|  
  

## <a name="error-0027"></a>Erreur 0027  
 Exception levée lorsque deux objets doivent avoir la même taille, mais ne sont pas.  
  
 Ceci est une erreur courante dans Azure Machine Learning et peut être dû à de nombreuses conditions.  
  
**Résolution :** Il n’existe aucune solution spécifique. Toutefois, vous pouvez vérifier des conditions telles que les éléments suivants :  
  
-   Si vous renommez les colonnes, assurez-vous que chaque liste (les colonnes d’entrée et la liste des nouveaux noms) le même nombre d’éléments.  
  
-   Si vous joindre ou concaténer deux jeux de données, assurez-vous qu’ils ont le même schéma.  
  
-   Si vous joignez deux jeux de données qui ont plusieurs colonnes, assurez-vous que les colonnes clés ont les mêmes données de types et sélectionnez l’option **autorise pas les doublons et conserver l’ordre des colonnes dans la sélection**.  
  
|Messages d’exception|  
|------------------------|  
|La taille des objets transmis est incohérente.|  
|La taille de «{0}« est incohérent avec la taille de »{1}».|  
  

## <a name="error-0028"></a>Erreur 0028  
 Exception se produit dans le cas le jeu de colonnes contient des noms de colonne en double et n’est pas autorisée.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque les noms de colonnes sont dupliqués ; Autrement dit, n’est pas unique.  
  
**Résolution :** Si toutes les colonnes ont même nom, ajoutez une instance de [modifier les métadonnées](edit-metadata.md) entre le jeu de données d’entrée et le module qui provoque l’erreur. Utilisez le sélecteur de colonne dans [modifier les métadonnées](edit-metadata.md) pour sélectionner les colonnes à renommer, puis tapez les nouveaux noms de colonnes dans le **nouveaux noms de colonnes** zone de texte. Si vous renommez plusieurs colonnes, vérifiez que les valeurs que vous tapez dans la **nouveaux noms de colonnes** sont uniques.  
  
|Messages d’exception|  
|------------------------|  
|Jeu de colonnes contient des noms de colonne en double.|  
|Le nom «{0}» est dupliqué.|  
|Le nom «{0}« est dupliqué dans »{1}».|  
  

## <a name="error-0029"></a>Erreur 0029  
 Exception levée lorsqu’un URI non valide est passé.  
  
 Cette erreur dans Azure Machine Learning est levée lorsqu’un URI non valide est passé.  Vous recevez cette erreur si une des conditions suivantes est vraie :, ou.  
  
-   Le Public ou un URI SAS fourni pour le stockage Blob Azure pour la lecture ou écriture contient une erreur.  
  
-   La fenêtre de temps pour les associations de sécurité a expiré.  
  
-   L’URL Web via une source HTTP représente un fichier ou un URI de bouclage.  
  
-   L’URL Web via HTTP contient une URL au format incorrect.  
  
-   L’URL ne peut pas être résolu par la source à distance.  
  
**Résolution :** Réexaminez le module et vérifier le format de l’URI. Si la source de données est une URL Web via HTTP, vérifiez que la source prévue n’est pas un fichier ou un bouclage URI (localhost).  
  
|Messages d’exception|  
|------------------------|  
|Uri non valide est passé.|  
  

## <a name="error-0030"></a>Erreur 0030  
 Exception se produit dans le cas lorsqu’il n’est pas possible de télécharger un fichier.  
  
 Cette exception dans Azure Machine Learning se produit lorsqu’il n’est pas possible de télécharger un fichier. Vous recevrez cette exception lorsqu’une lecture a été lancée à partir d’une source HTTP a échoué après une nouvelle tentative de trois (3) tentatives.  
  
**Résolution :** Vérifiez que l’URI à la source HTTP est correct et que le site est actuellement accessible via Internet.  
  
|Messages d’exception|  
|------------------------|  
|Impossible de télécharger un fichier.|  
|Erreur lors du téléchargement du fichier : {0}.|  
  

## <a name="error-0031"></a>Erreur 0031  
 Exception se produit si le nombre de colonnes dans le jeu de colonnes est inférieur au besoin.  
  
 Cette erreur dans Azure Machine Learning se produit si le nombre de colonnes sélectionnées est inférieur que nécessaire.  Vous recevez cette erreur si la valeur minimale nécessaire le nombre de colonnes n’est pas sélectionné.  
  
**Résolution :** Ajouter des colonnes supplémentaires à la sélection de colonne à l’aide de la **le sélecteur de colonne**.  
  
|Messages d’exception|  
|------------------------|  
|Nombre de colonnes dans le jeu de colonnes est inférieur au nombre requis.|  
|{0} ou les colonnes doivent être spécifiés. Le nombre réel de colonnes spécifiés est {1}.|  

## <a name="error-0032"></a>Erreur 0032  
 Exception est levée si l’argument n’est pas un nombre.  
  
 Vous recevez cette erreur dans Azure Machine Learning si l’argument est un double ou NaN.  
  
**Résolution :** Modifiez l’argument spécifié pour utiliser une valeur valide.  
  
|Messages d’exception|  
|------------------------|  
|Argument n’est pas un nombre.|  
|«{0}» n’est pas un nombre.|  
  

## <a name="error-0033"></a>Erreur 0033  
 Exception est levée si l’argument est l’infini.  
  
 Cette erreur dans Azure Machine Learning se produit si l’argument est infini. Vous recevez cette erreur si l’argument est `double.NegativeInfinity` ou `double.PositiveInfinity`.  
  
**Résolution :** Modifiez l’argument spécifié pour être une valeur valide.  
  
|Messages d’exception|  
|------------------------|  
|Argument doit être fini.|  
|«{0}» n’est pas fini.|  
  

## <a name="error-0034"></a>Erreur 0034  
 Exception se produit si plusieurs évaluations existent pour une paire utilisateur-élément donnée.  
  
 Cette erreur dans Azure Machine Learning se produit dans la recommandation si une paire utilisateur-élément a la valeur de plusieurs évaluation.  
  
**Résolution :** Assurez-vous que la paire utilisateur-élément possède une valeur d’évaluation uniquement.  
  
|Messages d’exception|  
|------------------------|  
|Plusieurs notations existent pour l’ou les valeurs dans le jeu de données.|  
|Plusieurs notations pour utilisateur {0} et élément {1} dans la table de données de prédiction de notation.|  
  

## <a name="error-0035"></a>Erreur 0035  
 Exception est levée si aucune fonctionnalité ont été fournies pour un élément ou un utilisateur donné.  
  
 Cette erreur dans Azure Machine Learning se produit vous essayez d’utiliser un modèle de recommandation pour calculer les scores, mais un vecteur de fonctionnalité est introuvable.  
  
**Résolution :**

La recommandation de Matchbox a certaines exigences qui doivent être remplies lors de l’utilisation des fonctionnalités de l’élément ou de fonctionnalités de l’utilisateur.  Cette erreur indique qu’un vecteur de fonctionnalité est manquant pour un utilisateur ou un élément que vous avez fourni comme entrée.  Vous devez vous assurer qu’un vecteur de fonctionnalités est disponible dans les données pour chaque utilisateur ou d’un élément.  
  
 Par exemple, si vous avez formé une recommandation de modèle à l’aide des fonctionnalités telles que l’âge, l’adresse ou le revenu de l’utilisateur, mais que vous souhaitez maintenant créer des scores pour les nouveaux utilisateurs qui n’étaient pas observés pendant la formation, vous devez fournir un ensemble équivalent de fonctionnalités (à savoir, âge, l’emplacement, et valeurs de revenu) pour les nouveaux utilisateurs afin d’effectuer des prévisions appropriées pour eux. 
 
 Si vous n’avez pas toutes les fonctionnalités pour ces utilisateurs, envisagez d’ingénierie des fonctionnalités pour générer des fonctionnalités appropriées.  Par exemple, si vous n’avez pas de valeurs d’âge ou le revenu utilisateur individuel, vous pouvez générer des valeurs approximatives à utiliser pour un groupe d’utilisateurs. 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > Résolution non applicable à votre cas ? Vous pouvez envoyer des commentaires sur cet article et fournissent des informations sur le scénario, y compris le module et le nombre de lignes dans la colonne. Nous utiliserons ces informations pour fournir plus détaillée des étapes de dépannage à l’avenir.
   
|Messages d’exception|  
|------------------------|  
|Aucune fonctionnalité ont été fournies pour un élément ou d’utilisateur requis.|  
|Fonctionnalités pour {0} requise mais non fournie.|  
  

## <a name="error-0036"></a>Erreur 0036  
 Exception se produit si plusieurs vecteurs de caractéristiques ont été fournis pour un élément ou un utilisateur donné.  
  
 Cette erreur dans Azure Machine Learning se produit si un vecteur de fonctionnalité est défini plusieurs fois.  
  
**Résolution :** Assurez-vous que le vecteur de fonctionnalité n’est pas défini plusieurs fois.  
  
|Messages d’exception|  
|------------------------|  
|Définition de fonctionnalité pour un utilisateur ou un élément en double.|  
|Définition en double fonctionnalité pour {0}.|  
  

## <a name="error-0037"></a>Erreur 0037  
 Exception se produit si plusieurs colonnes d’étiquettes sont spécifiées et qu’une seule est autorisée.  
  
 Cette erreur dans Azure Machine Learning se produit si plusieurs colonnes est sélectionné pour être la nouvelle colonne d’étiquette. Plus des algorithmes d’apprentissage supervisé nécessitent une seule colonne à marquer comme la cible ou l’étiquette.  
  
**Résolution :** Veillez à sélectionner une seule colonne en tant que la nouvelle colonne d’étiquette.  
  
|Messages d’exception|  
|------------------------|  
|Plusieurs colonnes d’étiquettes sont spécifiées.|  
  

## <a name="error-0038"></a>Erreur 0038  
 Exception se produit si le nombre d’éléments attendus doit être une valeur exacte, mais n’est pas.  
  
 Cette erreur dans Azure Machine Learning se produit si le nombre d’éléments attendus doit être une valeur exacte, mais n’est pas.  Vous recevez cette erreur si le nombre d’éléments n’est pas égal à la valeur attendue valide.  
  
**Résolution :** Modifiez l’entrée pour que le nombre correct d’éléments.  
  
|Messages d’exception|  
|------------------------|  
|Nombre d’éléments n’est pas valide.|  
|Nombre d’éléments dans «{0}» n’est pas valide.|  
|Nombre d’éléments dans «{0}» n’est pas égal au nombre valide de {1} élément (s).|  
  

## <a name="error-0039"></a>Erreur 0039  
 Exception se produit si une opération a échoué.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’une opération interne ne peut pas être terminée.  
  
**Résolution :** Cette erreur est due à de nombreuses conditions, et il n’existe aucune solution spécifique.  
 Le tableau suivant contient les messages génériques pour cette erreur, qui sont suivies d’une description de la condition spécifique. 
 
 Si aucun détail n’est disponible, [envoyer des commentaires](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) et fournissent des informations sur les modules qui a généré l’erreur et les conditions associées.
  
|Messages d’exception|  
|------------------------|  
|L’opération a échoué.|  
|Erreur lors de l’exécution de l’opération : {0}.|  
  

## <a name="error-0040"></a>Erreur 0040  
 Exception se produit lors de l’appel d’un module désapprouvé.  
  
 Cette erreur dans Azure Machine Learning se produit lors de l’appel d’un module désapprouvé.  
  
**Résolution :** Remplacer le module déconseillé par une prise en charge. Consultez le journal de sortie de module pour les informations sur le module à utiliser à la place.  
  
|Messages d’exception|  
|------------------------|  
|L’accès à module déconseillé.|  
|Module «{0}» est déconseillée. Utiliser le module «{1}» à la place.|  
 

## <a name="error-0041"></a>Erreur 0041  
 Exception se produit lors de l’appel d’un module désapprouvé.  
  
 Cette erreur dans Azure Machine Learning se produit lors de l’appel d’un module désapprouvé.  
  
**Résolution :** Remplacez le module déconseillé avec un ensemble de ceux pris en charge. Ces informations doivent être visibles dans le journal de sortie de module.  
  
|Messages d’exception|  
|------------------------|  
|L’accès à module déconseillé.|  
|Module «{0}» est déconseillée. Utiliser les modules «{1}» pour la fonctionnalité demandée.|  
 

## <a name="error-0042"></a>Erreur 0042  
 Exception se produit lorsqu’il n’est pas possible de convertir la colonne vers un autre type.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’il n’est pas possible de convertir la colonne dans le type spécifié.  Vous recevez cette erreur si un module a besoin d’un type de données particulier, tels que datetime, texte, un nombre à virgule flottante ou entier, mais il n’est pas possible de convertir une colonne existante dans le type requis.  
 
Par exemple, vous pourrez sélectionner une colonne et essayez de convertir en un type de données numérique pour une utilisation dans une opération mathématique et obtenez cette erreur si la colonne contenait des données non valides. 

Une autre raison, que vous pouvez obtenir cette erreur si vous essayez d’utiliser une colonne contenant des nombres à virgule flottante ou nombreuses valeurs uniques sous la forme d’une colonne catégorielle. 
  
**Résolution :**

+ Ouvrez la page d’aide pour le module qui a généré l’erreur et vérifier les exigences de type de données.
+ Passez en revue les types de données des colonnes dans le jeu de données d’entrée.
+ Inspecter les données provenant de sources de ce que l'on appelle données sans schéma.
+ Vérifiez le jeu de données pour les valeurs manquantes ou des caractères spéciaux qui peuvent bloquer la conversion en type de données souhaité. 
    + Types de données numérique doivent être cohérentes : par exemple, recherchez les nombres à virgule flottante dans une colonne d’entiers.
    + Recherchez les chaînes de texte ou les valeurs NA dans une colonne numérique. 
    + Valeurs booléennes peuvent être convertis en une représentation appropriée en fonction du type de données requises.
    + Examinez les colonnes de texte pour caractères non-unicode, les caractères de tabulation ou les caractères de contrôle
    + Données de date/heure doivent être cohérentes pour éviter les erreurs de modélisation, mais le nettoyage peut s’avérer complexe en raison du grand nombre de formats. Envisagez d’utiliser <!--the [Execute R Script](execute-r-script.md) or -->[Exécutez le Script Python](execute-python-script.md) modules permettent d’effectuer le nettoyage.  
+ Si nécessaire, modifiez les valeurs dans le jeu de données d’entrée afin que la colonne peut être convertie correctement. Modification peut inclure le compartimentage, la troncation ou d’opérations d’arrondi, élimination de valeurs hors norme ou imputation des valeurs manquantes. Consultez les articles suivants pour des scénarios de transformation de données courants dans machine learning :
    + [Nettoyage des données manquantes](clean-missing-data.md)
    + [Normaliser les données](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->
 
> [!TIP]
> Résolution claire ou non applicable à votre cas ? Vous pouvez envoyer des commentaires sur cet article et fournissent des informations sur le scénario, y compris le module et le type de données de la colonne. Nous utiliserons ces informations pour fournir plus détaillée des étapes de dépannage à l’avenir.  
  
|Messages d’exception|  
|------------------------|  
|Conversion non autorisée.|  
|Impossible de convertir la colonne de type {0} à la colonne de type {1}.|  
|Impossible de convertir la colonne «{2}» de type {0} à la colonne de type {1}.|  
|Impossible de convertir la colonne «{2}» de type {0} pour la colonne «{3}» de type {1}.|  
  

## <a name="error-0043"></a>Erreur 0043  
 Exception se produit lorsque le type d’élément n’implémente pas explicitement Equals.  
  
 Cette erreur dans Azure Machine Learning n’est pas utilisée et sera déconseillée.  
  
**Résolution :** Aucune.  
  
|Messages d’exception|  
|------------------------|  
|Aucune méthode accessible explicite ne Equals est trouvé.|  
|Impossible de comparer les valeurs de colonne \\»{0}\\» de type {1}. Aucune méthode accessible explicite ne Equals est trouvé.|  


## <a name="error-0044"></a>Erreur 0044  
 Exception se produit lorsqu’il n’est pas possible de dériver un type d’élément de colonne à partir de valeurs existantes.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’il n’est pas possible de déduire le type d’une ou plusieurs colonnes dans un jeu de données. Cela se produit généralement lors de la concaténation de deux ou plusieurs jeux de données avec différents types d’éléments. Si Azure Machine Learning est impossible de déterminer un type commun qui est en mesure de représenter toutes les valeurs dans une ou plusieurs colonnes sans perte d’informations, il génère cette erreur.  
  
**Résolution :** Assurez-vous que toutes les valeurs dans une colonne donnée dans les deux jeux de données combinées sont soit du même type (numérique, booléen, par catégorie, chaîne, date, etc.) ou peut être forcé au même type.  
  
|Messages d’exception|  
|------------------------|  
|Impossible de dériver le type d’élément de la colonne.|  
|Impossible de dériver le type d’élément pour la colonne «{0}»--tous les éléments sont des références null.|  
|Impossible de dériver le type d’élément pour la colonne «{0}« du jeu de données »{1}»--tous les éléments sont des références null.|  
  

## <a name="error-0045"></a>Erreur 0045  
 Exception se produit lorsqu’il n’est pas possible de créer une colonne en raison de types d’éléments mixtes dans la source.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque les types d’éléments des deux jeux de données combinées sont différentes.  
  
**Résolution :** Assurez-vous que toutes les valeurs dans une colonne donnée dans les deux jeux de données combinées sont du même type (numérique, booléen, par catégorie, chaîne, date, etc.).  
  
|Messages d’exception|  
|------------------------|  
|Impossible de créer la colonne avec les types d’éléments mixtes.|  
|Ne peut pas créer de colonne avec l’ID «{0}» des types d’éléments mixtes : \n\tType de données [{1}, {0}] est {2}\n\tType de données [{3}, {0}] est {4}.|  
  

## <a name="error-0046"></a>Erreur 0046  
 Exception se produit lorsqu’il n’est pas possible de créer un répertoire sur le chemin d’accès spécifié.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’il n’est pas possible de créer un répertoire sur le chemin d’accès spécifié. Vous recevez cette erreur si n’importe quelle partie du chemin vers le répertoire de sortie pour une requête Hive est incorrect ou inaccessible.  
  
**Résolution :** Réexaminez le module et vérifiez que le chemin du répertoire est correctement mise en forme et qu’il est accessible avec les informations d’identification actuelles.  
  
|Messages d’exception|  
|------------------------|  
|Spécifiez un répertoire de sortie valide.|  
|Répertoire : {0} ne peut pas être créé. Spécifiez le chemin d’accès valide.|  
  

## <a name="error-0047"></a>Erreur 0047  
 Exception se produit si le nombre de colonnes de fonctionnalités dans certains jeux de données transmis au module est trop petit.  
  
 Cette erreur dans Azure Machine Learning se produit si le jeu de données d’entrée à une formation ne contient pas le nombre minimal de colonnes requises par l’algorithme. En règle générale, le jeu de données est vide ou contient uniquement des colonnes de la formation.  
  
**Résolution :** Réexaminez le jeu de données d’entrée pour rendre bien sûr qu’un ou plusieurs des colonnes supplémentaires en dehors de la colonne d’étiquette.  
  
|Messages d’exception|  
|------------------------|  
|Nombre de colonnes de fonctionnalités dans le jeu de données d’entrée est inférieur au minimum autorisé.|  
|Nombre de colonnes de fonctionnalités dans le jeu de données d’entrée est inférieur au minimum autorisé de {0} ou les colonnes.|  
|Nombre de colonnes de fonctionnalités dans le jeu de données d’entrée «{0}» est inférieur au minimum autorisé de {1} ou les colonnes.|  
  

## <a name="error-0048"></a>Erreur 0048  
 Exception se produit dans le cas lorsqu’il n’est pas possible d’ouvrir un fichier.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’il n’est pas possible d’ouvrir un fichier pour la lecture ou d’écriture. Vous pouvez recevoir cette erreur pour ces raisons :  
  
-   Le conteneur ou le fichier (blob) n’existe pas  
  
-   Le niveau d’accès du fichier ou du conteneur ne vous permet pas d’accès au fichier  
  
-   Le fichier est trop volumineux pour être en lecture ou d’un format incorrect  
  
**Résolution :** Réexaminez le module et le fichier que vous voulez lire.  
  
 Vérifiez que les noms du conteneur et du fichier sont corrects.  
  
 Utilisez le portail Azure classic ou un outil de stockage Azure pour vérifier que vous êtes autorisé à accéder au fichier.  
  
  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->
  
|Messages d’exception|  
|------------------------|  
|Impossible d’ouvrir un fichier.|  
|Erreur lors de l’ouverture du fichier : {0}.|  


## <a name="error-0049"></a>Erreur 0049  
 Exception se produit dans le cas lorsqu’il n’est pas possible d’analyser un fichier.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’il n’est pas possible d’analyser un fichier. Vous recevez cette erreur si le format de fichier sélectionné dans le [importer des données](import-data.md) module ne correspond pas au format réel du fichier, ou si le fichier contient un caractère non reconnaissable.  
  
**Résolution :** Réexaminez le module et corriger la sélection du format de fichier si elle ne correspond pas au format du fichier. Si possible, examinez le fichier pour confirmer qu’il ne contient pas de caractères non autorisés.  
  
|Messages d’exception|  
|------------------------|  
|Impossible d’analyser un fichier.|  
|Erreur lors de l’analyse du fichier : {0}.|  
  

## <a name="error-0050"></a>Erreur 0050  
 Cette exception est levée dans le cas lorsque l’entrée et les fichiers de sortie sont identiques.  
  
**Résolution :** Cette erreur dans Azure Machine Learning n’est pas utilisée et sera déconseillée.  
  
|Messages d’exception|  
|------------------------|  
|Les fichiers spécifiés pour l’entrée et de sortie ne peut pas être le même.|


## <a name="error-0051"></a>Erreur 0051  
 Exception se produit dans le cas lorsque plusieurs fichiers de sortie sont identiques.  
  
**Résolution :** Cette erreur dans Azure Machine Learning n’est pas utilisée et sera déconseillée.  
  
|Messages d’exception|  
|------------------------|  
|Les fichiers spécifiés pour les sorties ne peuvent pas être identiques.|


## <a name="error-0052"></a>Erreur 0052  
 Exception est levée si la clé de compte de stockage Azure spécifiée est incorrect.  
  
 Cette erreur dans Azure Machine Learning se produit si la clé utilisée pour accéder au compte de stockage Azure est incorrecte. Par exemple, vous pouvez voir cette erreur si la clé de stockage Azure ont été tronquée lors de la copie et collage, ou si une clé erronée a été utilisée.  
  
 Pour plus d’informations sur la façon d’obtenir la clé pour un compte de stockage Azure, consultez [affichage, copie et régénération de clés d’accès de stockage](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  
  
**Résolution :** Réexaminez le module et vérifiez que la clé de stockage Azure est correcte pour le compte. Copiez la clé à nouveau à partir du portail Azure classic si nécessaire.  
  
|Messages d’exception|  
|------------------------|  
|La clé de compte de stockage Azure est incorrecte.|  
  

## <a name="error-0053"></a>Erreur 0053  
 Exception se produit dans le cas lorsqu’il n’y a aucun utilisateur ou des articles pour les recommandations de matchbox.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’un vecteur de fonctionnalité est introuvable.  
  
**Résolution :** Assurez-vous qu’un vecteur de fonctionnalité est présent dans le jeu de données d’entrée.  
  
|Messages d’exception|  
|------------------------|  
|Fonctionnalités de l’utilisateur ou / et les éléments sont requise mais non fournies.|  

## <a name="error-0054"></a>Erreur 0054  
 Exception se produit s’il existe trop peu de valeurs distinctes dans la colonne pour terminer l’opération.  
  
**Résolution :** Cette erreur dans Azure Machine Learning n’est pas utilisée et sera déconseillée.  
  
|Messages d’exception|  
|------------------------|  
|Données ont trop peu de valeurs distinctes dans la colonne spécifiée pour terminer l’opération.|  
|Données ont trop peu de valeurs distinctes dans la colonne spécifiée pour terminer l’opération. Le minimum requis correspond {0} éléments.|  
|Données ont trop peu de valeurs distinctes dans la colonne «{1}» pour terminer l’opération. Le minimum requis correspond {0} éléments.|  
  

## <a name="error-0055"></a>Erreur 0055  
 Exception se produit lors de l’appel d’un module désapprouvé.  Cette erreur dans Azure Machine Learning s’affiche si vous essayez d’appeler un module qui a été déconseillé.
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|L’accès à module déconseillé.|  
|Module «{0}» est déconseillée.|  

## <a name="error-0056"></a>Erreur 0056  
 Exception se produit si les colonnes que vous avez sélectionné pour une opération ne respecte pas la configuration requise.  
  
 Cette erreur dans Azure Machine Learning se produit lors du choix des colonnes pour une opération qui nécessite que la colonne soit d’un type de données particulier. 
 
 Cette erreur peut également se produire si la colonne est le type de données, mais le module que vous utilisez nécessite que la colonne également être marqués comme une fonctionnalité, une étiquette ou une colonne catégorielle.  
  
  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->
  
**Résolution :**
  
1.  Passez en revue le type de données des colonnes qui sont actuellement sélectionnés. 

2. Déterminer si les colonnes sélectionnées sont catégorielles, étiquette, ou colonnes de fonctionnalités.  
  
3.  Passez en revue la rubrique d’aide pour le module dans lequel vous avez la sélection des colonnes, pour déterminer s’il existe des exigences spécifiques pour l’utilisation de type ou une colonne de données.  
  
3.  Utilisez [modifier les métadonnées](edit-metadata.md) pour modifier le type de colonne pour la durée de cette opération. Veillez à modifier le type de colonne à sa valeur d’origine, à l’aide d’une autre instance de [modifier les métadonnées](edit-metadata.md), si vous avez besoin pour les opérations en aval.  
  
|Messages d’exception|  
|------------------------|  
|Une ou plusieurs colonnes sélectionnées n’étaient pas dans une catégorie autorisée.|  
|Colonne portant le nom «{0}» n’est pas dans une catégorie autorisée.|  
  

## <a name="error-0057"></a>Erreur 0057  
 Exception se produit lorsque vous tentez de créer un fichier ou un objet blob qui existe déjà.  
  
 Cette exception se produit lorsque vous utilisez le [exporter des données](export-data.md) module ou un autre module pour enregistrer les résultats d’une expérience dans Azure Machine Learning dans le stockage blob Azure, mais que vous tentez de créer un fichier ou un objet blob qui existe déjà.   
  
**Résolution :**
 
 Vous recevez cette erreur uniquement si vous définissez la propriété pour la précédemment **stockage blob Azure en mode écriture** à **erreur**. Par défaut, ce module génère une erreur si vous tentez d’écrire un jeu de données dans un objet blob qui existe déjà.
 
 - Ouvrez les propriétés du module et de modifier la propriété **stockage blob Azure en mode écriture** à **remplacer**.
 - Vous pouvez également, tapez le nom d’un fichier ou un objet blob de destination différent et veillez à spécifier un objet blob qui n’existe pas déjà.  
  
|Messages d’exception|  
|------------------------|  
|Fichier ou un objet Blob existe déjà.|  
|Fichier ou un objet Blob «{0}» existe déjà.|  
  

## <a name="error-0058"></a>Erreur 0058  
 Cette erreur dans Azure Machine Learning se produit si le jeu de données ne contient pas la colonne d’étiquette attendue.  
  
 Cette exception peut également se produire lors de la colonne d’étiquette fournie ne correspond pas aux données ou le type de données attendu par l’apprenant, ou les valeurs sont incorrectes. Par exemple, cette exception est générée lors de l’utilisation d’une colonne d’étiquette à valeurs réelles lors de l’apprentissage d’un classifieur binaire.  
  
**Résolution :** La résolution dépend de l’apprenant ou formateur que vous utilisez et les types de données des colonnes dans votre jeu de données. Tout d’abord, vérifiez la configuration requise de l’algorithme d’apprentissage automatique ou d’un module de formation.  
  
 Réexaminez le jeu de données d’entrée. Vérifiez que la colonne que vous voulez être traitée comme l’étiquette appropriées au type de données pour le modèle que vous créez.  
  
 Vérifiez les entrées pour les valeurs manquantes et éliminer ou les remplacer si nécessaire.  
  
 Si nécessaire, ajoutez le [modifier les métadonnées](edit-metadata.md) module et assurez-vous que la colonne d’étiquette est marquée en tant qu’étiquette.  
  
|Messages d’exception|  
|------------------------|  
|La colonne d’étiquette n’est ne pas comme prévu|  
|La colonne d’étiquette n’est ne pas comme prévu dans «{0}».|  
|La colonne d’étiquette «{0}« n’est pas attendu dans »{1}».|  
  

## <a name="error-0059"></a>Erreur 0059  
 Exception se produit si un index de colonne spécifié dans un sélecteur de colonne ne peut pas être analysé.  
  
 Cette erreur dans Azure Machine Learning se produit si un index de colonne spécifié lorsque vous utilisez le sélecteur de colonne ne peut pas être analysé.  Vous recevez cette erreur lorsque l’index de colonne est dans un format non valide qui ne peut pas être analysé.  
  
**Résolution :** Modifier l’index de colonne pour utiliser une valeur d’index valide.  
  
|Messages d’exception|  
|------------------------|  
|Un ou plusieurs index de colonne spécifiée ou les plages d’index n’a pas pu être analysés.|  
|Index de colonne ou une plage «{0}» pas pu être analysée.|  
  

## <a name="error-0060"></a>Erreur 0060  
 Exception se produit lorsqu’une plage de colonnes de plage à l’emploi est spécifié dans un sélecteur de colonne.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’une plage de la colonne d’out-of-range est spécifiée dans le sélecteur de colonne. Vous recevez cette erreur si la plage de colonnes dans le sélecteur de colonne ne correspond pas aux colonnes du jeu de données.  
  
**Résolution :** Modifier la plage de colonnes dans le sélecteur de colonne pour qu’elles correspondent aux colonnes dans le jeu de données.  
  
|Messages d’exception|  
|------------------------|  
|Non valide ou en dehors de la plage de plage d’index de colonne spécifié.|  
|Limites de la colonne «{0}» est non valide ou est hors plage.|  
  

## <a name="error-0061"></a>Erreur 0061  
 Exception se produit lorsque vous tentez d’ajouter une ligne à un DataTable qui possède un nombre différent de colonnes que la table.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous tentez d’ajouter une ligne à un dataset comportant un nombre différent de colonnes que le jeu de données.  Vous recevez cette erreur si la ligne qui est ajoutée au jeu de données comporte un nombre différent de colonnes du jeu de données d’entrée.  La ligne ne peut pas être ajoutée au jeu de données si le nombre de colonnes est différent.  
  
**Résolution :** Modifier le groupe de données d’entrée ont le même nombre de colonnes en tant que la ligne ajoutée, ou modifier la ligne ajoutée pour avoir le même nombre de colonnes que le jeu de données.  
  
|Messages d’exception|  
|------------------------|  
|Toutes les tables doivent avoir le même nombre de colonnes.|  
  

## <a name="error-0062"></a>Erreur 0062  
 Exception se produit lorsque vous tentez de comparer deux modèles avec des types différents d’apprenants.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque les mesures d’évaluation pour les deux jeux de données au score calculé différents ne peut pas être comparées. Dans ce cas, il n’est pas possible de comparer l’efficacité des modèles utilisés pour produire deux jeux de données au score calculé.  
  
**Résolution :** Vérifiez que les résultats notés sont produites par le même type de modèle d’apprentissage automatique (classification binaire, régression, classification multiclasse, recommandation, le clustering, la détection des anomalies, etc.). Tous les modèles qui vous permettent de comparer doivent avoir le même type d’apprenant.  
  
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
|L’erreur suivante s’est produite lors de l’évaluation du script R :---début du message d’erreur à partir de R--- {0} ---fin de message d’erreur à partir de R---|  
|Lors de l’évaluation du script R «{1}« l’erreur suivante s’est produite :---début du message d’erreur à partir de R--- {0} ---fin de message d’erreur à partir de R---|  
  


## <a name="error-0064"></a>Erreur 0064  
 Exception se produit si le nom de compte de stockage Azure ou de clé de stockage spécifiée est incorrect.  
  
 Cette erreur dans Azure Machine Learning se produit si le nom de compte de stockage Azure ou de la clé de stockage spécifié est incorrect. Vous recevez cette erreur si vous entrez un nom de compte incorrect ou le mot de passe du compte de stockage. Cela peut se produire si vous entrez manuellement le nom de compte ou mot de passe. Il peut également se produire si le compte a été supprimé.  
  
**Résolution :** Vérifiez que le nom de compte et le mot de passe sont entrés correctement, et que le compte existe.  
  
|Messages d’exception|  
|------------------------|  
|Le nom de compte de stockage Azure ou d’une clé de stockage est incorrecte.|  
|Le nom de compte de stockage Azure «{0}» ou de la clé de stockage pour le nom du compte est incorrect.|  
  

## <a name="error-0065"></a>Erreur 0065  
 Exception se produit si le nom d’objet blob Azure spécifié est incorrect.  
  
 Cette erreur dans Azure Machine Learning se produit si le nom de l’objet blob Azure spécifié est incorrect.  Vous recevrez l’erreur si :  
  
-   Impossible de trouver l’objet blob dans le conteneur spécifié.  
  
 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->
  
-   Seul le conteneur a été spécifié comme source dans un [importer des données](import-data.md) demander lors le format Excel ou CSV avec encodage ; concaténation du contenu de tous les objets BLOB dans un conteneur n’est pas autorisée avec ces formats.  
  
-   Un URI SAS ne contient pas le nom d’un objet blob valid.  
  
**Résolution :** Réexaminez le module qui lève l’exception. Vérifiez que l’objet blob spécifié n’existe dans le conteneur du compte de stockage et que les autorisations vous permettent de l’objet BLOB. Vérifiez que l’entrée est au format **containername/filename** si vous avez Excel ou CSV avec des formats d’encodage. Vérifiez qu’un URI SAS contient le nom d’un objet blob valid.  
  
|Messages d’exception|  
|------------------------|  
|L’objet blob de stockage Azure est incorrect.|  
|Le nom d’objet blob de stockage Azure «{0}» est incorrect|  
  

## <a name="error-0066"></a>Erreur 0066  
 Exception se produit si une ressource n’a pas pu être téléchargée vers un objet Blob Azure.  
  
 Cette erreur dans Azure Machine Learning se produit si une ressource n’a pas pu être téléchargée vers un objet Blob Azure.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Les deux sont enregistrés dans le même compte de stockage Azure que le compte qui contient le fichier d’entrée.  
  
**Résolution :** Le module est réversible. Vérifiez que le nom du compte Azure, la clé de stockage et le conteneur sont corrects et que le compte est autorisé à écrire dans le conteneur.  
  
|Messages d’exception|  
|------------------------|  
|La ressource n’a pas pu être téléchargée vers le stockage Azure.|  
|Le fichier «{0}» n’a pas pu être téléchargé vers le stockage Azure en tant que {1}.|  
  

## <a name="error-0067"></a>Erreur 0067  
 Exception se produit si un jeu de données a un nombre différent de colonnes que prévu.  
  
 Cette erreur dans Azure Machine Learning se produit si un jeu de données a un nombre différent de colonnes que prévu.  Vous recevez cette erreur lorsque le nombre de colonnes dans le jeu de données est différent du nombre de colonnes que le module attend pendant l’exécution.  
  
**Résolution :** Modifier le jeu de données d’entrée ou les paramètres.  
  
|Messages d’exception|  
|------------------------|  
|Nombre inattendu de colonnes dans le datatable.|  
|Attendu «{0}« colonnes mais trouvé »{1}» colonnes à la place.|  
  

## <a name="error-0068"></a>Erreur 0068  
 Exception se produit si le script Hive spécifié n’est pas correct.  
  
 Cette erreur dans Azure Machine Learning se produit s’il existe des erreurs de syntaxe dans un script QL Hive, ou si l’interpréteur Hive rencontre une erreur lors de l’exécution de la requête ou le script.  
  
**Résolution :**

Le message d’erreur à partir de Hive est normalement renvoyée dans le journal des erreurs afin que vous pouvez entreprendre une action basée sur l’erreur spécifique. 

+ Ouvrez le module et inspecter la requête pour les erreurs.  
+ Vérifiez que la requête fonctionne correctement en dehors d’Azure Machine Learning en vous connectant à la console Hive de votre cluster Hadoop et de la requête en cours d’exécution.  
+ Essayez de placer des commentaires dans votre script Hive dans une ligne distincte, par opposition à la combinaison d’instructions exécutables et les commentaires dans une seule ligne.  

### <a name="resources"></a>Ressources

Consultez les articles suivants de l’aide avec des requêtes Hive pour l’apprentissage :

+ [Créer des tables Hive et charger des données depuis le stockage Blob Azure](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Explorer les données dans des tables avec des requêtes Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Créer des fonctionnalités pour les données dans un cluster Hadoop à l’aide de requêtes Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive pour utilisateurs aide-mémoire (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Messages d’exception|  
|------------------------|  
|Le script Hive est incorrect.|  
|Script Hive {0} n’est pas correct.|  
  

## <a name="error-0069"></a>Erreur 0069  
 Exception se produit si le script SQL spécifié n’est pas correct.  
  
 Cette erreur dans Azure Machine Learning se produit si le script SQL spécifié a des problèmes de syntaxe, ou si les colonnes ou la table spécifiée dans le script n’est pas valide. 
 
 Vous recevez cette erreur si le moteur SQL rencontre une erreur lors de l’exécution de la requête ou le script. Le message d’erreur SQL est normalement renvoyée dans le journal des erreurs afin que vous pouvez entreprendre une action basée sur l’erreur spécifique.  
  
**Résolution :** Réexaminez le module et inspecter la requête SQL pour les erreurs.  
  
 Vérifiez que la requête fonctionne correctement en dehors d’Azure ML en vous connectant directement au serveur de base de données et de la requête en cours d’exécution.  
  
 S’il existe un message SQL généré signalé par l’exception de module, entreprendre une action basée sur l’erreur signalée. Par exemple, les messages d’erreur incluent parfois des instructions spécifiques sur l’erreur probable :
+ *Pas de colonne ou de la base de données manquante*, indiquant que vous avez peut-être tapé un nom de colonne incorrect. Si vous êtes sûr du nom de colonne est correct, essayez d’utiliser des crochets ou guillemets doubles pour encadrer l’identificateur de colonne.
+ *Erreur de logique SQL près \<mot clé SQL\>*, indiquant que vous pouvez avoir une erreur de syntaxe avant le mot clé spécifié

  
|Messages d’exception|  
|------------------------|  
|Le script SQL est incorrect.|  
|Requête SQL «{0}» n’est pas correct.|  
|Requête SQL «{0}» n’est pas correct : {1}|  
  

## <a name="error-0070"></a>Erreur 0070  
 Exception se produit lorsque vous tentez d’accéder à la table Azure inexistante.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous tentez d’accéder à une table Azure inexistante. Vous recevez cette erreur si vous spécifiez une table dans le stockage Azure, qui n’existe pas lors de la lecture ou écriture dans le stockage Table Azure. Cela peut se produire si vous orthographiez mal le nom de la table souhaitée, ou si vous disposez d’une incompatibilité entre le nom cible et le type de stockage. Par exemple, vous destiné à lire à partir d’une table mais entré le nom d’un objet blob à la place.  
  
**Résolution :** Réexaminez le module pour vérifier que le nom de la table est correct.  
  
|Messages d’exception|  
|------------------------|  
|Table Azure n’existe pas.|  
|Table Azure «{0}» n’existe pas.|  
  
## <a name="error-0071"></a>Erreur 0071  
 Exception est levée si fourni les informations d’identification sont incorrectes.  
  
 Cette erreur dans Azure Machine Learning se produit si les informations d’identification fournies sont incorrectes.  
  
 Vous pouvez également recevoir cette erreur si le module ne peut pas se connecter à un cluster HDInsight.  
  
**Résolution :** Passez en revue les entrées dans le module et vérifiez le nom de compte et le mot de passe.  
  
 Recherchez les problèmes suivants qui peuvent provoquer une erreur :  
  
-   Le schéma du jeu de données ne correspond pas le schéma de la table de données de destination.  
  
-   Noms de colonnes sont manquants ou mal orthographié  
  
-   Vous écrivez dans une table qui comporte des noms de colonne avec des caractères non autorisés. En règle générale vous pouvez placer ces noms de colonne entre crochets, mais si cela ne fonctionne pas, modifier les noms de colonne à utiliser uniquement des lettres et des traits de soulignement (_)  
  
-   Les chaînes que vous tentez d’écrire contiennent des guillemets simples  
  
 Si vous essayez de vous connecter à un cluster HDInsight, vérifiez que le cluster cible est accessible avec les informations d’identification fournies.  
  
|Messages d’exception|  
|------------------------|  
|Informations d’identification incorrectes sont transmises.|  
|Nom d’utilisateur incorrect «{0}» ou le mot de passe est transmis.|  
  

## <a name="error-0072"></a>Erreur 0072  
 Cette exception est levée en cas de délai de connexion.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’une connexion arrive à expiration. Vous recevez cette erreur s’il existe actuellement des problèmes de connectivité avec la source de données ou la destination, telle qu’une connexion internet lente, ou si le jeu de données est volumineux ou de la requête SQL pour lire les données effectue un traitement complexe.  
  
**Résolution :** Déterminer s’il existe actuellement des problèmes avec des connexions lentes vers stockage Azure ou internet.  
  
|Messages d’exception|  
|------------------------|  
|Délai de connexion s’est produite.|  
  

## <a name="error-0073"></a>Erreur 0073  
 Exception se produit si une erreur se produit lors de la conversion d’une colonne à un autre type.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’il n’est pas possible de convertir la colonne vers un autre type.  Vous recevez cette erreur si un module a besoin d’un type particulier et il n’est pas possible de convertir la colonne dans le nouveau type.  
  
**Résolution :** Modifier le jeu de données d’entrée afin que la colonne peut être convertie en fonction de l’exception interne.  
  
|Messages d’exception|  
|------------------------|  
|Impossible de convertir la colonne.|  
|Impossible de convertir la colonne à {0}.|  
  

## <a name="error-0074"></a>Erreur 0074  
 Cette exception est levée lorsque le [modifier les métadonnées](edit-metadata.md) tente de convertir une colonne éparse catégorielles.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque le [modifier les métadonnées](edit-metadata.md) tente de convertir une colonne éparse catégorielles.  Vous recevez cette erreur lorsque vous tentez de convertir les colonnes éparses catégorielles avec le **rendre par catégorie** option.  Azure machine Learning ne prend pas en charge les tableaux épars catégoriques, par conséquent, le module échoue.  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|Messages d’exception|  
|------------------------|  
|Les colonnes éparses ne peut pas être convertis par catégorie.|  
  

## <a name="error-0075"></a>Erreur 0075  
Exception se produit lorsqu’une fonction de compartimentage non valide est utilisée pour quantifier un jeu de données.  
  
Cette erreur dans Azure Machine Learning se produit lorsque vous tentez de placer des données à l’aide d’une méthode non prise en charge, ou lorsque les combinaisons de paramètres ne sont pas valides.  
  
**Résolution :**

Gestion des erreurs pour cet événement a été introduit dans une version antérieure d’Azure Machine Learning qui autorisé davantage de personnalisation de méthodes de compartimentage. Actuellement toutes les méthodes de compartimentage reposent sur une sélection dans une liste déroulante, techniquement, qu'il doit être n’est plus possible d’obtenir cette erreur.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|Messages d’exception|  
|------------------------|  
|Fonction de compartimentage non valide utilisée.|  
  

## <a name="error-0077"></a>Erreur 0077  
 Exception se produit quand fichier blob inconnu écrit mode passé.  
  
 Cette erreur dans Azure Machine Learning se produit si un argument non valide est passé dans les spécifications pour la source ou de destination de fichier blob.  
  
**Résolution :** Dans presque tous les modules importer ou exporter des données vers et depuis le stockage blob Azure, les valeurs de paramètre contrôle le mode d’écriture sont affectées à l’aide d’une liste déroulante ; Par conséquent, il n’est pas possible de passer une valeur non valide, et cette erreur ne doit pas apparaître. Cette erreur sera déconseillée dans une version ultérieure.  
  
|Messages d’exception|  
|------------------------|  
|Objet blob non pris en charge écrit mode.|  
|Objet blob non pris en charge écrit mode : {0}.|  
  

## <a name="error-0078"></a>Erreur 0078  
 Cette exception est levée lorsque le protocole HTTP option pour [importer des données](import-data.md) reçoit un code d’état 3xx indiquant une redirection.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque le protocole HTTP option pour [importer des données](import-data.md) reçoit un 3xx (301, 302, 304, etc.) code d’état indiquant la redirection. Vous recevez cette erreur si vous tentez de vous connecter à une source HTTP qui redirige le navigateur vers une autre page. Pour la sécurité des raisons, redirection des sites Web ne sont pas autorisés comme sources de données pour Azure Machine Learning.  
  
**Résolution :** Si le site Web est un site Web de confiance, entrez l’URL redirigée directement.  
  
|Messages d’exception|  
|------------------------|  
|Redirection HTTP non autorisée|  
  

## <a name="error-0079"></a>Erreur 0079  
 Exception se produit si le nom de conteneur de stockage Azure spécifié est incorrect.  
  
 Cette erreur dans Azure Machine Learning se produit si le nom de conteneur de stockage Azure spécifié est incorrect. Vous recevez cette erreur si vous n’avez pas spécifié le conteneur et le nom d’objet blob (fichier) à l’aide **le chemin d’accès commençant par le conteneur d’objets blob** option lors de l’écriture dans le stockage Blob Azure.  
  
**Résolution :** Revoir les [exporter des données](export-data.md) module et vérifiez que le chemin d’accès spécifié dans l’objet blob contient à la fois le conteneur et le nom de fichier, au format **conteneur/filename**.  
  
|Messages d’exception|  
|------------------------|  
|Le nom de conteneur de stockage Azure est incorrect.|  
|Le nom de conteneur de stockage Azure «{0}» est incorrect ; un nom de conteneur du format conteneur/blob était attendu.|  
  

## <a name="error-0080"></a>Erreur 0080  
 Exception se produit lors de la colonne avec toutes les valeurs manquantes n’est pas autorisée par le module.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’un ou plusieurs des colonnes utilisées par le module contient toutes les valeurs manquantes. Par exemple, si un module est computing statistiques agrégées pour chaque colonne, il ne peut pas fonctionner sur une colonne ne contenant aucune donnée. Dans ce cas, l’exécution de module est arrêtée avec cette exception.  
  
**Résolution :** Réexaminez le jeu de données d’entrée et supprimez toutes les colonnes qui contiennent toutes les valeurs manquantes.  
  
|Messages d’exception|  
|------------------------|  
|Colonnes avec toutes les valeurs manquantes ne sont pas autorisées.|  
|Colonne {0} a toutes les valeurs manquantes.|  
  

## <a name="error-0081"></a>Erreur 0081  
 Exception se produit dans le module PCA si le nombre de dimensions à réduire est égal au nombre de colonnes de fonctionnalités dans le jeu de données d’entrée, contenant au moins une colonne de fonctionnalité éparse.  
  
 Cette erreur dans Azure Machine Learning se produit si les conditions suivantes sont remplies : (a) le jeu de données d’entrée possède au moins une colonne fragmentée et (b) le nombre final de dimensions demandé est identique au nombre de dimensions d’entrée.  
  
**Résolution :** Envisagez de réduire le nombre de dimensions dans la sortie soit inférieur au nombre de dimensions dans l’entrée. Cela est courant dans les applications de PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|Messages d’exception|  
|------------------------|  
|Pour le jeu de données contenant des colonnes de fonctionnalités éparses, nombre de dimensions après réduction doit être inférieur au nombre de colonnes de fonctionnalités.|  
 

## <a name="error-0082"></a>Erreur 0082  
 Exception se produit lorsqu’un modèle ne peut pas être désérialisé avec succès.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’un modèle d’apprentissage enregistré ou transformation ne peut pas être chargée par une version plus récente du runtime Azure Machine Learning à la suite d’une modification avec rupture.  
  
**Résolution :** L’expérience de formation qui a généré le modèle ou la transformation doit être réexécuter et le modèle ou de transformation doit être stockée.  
  
|Messages d’exception|  
|------------------------|  
|Modèle n’a pas pu être désérialisé, car il est probablement sérialisé avec un format de sérialisation antérieur. Reformer, enregistrez de nouveau le modèle.|  
  

## <a name="error-0083"></a>Erreur 0083  
 Cette exception est levée si le jeu de données utilisé pour formation ne peut pas être utilisée pour un type concret d’apprenant.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque le jeu de données est incompatible avec l’apprenant qui est formé. Par exemple, le jeu de données peut contenir au moins une valeur manquante dans chaque ligne, et par conséquent, le dataset entier est ignoré pendant la formation. Dans d’autres cas, certains algorithmes d’apprentissage machine telles que la détection d’anomalie n’attendent pas d’étiquettes à être présent et peuvent lever cette exception si les étiquettes sont présentes dans le jeu de données.  
  
**Résolution :** Consultez la documentation de l’apprenant utilisé pour vérifier la configuration requise pour le jeu de données d’entrée. Examinez les colonnes pour afficher toutes les colonnes requises sont présentes.  
  
|Messages d’exception|  
|------------------------|  
|Jeu de données utilisé pour l’apprentissage n’est pas valide.|  
|{0} contient des données non valides pour l’apprentissage.|  
|{0} contient des données non valides pour l’apprentissage. Type d’apprenant : {1}.|  
  

## <a name="error-0084"></a>Erreur 0084  
 Exception se produit lors de l’évaluation des scores générés à partir d’un Script R. Il s’agit actuellement non pris en charge.  
  
 Cette erreur dans Azure Machine Learning se produit si vous essayez d’utiliser un des modules pour évaluer un modèle avec une sortie à partir d’un script R qui contient les scores.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|L’évaluation des scores produits par R est actuellement pas pris en charge.|  
  

## <a name="error-0085"></a>Erreur 0085  
 Exception se produit lors de l’évaluation de script échoue avec une erreur.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous exécutez un script personnalisé qui contient des erreurs de syntaxe.  
  
**Résolution :** Passez en revue votre code dans un éditeur externe et la vérification des erreurs.  
  
|Messages d’exception|  
|------------------------|  
|Erreur lors de l’évaluation du script.|  
|L’erreur suivante s’est produite lors de l’évaluation de script, afficher le journal de sortie pour plus d’informations :---début du message d’erreur à partir de {0} interpréteur--- {1} ---fin de message d’erreur à partir de {0} interpréteur--- ------|  
  

## <a name="error-0086"></a>Erreur 0086  
 Exception se produit lorsqu’une transformation de comptage n’est pas valide.  
  
 Cette erreur dans Azure Machine Learning se produit quand vous sélectionnez une transformation basée sur une table de nombres, mais le fichier de transformation sélectionné n’est pas compatible avec les données actuelles, ou avec la nouvelle table de nombres.  
  
**Résolution :** Le module prend en charge l’enregistrement les nombres et les règles qui composent la transformation dans deux formats différents. Si vous fusionnez des tables de comptage, vérifiez que les deux tables que vous avez l’intention de fusion utilisent le même format.  
  
En règle générale, une transformation basée sur le nombre d’être applicable uniquement aux jeux de données qui ont le même schéma que le jeu de données sur lequel la transformation a été créée à l’origine.  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Messages d’exception|  
|------------------------|  
|Transformation de comptage non valide spécifiée.|  
|La transformation de comptage sur le port d’entrée '{0}' n’est pas valide.|  
|La transformation de comptage sur le port d’entrée «{0}'ne peut pas être fusionnée avec la transformation de comptage sur le port d’entrée'{1}'. Consulter pour vérifier les métadonnées utilisées pour le comptage des correspondances.|  
  

## <a name="error-0087"></a>Erreur 0087  
 Exception se produit lorsqu’un type de table de nombres non valide est spécifié pour l’apprentissage avec des modules de nombres.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous essayez d’importer une table de nombres existant, mais la table n’est pas compatible avec les données actuelles, ou avec la nouvelle table de nombres.  
  
**Résolution :** Il existe des formats différents pour enregistrer les nombres et les règles qui composent la transformation. Si vous fusionnez des tables de comptage, vérifiez que les deux utilisent le même format.  
  
 En règle générale, une transformation basée sur le nombre est applicable uniquement aux jeux de données qui ont le même schéma que le jeu de données sur lequel la transformation a été créée à l’origine.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>Erreur 0088  
 Exception se produit lorsqu’un menu de comptage non valide type est spécifié pour l’apprentissage avec des modules de nombres.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous essayez d’utiliser une autre méthode de comptage qu’est prise en charge pour la personnalisation basée sur un nombre.  
  
**Résolution :** En général, les méthodes de comptage sont choisies dans une liste déroulante, donc vous ne devriez pas voir cette erreur.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Messages d’exception|  
|------------------------|  
|Type de comptage non valide est spécifié.|  
|Le type de comptage spécifié «{0}» n’est pas un type de comptage valide.|  
  

## <a name="error-0089"></a>Erreur 0089  
 Exception se produit lorsque le nombre de classes spécifié est inférieur au nombre réel de classes dans un jeu de données utilisé pour le comptage.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous créez une table de nombres et de la colonne d’étiquette contient un nombre différent de classes que vous avez spécifié dans les paramètres du module.  
  
**Résolution :** Vérifiez votre jeu de données et savoir exactement comment de nombreuses valeurs distinctes (classes possibles) il existe dans la colonne d’étiquette. Lorsque vous créez la table de nombres, vous devez spécifier au moins ce nombre de classes.  
  
 La table de nombres ne peut pas déterminer automatiquement le nombre de classes disponibles.  
  
 Lorsque vous créez la table de nombres, vous ne pouvez pas spécifier 0 ou un nombre quelconque qui est inférieur au nombre réel de classes dans la colonne d’étiquette.  
  
|Messages d’exception|  
|------------------------|  
|Le nombre de classes est incorrect. Assurez-vous que le nombre de classes que vous spécifiez dans le volet de paramètre est supérieur ou égal au nombre de classes dans la colonne d’étiquette.|  
|Le nombre de classes spécifié est «{0}«, qui n’est pas supérieur à une valeur de l’étiquette »{1}» dans le jeu de données utilisé pour compter. Assurez-vous que le nombre de classes que vous spécifiez dans le volet de paramètre est supérieur ou égal au nombre de classes dans la colonne d’étiquette.|  
  

## <a name="error-0090"></a>Erreur 0090  
 Exception est levée en cas d’échec de création de la table Hive.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous utilisez [exporter des données](export-data.md) ou une autre option pour enregistrer les données dans un cluster HDInsight et la table Hive spécifiée ne peut pas être créée.  
  
**Résolution :** Vérifiez le nom du compte de stockage Azure associé au cluster et que vous utilisez le même compte dans les propriétés du module.  
  
|Messages d’exception|  
|------------------------|  
|La table Hive n’a pas pu être créée. Pour un cluster HDInsight, vérifiez le nom du compte de stockage Azure associé au cluster est identique à ce qui est passé par le paramètre de module.|  
|La table Hive «{0}» ne peut pas être créé. Pour un cluster HDInsight, vérifiez le nom du compte de stockage Azure associé au cluster est identique à ce qui est passé par le paramètre de module.|  
|La table Hive «{0}» ne peut pas être créé. Pour un cluster HDInsight, vérifiez le nom du compte de stockage Azure associé au cluster est «{1}».|  
 

## <a name="error-0100"></a>Erreur 0100  
 Exception se produit lorsqu’un langage non pris en charge est spécifié pour un module personnalisé.  
  
 Cette erreur dans Azure Machine Learning se produit lors de la création d’un module personnalisé et la propriété de nom de la **langage** élément dans un fichier de définition de module personnalisé xml a une valeur non valide. Actuellement, la seule valeur valide pour cette propriété est `R`. Par exemple :   
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Résolution :** Vérifiez que la propriété name de le **langage** élément du fichier de définition xml du module personnalisé est défini sur `R`. Enregistrez le fichier, mettre à jour le package zip de module personnalisé et essayez d’ajouter le module personnalisé à nouveau.  
  
|Messages d’exception|  
|------------------------|  
|Langue du module personnalisé non pris en charge spécifiée|  
  

## <a name="error-0101"></a>Erreur 0101  
 Tous les ID de port et le paramètre doivent être uniques.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’un ou plusieurs ports ou les paramètres sont attribués à la même valeur d’ID dans un fichier XML de définition de module personnalisé.  
  
**Résolution :** Vérifiez que les valeurs d’ID entre tous les ports et les paramètres sont uniques. Enregistrez le fichier xml, mettre à jour le package zip de module personnalisé et essayez d’ajouter le module personnalisé à nouveau.  
  
|Messages d’exception|  
|------------------------|  
|Tous les ports et le paramètre ID pour un module doit être unique|  
|Module '{0}' a port/argument ID en double. Tous les ID de port/argument doivent être uniques pour un module.|  
  

## <a name="error-0102"></a>Erreur 0102  
 Levée lorsqu’un fichier ZIP ne peut pas être extraite.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous importez un package compressé avec l’extension .zip, mais le package n’est pas un fichier zip, ou le fichier n’utilise pas un format zip pris en charge.  
  
**Résolution :** Assurez-vous que le fichier sélectionné est un fichier .zip valide et qu’il a été compressée à l’aide d’un des algorithmes de compression pris en charge.  
  
 Si vous obtenez cette erreur lors de l’importation des jeux de données dans un format compressé, vérifiez que tous les fichiers de relation contenant-contenus utilisent un des formats de fichier pris en charge et sont au format Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 Essayez de rajouter les fichiers souhaités dans un nouveau dossier compressé compressé et essayez de nouveau d’ajouter le module personnalisé.  
  
|Messages d’exception|  
|------------------------|  
|ZIP donné fichier n’est pas dans le format correct|  


## <a name="error-0103"></a>Erreur 0103  
 Levée lorsqu’un fichier ZIP ne contient-elle pas tous les fichiers .xml  
  
 Cette erreur dans Azure Machine Learning se produit lorsque le package zip de module personnalisé ne contient-elle pas les fichiers de définition (.xml) de n’importe quel module. Ces fichiers doivent se trouver dans la racine du package zip (par exemple, pas dans un sous-dossier.)  
  
**Résolution :** Vérifiez qu’un ou plusieurs fichiers de définition de module xml sont dans le dossier racine du package zip en l’extrayant dans un dossier temporaire sur votre lecteur de disque. Tous les fichiers xml doivent être directement dans le dossier que vous avez extrait le fichier zip à. Assurez-vous que lorsque vous créez le fichier zip que vous ne sélectionnez pas un dossier qui contient des fichiers xml pour le code postal que cette action crée un sous-dossier dans le fichier zip avec le même nom que le dossier que vous avez sélectionné le code postal.  
  
|Messages d’exception|  
|------------------------|  
|ZIP donné fichier ne contient pas les fichiers de définition de module (fichiers .xml)|  


## <a name="error-0104"></a>Erreur 0104  
 Levée lorsqu’un fichier de définition de module fait référence à un script qui est introuvable  
  
 Cette erreur dans Azure Machine Learning est levée lorsqu’un fichier de définition de module personnalisé xml fait référence à un fichier de script dans le **langage** élément qui n’existe pas dans le fichier zip. Le chemin d’accès du fichier de script est défini dans le **sourceFile** propriété de la **langage** élément. Le chemin d’accès au fichier source est relatif à la racine du package zip (même emplacement que les fichiers de définition de module xml). Si le fichier de script est dans un sous-dossier, vous devez spécifier le chemin d’accès relatif au fichier de script. Par exemple, si tous les scripts ont été stockés dans un **myScripts** dossier dans le fichier zip, le **langage** élément aurait ajouter ce chemin d’accès à la **sourceFile** propriété en tant que ci-dessous. Par exemple :   
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Résolution :** Assurez-vous que la valeur de la **sourceFile** propriété dans le **langage** élément de la définition xml du module personnalisé est correct et que le fichier source existe dans le chemin d’accès relatif correct dans le fichier zip.  
  
|Messages d’exception|  
|------------------------|  
|Fichier de script R référencé n’existe pas.|  
|Fichier de script R référencé '{0}' est introuvable. Vérifiez que le chemin d’accès relatif au fichier est correct à partir de l’emplacement des définitions.|  


## <a name="error-0105"></a>Erreur 0105  
 Cette erreur s’affiche lorsqu’un fichier de définition de module contient un type de paramètre non pris en charge  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous créer une définition xml de module personnalisé et le type d’un paramètre ou d’argument dans la définition ne correspond pas à un type pris en charge.  
  
**Résolution :** Assurez-vous que la propriété de type de n’importe quel **Arg** élément du fichier de définition xml du module personnalisé est un type pris en charge.  
  
|Messages d’exception|  
|------------------------|  
|Type de paramètre non pris en charge.|  
|Non pris en charge de type de paramètre '{0}' spécifié.|  


## <a name="error-0106"></a>Erreur 0106  
 Levée quand un fichier de définition de module définit un type d’entrée non pris en charge  
  
 Cette erreur dans Azure Machine Learning se produit lorsque le type d’un port d’entrée dans un module personnalisé la définition XML ne correspond pas à un type pris en charge.  
  
**Résolution :** Assurez-vous que la propriété de type d’un élément d’entrée dans le fichier de définition de module personnalisé XML est un type pris en charge.  
  
|Messages d’exception|  
|------------------------|  
|Type d’entrée non pris en charge.|  
|Non pris en charge de type d’entrée '{0}' spécifié.|  


## <a name="error-0107"></a>Erreur 0107  
 Levée quand un fichier de définition de module définit un type de sortie non pris en charge  
  
 Cette erreur dans Azure Machine Learning se produit lorsque le type d’un port de sortie dans une définition xml de module personnalisé ne correspond pas à un type pris en charge.  
  
**Résolution :** Assurez-vous que la propriété de type d’un élément de sortie du fichier de définition xml du module personnalisé est un type pris en charge.  
  
|Messages d’exception|  
|------------------------|  
|Type de sortie non pris en charge.|  
|Non pris en charge de type de sortie '{0}' spécifié.|  


## <a name="error-0108"></a>Erreur 0108  
 Levée quand un fichier de définition de module définit plus de ports d’entrée ou de sortie que sont pris en charge  
  
 Cette erreur dans Azure Machine Learning se produit lorsque trop de ports d’entrée ou de sortie sont définies dans une définition xml de module personnalisé.  
  
**Résolution :** Permet de s’assurer que le nombre maximal de ports d’entrée et de sortie définis dans la définition xml de module personnalisé ne dépasse pas le nombre maximal de ports pris en charge.  
  
|Messages d’exception|  
|------------------------|  
|Dépassement du nombre pris en charge des ports d’entrée ou de sortie.|  
|Dépassement du nombre de prises en charge '{0}' ports. Nombre maximal autorisé de «{0}'ports is'{1}'.| 

## <a name="error-0109"></a>Erreur 0109  
 Levée quand un fichier de définition de module définit un sélecteur de colonne incorrecte  
  
 Cette erreur dans Azure Machine Learning se produit quand la syntaxe pour un argument de sélecteur de colonne contient une erreur dans une définition xml de module personnalisé.  
  
**Résolution :** Cette erreur se produit lorsque la syntaxe pour un argument de sélecteur de colonne contient une erreur dans une définition xml de module personnalisé.  
  
|Messages d’exception|  
|------------------------|  
|Syntaxe non prise en charge pour le sélecteur de colonne.|  
  

## <a name="error-0110"></a>Erreur 0110  
 Levée quand un fichier de définition de module définit un sélecteur de colonne qui fait référence à un ID de port d’entrée inexistant  
  
 Cette erreur dans Azure Machine Learning se produit lorsque le *portId* propriété dans l’élément Propriétés d’un argument de type ColumnPicker ne correspond pas à la valeur d’ID d’un port d’entrée.  
  
**Résolution :** Assurez-vous que la propriété ID de port correspond à la valeur d’ID d’un port d’entrée défini dans la définition xml de module personnalisé.  
  
|Messages d’exception|  
|------------------------|  
|Sélecteur de colonne fait référence à un ID de port d’entrée inexistant.|  
|Sélecteur de colonne fait référence à un ID de port d’entrée inexistant '{0}'.|  
  

## <a name="error-0111"></a>Erreur 0111  
 Levée quand un fichier de définition de module définit une propriété non valide  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’une propriété non valide est assignée à un élément dans la définition XML du module personnalisé.  
  
**Résolution :** Assurez-vous que la propriété est prise en charge par l’élément de module personnalisé.  
  
|Messages d’exception|  
|------------------------|  
|Définition de propriété n’est pas valide.|  
|Définition de la propriété '{0}' n’est pas valide.|  
  

## <a name="error-0112"></a>Erreur 0112  
 Levée lorsqu’un fichier de définition de module ne peut pas être analysé.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’il existe une erreur dans le format xml qui empêche le module personnalisé de définition XML à partir d’analysé comme un fichier XML valide.  
  
**Résolution :** Assurez-vous que chaque élément est ouvert et fermé correctement. Assurez-vous qu’il n’y a aucune erreur dans la mise en forme du XML.  
  
|Messages d’exception|  
|------------------------|  
|Impossible d’analyser le fichier de définition de module.|  
|Impossible d’analyser le fichier de définition de module '{0}'.|  
  

## <a name="error-0113"></a>Erreur 0113  
 Levée lorsqu’un fichier de définition de module contient des erreurs.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque le fichier XML de définition de module personnalisé peut être analysé mais contient des erreurs, tels que la définition d’éléments non pris en charge par les modules personnalisés.  
  
**Résolution :** Assurez-vous que le fichier de définition de module personnalisé définit les éléments et les propriétés qui sont prises en charge par les modules personnalisés.  
  
|Messages d’exception|  
|------------------------|  
|Fichier de définition de module contient des erreurs.|  
|Fichier de définition de module '{0}' contient des erreurs.|  
|Fichier de définition de module '{0}' contient des erreurs. {1}|  
  

## <a name="error-0114"></a>Erreur 0114  
 Levée lors de la création d’un module personnalisé échoue.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’une génération de module personnalisé échoue. Cela se produit lorsqu’une ou plusieurs erreurs liées aux modules personnalisés sont rencontrées lors de l’ajout du module personnalisé. Les autres erreurs sont signalées au sein de ce message d’erreur.  
  
**Résolution :** Résoudre les erreurs signalées au sein de ce message d’exception.  
  
|Messages d’exception|  
|------------------------|  
|Impossible de générer le module personnalisé.|  
|Module personnalisé builds ayant échoué avec l’erreur (s) : {0}|  
  

## <a name="error-0115"></a>Erreur 0115  
 Levée lorsqu’un script par défaut de module personnalisé a une extension non pris en charge.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous fournissez un script pour un module personnalisé qui utilise une extension de nom de fichier inconnu.  
  
**Résolution :** Vérifiez l’extension de format et le nom de fichier de tous les fichiers de script inclus dans le module personnalisé.  
  
|Messages d’exception|  
|------------------------|  
|Non pris en charge d’extension de script par défaut.|  
|Extension de fichier non pris en charge {0} de script par défaut.|  
  

## <a name="error-0121"></a>Erreur 0121  
 Levée lorsque SQL écrit échoue, car la table est impossible d’écrire dans  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous utilisez le [exporter des données](export-data.md) module pour enregistrer les résultats dans une table dans une base de données SQL et la table ne peut pas être écrites dans. En règle générale, vous verrez cette erreur si le [exporter des données](export-data.md) module correctement établit une connexion avec l’instance de SQL Server, mais ensuite ne peut pas écrire le contenu du jeu de données Azure ML dans la table.  
  
**Résolution :**
 - Ouvrez le volet de propriétés de la [exporter des données](export-data.md) module et vérifiez que les noms de base de données et de table sont correctes. 
 - Examinez le schéma du jeu de données que vous exportez et vous assurer que les données sont compatibles avec la table de destination.
 - Vérifiez que le SQL connectez-vous associé au nom d’utilisateur et mot de passe dispose des autorisations pour écrire dans la table. 
 - Si l’exception contient des informations d’erreur supplémentaires à partir de SQL Server, utilisez ces informations pour apporter des corrections.  
  
|Messages d’exception|  
|------------------------|  
|Connecté au serveur, Impossible d’écrire à la table.|  
|Impossible d’écrire dans la table Sql : {0}|  


## <a name="error-0122"></a>Erreur 0122  
 Exception se produit si plusieurs colonnes de pondération sont spécifiées et qu’une seule est autorisée.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque trop de colonnes ont été sélectionnées comme colonnes de pondération.  
  
**Résolution :** Passez en revue le jeu de données d’entrée et de ses métadonnées. Assurez-vous que seule une colonne contient le poids.  
  
|Messages d’exception|  
|------------------------|  
|Plusieurs colonnes de pondération sont spécifiées.|  


## <a name="error-0123"></a>Erreur 0123  
 Exception se produit si la colonne de vecteurs est spécifiée pour la colonne d’étiquette.  
  
 Cette erreur dans Azure Machine Learning se produit si vous utilisez un vecteur comme colonne d’étiquette.  
  
**Résolution :** Modifier le format de données de la colonne si nécessaire, ou choisissez une autre colonne.  
  
|Messages d’exception|  
|------------------------|  
|Colonne de vecteurs est spécifiée en tant que colonne d’étiquette.|  


## <a name="error-0124"></a>Erreur 0124  
 Exception se produit si les colonnes non numériques sont spécifiées pour être la colonne de pondération.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Une colonne non numérique est spécifiée en tant que la colonne de pondération.|  
  


## <a name="error-0125"></a>Erreur 0125  
 Levée lorsque le schéma pour plusieurs jeux de données ne correspond pas.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Schéma du jeu de données ne correspond pas.|  


## <a name="error-0126"></a>Erreur 0126  
 Exception se produit si l’utilisateur spécifie un domaine SQL qui n’est pas pris en charge dans Azure ML.  
  
 Cette erreur se produit lorsque l’utilisateur spécifie un domaine SQL qui n’est pas pris en charge dans Azure Machine Learning. Vous recevez cette erreur si vous essayez de vous connecter à un serveur de base de données dans un domaine qui n’est pas dans la liste verte. Actuellement, les domaines SQL autorisées sont : «. database.windows.net «, ». cloudapp.net », ou «. database.secure.windows.net ». Autrement dit, le serveur doit être un serveur SQL Azure ou un serveur dans une machine virtuelle sur Azure.  
  
**Résolution :** Le module est réversible. Vérifiez que le serveur de base de données SQL appartient à un des domaines acceptés :  
  
-   .database.windows.net  
  
-   .cloudapp.net  
  
-   .database.secure.windows.net  
  
|Messages d’exception|  
|------------------------|  
|Domaine SQL non pris en charge.|  
|Le domaine SQL {0} n’est pas actuellement pris en charge dans Azure ML|  
  

## <a name="error-0127"></a>Erreur 0127  
 Taille en pixels image dépasse la limite autorisée  
  
 Cette erreur se produit si vous lisez des images à partir d’un jeu de données d’image pour la classification et les images sont plus volumineux que le modèle peut gérer.  
  
 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  
  
-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->
  
|Messages d’exception|  
|------------------------|  
|Taille en pixels image dépasse la limite autorisée.|  
|La taille en pixels dans le fichier de l’image '{0}' dépasse la limite autorisée : «{1}»|  


## <a name="error-0128"></a>Erreur 0128  
 Nombre de probabilités conditionnelles pour les colonnes catégorielles dépasse la limite.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Nombre de probabilités conditionnelles pour les colonnes catégorielles dépasse la limite.|  
|Nombre de probabilités conditionnelles pour les colonnes catégorielles dépasse la limite. Colonnes{0}'et'{1}' sont la paire problématique.|  


## <a name="error-0129"></a>Erreur 0129  
 Nombre de colonnes dans le jeu de données dépasse la limite autorisée.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Nombre de colonnes dans le jeu de données dépasse la limite autorisée.|  
|Nombre de colonnes dans le jeu de données dans '{0}« dépasse autorisées ».|  
|Nombre de colonnes dans le jeu de données dans «{0}'dépasse la limite autorisée de'{1}'.'|  
|Nombre de colonnes dans le jeu de données dans «{0}'dépasse autorisées'{1}'limite de'{2}'.'|  
## <a name="error-0130"></a>Erreur 0130  
 Exception se produit lorsque toutes les lignes dans le jeu de données de formation contiennent des valeurs manquantes.  
  
 Cela se produit quand une colonne dans le jeu de données de formation est vide.  
  
**Résolution :** Utilisez le [Clean Missing Data](clean-missing-data.md) module à supprimer les colonnes avec toutes les valeurs manquantes.  
  
|Messages d’exception|  
|------------------------|  
|Toutes les lignes dans le jeu de données de formation contiennent des valeurs manquantes.  Envisagez d’utiliser le module de nettoyage des données manquantes pour supprimer les valeurs manquantes.|  
 

## <a name="error-0131"></a>Erreur 0131  
 Cette exception est levée si un ou plusieurs jeux de données dans un fichier zip ne parvient pas à être décompressé et inscrit correctement  
  
 Cette erreur se produit lorsqu’un ou plusieurs jeux de données dans un fichier zip ne parvient pas à être décompressé et lire correctement. Vous recevez cette erreur si la décompression échoue parce que le fichier zip lui-même ou l’un des fichiers qu’il est endommagé ou il existe une erreur système lors de la décompression et développez un fichier.  
  
**Résolution :** Utilisez les informations fournies dans le message d’erreur pour déterminer la marche à suivre.  
  
|Messages d’exception|  
|------------------------|  
|Télécharger des jeux de données compressé a échoué|  
|Compressé dataset {0} a échoué avec le message suivant : {1}|  
|Compressé dataset {0} a échoué avec une {1} exception avec le message : {2}|  
  

## <a name="error-0132"></a>Erreur 0132  
 Aucun nom de fichier a été spécifié pour la décompression ; plusieurs fichiers ont été trouvées dans le fichier zip.  
  
 Cette erreur se produit quand aucun nom de fichier a été spécifié pour la décompression ; plusieurs fichiers ont été trouvées dans le fichier zip. Vous recevez cette erreur si le fichier .zip contient plus d’un fichier compressé, mais vous n’avez pas spécifié un fichier pour l’extraction dans le **jeu de données à décompresser** zone de texte, dans le **propriété** volet du module. Actuellement, un seul fichier peut être extrait chaque fois que le module est exécuté.  
  
**Résolution :** Le message d’erreur fournit une liste des fichiers trouvés dans le fichier .zip. Copiez le nom du fichier souhaité et collez-la dans la **jeu de données à décompresser** zone de texte.  
  
|Messages d’exception|  
|------------------------|  
|Fichier zip contient plusieurs fichiers ; Vous devez spécifier le fichier pour le développer.|  
|Le fichier contient plusieurs fichiers. Spécifiez le fichier pour le développer. Les fichiers suivants ont été trouvées : {0}|  
  

## <a name="error-0133"></a>Erreur 0133  
 Le fichier spécifié est introuvable dans le fichier zip  
  
 Cette erreur se produit lorsque le nom de fichier entré dans le **jeu de données à décompresser** champ la **propriété** volet ne correspond pas au nom de n’importe quel fichier trouvé dans le fichier .zip. Les causes les plus courantes de cette erreur sont une erreur de frappe ou recherche le fichier d’archive incorrect pour le fichier pour le développer.  
  
**Résolution :** Le module est réversible. Si le nom du fichier que vous souhaitez décompresser apparaît dans la liste des fichiers qui se trouvent, copiez le nom de fichier et collez-la dans la **jeu de données à décompresser** zone de propriété. Si vous ne voyez pas le nom de fichier souhaité dans la liste, vérifiez que vous disposez du fichier .zip approprié et le nom correct pour le fichier souhaité.  
  
|Messages d’exception|  
|------------------------|  
|Le fichier spécifié n’a pas été trouvé int le fichier zip.|  
|Le fichier spécifié est introuvable. Trouvé les ou les fichiers suivants : {0}|  
  

## <a name="error-0134"></a>Erreur 0134
Exception se produit lors de la colonne d’étiquette est manquant ou a un nombre insuffisant de lignes étiquetées.  
  
Cette erreur se produit lorsque le module a besoin d’une colonne d’étiquette, mais vous n’avez pas inclus dans la sélection des colonnes, ou la colonne d’étiquette est trop grand nombre des valeurs manquantes.

Cette erreur peut également se produire quand une opération précédente change le jeu de données telles que les lignes insuffisantes sont disponibles pour une opération en aval. Par exemple, supposons que vous utilisez une expression dans le **Partition and Sample** module pour diviser un jeu de données par valeurs. Si aucune correspondance n’est trouvée pour votre expression, un des jeux de données résultant de la partition est vide.

Résolution : 

 Si vous incluez une colonne d’étiquette dans la sélection des colonnes, mais il n’est pas reconnu, utilisez la [modifier les métadonnées](edit-metadata.md) module pour la marquer comme colonne d’étiquette.
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->Ensuite, vous pouvez utiliser la [Clean Missing Data](clean-missing-data.md) module à supprimer des lignes avec des valeurs manquantes dans la colonne d’étiquette. 

 Vérifiez vos jeux de données d’entrée pour vous assurer qu’ils contiennent des données valides et suffisamment de lignes pour répondre aux exigences de l’opération. De nombreux algorithmes génère un message d’erreur si elles nécessitent des lignes de nombre minimales de données, mais les données contiennent uniquement quelques lignes, ou uniquement un en-tête.
  
|Messages d’exception|
|------------------------|
|Exception se produit lors de la colonne d’étiquette est manquant ou a un nombre insuffisant de lignes étiquetées.|  
|Cette exception est levée lors de la colonne d’étiquette est manquante ou a moins de {0} étiqueté des lignes|  
  

## <a name="error-0135"></a>Erreur 0135  
 En fonction du Centroïde uniquement de cluster est prise en charge.  
  
**Résolution :** Vous pouvez rencontrer ce message d’erreur si vous avez tenté d’évaluer un modèle de clustering qui repose sur un algorithme de clustering personnalisé qui n’utilise pas de centroïdes pour initialiser le cluster.  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|Messages d’exception|  
|------------------------|  
|En fonction du Centroïde uniquement de cluster est prise en charge.|  
  

## <a name="error-0136"></a>Erreur 0136  
 Aucun nom de fichier a été retournée ; Impossible de traiter le fichier en conséquence.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Aucun nom de fichier a été retournée ; Impossible de traiter le fichier en conséquence.|  
  

## <a name="error-0137"></a>Erreur 0137  
 SDK de stockage Azure a rencontré une erreur de conversion entre les propriétés de la table et les colonnes de jeu de données au cours de lecture ou d’écriture.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Erreur de conversion entre les colonnes de propriété et le jeu de données de stockage table Azure.|  
|Erreur de conversion entre les colonnes de propriété et le jeu de données de stockage table Azure. Informations supplémentaires : {0}|  

## <a name="error-0138"></a>Erreur 0138  
 Mémoire épuisée, Impossible de s’exécuter complètement du module. Sous-échantillonnage le jeu de données peut aider à atténuer le problème.  
  
 Cette erreur se produit lorsque le module est en cours d’exécution requiert plus de mémoire que celles disponibles dans le conteneur Azure. Cela peut se produire si vous travaillez avec un jeu de données volumineux et que l’opération en cours ne peut pas tenir en mémoire.  
  
**Résolution :** Si vous essayez de lire un jeu de données volumineux et ne peut pas effectuer l’opération, le sous-échantillonnage le jeu de données peut-être vous aider.  
  
  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  
  
 Sometimes transient loads can lead to this error. Machine support also changes over time. 
  
 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->
  
|Messages d’exception|  
|------------------------|  
|Mémoire épuisée, Impossible de s’exécuter complètement du module.|  
  

## <a name="error-0139"></a>Erreur 0139  
 Exception se produit lorsqu’il n’est pas possible de convertir une colonne en un autre type.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous tentez de convertir une colonne en un autre type de données, mais que le type n’est pas pris en charge par l’opération en cours ou par le module.  
  
 L’erreur peut également apparaître quand un module tente de convertir implicitement des données pour répondre aux exigences du module en cours, mais la conversion n’est pas possible.  
  
**Résolution :**

1. Passez en revue vos données d’entrée et déterminer le type de données exactes de la colonne que vous souhaitez utiliser et le type de données de la colonne qui génère l’erreur. Parfois, vous pouvez considérer le type de données est correct, mais trouver qu’une opération en amont a modifié le type de données ou l’utilisation d’une colonne. Utilisez le [modifier les métadonnées](edit-metadata.md) module pour réinitialiser les métadonnées de colonne à son état d’origine. 
2. Examinez la page d’aide de module pour vérifier la configuration requise pour l’opération spécifiée. Déterminer les types de données sont pris en charge par le module actuel, et quelle plage de valeurs est prise en charge. 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. Prendre en compte s’il est possible de convertir ou d’effectuer un cast de la colonne à un autre type de données. Tous les modules suivants fournissent une flexibilité considérable et la puissance de modification des données : 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [Exécutez le Script Python](execute-python-script.md).  

> [!NOTE]
> Fonctionne toujours ne pas ? Envisagez de fournir des commentaires supplémentaires sur le problème, pour nous aider à développer des conseils de dépannage mieux. Simplement envoyer vos commentaires sur cette page et indiquez le nom du module qui a généré l’erreur et la conversion de type de données qui a échoué.
  
|Messages d’exception|  
|------------------------|  
|Conversion non autorisée.|  
|Impossible de convertir : {0}.|  
|Impossible de convertir : {0}, sur la ligne {1}.|  
|Impossible de convertir la colonne de type {0} à la colonne de type {1} sur la ligne {2}.|  
|Impossible de convertir la colonne «{2}» de type {0} à la colonne de type {1} sur la ligne {3}.|  
|Impossible de convertir la colonne «{2}» de type {0} pour la colonne «{3}» de type {1} sur la ligne {4}.| 

## <a name="error-0140"></a>Erreur 0140  
 Exception se produit si passé argument défini de colonne ne contient-elle pas d’autres colonnes à l’exception de colonne d’étiquette.  
  
 Cette erreur se produit si vous connecté un jeu de données à un module qui requiert plusieurs colonnes, y compris les fonctionnalités, mais vous avez fourni uniquement la colonne d’étiquette.  
  
**Résolution :** Choisissez au moins une colonne de fonctionnalité à inclure dans le jeu de données.  
  
|Messages d’exception|  
|------------------------|  
|Jeu de colonnes spécifié ne contient-elle pas d’autres colonnes à l’exception de colonne d’étiquette.|  
  

## <a name="error-0141"></a>Erreur 0141  
 Exception se produit si le nombre de colonnes numériques sélectionnées et de valeurs uniques dans la catégorie et de colonnes de type chaîne est trop petit.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’il n’existe pas assez de valeurs uniques dans la colonne sélectionnée pour effectuer l’opération.  
  
**Résolution :** Certaines opérations effectuent des opérations statistiques sur les fonctionnalités et les colonnes catégorielles, et s’il n’existe pas suffisamment de valeurs, l’opération peut échouer ou retourner un résultat non valide. Vérifiez votre jeu de données pour voir combien il existe dans les colonnes de fonctionnalités et des étiquettes de valeurs et déterminer si l’opération que vous essayez d’exécuter est statistiquement valide.  
  
 Si le jeu de données source est valide, vous pouvez également vérifier si une opération de métadonnées ou de manipulation de données en amont a modifié les données et supprimé certaines valeurs.  
  
 Si les opérations en amont incluent fractionnement, l’échantillonnage ou le rééchantillonnage, vérifiez que les sorties contiennent le nombre attendu de lignes et de valeurs.  
  
|Messages d’exception|  
|------------------------|  
|Le nombre de colonnes numériques sélectionnées et de valeurs uniques dans la catégorie et de colonnes de type chaîne est trop petit.|  
|Le nombre total des colonnes numériques sélectionnées et des valeurs uniques dans la catégorie et des colonnes de chaîne (actuellement {0}) doit être au moins {1}|  
  

## <a name="error-0142"></a>Erreur 0142  
 Exception se produit lorsque le système ne peut pas charger le certificat pour s’authentifier.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Le certificat ne peut pas être chargé.|  
|Le certificat {0} ne peut pas être chargé. Son empreinte numérique est {1}.|  
  

## <a name="error-0143"></a>Erreur 0143  
 Impossible d’analyser les URL fournies par l’utilisateur qui est censé se pour trouver à partir de GitHub.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous spécifiez une URL non valide et que le module requiert une URL valide de GitHub.  
  
**Résolution :** Vérifiez que l’URL fait référence à un référentiel GitHub valid. Autres types de site ne sont pas pris en charge.  
  
|Messages d’exception|  
|------------------------|  
|URL n’est pas à partir de github.com.|  
|URL n’est pas à partir de github.com : {0}|  

## <a name="error-0144"></a>Erreur 0144  
 La partie attendue est manquante dans les url GitHub fourni par l’utilisateur.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous spécifiez une source de fichier de GitHub à l’aide d’un format d’URL non valide.  
  
**Résolution :** Vérifiez que l’URL du référentiel GitHub est valide et qu’il se termine par \blob\ ou \tree\\.  
  
|Messages d’exception|  
|------------------------|  
|Impossible d’analyser les URL GitHub.|  
|Impossible d’analyser les URL GitHub (attendu ' \blob\\' ou ' \tree\\» après le nom du référentiel) : {0}|  

## <a name="error-0145"></a>Erreur 0145  
 Impossible de créer le répertoire de réplication pour une raison quelconque.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque le module ne parvient pas à créer le répertoire spécifié.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Impossible de créer le répertoire de la réplication.|  
  

## <a name="error-0146"></a>Erreur 0146  
 Lorsque les fichiers de l’utilisateur sont décompressés dans le répertoire local, le chemin d’accès combiné peut être trop long.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous extrayez des fichiers, mais certains noms de fichiers sont trop longs, lorsque vous avez décompressé.  
  
**Résolution :** Modifiez les noms de fichiers tels que combinées de chemin d’accès et nom de fichier n’est pas dépasser 248 caractères.  
  
|Messages d’exception|  
|------------------------|  
|Chemin d’accès de la réplication est plu de 248 caractères, raccourcissez le nom du script ou un chemin d’accès.|  

## <a name="error-0147"></a>Erreur 0147  
 Pas pu télécharger choses à partir de GitHub pour une raison quelconque  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous ne pouvez pas lire ou télécharger les fichiers spécifiés à partir de GitHub.  
  
**Résolution :** Le problème peut être temporaire. Vous pouvez essayer d’accéder aux fichiers à un autre moment. Ou vérifiez que vous disposez des autorisations nécessaires et que la source est valide.  
  
|Messages d’exception|  
|------------------------|  
|Erreur d’accès de GitHub.|  
|Erreur d’accès de GitHub. {0}|  
  

## <a name="error-0148"></a>Erreur 0148  
 Problèmes d’accès non autorisé lors de l’extraction de données ou de création du répertoire.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque vous essayez de créer un répertoire ou de lire les données à partir du stockage mais que vous ne disposez pas des autorisations nécessaires.  
  
**Résolution :**
  
|Messages d’exception|  
|------------------------|  
|Exception d’accès non autorisé lors de l’extraction des données.|  
  

## <a name="error-0149"></a>Erreur 0149  
 Le fichier de l’utilisateur n’existe pas à l’intérieur du bundle de GitHub.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque le fichier spécifié est introuvable.  
  
Résolution : 
  
|Messages d’exception|  
|------------------------|  
|Impossible de trouver le fichier de GitHub.|  
|Fichier de GitHub est introuvable. : {0}|  
  

## <a name="error-0150"></a>Erreur 0150  
 Les scripts sont fournis à partir du package utilisateur pas pu être décompressés, probablement en raison d’une collision avec les fichiers de GitHub.  
  
 Cette erreur dans Azure Machine Learning se produit lorsqu’un script ne peut pas être extraites, en général, lorsqu’il existe un fichier existant du même nom.  
  
Résolution :
  
|Messages d’exception|  
|------------------------|  
|Impossible de décompresser le regroupement ; collision de nom possible avec les fichiers de GitHub.|  
  

## <a name="error-0151"></a>Erreur 0151  
 Il y a une erreur d’écriture dans le stockage cloud. Vérifiez l’URL.  
  
 Cette erreur dans Azure Machine Learning se produit lorsque le module tente d’écrire des données dans un stockage cloud, mais l’URL est indisponible ou non valide.  
  
Résolution : Vérifiez l’URL et qu’il est accessible en écriture.  
  
|Messages d’exception|  
|------------------------|  
|Erreur d’écriture dans le cloud de stockage (et éventuellement une url incorrecte).|  
|Erreur d’écriture dans le stockage cloud : {0}. Vérifiez l’url.|  
  
## <a name="error-0152"></a>Erreur 0152  
 Le type de cloud Azure a été spécifié de manière incorrecte dans le contexte du module.  
  
|Messages d’exception|  
|------------------------|  
|Type de Cloud Azure incorrecte|  
|Type de Cloud Azure incorrecte : {0}|  
  
## <a name="error-0153"></a>Erreur 0153  
 Le point de terminaison de stockage spécifié n’est pas valide.  
  
|Messages d’exception|  
|------------------------|  
|Type de Cloud Azure incorrecte|  
|Point de terminaison de stockage incorrecte : {0}|  

## <a name="error-0154"></a>Erreur 0154  
 Le nom du serveur spécifié n’a pas pu être résolu.  
  
|Messages d’exception|  
|------------------------|  
|Le nom du serveur spécifié n’a pas pu être résolu.|  
|Le serveur spécifié {0}. documents.azure.com n’a pas pu être résolu|

## <a name="error-0155"></a>Erreur 0155  
 Le DocDb Client a levé une exception  
  
|Messages d’exception|  
|------------------------|  
|Le DocDb Client a levé une exception|  
|DocDb Client : {0}|

## <a name="error-0156"></a>Erreur 0156  
 Réponse incorrecte pour le serveur HCatalog.  
  
|Messages d’exception|  
|------------------------|  
|Réponse incorrecte pour le serveur HCatalog. Vérifiez que tous les services sont en cours d’exécution.|  
|Réponse incorrecte pour le serveur HCatalog. Vérifiez que tous les services sont en cours d’exécution. Détails de l’erreur : {0}|

## <a name="error-0157"></a>Erreur 0157  
 Il était une erreur de lecture à partir d’Azure Cosmos DB en raison des schémas de document incohérentes ou différentes. Lecteur nécessite tous les documents d’avoir le même schéma.  
  
|Messages d’exception|  
|------------------------|  
|Documents détectés avec des schémas différents. Assurez-vous que tous les documents ont le même schéma|

## <a name="error-1000"></a>Erreur 1000  
Exception de la bibliothèque interne.  
  
Cette erreur est fournie pour capturer sinon des erreurs du moteur interne non prise en charge. Par conséquent, la cause de cette erreur peut être différente en fonction du module qui a généré l’erreur.  
  
Pour obtenir une assistance supplémentaire, nous recommandons que vous validez le message détaillé qui accompagnent l’erreur sur le forum Azure Machine Learning, avec une description du scénario, y compris les données utilisées comme entrées. Ces commentaires nous aideront à hiérarchiser les erreurs et à identifier les problèmes les plus importants pour les tâches ultérieures.  
  
|Messages d’exception|  
|------------------------|  
|Exception de la bibliothèque.|  
|Exception de la bibliothèque : {0}|  
|{0} exception de la bibliothèque : {1}|  
