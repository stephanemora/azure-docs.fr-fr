---
title: Foire aux questions sur le service de reconnaissance vocale dans Azure
description: Vous trouverez ici des réponses aux questions les plus posées sur le service de reconnaissance vocale.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: e5ba01c25646578da22f054659051be3515e9e4b
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281828"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Foire aux questions sur la reconnaissance vocale

Si vous ne trouvez pas de réponses à vos questions dans cette foire aux questions, consultez d’autres options d’assistance [ici](support.md).

## <a name="general"></a>Généralités

**Question** : Quelle est la différence entre les modèles de reconnaissance vocale personnalisés et les modèles de base ?

**Réponse** : Les modèles de base ont été formés avec des données appartenant à Microsoft et sont déjà déployés dans le cloud. Les modèles personnalisés permettent à l’utilisateur d’adapter un modèle afin qu’il corresponde mieux à un environnement particulier avec du bruit ambiant ou un langage spécifiques. Les ateliers, les voitures et les rues bruyantes supposent un modèle acoustique adapté, tandis que des sujets tels que la biologie, la physique ou la radiologie, les noms de produits et les acronymes personnalisés nécessitent un modèle de langage approprié.

**Question** : Par où commencer si je souhaite utiliser un modèle de base ?

**Réponse** : Vous avez tout d’abord besoin d’une [clé d’abonnement](get-started.md). Si vous voulez passer des appels REST à des modèles de référence prédéployés, consultez les [informations disponibles ici](rest-apis.md). Si vous voulez utiliser WebSockets, téléchargez le [kit SDK](speech-sdk.md).

**Question** : Dois-je toujours générer un modèle vocal personnalisé ?

**Réponse** : Non. Si votre application utilise un langage générique de tous les jours, il n’est pas nécessaire de personnaliser un modèle. Si elle est utilisée dans un environnement dénué de bruit de fond ou presque, ce n’est pas nécessaire non plus. Le portail permet aux utilisateurs de déployer des modèles de base et des modèles personnalisés, ainsi que d’exécuter des tests de précision dessus. Ils peuvent utiliser cette fonctionnalité pour mesurer la précision d’un modèle de base et celle d’un modèle personnalisé.

**Question** : Comment savoir si le traitement de mon jeu de données ou de mon modèle est terminé ?

**Réponse** : Actuellement, la seule façon de le savoir est de consulter l’état du modèle ou du jeu de données situé dans la table.
Quand le traitement est terminé, l’état indique « Réussi ».

**Question** : Puis-je créer plusieurs modèles ?

**Réponse** : Le nombre de modèles dans une collection n’est pas limité.

**Question** : J’ai réalisé que j’ai fait une erreur. Comment annuler l’importation de mes données ou la création d’un modèle en cours ? 

**Réponse** : Actuellement, vous ne pouvez pas restaurer un processus d’adaptation de langage ou acoustique. Les modèles et les données importés peuvent être supprimés une fois qu’ils sont dans un état final.

**Question** : Quelle est la différence entre les modèles Recherche & Dictée et les modèles de conversation ?

**Réponse** : Il existe plusieurs modèles de base dans le service de reconnaissance vocale. Le modèle Conversation est adapté à la reconnaissance vocale de type conversationnel. Il est idéal pour transcrire des appels, tandis que Recherche & Dictée convient tout particulièrement aux applications à commande vocale. Le nouveau modèle Universel vise à couvrir ces deux scénarios.

**Question** : Puis-je mettre à jour mon modèle existant (empilement de modèles) ?

**Réponse** : Les modèles existants ne peuvent pas être mis à jour. Pour contourner cet inconvénient, combinez l’ancien jeu de données avec le nouveau, puis procédez à nouveau aux adaptions souhaitées.

L’ancien jeu de données et le nouveau doivent être combinés en un seul fichier .zip (s’il s’agit de données acoustiques) ou en un fichier .txt (s’il s’agit de données de langage). Une fois l’adaptation terminée, le déploiement du nouveau modèle mis à jour doit être annulé afin d’obtenir un nouveau point de terminaison.

**Question** : Que faire si j’ai besoin d’une concurrence plus élevée pour mon modèle déployé que ce que propose le portail ? 

**Réponse** : Vous pouvez augmenter la taille de votre modèle par incréments de 20 requêtes simultanées. 

Contactez-nous si vous avez besoin d’augmenter cette valeur.

**Question** : Puis-je télécharger mon modèle et l’exécuter localement ?

**Réponse** : Les modèles ne peuvent pas être téléchargés et exécutés localement.

**Question** : Mes requêtes sont-elles journalisées ?

**Réponse** : Lors de la création d’un déploiement, vous pouvez décider de désactiver le suivi, ce qui signifie qu’aucun contenu audio ni aucune transcription ne sera journalisé. Dans le cas contraire, les requêtes sont généralement journalisées dans un stockage sécurisé dans Azure. Si vous avez d’autres questions sur la confidentialité qui vous empêchent d’utiliser Custom Speech Service, contactez l’un des canaux de support.

## <a name="importing-data"></a>Importation de données

**Question** : Quelle est la limite de taille du jeu de données ? Pourquoi ? 

**Réponse** : La limite actuelle pour un jeu de données est de 2 Go, en raison de la restriction de taille d’un fichier pour le chargement HTTP. 

