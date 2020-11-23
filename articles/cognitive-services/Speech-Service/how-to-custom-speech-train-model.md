---
title: Entraîner et déployer un modèle Custom Speech - Service Speech
titleSuffix: Azure Cognitive Services
description: Dans cet article, vous allez apprendre à entraîner et déployer des modèles Custom Speech. L’entraînement d’un modèle de reconnaissance vocale peut améliorer la précision de la reconnaissance du modèle de référence de Microsoft ou un modèle personnalisé. L’entraînement d’un modèle s’appuie sur des transcriptions étiquetées à la main et sur le texte associé.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 34c0703ee7c335ca904a21bcce6ed44abc6dc13f
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555785"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Entraîner et déployer un modèle Custom Speech

Dans cet article, vous allez apprendre à entraîner et déployer des modèles Custom Speech. La formation d’un modèle de reconnaissance vocale peut améliorer la précision de la reconnaissance du modèle de référence de Microsoft. L’entraînement d’un modèle s’appuie sur des transcriptions étiquetées à la main et sur le texte associé. Ces jeux de données, ainsi que les données audio précédemment chargées, permettent d’affiner et d’entraîner le modèle de reconnaissance vocale.

## <a name="use-training-to-resolve-accuracy-issues"></a>Utiliser l’entraînement pour résoudre les problèmes de précision

Si vous rencontrez des problèmes de reconnaissance avec un modèle de base, l’utilisation de transcriptions étiquetées à la main et de données associées pour l’entraînement d’un modèle personnalisé peuvent contribuer à améliorer la justesse. Servez-vous de ce tableau pour savoir quel jeu de données utiliser pour résoudre vos problèmes :

| Cas d’utilisation | Type de données |
| -------- | --------- |
| Améliorez la précision de la reconnaissance du vocabulaire ou de la grammaire spécifiques d’un secteur, par exemple, la terminologie médicale ou le jargon informatique. | Texte associé (phrases/énoncés) |
| Définissez la forme phonétique et affichée d’un mot ou d’un terme dont la prononciation n’est pas standard, par exemple, les noms de produits ou les acronymes. | Texte associé (prononciation) |
| Améliorez la précision de la reconnaissance de styles oraux, d’accents ou de bruits de fond spécifiques. | Transcriptions audio + étiquetées à la main |

## <a name="train-and-evaluate-a-model"></a>Entraîner et évaluer un modèle

La première étape pour entraîner un modèle consiste à charger des données d’entraînement. Consultez [Préparer et tester les données](how-to-custom-speech-test-data.md) pour préparer des transcriptions étiquetées à la main et du texte associé (énoncés et prononciations) en suivant des instructions pas à pas. Après avoir chargé des données d’entraînement, suivez ces instructions pour commencer à entraîner votre modèle :

1. Connectez-vous au [portail Custom Speech](https://speech.microsoft.com/customspeech).
2. Accédez à **Synthèse vocale > Custom Speech > [nom du projet] > Entraînement en cours**.
3. Cliquez sur **Train model**.
4. Ensuite, nommez et décrivez votre entraînement dans les champs **Name** et **Description**.
5. À partir du menu déroulant **Scenario and Baseline model** (Scénario et modèle de référence), sélectionnez le scénario qui correspond le mieux à votre domaine. Si vous ne savez pas quel scénario choisir, sélectionnez **General**. Le modèle de référence est le point de départ de votre entraînement. Le modèle le plus récent est généralement le meilleur choix.
6. Dans la page **Select training data** (Sélectionner les données d’entraînement), choisissez le ou les jeux de données de transcriptions audio + étiquetées à la main à utiliser pour l’entraînement.
7. Une fois l’entraînement terminé, vous pouvez choisir d’effectuer le test de précision sur le modèle nouvellement entraîné. Cette étape est facultative.
8. Sélectionnez **Create** pour générer votre modèle personnalisé.

La table Training (Entraînement) comporte une nouvelle entrée correspondant à ce nouveau modèle. Elle indique également l’état : Processing, Succeeded, Failed (En cours de traitement, Réussite, Échec).

Consultez le [Guide pratique](how-to-custom-speech-evaluate-data.md) sur l’évaluation et l’amélioration de la justesse du modèle Custom Speech. Si vous choisissez de tester l’exactitude, il est important de sélectionner un jeu de données acoustique différent de celui que vous avez utilisé avec votre modèle pour vous faire une idée réaliste des performances du modèle.

## <a name="deploy-a-custom-model"></a>Déployer un modèle personnalisé

Après avoir chargé et inspecté les données, évalué la précision et formé un modèle personnalisé, vous pouvez déployer un point de terminaison personnalisé à utiliser avec vos applications, outils et produits. 

Pour créer un point de terminaison personnalisé, connectez-vous au [portail Custom Speech](https://speech.microsoft.com/customspeech), puis sélectionnez **Déploiement** dans le menu Custom Speech en haut de la page. S’il s’agit de votre première exécution, vous remarquerez que la table ne contient aucun point de terminaison. Une fois que vous avez créé un point de terminaison, vous utilisez cette page pour effectuer le suivi de chaque point de terminaison déployé.

Ensuite, sélectionnez **Ajouter un point de terminaison** et entrez un **nom** et une **description** pour votre point de terminaison personnalisé. Puis sélectionnez le modèle personnalisé que vous souhaitez associer à ce point de terminaison. À partir de cette page, vous pouvez également activer la journalisation. La journalisation vous permet de surveiller le trafic du point de terminaison. Si elle est désactivée, le trafic ne sera pas enregistré.

![Déploiement d’un modèle](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> N’oubliez pas d’accepter les conditions d’utilisation et la tarification.

Ensuite, sélectionnez **Créer**. Cette action vous renvoie à la page **Déploiement**. La table inclut désormais une entrée qui correspond à votre point de terminaison personnalisé. L’état du point de terminaison indique son état actuel. L’instanciation d’un nouveau point de terminaison avec vos modèles personnalisés peut prendre jusqu’à 30 minutes. Lorsque l’état du déploiement est **Complete**, le point de terminaison est prêt à être utilisé.

Une fois le point de terminaison déployé, son nom s’affiche sous forme de lien. Cliquez sur ce lien pour afficher les informations qui concernent spécifiquement votre point de terminaison, comme sa clé, son URL et l’exemple de code.

## <a name="view-logging-data"></a>Afficher les données de journalisation

Les données de journalisation sont disponibles au téléchargement sous **Point de terminaison > Détails**.
> [!NOTE]
>Les données de journalisation sont disponibles pendant 30 jours sur le stockage détenu par Microsoft, avant d’être supprimées. Si un compte de stockage appartenant à un client est lié à l’abonnement Cognitive Services, les données de journalisation ne sont pas automatiquement supprimées.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez [comment utiliser votre modèle personnalisé](how-to-specify-source-language.md).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Préparer et tester les données](how-to-custom-speech-test-data.md)
- [Inspecter les données](how-to-custom-speech-inspect-data.md)
- [Évaluer les données](how-to-custom-speech-evaluate-data.md)
