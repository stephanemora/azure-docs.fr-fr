---
title: Déployer un modèle pour Custom Speech - Service Speech
titleSuffix: Azure Cognitive Services
description: Dans ce document, vous allez apprendre à créer et à déployer un point de terminaison à l’aide du portail Custom Speech.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: e41653e4644b09cc357510223e5ee86c3806ae2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806111"
---
# <a name="deploy-a-custom-model"></a>Déployer un modèle personnalisé

Après avoir chargé et inspecté les données, évalué la précision et formé un modèle personnalisé, vous pouvez déployer un point de terminaison personnalisé à utiliser avec vos applications, outils et produits. Dans ce document, vous allez apprendre à créer et à déployer un point de terminaison à l’aide du [portail Custom Speech](https://speech.microsoft.com/customspeech).

## <a name="create-a-custom-endpoint"></a>Créer un point de terminaison personnalisé

Pour créer un point de terminaison personnalisé, connectez-vous au [portail Custom Speech](https://speech.microsoft.com/customspeech), puis sélectionnez **Déploiement** dans le menu Custom Speech en haut de la page. S’il s’agit de votre première exécution, vous remarquerez que la table ne contient aucun point de terminaison. Une fois que vous avez créé un point de terminaison, vous utiliserez cette page pour effectuer le suivi de chaque point de terminaison déployé.

Ensuite, sélectionnez **Ajouter un point de terminaison** et entrez un **nom** et une **description** pour votre point de terminaison personnalisé. Puis sélectionnez le modèle personnalisé que vous souhaitez associer à ce point de terminaison. À partir de cette page, vous pouvez également activer la journalisation. La journalisation vous permet de surveiller le trafic du point de terminaison. Si elle est désactivée, le trafic ne sera pas enregistré.

![Déploiement d’un modèle](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> N’oubliez pas d’accepter les conditions d’utilisation et la tarification.

Ensuite, sélectionnez **Créer**. Cette action vous renvoie à la page **Déploiement**. La table inclut désormais une entrée qui correspond à votre point de terminaison personnalisé. L’état du point de terminaison indique son état actuel. L’instanciation d’un nouveau point de terminaison avec vos modèles personnalisés peut prendre jusqu’à 30 minutes. Lorsque l’état du déploiement est **Complete**, le point de terminaison est prêt à être utilisé.

Une fois le point de terminaison déployé, son nom s’affiche sous forme de lien. Cliquez sur ce lien pour afficher les informations qui concernent spécifiquement votre point de terminaison, comme sa clé, son URL et l’exemple de code.

## <a name="view-logging-data"></a>Afficher les données de journalisation

Les données de journalisation sont disponibles au téléchargement sous **Point de terminaison > Détails**.

## <a name="next-steps"></a>Étapes suivantes

* Utiliser votre point de terminaison personnalisé avec le [SDK Speech](speech-sdk.md)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Préparer et tester les données](how-to-custom-speech-test-data.md)
* [Inspecter les données](how-to-custom-speech-inspect-data.md)
* [Évaluer les données](how-to-custom-speech-evaluate-data.md)
* [Entraîner un modèle](how-to-custom-speech-train-model.md)
* [Déployer un modèle](how-to-custom-speech-deploy-model.md)
