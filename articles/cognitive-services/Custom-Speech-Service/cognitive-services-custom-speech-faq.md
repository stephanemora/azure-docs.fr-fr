---
title: Forum aux questions - Custom Speech Service
titlesuffix: Azure Cognitive Services
description: Vous trouverez ici des réponses aux questions les plus posées sur Custom Speech Service.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: faq
ms.date: 11/21/2016
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 2de2efe3287cbb0b915434c2c9ad982fe162d741
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223716"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Foire aux questions sur Custom Speech Service

Si vous ne trouvez pas les réponses à vos questions dans cette FAQ, essayez d’interroger la communauté Custom Speech Service sur [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) et [UserVoice](https://cognitive.uservoice.com/).

## <a name="general"></a>Généralités

**Question** : Comment savoir si le traitement de mon jeu de données ou de mon modèle est terminé ?

**Réponse** : Actuellement, la seule façon de le savoir est de consulter l’état du modèle ou du jeu de données situé dans la table.
Quand le traitement est terminé, l’état indique « Prêt ».
Nous sommes actuellement à la recherche de méthodes plus efficaces pour communiquer l’état du traitement, comme les notifications par e-mail.

**Question** : Puis-je créer plusieurs modèles à la fois ?

**Réponse** : Le nombre de modèles dans votre collection n’est pas limité, mais vous ne pouvez en créer qu’un seul à la fois sur chaque page.
Par exemple, vous ne pouvez pas démarrer le processus de création d’un modèle de langage s’il y en a déjà un en cours de traitement.
Il est toutefois possible qu’un modèle acoustique et un modèle de langage soient traités simultanément. 

**Question** : J’ai réalisé que j’ai fait une erreur. Comment annuler l’importation de mes données ou la création d’un modèle en cours ? 

**Réponse** : Actuellement, vous ne pouvez pas restaurer un processus d’adaptation de langage ou acoustique.
Les données importées peuvent être supprimées après leur importation.

**Question** : Quelle est la différence entre les modèles Recherche & Dictée et les modèles de conversation ?

**Réponse** : Custom Speech Service permet de choisir entre deux modèles de langage et acoustiques de base :
requêtes de recherche ou dictée. Le modèle acoustique de conversation (Conversational) Microsoft est approprié pour la reconnaissance vocale de mots prononcés dans un style conversationnel.
Ce type de contenu vocal s’adresse généralement à une autre personne, comme dans les centres d’appel ou les réunions.

**Question** : Puis-je mettre à jour mon modèle existant (empilement de modèles) ?

**Réponse**: Nous ne vous offrons pas la possibilité de mettre à jour un modèle existant avec de nouvelles données.
Si vous disposez d’un nouveau jeu de données et si vous souhaitez personnaliser un modèle existant, vous devez l’ajuster avec les nouvelles données et l’ancien jeu de données que vous avez utilisé.
Les jeux de données anciens et nouveaux doivent être rassemblés dans un seul fichier .zip (en cas de données acoustiques) ou .txt (en cas de données de langage). Une fois l’adaptation effectuée, le nouveau modèle mis à jour doit être redéployé pour obtenir un nouveau point de terminaison.

**Question** : Que faire si j’ai besoin d’une simultanéité plus élevée que la valeur par défaut ? 

**Réponse** : Vous pouvez augmenter la taille de votre modèle par incréments de 5 requêtes simultanées que nous appelons unités d’échelle. Chaque unité d’échelle garantit que votre modèle peut traiter simultanément 5 flux audio. Vous pouvez acheter100 unités d’échelle maximum (ou 500 requêtes simultanées).

Contactez-nous si vous avez besoin d’augmenter cette valeur.

**Question** : Puis-je télécharger mon modèle et l’exécuter localement ?

**Réponse** : Nous ne proposons pas de télécharger et d’exécuter les modèles localement.

**Question** : Mes requêtes sont-elles journalisées ?

**Réponse** : Lors de la création d’un déploiement, vous pouvez décider de désactiver le suivi, ce qui signifie qu’aucun contenu audio ni aucune transcription ne sera journalisé. Dans le cas contraire, les requêtes sont généralement journalisées dans un stockage sécurisé dans Azure. Si vous avez d’autres questions sur la confidentialité qui vous empêchent d’utiliser Custom Speech Service, contactez-nous.

## <a name="importing-data"></a>Importation de données

**Question** : Quelle est la limite de taille du jeu de données ? Pourquoi ? 

**Réponse** : La limite actuelle pour un jeu de données est de 2 Go, en raison de la restriction de taille d’un fichier pour le chargement HTTP. 

**Question** : Puis-je compresser mes fichiers texte pour charger un fichier texte plus volumineux ? 

**Réponse** : Non, seuls des fichiers texte non compressés sont actuellement autorisés.

**Question** : Le rapport de données indique que des énoncés ont échoué. Cela est-il un problème ?

**Réponse** : Si seuls quelques énoncés n’ont pas pu être importés, cela n’est pas problématique.
Si la majeure partie des énoncés d’un jeu de données acoustiques ou de langage (par exemple, plus de 95 %) est importée avec succès, le jeu de données peut être utilisable. Toutefois, il est recommandé d’essayer de comprendre pourquoi les énoncés n’ont pas pu être importés et ainsi corriger le problème.
La plupart des problèmes courants, comme les erreurs de mise en forme, sont faciles à corriger. 

## <a name="creating-am"></a>Création d’un modèle acoustique

**Question** : De quel volume de données acoustiques ai-je besoin ?

**Réponse** : Nous vous recommandons de commencer par un volume de 30 minutes à une heure de données acoustiques.

**Question** : Quel type de données dois-je collecter ?

**Réponse** : Vous devez collecter les données qui sont le plus proches possible du scénario d’application et du cas d’usage.
Cela signifie que la collection de données doit correspondre à l’application et aux utilisateurs cibles en termes d’appareils, d’environnements et de types d’orateurs. En général, vous devez collecter des données d’un éventail d’orateurs aussi large que possible. 

**Question** : Comment les collecter ? 

**Réponse** : Vous pouvez créer une application de collecte de données autonome, ou en utiliser une des logiciels d’enregistrement audio du commerce.
Vous pouvez également créer une version de votre application qui journalise les données audio et les utilise. 

**Question** : Dois-je transcrire moi-même les données d’adaptation ? 

**Réponse** : Les données doivent être transcrites. Vous pouvez les transcrire vous-même ou utiliser un service de transcription professionnel. Certains de ces services utilisent des transcripteurs professionnels, tandis que d’autres utilisent le crowdsourcing. Nous vous recommandons également d’utiliser un service de transcription à la demande.

**Question** : Combien de temps faut-il pour créer un modèle acoustique personnalisé ?

**Réponse** : Le temps de traitement pour la création d’un modèle acoustique personnalisé est à peu près identique à la longueur du jeu de données acoustiques.
Par conséquent, le traitement d’un modèle acoustique personnalisé créé à partir d’un jeu de données de cinq heures prend environ cinq heures. 

## <a name="offline-testing"></a>Tests hors ligne

**Question** : Puis-je effectuer des tests hors ligne de mon modèle acoustique personnalisé à l’aide d’un modèle de langage personnalisé ?

**Réponse** : Oui. Sélectionnez simplement le modèle de langage personnalisé dans la liste déroulante quand vous configurez le test hors ligne.

**Question** : Puis-je effectuer un test hors ligne de mon modèle de langage personnalisé à l’aide d’un modèle acoustique personnalisé ?

**Réponse** : Oui. Sélectionnez simplement le modèle acoustique personnalisé dans le menu déroulant quand vous configurez le test hors ligne.

**Question** : Quel est le taux d’erreur de mots et comment est-il calculé ?

**Réponse** : Le taux d’erreur de mots est la métrique d’évaluation de la reconnaissance vocale. Il est calculé comme le nombre total d’erreurs, lequel inclut les insertions, les suppressions et les remplacements, divisé par le nombre total de mots présents dans la transcription de référence.

**Question** : Maintenant que je connais les résultats de test de mon modèle personnalisé, s’agit-il d’un bon ou d’un mauvais chiffre ?

**Réponse** : Les résultats montrent une comparaison entre le modèle de base et celui que vous avez personnalisé.
Vous devez veiller à améliorer le modèle de base pour rendre la personnalisation pertinente.

**Question** : Comment déterminer le taux d’erreurs de mots des modèles de base, afin de savoir s’il y a eu une amélioration ? 

**Réponse** : Les résultats des tests hors ligne indiquent la précision de base du modèle personnalisé et l’amélioration par rapport à cette précision de base

## <a name="creating-lm"></a>Création d’un modèle de langage

**Question** : Quel volume de données texte dois-je charger ?

**Réponse** : Il est difficile de donner une réponse précise à cette question, car cela dépend de la différence du vocabulaire et des expressions utilisées dans votre application par rapport aux modèles de langage de départ. Pour tous les nouveaux mots, il est utile de fournir autant d’exemples que possible de l’utilisation de ces mots. Pour les expressions courantes qui sont utilisées dans votre application, il est également utile d’inclure des expressions dans les données de langage, car cela indique au système d’écouter aussi ces termes.
Il est courant d’avoir au moins une centaine, et généralement plusieurs centaines, d’énoncés dans le jeu de données de langage.
De plus, si certains types * de requêtes sont censés être plus courants que d’autres, vous pouvez insérer plusieurs copies des requêtes courantes dans le jeu de données.

**Question** : Puis-je simplement charger une liste de mots ?

**Réponse** : Le chargement d’une liste de mots place les mots dans le vocabulaire, mais n’apprend pas au système comment les mots sont généralement utilisés.
En fournissant des énoncés complets ou partiels (phrases ou expressions que les utilisateurs sont susceptibles de dire), le modèle de langage peut apprendre les mots nouveaux et la façon dont ils sont utilisés. Le modèle de langage personnalisé est approprié non seulement pour obtenir de nouveaux mots dans le système, mais également pour ajuster la probabilité de mots connus pour votre application. Fournir des énoncés complets permet au système de mieux apprendre. 

-----

 * [Vue d’ensemble](cognitive-services-custom-speech-home.md)
 * [Get Started with Custom Speech Service](cognitive-services-custom-speech-get-started.md) (Prise en main de Custom Speech Service)
 * [Glossaire](cognitive-services-custom-speech-glossary.md)