**Question** : Puis-je compresser mes fichiers texte pour charger un fichier texte plus volumineux ? 

**Réponse** : Non, seuls des fichiers texte non compressés sont actuellement autorisés.

**Question** : Le rapport de données indique que des énoncés ont échoué. Quel est le problème ?

**Réponse** : L’échec du chargement de 100 % des énoncés dans un fichier n’est pas un problème.
Si la majeure partie des énoncés d’un jeu de données acoustiques ou de langage (par exemple, plus de 95 %) est importée avec succès, le jeu de données peut être utilisable. Toutefois, il est recommandé d’essayer de comprendre la raison de l’échec des énoncés et d’y remédier. La plupart des problèmes courants, comme les erreurs de mise en forme, sont faciles à corriger. 

## <a name="creating-am"></a>Création d’un modèle acoustique

**Question** : De quel volume de données acoustiques ai-je besoin ?

**Réponse** : Nous vous recommandons de commencer par un volume de 30 minutes à une heure de données acoustiques.

**Question** : Quelles données dois-je collecter ?

**Réponse** : Collectez les données qui sont le plus proche possible du scénario d’application et du cas d’usage.
La collection de données doit correspondre à l’application et aux utilisateurs cibles en termes d’appareils, d’environnements et de types d’orateurs. En général, vous devez collecter des données d’un éventail d’orateurs aussi large que possible. 

**Question** : Comment les collecter ? 

**Réponse** : Vous pouvez créer une application de collecte de données autonome, ou en utiliser une des logiciels d’enregistrement audio du commerce.
Vous pouvez également créer une version de votre application qui journalise les données audio et les utilise. 

**Question** : Dois-je transcrire moi-même les données d’adaptation ? 

**Réponse** : Oui ! Vous pouvez les transcrire vous-même ou utiliser un service de transcription professionnel. Certains utilisateurs préfèrent les transcripteurs professionnels, tandis que d’autres utilisent le crowdsourcing ou font les transcriptions eux-mêmes.

## <a name="accuracy-testing"></a>Tests de précision

**Question** : Puis-je effectuer des tests hors ligne de mon modèle acoustique personnalisé à l’aide d’un modèle de langage personnalisé ?

**Réponse** : Oui. Sélectionnez simplement le modèle de langage personnalisé dans la liste déroulante quand vous configurez le test hors ligne.

**Question** : Puis-je effectuer un test hors ligne de mon modèle de langage personnalisé à l’aide d’un modèle acoustique personnalisé ?

**Réponse** : Oui. Sélectionnez simplement le modèle acoustique personnalisé dans le menu déroulant quand vous configurez le test hors ligne.

**Question** : Qu’est-ce que le taux d’erreur de mots ? Comment est-il calculé ?

**Réponse** : Le taux d’erreur de mots est la métrique d’évaluation de la reconnaissance vocale. Il est calculé comme le nombre total d’erreurs, lequel inclut les insertions, les suppressions et les remplacements, divisé par le nombre total de mots présents dans la transcription de référence. Plus de détails [ici](https://en.wikipedia.org/wiki/Word_error_rate).

**Question** : Comment déterminer si les résultats d’un test de précision sont corrects ?

**Réponse** : Les résultats montrent une comparaison entre le modèle de base et celui que vous avez personnalisé.
L’objectif doit être de surpasser le modèle de base pour que la personnalisation soit pertinente.

**Question** : Comment déterminer le taux d’erreurs de mots des modèles de base, pour savoir s’il y a eu une amélioration ? 

**Réponse** : Les résultats de test hors ligne indiquent la précision de base du modèle personnalisé et l’amélioration atteinte par rapport à cette référence.

## <a name="creating-lm"></a>Création d’un modèle de langage

**Question** : Quel volume de données texte dois-je télécharger ?

**Réponse** : Cela dépend de la différence du vocabulaire et des expressions utilisées dans votre application par rapport aux modèles de langage de départ. Pour tous les nouveaux mots, il est utile de fournir autant d’exemples que possible de l’utilisation de ces mots. Pour les expressions courantes qui sont utilisées dans votre application, il est également utile d’inclure des expressions dans les données de langage, car cela indique au système d’écouter aussi ces termes. Il est courant d’avoir au moins une centaine, et généralement plusieurs centaines, d’énoncés dans le jeu de données de langage. De plus, si certains types de requêtes sont censés être plus courants que d’autres, vous pouvez insérer plusieurs copies des requêtes courantes dans le jeu de données.

**Question** : Puis-je simplement charger une liste de mots ?

**Réponse** : Le chargement d’une liste de mots place les mots dans le vocabulaire, mais n’apprend pas au système comment les mots sont généralement utilisés.
En fournissant des énoncés complets ou partiels (phrases ou expressions que les utilisateurs sont susceptibles de dire), le modèle de langage peut apprendre les mots nouveaux et la façon dont ils sont utilisés. Le modèle de langage personnalisé est approprié non seulement pour obtenir de nouveaux mots dans le système, mais également pour ajuster la probabilité de mots connus pour votre application. Fournir des énoncés complets permet au système de mieux apprendre. 

## <a name="next-steps"></a>Étapes suivantes

* [Résolution des problèmes](troubleshooting.md)
* [Notes de publication](releasenotes.md)
