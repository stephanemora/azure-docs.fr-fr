---
title: Mettre à l’échelle et gérer des compétences personnalisées
titleSuffix: Azure Cognitive Search
description: Découvrez les outils et techniques permettant de mettre à l’échelle efficacement une compétence personnalisée pour un débit maximal. Les compétences personnalisées appellent des modèles d’IA ou une logique définis par l’utilisateur que vous pouvez ajouter à un pipeline d’indexation enrichi par l’IA dans la Recherche cognitive Azure.
manager: luisca
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 22e48239631850d82cbb3e3208748416087da87c
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103422093"
---
# <a name="efficiently-scale-out-a-custom-skill"></a>Faire évoluer efficacement une compétence personnalisée

Les compétences personnalisées sont des API web qui implémentent une interface spécifique. Une compétence personnalisée peut être implémentée sur n’importe quelle ressource adressable publiquement. Les implémentations les plus courantes pour les compétences personnalisées sont les suivantes :
* Azure Functions pour les compétences logiques personnalisées
* Azure Webapps pour les compétences d’IA en conteneur simple
* Azure Kubernetes Service pour des compétences plus complexes ou plus volumineuses.

## <a name="prerequisites"></a>Prérequis

+ Pour connaître l’interface d’entrée et de sortie qu’une compétence personnalisée doit implémenter, examinez l’[interface de compétence personnalisée](cognitive-search-custom-skill-interface.md).

+ Configurez votre environnement. Vous pouvez commencer avec [ce tutoriel de bout en bout](/python/tutorial-vs-code-serverless-python-01) pour configurer une fonction Azure serverless à l’aide d’extensions Visual Studio Code et Python.

## <a name="skillset-configuration"></a>Configuration d’un ensemble de compétences

La configuration d’une compétence personnalisée pour optimiser le débit du processus d’indexation requiert une compréhension de la compétence, des configurations de l’indexeur et de la façon dont les compétences sont liées à chaque document. Par exemple, le nombre de fois qu’une compétence est appelée par document et la durée attendue par appel.

### <a name="skill-settings"></a>Paramètres de compétence

Sur la [compétence personnalisée](cognitive-search-custom-skill-web-api.md), définissez les paramètres suivants.

1. Définissez la `batchSize` de la compétence personnalisée pour configurer le nombre d’enregistrements envoyés à la compétence dans un unique appel à la compétence.

2. Définissez le `degreeOfParallelism` pour étalonner le nombre de requêtes simultanées que l’indexeur enverra à votre compétence.

3. Définissez `timeout` sur une valeur suffisante pour que la compétence réponde avec une réponse valide.

4. Dans la définition `indexer`, définissez [`batchSize`](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters) sur le nombre de documents qui doivent être lus à partir de la source de données et enrichis simultanément.

### <a name="considerations"></a>Considérations

La définition de ces variables pour optimiser les performances des indexeurs nécessite de déterminer si vos compétences sont optimales avec de nombreuses petites requêtes simultanées ou un nombre moindre de requêtes plus volumineuses. Voici quelques questions à prendre en compte :

* Quelle est la cardinalité de l’appel de compétence ? La compétence s’exécute-t-elle une fois pour chaque document, par exemple une compétence de classification de documents, ou peut-elle être exécutée plusieurs fois par document, une compétence de classification de paragraphes ?

* En moyenne, combien de documents sont lus à partir de la source de données pour remplir une demande de compétence basée sur la taille du lot de compétences ? Dans l’idéal, cette valeur doit être inférieure à la taille du lot de l’indexeur. Avec des tailles de lot supérieures à 1, votre compétence peut recevoir des enregistrements de plusieurs documents sources. Par exemple, si le nombre de lots de l’indexeur est de 5 et que le nombre de lots de compétences est de 50, et que chaque document ne génère que cinq enregistrements, l’indexeur doit remplir une requête de compétence personnalisée sur plusieurs lots d’indexeurs.

* Le nombre moyen de demandes qu’un lot d’indexeurs peut générer doit vous fournir un paramètre optimal pour les degrés de parallélisme. Si votre infrastructure hébergeant la compétence ne peut pas prendre en charge ce niveau de simultanéité, envisagez de réduire les degrés de parallélisme. Il est recommandé de tester votre configuration avec quelques documents pour valider vos choix sur les paramètres.

