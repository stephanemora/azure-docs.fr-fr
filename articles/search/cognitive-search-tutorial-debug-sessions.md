---
title: 'Tutoriel : Utiliser Sessions de débogage pour corriger les ensembles de compétences'
titleSuffix: Azure Cognitive Search
description: Sessions de débogage (préversion) est un outil du portail Azure utilisé pour rechercher, diagnostiquer et réparer les problèmes dans un ensemble de compétences.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/02/2021
ms.openlocfilehash: ed988baec46152d55cf63aec09fce7a298157212
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509146"
---
# <a name="tutorial-debug-a-skillset-using-debug-sessions"></a>Tutoriel : Déboguer un ensemble de compétences avec Sessions de débogage

Les ensembles de compétences coordonnent une série d’actions qui analysent ou transforment du contenu, où la sortie d’une compétence devient l’entrée d’une autre compétence. Lorsque les entrées sont tributaires des sorties, les erreurs dans les définitions des ensembles de compétences et les associations de champs peuvent se traduire par des données et des opérations omises.

L’outil **Sessions de débogage** du portail Azure offre une visualisation holistique d’un ensemble de compétences. À l’aide de cet outil, vous pouvez atteindre des étapes spécifiques dans le but de découvrir facilement où peut se situer l’échec d’une action.

Dans cet article, vous allez utiliser **Sessions de débogage** pour rechercher et corriger 1) une entrée manquante, et 2) des mappages de champs de sortie. Le tutoriel est complet. Il fournit des données que vous pouvez indexer (données d’essais cliniques), une collection Postman qui crée des objets, ainsi que des instructions relatives à l’utilisation de **Sessions de débogage** permettant de rechercher et de résoudre des problèmes dans l’ensemble de compétences.

> [!Important]
> Sessions de débogage est une fonctionnalité en préversion fournie sans contrat de niveau de service ; elle n’est pas recommandée pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez disposer des prérequis suivants :

+ Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/).

