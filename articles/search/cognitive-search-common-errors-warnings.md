---
title: Erreurs et avertissements courants - Recherche azure
description: Cet article fournit des informations et des solutions aux erreurs et aux avertissements courants que vous pourriez rencontrer lors de l'enrichissement de l'IA dans Recherche Azure.
services: search
manager: heidist
author: amotley
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: abmotley
ms.openlocfilehash: b5a161e570489e6382f2226ab5dc9a1c34dc67df
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028317"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Erreurs et avertissements courants du pipeline d'enrichissement de l'IA dans Recherche Azure

Cet article fournit des informations et des solutions aux erreurs et aux avertissements courants que vous pourriez rencontrer lors de l'enrichissement de l'IA dans Recherche Azure.

## <a name="errors"></a>Errors
L’indexation s’arrête quand le nombre d’erreurs dépasse la valeur de ['maxFailedItems'](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Si vous souhaitez que les indexeurs ignorent ces erreurs (et sautent les « documents en échec »), envisagez de mettre à jour `maxFailedItems` et `maxFailedItemsPerBatch` comme décrit [ici](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Chaque document en échec et la clé de document correspondante (quand elle est disponible) sont présentées comme une erreur dans l’état d’exécution de l’indexeur. Vous pouvez utiliser l’[api index](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) pour charger manuellement les documents à un moment ultérieur si vous avez défini l’indexeur de façon à tolérer les échecs.

Les sections suivantes peuvent vous aider à résoudre les erreurs et à poursuivre l'indexation.

### <a name="could-not-read-document"></a>Impossible de lire le document
L'indexeur n'a pas pu lire le document à partir de la source de données. Cela peut se produire si :

| Motif | Exemples | Action |
| --- | --- | --- |
| types de champs incohérents dans différents documents | Le type de valeur ne correspond pas au type de colonne. Impossible de stocker `'{47.6,-122.1}'` dans la colonne des auteurs.  Le type attendu est JArray. | Assurez-vous que le type de chaque champ est identique dans les différents documents. Par exemple, si le champ `'startTime'` du premier document est un champ DateTime et une chaîne de caractères dans le second document, cette erreur s’affichera. |
| erreurs provenant du service sous-jacent de la source de données | (de Cosmos DB) `{"Errors":["Request rate is large"]}` | Vérifiez l’intégrité de votre instance de stockage. Vous devrez peut-être ajuster votre mise à l'échelle/partitionnement. |
| problèmes temporaires | Une erreur de niveau transport s’est produite lors de la réception des résultats à partir du serveur. (fournisseur : Fournisseur TCP, erreur : 0 - Une connexion existante a été fermée de force par l'hôte distant | Certains problèmes de connectivité inattendus peuvent survenir. Essayez d'exécuter ultérieurement le document dans votre indexeur. |

### <a name="could-not-extract-document-content"></a>Impossible d'extraire le contenu du document
L'indexeur avec une source de données Blob n'a pas pu extraire le contenu du document (par exemple, un fichier PDF). Cela peut se produire si :

| Motif | Exemples | Action |
| --- | --- | --- |
| l’objet Blob dépasse la limite de taille | Le document affiche une taille de `'134217728'`, ce qui est supérieur à la taille maximale de `'150441598'` pour l’extraction de document correspondant à votre niveau de service actuel. | [Erreurs d’indexation des objets Blob](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| L’objet Blob a un type de contenu non pris en charge | Le document contient un type de contenu `'image/png'` non pris en charge | [Erreurs d’indexation des objets Blob](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| L’objet Blob est chiffré | Le document n'a pas pu être traité car il est peut-être chiffré ou protégé par un mot de passe. | [Paramètres des objets Blob](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| problèmes temporaires | Erreur de traitement des objets Blob : La demande a été abandonnée : La demande a été annulée. | Certains problèmes de connectivité inattendus peuvent survenir. Essayez d'exécuter ultérieurement le document dans votre indexeur. |

### <a name="could-not-parse-document"></a>Impossible d’analyser le document
L’indexeur a lu le document à partir de la source de données, mais un problème est survenu lors de la conversion du contenu du document au schéma de mappage de champs spécifié. Cela peut se produire si :

| Motif | Exemples | Action |
| --- | --- | --- |
| La clé du document est manquante | La clé du document ne peut pas être manquante ou vide | Vérifiez que tous les documents contiennent des clés de documents valides |
| La clé du document n’est pas valide | La clé du document ne peut pas contenir plus de 1024 caractères | Modifiez la clé du document pour répondre aux exigences de validation. |
| Impossible d'appliquer le mappage de champs à un champ | Impossible d'appliquer la fonction de mappage `'functionName'` au champ `'fieldName'`. Le tableau ne peut pas être null. Nom du paramètre : octets | Vérifiez soigneusement les [mappages de champs](search-indexer-field-mappings.md) définis sur l'indexeur, puis comparez ces valeurs avec les données du champ spécifié du document en échec. Il peut être nécessaire de modifier les mappages de champs ou les données du document. |
| Impossible de lire la valeur du champ | Impossible de lire la valeur de la colonne `'fieldName'` à l'index `'fieldIndex'`. Une erreur de niveau transport s’est produite lors de la réception des résultats à partir du serveur. (fournisseur : Fournisseur TCP, erreur : 0 - Une connexion existante a été fermée de force par l'hôte distant.) | Ces erreurs sont généralement dues à des problèmes de connectivité inattendus avec le service sous-jacent de la source de données. Essayez d'exécuter ultérieurement le document dans votre indexeur. |

### <a name="could-not-index-document"></a>Impossible d’indexer le document
Le document a été lu et traité, mais l’indexeur n’a pas pu l’ajouter à l’index de recherche. Cela peut se produire si :

| Motif | Exemples | Action |
| --- | --- | --- |
| Un champ contient un terme trop grand | Votre document contient un terme qui dépasse la [limite de 32 Ko](search-limits-quotas-capacity.md#api-request-limits) | Vous pouvez éviter cette restriction en vérifiant que le champ n’est pas configuré comme étant filtrable, à choix multiple ou triable.
| Le document est trop volumineux pour être indexé | Un document est plus volumineux que la [taille maximale de demande d’API](search-limits-quotas-capacity.md#api-request-limits) | [Indexer les jeux de données volumineux](search-howto-large-index.md)

### <a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>L'entrée de compétence 'languageCode' contient les codes de langue suivants 'X,Y,Z', dont au moins un élément n’est pas valide.
Une ou plusieurs des valeurs passées dans l'entrée optionnelle `languageCode` d'une compétence en aval n'est pas prise en charge. Cela peut se produire si vous passez la sortie de [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) à des compétences ultérieures, et que la sortie comporte plus de langues que celles prises en charge dans ces compétences en aval.

Si vous savez que votre jeu de données utilise une seule langue, vous devriez supprimer [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) et l’entrée de compétence `languageCode`, puis utiliser plutôt `defaultLanguageCode` pour ce paramètre de compétence, en supposant que la langue est prise en charge pour cette compétence.

Si vous savez que votre jeu de données contient plusieurs langues et que vous avez donc besoin de [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) et de l’entrée `languageCode`, ajoutez un paramètre [ConditionalSkill](cognitive-search-skill-conditional.md) pour filtrer le texte selon les langues non prises en charge avant de passer le texte à la compétence en aval.  Voici un exemple de résultat avec le paramètre EntityRecognitionSkill :

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

Voici quelques références pour les langues actuellement prises en charge pour chacune des compétences qui peuvent générer ce message d'erreur :
* [Langues prises en charge pour l'analyse de texte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (pour [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) et [SentimentSkill](cognitive-search-skill-sentiment.md))
* [Langues prises en charge par le traducteur](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (pour [Text TranslationSkill](cognitive-search-skill-text-translation.md))
* [Text SplitSkill](cognitive-search-skill-textsplit.md) - langues prises en charge : `da, de, en, es, fi, fr, it, ko, pt`

### <a name="skill-did-not-execute-within-the-time-limit"></a>La compétence n'a pas été exécutée dans le délai imparti
Il existe deux cas dans lesquels vous pouvez rencontrer ce message d'erreur, chacun d'entre eux devant être traité différemment. Veuillez suivre les instructions ci-dessous en fonction de la compétence qui a retourné cette erreur.

#### <a name="built-in-cognitive-service-skills"></a>Compétences de service cognitives intégrées
Bon nombre des compétences cognitives intégrées, notamment la détection de la langue, la reconnaissance d'entités ou la reconnaissance optique de caractères (OCR), s’appuient sur un point de terminaison Cognitive Service API. Parfois, des problèmes temporaires peuvent survenir avec ces points de terminaison, entraînant l’expiration d’une demande. Il n’existe aucun remède pour ces problèmes temporaires, si ce n'est d'attendre et de réessayer. Pour résoudre le problème, réglez votre indexeur afin de l’[exécuter selon une planification](search-howto-schedule-indexers.md). L'indexation planifiée reprend là où elle s'était arrêtée. Si les problèmes temporaires ont été résolus, l'indexation et le traitement des compétences cognitives devraient reprendre à la prochaine exécution planifiée.

#### <a name="custom-skills"></a>Compétences personnalisées
Si vous rencontrez une erreur d’expiration d’une compétence personnalisée que vous avez créée, vous pouvez essayer plusieurs solutions. Tout d'abord, passez en revue votre compétence personnalisée et vérifiez qu'elle ne se retrouve pas bloquée dans une boucle infinie et qu'elle renvoie un résultat constant. Une fois ce point vérifié, déterminez le délai d'exécution de votre compétence. Si vous n'avez pas explicitement défini une valeur `timeout` dans votre définition de compétence personnalisée, la valeur par défaut `timeout` est 30 secondes. Si 30 secondes ne suffisent pas à l’exécution de votre compétence, vous pouvez spécifier une valeur plus élevée `timeout` dans la définition de votre compétence personnalisée. Voici un exemple de définition de compétence personnalisée où le délai d’expiration est fixé à 90 secondes :

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

La valeur maximale que vous pouvez définir pour le paramètre `timeout` est de 230 secondes.  Si votre compétence personnalisée ne peut pas s'exécuter de façon constante en 230 secondes, vous pouvez réduire la valeur `batchSize` de votre compétence personnalisée afin qu'elle ait moins de documents à traiter en une seule exécution.  Si vous avez déjà réglé votre valeur `batchSize` sur 1, vous devrez réécrire la compétence pour pouvoir l'exécuter en moins de 230 secondes ou la diviser en plusieurs compétences personnalisées de sorte que le délai d'exécution d'une seule compétence personnalisée soit au maximum de 230 secondes. Pour plus d'informations, consultez la [documentation sur les compétences personnalisées](cognitive-search-custom-skill-web-api.md).

### <a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>Impossible de « `MergeOrUpload` » | « `Delete` » le document dans l’index de recherche

Le document a été lu et traité, mais l’indexeur n’a pas pu l’ajouter à l’index de recherche. Cela peut se produire si :

| Motif | Exemples | Action |
| --- | --- | --- |
| Votre document contient un terme qui dépasse la [limite de 32 Ko](search-limits-quotas-capacity.md#api-request-limits) | Un champ contient un terme trop grand | Vous pouvez éviter cette restriction en vérifiant que le champ n’est pas configuré comme étant filtrable, à choix multiple ou triable.
| Un document est plus volumineux que la [taille maximale de demande d’API](search-limits-quotas-capacity.md#api-request-limits) | Le document est trop volumineux pour être indexé | [Indexer les jeux de données volumineux](search-howto-large-index.md)
| Difficultés à se connecter à l’index cible (qui persiste après les nouvelles tentatives), car le service est soumis à une autre charge, par exemple l’interrogation ou l’indexation. | Échec de l’établissement d’une connexion pour mettre à jour l’index. Le service de recherche est soumis à une charge importante. | [Effectuer le scale-up de votre service de recherche](search-capacity-planning.md)
| Un correctif est appliqué au service de recherche en vue de sa mise à jour ou fait l’objet d’une reconfiguration de topologie. | Échec de l’établissement d’une connexion pour mettre à jour l’index. Le service de recherche est actuellement inopérant/Le service de recherche est en cours de transition. | Configurer le service avec au moins trois réplicas pour une disponibilité de 99,9 % selon la [documentation SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Échec dans la ressource de calcul/réseau sous-jacente (rare) | Échec de l’établissement d’une connexion pour mettre à jour l’index. Une erreur inconnue s’est produite. | Configurer les indexeurs pour une [exécution selon une planification](search-howto-schedule-indexers.md) pour récupérer d’un état d’échec.

##  <a name="warnings"></a>Avertissements
L’indexation des avertissements ne s’arrête pas, mais certaines conditions pourraient entraîner des résultats inattendus. Vos données et votre scénario conditionnent les mesures que vous devriez prendre.

### <a name="skill-input-was-truncated"></a>L'entrée de la compétence a été tronquée
Les compétences cognitives comportent des limites quant à la longueur du texte qui peut être analysé en une seule fois. Si la saisie de texte pour ces compétences dépasse cette limite, nous tronquerons le texte pour respecter la limite, puis appliquerons un enrichissement à ce texte tronqué. Cela signifie que la compétence est exécutée, mais pas sur toutes vos données.

Dans l'exemple LanguageDetectionSkill ci-dessous, le champ de saisie `'text'` peut déclencher cet avertissement s'il dépasse la limite de caractères. Vous trouverez les limites de saisie des compétences dans la [documentation sur les compétences](cognitive-search-predefined-skills.md).

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

Si vous voulez vous assurer que tout le texte est analysé, pensez à utiliser la [compétence Split](cognitive-search-skill-textsplit.md).