---
title: Résolution des problèmes de l’API Détecteur d’anomalies (multivarié)
titleSuffix: Azure Cognitive Services
description: Découvrez comment corriger les codes d’erreur courants lors de l’utilisation de l’API Détecteur d’anomalies
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: détection d’anomalie, Machine Learning, algorithmes
ms.openlocfilehash: 46b67e3f465bfb481389049e4c45e5c2edbefbfc
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292689"
---
# <a name="troubleshooting-the-multivariate-api"></a>Résolution des problèmes liés à l’API multivariée

Cet article fournit des conseils sur la façon de résoudre et de corriger les messages d’erreur courants lors de l’utilisation de l’API multivariée.

## <a name="multivariate-error-codes"></a>Codes d’erreur Détecteur d’anomalies (multivarié)

### <a name="common-errors"></a>Erreurs courantes

| Code d'erreur                 | Code d'erreur HTTP | Message d’erreur                                  | Commentaire                                                      |
| -------------------------- | --------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| `SubscriptionNotInHeaders` | 400             | apim-subscription-id introuvable dans les en-têtes | Ajoutez votre ID d’abonnement APIM dans l’en-tête. Exemple d’en-tête : `{"apim-subscription-id": <Your Subscription ID>}` |
| `FileNotExist`             | 400             | Le fichier \<source> n’existe pas.                  | Vérifiez la validité de la signature d’accès partagé (SAS) de votre blob. Vérifiez qu’elle n’a pas expiré. |
| `InvalidBlobURL`           | 400             |                                                | La signature d’accès partagé (SAS) de votre blob n’est pas valide.                            |
| `StorageWriteError`        | 403             |                                                | Cette erreur peut être due à des problèmes d’autorisation. Notre service n’est pas autorisé à écrire les données dans le blob chiffré par une clé gérée par le client (CMK). Vous pouvez supprimer la CMK ou accorder de nouveau l’accès à notre service. Pour plus d’informations, consultez [cette page](../../encryption/cognitive-services-encryption-keys-portal.md). |
| `StorageReadError`         | 403             |                                                | Comme pour `StorageWriteError`.                                 |
| `UnexpectedError`          | 500             |                                                | Veuillez nous contacter pour nous fournir des informations détaillées sur l’erreur. Vous pouvez utiliser les options de support décrites dans [ce document](../../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fanomaly-detector%2fcontext%2fcontext) ou nous envoyer un e-mail à l’adresse [AnomalyDetector@microsoft.com](mailto:AnomalyDetector@microsoft.com).           |


### <a name="train-a-multivariate-anomaly-detection-model"></a>Former un modèle Détecteur d’anomalies (multivarié)

