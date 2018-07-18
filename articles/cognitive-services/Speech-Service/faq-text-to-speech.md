---
title: Forum aux questions sur le service de reconnaissance vocale dans Azure | Microsoft Docs
description: Vous y trouverez les réponses aux questions les plus posées sur le service de reconnaissance vocale.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 64e505889ef9472603471d67a961985c1290663a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045841"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Forum aux questions : Custom Speech Service

Si vous ne trouvez pas les réponses dans ce FAQ, posez votre question à la communauté Custom Speech Service sur [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) et [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Généralités

**Question** : Quelle est la différence entre les modèles de ligne de base et de reconnaissance vocale personnalisés Custom Speech ?

**Réponse** : les modèles de ligne de base ont été formés avec des données possédées par Microsoft et sont déjà déployés dans le cloud. Les modèles personnalisés permettent à l’utilisateur d’adapter un modèle afin qu’il corresponde mieux à un environnement particulier avec bruit ambiant et langage spécifiques. Des ateliers, des voitures ou des rues bruyantes ont besoin d’un modèle acoustique adapté tandis que des sujets tels que la biologie, la physique, la radiologie, les noms de produits et les acronymes personnalisés nécessitent un modèle de langage.

**Question** : Où commencer si je souhaite utiliser un modèle de ligne de base ?

**Réponse** : Il vous faut d’abord une [clé d’abonnement](get-started.md). Si vous souhaitez passer des appels REST vers des modèles de ligne de base prédéployés, consultez ces [informations](rest-apis.md). Si vous voulez utiliser WebSockets, télécharger le [Kit de développement logiciel (SDK)](speech-sdk.md)

**Question** : Faut-il toujours que je crée un modèle vocal personnalisé ?

**Réponse** : Non. Si votre application utilise un langage quotidien générique, sans vocabulaire spécifique ou rare, vous n’êtes pas obligé de personnaliser un modèle. En outre, si votre application a pour but d’être utilisée comme environnement avec peu de bruit de fond, voire aucun, vous n’êtes pas non plus obligé de personnaliser un modèle. Le portail permet aux utilisateurs de déployer des modèles de ligne de base et personnalisés, et d’exécuter des tests de précision. Ils peuvent utiliser cette fonctionnalité pour mesurer la précision d’un modèle de ligne de base et celle d’un modèle personnalisé.

**Question** : Comment savoir si le traitement de mon jeu de données ou de mon modèle est terminé ?

**Réponse** : Actuellement, le statut du modèle ou du jeu de données situé dans le tableau est le seul qui compte.
Lorsque le traitement est terminé, l’état indiquera « Prêt ».

**Question** : Puis-je créer plus d’un modèle à la fois ?

**Réponse** :Il n’y aucune limite au nombre de modèles dans votre collection, mais vous ne pouvez en créer qu’un à la fois sur chaque page.
Par exemple, vous ne pouvez pas démarrer la création d’un modèle de langage si un modèle de langage est déjà en cours de création.
Toutefois, vous pouvez traiter un modèle acoustique et un modèle de langage en même temps. 

**Question** : J’ai réalisé que j’ai fait une erreur. Comment annuler l’importation de mes données ou la création d’un modèle en cours ? 

**Réponse** : Actuellement, vous ne pouvez pas restaurer un processus d’adaptation de langage ou acoustique.
Les données importées peuvent être supprimées après leur importation.

**Question** : Quelle est la différence entre les modèles Recherche & Dictée et les modèles Conversational ?

**Réponse** : Il existe deux choix modèles de langage et acoustique de base dans Custom Speech Service :
les requêtes de recherche, ou la dictée. Le modèle Microsoft Conversational AM est approprié pour la reconnaissance vocale dans un style conversationnel.
Ce type de voix s’adresse généralement à une autre personne, comme dans les centres d’appel ou les réunions.

**Question** : Puis-je mettre à jour mon modèle existant (pile de modèles) ?

**Réponse** : Les modèles existants ne peuvent pas être mis à jour. Pour contourner cela, combinez l’ancien jeu de données avec le nouveau, et réadaptez.

Les anciens et nouveaux jeux de données doivent être combinés en un seul fichier .zip (s’il s’agit de données acoustiques) ou en un fichier .txt (s’il s’agit de données de langage). Une fois l’adaptation terminée, le nouveau modèle mis à jour doit être retiré afin d’obtenir un nouveau point de terminaison

**Question** : Que faire si j’ai besoin d’une concurrence plus élevée que la valeur par défaut ou que ce qui est offert dans le portail ? 

**Réponse** : Vous pouvez augmenter la taille de votre modèle par incrément de 20 requêtes concurrentes. 

Contactez-nous si vous avez besoin d’augmenter cette taille.

**Question** : Puis-je télécharger mon modèle et l’exécuter en local ?

**Réponse** : Les modèles ne peuvent pas être téléchargés et exécutés en local.

**Question** : Mes requêtes sont-elles consignées ?

**Réponse** : Lors de la création d’un déploiement, vous pouvez décider de désactiver le suivi, ce qui signifie qu’aucun son ni transcription ne sera consigné. Autrement, les requêtes sont généralement consignées dans Azure dans un stockage sécurisé. Si vous avez d’autres questions sur la confidentialité qui vous empêchent d’utiliser Custom Speech Service, contactez l’un des canaux de support.

## <a name="importing-data"></a>Importation de données

**Question** : Quelle est la limite de taille du jeu de données ? Pourquoi ? 

**Réponse** : La limite actuelle pour un jeu de données est de 2 Go, en raison de la restriction de taille d’un fichier pour le chargement HTTP. 

**Question** : Puis-je compresser mes fichiers texte pour charger un fichier texte plus grand ? 

**Réponse** : Non, seuls des fichiers texte non compressés sont actuellement autorisés.

**Question** : Le rapport de données indique que des énoncés ont échoué. Quel est le problème ?

**Réponse** : L’échec du chargement à 100 % des énoncés dans un fichier n’est pas un problème.
Si la majeure partie des énoncés dans un jeu de données acoustiques ou de langage (par exemple, plus de 95 %) est importée avec succès, le jeu de données peut être utilisable. Toutefois, il est recommandé d’essayer de comprendre la raison de l’échec des énoncés et d’y remédier. La plupart des problèmes communs, comme les erreurs de formatage, sont faciles à régler. 

## <a name="creating-am"></a>Création de l'AM

**Question** : De quel volume de données acoustiques aie-je besoin ?

**Réponse** : Nous vous recommandons de commencer par 30 minutes voire une heure de données acoustiques.

**Question** : Quelles données dois-je collecter ?

**Réponse** : Collectez les données qui sont le plus proche possible du scénario d’application et du cas d’usage.
La collection de données doit correspondre l’application et les utilisateurs cible en termes d’appareil(s), environnements, et types d’orateurs. En général, vous devez collecter des données d’un éventail d’orateurs aussi large que possible. 

**Question** : Comment les collecter ? 

**Réponse** : Vous pouvez créer une application de collection de données autonome, ou en utiliser depuis le logiciel d’enregistrement audio.
Vous pouvez également créer une version de votre application qui journalise les données audio et les utilise. 

**Question** : Dois-je transcrire moi-même les données d’adaptation ? 

**Réponse** : Les données doivent être transcrites. Vous pouvez les transcrire vous-même ou utiliser un service de transcription professionnel. Certains de ces services utilisent des transcripteurs professionnels, tandis que d’autres utilisent le crowdsourcing.

**Question** : Combien de temps faut-il pour créer un modèle acoustique personnalisé ?

**Réponse** : Le temps de traitement de la création d’un modèle acoustique personnalisé est à peu près identique à la longueur du jeu de données acoustiques.
Par conséquent, le traitement d’un modèle acoustique personnalisé créé à partir d’un jeu de données de cinq heures prend environ cinq heures. 

## <a name="offline-testing"></a>Tests hors ligne

**Question** : Puis-je effectuer des tests hors ligne de mon modèle acoustique personnalisé à l’aide d’un modèle de langage personnalisé ?

**Réponse** : Oui. Sélectionnez simplement le modèle de langage personnalisé dans la liste déroulante quand vous configurez le test hors ligne.

**Question** : Puis-je effectuer un test hors ligne de mon modèle de langage personnalisé à l’aide d’un modèle acoustique personnalisé ?

**Réponse** : Oui. Sélectionnez simplement le modèle acoustique personnalisé dans la liste déroulante quand vous configurez le test hors ligne.

**Question** : Quel est le taux d’erreur de mots et comment est-il calculé ?

**Réponse** : Le taux d’erreur de mots est la métrique d’évaluation de la reconnaissance vocale. Il est calculé comme le nombre total d’erreurs, lequel inclut les insertions, les suppressions et les remplacements, divisé par le nombre total de mots présents dans la transcription de référence.

**Question** : Comment déterminer si les résultats d’un test de précision sont corrects ?

**Réponse** : Les résultats montrent une comparaison entre le modèle de base et celui que vous avez personnalisé.
Vous devez veiller à améliorer le modèle de base pour rendre la personnalisation pertinente.

**Question** : Comment déterminer le taux d’erreurs de mots des modèles de base, afin de savoir s’il y a eu une amélioration ? 

**Réponse** : Les résultats des tests hors ligne indiquent la précision de base du modèle personnalisé et l’amélioration par rapport à cette précision de base

## <a name="creating-lm"></a>Création d’un modèle de langage

**Question** : Quel volume de données texte dois-je télécharger ?

**Réponse** : Cela dépend de la différence du vocabulaire et des expressions utilisées dans votre application par rapport aux modèles de langage de départ. Pour tous les nouveaux mots, il est utile de fournir autant d’exemples que possible de l’utilisation de ces mots. Pour les expressions courantes qui sont utilisées dans votre application, il est également utile d’inclure des expressions dans les données de langage, car cela indique au système d’écouter aussi ces termes. Il est courant d’avoir au moins une centaine, et généralement plusieurs centaines, d’énoncés dans le jeu de données de langage. De plus, si certains types de requêtes sont censés être plus courants que d’autres, vous pouvez insérer plusieurs copies des requêtes courantes dans le jeu de données.

**Question** : Puis-je simplement charger une liste de mots ?

**Réponse** : Le chargement d’une liste de mots place les mots dans le vocabulaire, mais n’apprend pas au système comment les mots sont généralement utilisés.
En fournissant des énoncés complets ou partiels (phrases ou expressions que les utilisateurs sont susceptibles de dire), le modèle de langage peut apprendre les mots nouveaux et la façon dont ils sont utilisés. Le modèle de langage personnalisé est approprié non seulement pour obtenir de nouveaux mots dans le système, mais également pour ajuster la probabilité de mots connus pour votre application. Fournir des énoncés complets permet au système de mieux apprendre. 

## <a name="next-steps"></a>Étapes suivantes

* [Résolution des problèmes](troubleshooting.md)
* [Notes de publication](releasenotes.md)