* Testez avec un échantillon de documents plus petit, évaluez le temps d’exécution de vos compétences par rapport au temps total nécessaire pour traiter le sous-ensemble de documents. Votre indexeur consacre-t-il plus de temps à la création d’un lot ou à l’attente d’une réponse de votre compétence ? 

* Tenez compte des implications en amont du parallélisme. Si l’entrée d’une compétence personnalisée est une sortie d’une compétence antérieure, toutes les compétences de l’ensemble sont-elles mises à l’échelle efficacement pour réduire la latence ?

## <a name="error-handling-in-the-custom-skill"></a>Gestion des erreurs dans la compétence personnalisée

Les compétences personnalisées doivent renvoyer un code d’état de réussite HTTP 200 lorsque la compétence se termine correctement. Si un ou plusieurs enregistrements d’un lot génèrent des erreurs, envisagez de renvoyer le code d’état multiple 207. La liste d’erreurs ou d’avertissements de l’enregistrement doit contenir le message approprié.

Tout élément d’un lot qui génère des erreurs entraînera l’échec du document correspondant. Si vous avez besoin que le document aboutisse, retournez un avertissement.

Tout code d’état supérieur à 299 est évalué comme une erreur et tous les enrichissements échouent, ce qui entraîne l’échec du document. 

### <a name="common-error-messages"></a>Messages d’erreur courants

* `Could not execute skill because it did not execute within the time limit '00:00:30'. This is likely transient. Please try again later. For custom skills, consider increasing the 'timeout' parameter on your skill in the skillset.` Définissez le paramètre de délai d’expiration sur la compétence pour permettre une durée d’exécution plus longue.

* `Could not execute skill because Web Api skill response is invalid.` Indique que la compétence ne renvoie pas de message au format de réponse de la compétence personnalisée. Cela peut être le résultat d’une exception non interceptée dans la compétence.

* `Could not execute skill because the Web Api request failed.` Très probablement causé par des erreurs d’autorisation ou des exceptions.

* `Could not execute skill.` Généralement, le résultat du mappage de la réponse de compétence à une propriété existante dans la hiérarchie du document.

## <a name="testing-custom-skills"></a>Test des compétences personnalisées

Commencez par tester vos compétences personnalisées avec un client d’API REST pour confirmer que :

* La compétence implémente l’interface de compétence personnalisée pour les demandes et les réponses

* La compétence retourne un JSON valide avec le type MIME `application/JSON`

* La compétence retourne un code d’état HTTP valide

Créez une [session de débogage](cognitive-search-debug-session.md) pour ajouter votre compétence aux compétences et vous assurer qu’elle produit un enrichissement valide. Bien qu’une session de débogage ne vous permette pas d’ajuster les performances de la compétence, elle vous permet de vous assurer que la compétence est configurée avec des valeurs valides et retourne les objets enrichis attendus.

## <a name="best-practices"></a>Meilleures pratiques

* Bien que les compétences peuvent accepter et renvoyer des charges utiles plus volumineuses, envisagez de limiter la réponse à 150 Mo ou moins lors du retour de JSON.

* Envisagez de définir la taille du lot sur l’indexeur et la compétence pour vous assurer que chaque lot de sources de données génère une charge utile complète pour vos compétences.

* Pour les tâches de longue durée, définissez le délai d’expiration sur une valeur suffisamment élevée pour vous assurer que l’indexeur n’ait pas d’erreur d’expiration lors du traitement de plusieurs documents simultanément.

* Optimisez la taille des lots de l’indexeur, la taille des lots de compétences et les degrés de parallélisme des compétences pour générer le modèle de charge attendu par vos compétences, avec moins de requêtes volumineuses ou de nombreuses petites requêtes.

* Surveillez les compétences personnalisées avec des journaux détaillés des défaillances, car vous pouvez avoir des scénarios dans lesquels des requêtes spécifiques échouent systématiquement en raison de la variabilité des données.


## <a name="next-steps"></a>Étapes suivantes
Félicitations ! Vos compétences personnalisées sont désormais mises à l’échelle pour maximiser le débit sur l’indexeur. 

+ [Super compétences : référentiel de compétences personnalisées](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Ajouter une qualification personnalisée à un pipeline d’enrichissement par IA](cognitive-search-custom-skill-interface.md)
+ [Ajouter une compétence Azure Machine Learning](https://docs.microsoft.com/azure/search/cognitive-search-aml-skill)
+ [Utiliser les sessions de débogage pour tester les changements](https://docs.microsoft.com/azure/search/cognitive-search-debug-session)