| Code d'erreur               | Code d'erreur HTTP | Message d’erreur                                                | Commentaire                                                      |
| ------------------------ | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `TooManyModels`          | 400             | Cet abonnement a atteint le nombre maximal de modèles.  | Chaque ID d’abonnement APIM peut avoir 300 modèles actifs. Supprimez les modèles inutilisés avant d’entraîner un nouveau modèle. |
| `TooManyRunningModels`   | 400             | Cet abonnement a atteint le nombre maximal de modèles en cours d’exécution. | Chaque ID d’abonnement APIM peut entraîner 5 modèles simultanément. Entraînez un nouveau modèle quand le processus d’entraînement des modèles précédents est terminé. |
| `InvalidJsonFormat`      | 400             | Format JSON non valide.                                         | La demande d’entraînement n’est pas un JSON valide.                        |
| `InvalidAlignMode`       | 400             | Le champ `'alignMode'` doit être `'Inner'` ou `'Outer'`. | Vérifiez la valeur de `'alignMode'`, qui doit être `'Inner'` ou `'Outer'` (avec respect de la casse). |
| `InvalidFillNAMethod`    | 400             | Le champ `'fillNAMethod'` doit être `'Previous'`, `'Subsequent'`, `'Linear'`, `'Zero'`, `'Fixed'` ou `'NotFill'` et ne peut pas être `'NotFill'` quand `'alignMode'` est `'Outer'`. | Vérifiez la valeur de `'fillNAMethod'`. Reportez-vous à [cette section](./best-practices-multivariate.md#optional-parameters-for-training-api) pour plus d’informations. |
| `RequiredPaddingValue`   | 400             | Le champ `'paddingValue'` est nécessaire dans la demande quand `'fillNAMethod'` est `'Fixed'`. | Vous devez fournir une valeur de remplissage valide quand `'fillNAMethod'` est `'Fixed'`. Reportez-vous à [cette section](./best-practices-multivariate.md#optional-parameters-for-training-api) pour plus d’informations. |
| `RequiredSource`         | 400             | Le champ `'source'` est nécessaire dans la demande.             | Aucune valeur n’est spécifiée pour le champ `'source'` dans votre demande d’entraînement. Exemple : `{"source": <Your Blob SAS>}`. |
| `RequiredStartTime`      | 400             | Le champ `'startTime'` est nécessaire dans la demande.          | Aucune valeur n’est spécifiée pour le champ `'startTime'` dans votre demande d’entraînement. Exemple : `{"startTime": "2021-01-01T00:00:00Z"}`. |
| `InvalidTimestampFormat` | 400             | Format d’horodatage non valide. `<timestamp>` n’est pas un format valide. | Le format de l’horodatage dans le corps de la demande n’est pas correct. Vous pouvez essayer `import pandas as pd; pd.to_datetime(timestamp)` pour vérifier. |
| `RequiredEndTime`        | 400             | Le champ `'endTime'` est nécessaire dans la demande.            | Aucune valeur n’est spécifiée pour le champ `'startTime'` dans votre demande d’entraînement. Exemple : `{"endTime": "2021-01-01T00:00:00Z"}`. |
| `InvalidSlidingWindow`   | 400             | Le champ `'slidingWindow'` doit être un entier compris entre 28 et 2880. | `'slidingWindow'` doit être un entier compris entre 28 et 2880 (inclus). |

### <a name="get-multivariate-model-with-model-id"></a>Obtenir un modèle multivarié avec l’ID de modèle

| Code d'erreur      | Code d'erreur HTTP | Message d’erreur             | Commentaire                                                      |
| --------------- | --------------- | ------------------------- | ------------------------------------------------------------ |
| `ModelNotExist` | 404             | Le modèle n’existe pas. | Le modèle avec l’ID de modèle correspondant n’existe pas. Vérifiez l’ID de modèle dans l’URL de la demande. |

### <a name="list-multivariate-models"></a>Lister les modèles multivariés

| Code d'erreur      | Code d'erreur HTTP | Message d’erreur             | Commentaire                                                      |
| --------------- | --------------- | ------------------------- | ------------------------------------------------------------ |
|`InvalidRequestParameterError`| 400             | Valeurs non valides pour $skip ou $top... | Vérifiez que les valeurs des deux paramètres sont numériques. Les paramètres $skip et $top sont utilisés pour lister les modèles avec pagination. Étant donné que l’API retourne uniquement les 10 modèles les plus récemment mis à jour, vous pouvez utiliser $skip et $top pour obtenir les modèles mis à jour plus tôt. | 

### <a name="anomaly-detection-with-a-trained-model"></a>Détection d’anomalie avec un modèle entraîné

| Code d'erreur        | Code d'erreur HTTP | Message d’erreur                                                | Commentaire                                                      |
| ----------------- | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `ModelNotExist`   | 404             | Le modèle n’existe pas.                                    | Le modèle utilisé pour l’inférence n’existe pas. Vérifiez l’ID de modèle dans l’URL de la demande. |
| `ModelFailed`     | 400             | L’entraînement du modèle a échoué.                                  | Le modèle n’a pas été entraîné correctement. Récupérez des informations détaillées en obtenant le modèle avec l’ID de modèle. |
| `ModelNotReady`   | 400             | Le modèle n’est pas encore prêt.                                  | Le modèle n’est pas encore prêt. Veuillez patienter jusqu’à la fin du processus d’entraînement. |
| `InvalidFileSize` | 413             | Le fichier \<file> dépasse la limite de taille de fichier (\<size limit> octets). | La taille des données d’inférence dépasse la limite supérieure (2 Go actuellement). Utilisez moins de données pour l’inférence. |

### <a name="get-detection-results"></a>Obtenir les résultats de la détection

| Code d'erreur       | Code d'erreur HTTP | Message d’erreur              | Commentaire                                                      |
| ---------------- | --------------- | -------------------------- | ------------------------------------------------------------ |
| `ResultNotExist` | 404             | Le résultat n’existe pas. | Le résultat par demande n’existe pas. L’inférence n’est pas terminée ou le résultat a expiré (7 jours). |

### <a name="data-processing-errors"></a>Erreurs de traitement des données

Les codes d’erreur suivants n’ont pas de code d’erreur HTTP associé.

| Code d'erreur             | Message d’erreur                                                | Commentaire                                                      |
| ---------------------  | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `NoVariablesFound`     | Aucune variable n’a été trouvée. Vérifiez que vos fichiers sont organisés selon les instructions. | Aucun fichier CSV n’a été trouvé à partir de la source de données. Cette erreur est généralement due à une mauvaise organisation des fichiers. Reportez-vous aux exemples de données pour la structure souhaitée. |
| `DuplicatedVariables`  | Il existe plusieurs variables portant le même nom.             | Il existe des noms de variables en double.                         |
| `FileNotExist`         | Le fichier \<filename> n’existe pas.                              | Cette erreur se produit généralement durant l’inférence. La variable est apparue dans les données d’entraînement, mais elle est absente des données d’inférence. |
| `RedundantFile`        | Le fichier \<filename> est redondant.                                | Cette erreur se produit généralement durant l’inférence. La variable était absente des données d’entraînement, mais est apparue dans les données d’inférence. |
| `FileSizeTooLarge`     | La taille du fichier \<filename> est trop élevée.                    | La taille du fichier CSV unique \<filename> dépasse la limite. Effectuez l’entraînement avec moins de données. |
| `ReadingFileError`     | Des erreurs se sont produites durant la lecture de \<filename>. \<error messages>    | Échec de la lecture du fichier \<filename>. Vous pouvez vous reporter aux \<error messages> pour plus d’informations ou vérifier à l’aide de `pd.read_csv(filename)` dans un environnement local. |
| `FileColumnsNotExist`  | Colonne timestamp ou value inexistante dans le fichier \<filename>.  | Chaque fichier CSV doit avoir deux colonnes avec les noms **timestamp** et **value** (avec respect de la casse). |
| `VariableParseError`   | Erreur \<error message> d’analyse de la variable \<variable>.             | La \<variable> n’a pas pu être traitée en raison d’erreurs d’exécution. Reportez-vous au \<error message> pour plus d’informations, ou contactez-nous pour nous communiquer le \<error message>. |
| `MergeDataFailed`      | Échec de la fusion des données. Vérifiez le format des données.              | La fusion des données a échoué. Cette erreur peut être due à un format de données incorrect, à une mauvaise organisation des fichiers, etc. Reportez-vous aux exemples de données pour la structure de fichiers actuelle. |
| `ColumnNotFound`       | Colonne \<column> introuvable dans les données fusionnées.          | Une colonne est manquante après la fusion. Vérifiez les données.     |
| `NumColumnsMismatch`   | Le nombre de colonnes de données fusionnées ne correspond pas au nombre de variables. | Vérifiez les données.                                      |
| `TooManyData`          | Points de données trop nombreux. Le nombre maximal est 1 000 000 par variable.       | Veuillez réduire la taille des données d’entrée.                        |
| `NoData`               | Aucune donnée effective                                   | Il n’y a aucune donnée à entraîner/inférencer après le traitement. Vérifiez l’heure de début et l’heure de fin. |
| `DataExceedsLimit`     | La longueur des données dont l’horodatage est compris entre `startTime` et `endTime` dépasse la limite (\<limit>). | La taille des données après le traitement dépasse la limite. (Il n’existe actuellement aucune limite pour les données traitées.) |
| `NotEnoughInput`       | Données insuffisantes. La longueur des données est \<data length>, mais la longueur minimale doit être supérieure à celle de la fenêtre glissante, soit \<sliding window size>. | Le nombre minimal de points de données pour l’inférence correspond à la taille de la fenêtre glissante. Essayez de fournir plus de données pour l’inférence. |