+ Service Recherche cognitive Azure. [Créez un service](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce guide de démarrage rapide. 

+ Compte de stockage Azure avec le [stockage Blob](../storage/blobs/index.yml), utilisé pour héberger des exemples de données et rendre persistantes les données temporaires créées pendant une session de débogage.

+ [Application de bureau Postman](https://www.getpostman.com/) et une [collection Postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) pour créer des objets à l’aide des API REST.

+ [Exemples de données (essais cliniques)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19).

> [!NOTE]
> Ce guide de démarrage rapide utilise également [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) pour l’intelligence artificielle. Parce que la charge de travail est vraiment petite, Cognitive Services est utilisé en arrière-plan pour traiter gratuitement jusqu’à 20 transactions. Cela signifie que vous pouvez effectuer cet exercice sans avoir à créer une ressource Cognitive Services supplémentaire.

## <a name="set-up-your-data"></a>Configurer vos données

Cette section crée l’exemple de jeu de données dans le stockage Blob Azure, afin que l’indexeur et l’ensemble de compétences disposent de contenu à utiliser.

1. [Téléchargez l’exemple de données (clinical-trials-pdf-19)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19) composé de 19 fichiers.

1. [Créez un compte de stockage Azure](../storage/common/storage-account-create.md?tabs=azure-portal) ou [recherchez un compte existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   + Choisissez la même région que celle de la Recherche cognitive Azure pour éviter des frais de bande passante.

   + Il doit être de type StorageV2 (V2 universel).

1. Accédez aux pages des services du Stockage Azure dans le portail, et créez un conteneur de blobs. Une bonne pratique consiste à spécifier le niveau d’accès «privé ». Nommez votre conteneur `clinicaltrialdataset`.

1. Dans le conteneur, cliquez sur **Charger** pour charger les exemples de fichiers que vous avez téléchargés et décompressés au cours de la première étape.

1. Dans le portail, récupérez et enregistrez la chaîne de connexion pour le Stockage Azure. Vous en aurez besoin pour les appels d’API REST qui indexent les données. Vous pouvez obtenir la chaîne de connexion à partir de **Paramètres** > **Clés d’accès** dans le portail.

## <a name="get-a-key-and-url"></a>Obtenir une clé et une URL

Les appels REST requièrent l’URL du service et une clé d’accès et ce, sur chaque demande. Un service de recherche est créé avec les deux. Ainsi, si vous avez ajouté la Recherche cognitive Azure à votre abonnement, effectuez ce qui suit pour obtenir les informations nécessaires :

1. [Connectez-vous au portail Azure](https://portal.azure.com/), puis dans la page **Vue d’ensemble** du service de recherche, récupérez l’URL. Voici un exemple de point de terminaison : `https://mydemo.search.windows.net`.

1. Dans **Paramètres** > **Clés**, obtenez une clé d’administration pour avoir des droits d’accès complets sur le service. Il existe deux clés d’administration interchangeables, fournies pour assurer la continuité de l’activité au cas où vous deviez en remplacer une. Vous pouvez utiliser la clé primaire ou secondaire sur les demandes d’ajout, de modification et de suppression d’objets.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Obtenir un point de terminaison et une clé d’accès HTTP" border="false":::

Toutes les demandes nécessitent une clé API sur chaque demande envoyée à votre service. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

## <a name="create-data-source-skillset-index-and-indexer"></a>Créer une source de données, un ensemble de compétences, un index et un indexeur

Dans cette section, Postman et la collection fournie sont utilisés pour créer la source de données, l’ensemble de compétences, l’index et l’indexeur de Recherche cognitive. Si vous ne connaissez pas suffisamment Postman, consultez [ce guide de démarrage rapide](search-get-started-rest.md).

Pour effectuer cette tâche, vous avez besoin de la [collection Postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) créée pour ce tutoriel. 

1. Démarrez et importez la collection. Sous **Fichiers** > **Nouveau**, sélectionnez la collection à importer.
1. Une fois la collection importée, développez la liste des actions (...).
1. Cliquez sur **Modifier**.
1. Sous Valeur actuelle, entrez le nom de votre `searchService` (par exemple, si le point de terminaison est `https://mydemo.search.windows.net`, le nom du service est « `mydemo` »).
1. Entrez l’`apiKey` avec la clé primaire ou secondaire de votre service de recherche.
1. Entrez la valeur `storageConnectionString` à partir de la page des clés de votre compte de stockage Azure.
1. Entrez la valeur `containerName` pour le conteneur que vous avez créé dans le compte de stockage, puis cliquez sur **Mettre à jour**.

   :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="modifier des variables dans Postman":::

La collection contient quatre appels REST différents qui servent à créer les objets utilisés dans cette section.

Le premier appel crée la source de données. `clinical-trials-ds`. Le deuxième appel crée l’ensemble de compétences, `clinical-trials-ss`. Le troisième appel crée l’index, `clinical-trials`. Le quatrième et dernier appel crée l’indexeur, `clinical-trials-idxr`.

+ Ouvrez chaque requête successivement et cliquez sur **Envoyer** pour transmettre chaque requête au service de recherche. 

Une fois tous les appels de la collection terminés, fermez Postman, puis revenez au portail Azure.

## <a name="check-results-in-the-portal"></a>Contrôler les résultats sur le portail

L’exemple de code crée délibérément un index incorrect à la suite de problèmes qui se sont produits lors de l’exécution d’un ensemble de compétences. Le problème est lié à des données manquantes. 

1. À partir de la page Vue d’ensemble du service de recherche dans le portail Azure, sélectionnez l’onglet **Index**. 
1. Sélectionnez l’index `clinical-trials`.
1. Entrez la chaîne de requête `$select=metadata_storage_path, organizations, locations&$count=true` pour retourner les champs de documents spécifiques (identifiés par le champ `metadata_storage_path` unique).
1. Cliquez sur **Rechercher** pour exécuter la requête qui retourne les 19 documents et affiche des valeurs vides pour « organizations » et « locations ».

Ces champs auraient dû être remplis à l’aide de la [compétence de reconnaissance d’entité](cognitive-search-skill-entity-recognition.md) de l’ensemble de compétences ; celle-ci est utilisée pour rechercher des organisations et des localisations n’importe où dans le contenu du blob. Dans l’exercice suivant, vous allez utiliser la session de débogage pour déterminer la cause du problème.

Vous pouvez également investiguer les erreurs et les avertissements en utilisant le portail Azure.

1. Ouvrez l’onglet **Indexeurs** et sélectionnez `clinical-trials-idxr`.
1. Remarquez que même si le travail de l’indexeur a été correctement effectué, 57 avertissements sont répertoriés.
1. Cliquez sur **Réussite** pour afficher les avertissements (s’il s’agissait principalement d’erreurs, le lien des détails se nommerait **Échec**). Vous voyez une liste exhaustive de tous les avertissements émis par l’indexeur.

   :::image type="content" source="media/cognitive-search-debug/portal-indexer-errors-warnings.png" alt-text="afficher les avertissements":::

## <a name="start-your-debug-session"></a>Démarrer votre session de débogage

:::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="démarrer une nouvelle session de débogage":::

1. Dans la page de la vue d’ensemble de la recherche, cliquez sur l’onglet **Sessions de débogage**.
1. Sélectionnez **+ Nouvelle session de débogage**.
1. Donnez un nom à la session. 
1. Connectez la session à votre compte de stockage. 
1. Dans le modèle Indexeur, indiquez le nom de l’indexeur. L’indexeur a des références à la source de données, à l’ensemble de compétences et à l’index.
1. Acceptez le choix de document par défaut pour le premier document de la collection. 
1. **Enregistrez** la session. L’enregistrement de la session lance le pipeline d’enrichissement par IA tel que défini par l’ensemble de compétences.

> [!Important]
> Une session de débogage ne fonctionne qu’avec un seul document. Vous pouvez choisir le document à déboguer, ou simplement utiliser le premier proposé.

<!-- > :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="New debug session started"::: -->

Quand l’initialisation de la session de débogage est terminée, la session affiche par défaut l’onglet **Enrichissements par IA**, en mettant en évidence le **Graphe des compétences**. Le graphe des compétences fournit une hiérarchie visuelle de l’ensemble de compétences, et de son ordre d’exécution de manière séquentielle et en parallèle.

## <a name="find-issues-with-the-skillset"></a>Trouver les problèmes liés à l’ensemble de compétences

Tous les problèmes signalés par l’indexeur se trouvent sous l’onglet adjacent **Erreurs/avertissements**. 

Remarquez que l’onglet **Erreurs/avertissements** propose une liste bien plus courte que celle affichée précédemment puisqu’elle renseigne uniquement sur les erreurs d’un seul document. Comme avec la liste affichée par l’indexeur, vous pouvez cliquer sur un message d’avertissement pour voir les détails le concernant.

Sélectionnez **Erreurs/avertissements** pour passer en revue les notifications. Vous devez en voir trois :

   + « Impossible de mapper le champ de sortie "locations" à l’index de recherche. Vérifiez la propriété outputFieldMappings de votre indexeur.
Valeur /document/merged_content/locations manquante ».

   + « Impossible de mapper le champ de sortie "organizations" à l’index de recherche. Vérifiez la propriété outputFieldMappings de votre indexeur.
Valeur /document/merged_content/organizations manquante ».

   + « Compétence exécutée mais susceptible de présenter des résultats inattendus, car une ou plusieurs entrées de compétence ne sont pas valides.
L’entrée de compétence facultative est manquante. Nom : languageCode, Source : /document/languageCode. Problèmes d’analyse de la langue d’expression : Valeur /document/languageCode manquante ».

   Nombreuses sont les compétences à être dotées d’un paramètre languageCode. En examinant l’opération de plus près, vous pouvez voir que cette entrée de code de langue est absente de `Enrichment.NerSkillV2.#1` ; il s’agit de la même compétence de reconnaissance d’entité qui rencontre des problèmes avec les sorties « locations » et « organizations ». 

Étant donné que les trois notifications se rapportent à cette compétence, l’étape suivante se limite à déboguer cette compétence. Dans la mesure du possible, commencez par résoudre les problèmes d’entrée avant de passer aux problèmes de outputFieldMapping.

 :::image type="content" source="media/cognitive-search-debug/debug-session-errors-warnings.png" alt-text="Nouvelle session de débogage démarrée":::

<!-- + The Skill Graph provides a visual hierarchy of the skillset and its order of execution from top to bottom. Skills that are side by side in the graph are executed in parallel. Color coding of skills in the graph indicate the types of skills that are being executed in the skillset. In the example, the green skills are text and the red skill is vision. Clicking on an individual skill in the graph will display the details of that instance of the skill in the right pane of the session window. The skill settings, a JSON editor, execution details, and errors/warnings are all available for review and editing.

+ The Enriched Data Structure details the nodes in the enrichment tree generated by the skills from the source document's contents. -->

## <a name="fix-missing-skill-input-value"></a>Remédier à la valeur d’entrée de compétence manquante

Sous l’onglet **Erreurs/avertissements**, une erreur est signalée par rapport à une opération libellée `Enrichment.NerSkillV2.#1`. Les détails de cette erreur expliquent qu’il y a un problème avec une valeur d’entrée de compétence « /document/languageCode ». 

1. Revenez à l’onglet **Enrichissements par IA**.
1. Cliquez sur le **graphe des compétences**.
1. Cliquez sur la compétence étiquetée  **#1** pour afficher ses détails dans le volet de droite.
1. Recherchez l’entrée correspondant à « languageCode ».
1. Sélectionnez le symbole **</>** au début de la ligne, puis ouvrez l’évaluateur d’expression.
1. Cliquez sur le bouton **Évaluer** pour vérifier que cette expression génère une erreur. Cette opération permet de vérifier que la propriété « languageCode » n’est pas une entrée valide.

   :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="Évaluateur d’expression":::

Il existe deux façons de rechercher cette erreur dans la session. La première consiste à examiner d’où provient l’entrée, c’est-à-dire quelle compétence de la hiérarchie est supposée produire ce résultat ? L’onglet Exécutions du volet des détails des compétences doit afficher la source de l’entrée. Si aucune source n’est affichée, cela indique une erreur de mappage de champ.

1. Cliquez sur l’onglet **Exécutions**.
1. Examinez les entrées (INPUTS) et recherchez « languageCode ». Aucune source n’est indiquée pour cette entrée. 
1. Basculez vers le volet de gauche pour afficher la structure de données enrichie. Aucun chemin mappé ne correspond à « languageCode ».

   :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Structure de données enrichie":::

Il existe un chemin mappé pour « language ». Par conséquent, il y a une faute de frappe dans les paramètres des compétences. Pour résoudre ce problème, l’expression dans « /document/language » de la compétence #1 doit être mise à jour.

1. Ouvrez l’évaluateur d’expression **</>** pour le chemin « language ».
1. Copiez l’expression. Fermez la fenêtre.
1. Accédez aux paramètres des compétences de la compétence #1, puis ouvrez l’évaluateur d’expression **</>** pour l’entrée « languageCode ».
1. Collez la nouvelle valeur « /document/language » dans la zone Expression, puis cliquez sur **Évaluer**.
1. Elle doit afficher l’entrée correcte « en ». Cliquez sur Appliquer pour mettre à jour l’expression.
1. Cliquez sur **Enregistrer** dans le volet des détails des compétences, à droite.
1. Cliquez sur **Exécuter** dans le menu Fenêtre de la session. Cela lance une autre exécution de l’ensemble de compétences avec le document. 

Une fois l’exécution de la session de débogage terminée, cliquez sur l’onglet Erreurs/avertissements. Il indique alors que l’erreur intitulée « Enrichment.NerSkillV2.#1 » a disparu. Toutefois, il y a toujours deux avertissements indiquant que le service n’a pas pu mapper les champs de sortie des organisations (organizations) et des emplacements (locations) à l’index de recherche. Des valeurs sont manquantes : « /document/merged_content/organizations » et « /document/merged_content/locations ».

## <a name="fix-missing-skill-output-values"></a>Remédier aux valeurs de sortie de compétence manquantes

:::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Erreurs et avertissements":::

Il manque des valeurs de sortie d’une compétence. Pour identifier la compétence avec l’erreur, accédez à la structure de données enrichie, recherchez le nom de la valeur, puis examinez sa source d’origine. Dans le cas des valeurs « organizations » et « locations » manquantes, il s’agit de sorties de la compétence #1. Le fait d’ouvrir l’évaluateur d’expression </> pour chaque chemin affiche les expressions listées sous la forme « /document/content/organizations » et « /document/content/locations », respectivement.

:::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="Entité organizations de l’évaluateur d’expression":::

La sortie de ces entités est vide alors qu’elle ne doit pas l’être. Quelles sont les entrées produisant ce résultat ?

1. Accédez au **graphe des compétences**, puis sélectionnez la compétence #1.
1. Sélectionnez l’onglet **Exécutions** dans le volet des détails des compétences, à droite.
1. Ouvrez l’évaluateur d’expression **</>** pour l’entrée (INPUT) « text ».

   :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Entrée de la compétence de texte":::

Le résultat affiché pour cette entrée ne ressemble pas à une entrée de texte. Il ressemble à une image qui est entourée de nouvelles lignes. L’absence de texte signifie qu’aucune entité ne peut être identifiée. Si vous observez la hiérarchie de l’ensemble de compétences, vous pouvez voir que le contenu est d’abord traité par la compétence #6 (OCR), puis transmis à la compétence #5 (Fusion). 

1. Sélectionnez la compétence #5 (Fusion) dans le **graphe des compétences**.
1. Sélectionnez l’onglet **Exécutions** dans le volet des détails des compétences, à droite, puis ouvrez l’évaluateur d’expression **</>** pour les sorties (OUTPUTS) « mergedText ».

   :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Sortie de la compétence Fusion":::

Ici, le texte est associé à l’image. Si vous observez l’expression « /document/merged_content », l’erreur dans les chemins « organizations » et « locations » pour la compétence #1 est visible. Au lieu d’utiliser « /document/content », elle doit utiliser « /document/merged_content » pour les entrées « text ».

1. Copiez l’expression correspondant à la sortie « mergedText », puis fermez la fenêtre de l’évaluateur d’expression.
1. Sélectionnez la compétence #1 dans le **graphe des compétences**.
1. Sélectionnez l’onglet **Paramètres des compétences** dans le volet des détails des compétences, à droite.
1. Ouvrez l’évaluateur d’expression **</>** pour l’entrée « text ».
1. Collez la nouvelle expression dans la zone. Cliquez sur **Évaluer**.
1. L’entrée correcte avec le texte ajouté doit s’afficher. Cliquez sur **Appliquer** pour mettre à jour les paramètres des compétences.
1. Cliquez sur **Enregistrer** dans le volet des détails des compétences, à droite.
1. Cliquez sur **Exécuter** dans le menu Fenêtre de la session. Cela lance une autre exécution de l’ensemble de compétences avec le document.

Une fois l’exécution de l’indexeur terminée, les erreurs sont toujours présentes. Revenez à la compétence #1, puis recherchez la cause du problème. L’entrée pour la compétence a été corrigée de « contenu » en « merged_content ». Quelles sont les sorties de ces entités dans la compétence ?

1. Sélectionnez l’onglet **Enrichissements par IA**.
1. Sélectionnez le **graphe des compétences**, puis cliquez sur la compétence #1.
1. Parcourez les **paramètres des compétences** pour rechercher «outputs » (sorties).
1. Ouvrez l’évaluateur d’expression **</>** pour l’entité « organizations ».

   :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="Sortie de l’entité organizations":::

L’évaluation du résultat de l’expression donne le résultat correct. La compétence consiste à identifier la valeur correcte pour l’entité, « organizations ». Toutefois, le mappage de sortie dans le chemin de l’entité génère toujours une erreur. En comparant le chemin de sortie de la compétence avec le chemin de sortie de l’erreur, la compétence apparente les sorties, les organisations et les emplacements sous le nœud /document/content, tandis que le mappage de champs de sortie s’attend à ce que les résultats soient apparentés sous le nœud /document/merged_content. À l’étape précédente, l’entrée est passée de « /document/content » à « /document/merged_content ». Le contexte dans les paramètres des compétences doit être changé afin de garantir que la sortie est générée avec le contexte approprié.

1. Sélectionnez l’onglet **Enrichissements par IA**.
1. Sélectionnez le **graphe des compétences**, puis cliquez sur la compétence #1.
1. Accédez aux **Paramètres des compétences** pour rechercher « context » (contexte).
1. Double-cliquez sur le paramètre pour « context », puis remplacez-le pour qu’il indique « /document/merged_content ».
1. Cliquez sur **Enregistrer** dans le volet des détails des compétences, à droite.
1. Cliquez sur **Exécuter** dans le menu Fenêtre de la session. Cela lance une autre exécution de l’ensemble de compétences avec le document.

   :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Correction du contexte dans les paramètres des compétences":::

Toutes les erreurs ont été résolues.

## <a name="commit-changes-to-the-skillset"></a>Valider les changements apportés à l’ensemble de compétences

Quand la session de débogage a été lancée, le service de recherche a créé une copie de l’ensemble de compétences. Cette opération a été effectuée pour protéger l’ensemble de compétences d’origine de votre service de recherche. Maintenant que vous avez terminé le débogage de votre ensemble de compétences, les correctifs peuvent être validés (remplacez l’ensemble de compétences d’origine). 

Si vous n’êtes pas prêt à valider les modifications, vous pouvez également enregistrer la session de débogage et la rouvrir ultérieurement.

1. Cliquez sur **Valider les changements** dans le menu principal Sessions de débogage.
1. Cliquez sur **OK** pour vérifier que vous souhaitez mettre à jour votre ensemble de compétences.
1. Fermez la session de débogage, puis sélectionnez l’onglet **Indexeurs**.
1. Ouvrez votre indexeur « clinical-trials-idxr ».
1. Cliquez sur **Réinitialiser**.
1. Cliquez sur **Exécuter**. Cliquez sur **OK** pour confirmer.

Une fois l’exécution de l’indexeur terminée, une coche verte et le mot « Réussite » doivent s’afficher en regard de l’horodatage de la dernière exécution, sous l’onglet **Historique d’exécution**. Pour vérifier que les changements ont été appliqués :

1. Dans la page de la vue d’ensemble de la recherche, sélectionnez l’onglet **Index**.
1. Ouvrez l’index « clinical-trials », puis sous l’onglet Explorateur de recherche, entrez la chaîne de requête `$select=metadata_storage_path, organizations, locations&$count=true` pour retourner les champs de documents spécifiques (identifiés par le champ `metadata_storage_path` unique).
1. Cliquez sur **Rechercher**.

Les résultats doivent indiquer que les organisations et les localisations sont maintenant renseignées avec les valeurs attendues.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous travaillez dans votre propre abonnement, il est recommandé, à la fin de chaque projet, de déterminer si vous avez toujours besoin des ressources que vous avez créées. Les ressources laissées en cours d’exécution peuvent vous coûter de l’argent. Vous pouvez supprimer les ressources une par une, ou choisir de supprimer le groupe de ressources afin de supprimer l’ensemble des ressources.

Vous pouvez rechercher et gérer les ressources dans le portail à l’aide des liens **Toutes les ressources** ou **Groupes de ressources** situés dans le volet de navigation de gauche.

Si vous utilisez un service gratuit, n’oubliez pas que vous êtes limité à trois index, indexeurs et sources de données. Vous pouvez supprimer des éléments un par un dans le portail pour ne pas dépasser la limite. 

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel a abordé différents aspects de la définition et du traitement de l’ensemble de compétences. Pour en savoir plus sur les concepts et les workflows, consultez les articles suivants :

+ [Guide pratique pour mapper les champs de sortie d’un ensemble de compétences aux champs d’un index de recherche](cognitive-search-output-field-mapping.md)

+ [Ensembles de compétences dans Recherche cognitive Azure](cognitive-search-working-with-skillsets.md)

+ [Guide pratique pour configurer la mise en cache de l’enrichissement incrémentiel](cognitive-search-incremental-indexing-conceptual.md)