---
title: Déployer un modèle pour la reconnaissance vocale personnalisée - Services de reconnaissance vocale
titlesuffix: Azure Cognitive Services
description: Dans ce document, vous allez apprendre à créer et déployer un point de terminaison à l’aide du portail de reconnaissance vocale personnalisé.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: fc51c1d9d47340da85d42f7c398c8ee21c601beb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025869"
---
# <a name="deploy-a-custom-model"></a>Déployer un modèle personnalisé

Après avoir téléchargé et inspecter les données, évaluer la précision et formé un modèle personnalisé, vous pouvez déployer un point de terminaison personnalisé à utiliser avec vos applications, les outils et les produits. Dans ce document, vous allez apprendre à créer et déployer un point de terminaison à l’aide du portail de reconnaissance vocale personnalisé.

## <a name="create-a-custom-endpoint"></a>Créer un point de terminaison personnalisé

Pour créer un nouveau point de terminaison personnalisé, sélectionnez **déploiement** dans le menu de reconnaissance vocale personnalisé en haut de la page. S’il s’agit de votre première exécution, vous remarquerez qu’il n’y a aucun point de terminaison répertoriés dans le tableau. Une fois que vous avez créé un point de terminaison, vous allez utiliser cette page pour effectuer le suivi de chaque point de terminaison déployé.

Ensuite, sélectionnez **ajouter le point de terminaison** et entrez un **nom** et **Description** pour votre point de terminaison personnalisé. Puis sélectionnez le modèle personnalisé que vous souhaitez associer à ce point de terminaison. À partir de cette page, vous pouvez également activer la journalisation. Journalisation vous permet de surveiller le trafic de point de terminaison. Si désactivé, le trafic ne sera pas enregistrée.

![Comment déployer un modèle](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> N’oubliez pas d’accepter les conditions d’utilisation et les détails de la tarification.

Ensuite, sélectionnez **Créer**. Cette action vous renvoie à la **déploiement** page. Le tableau inclut désormais une entrée qui correspond à votre point de terminaison personnalisé. État du point de terminaison affiche son état actuel. Il peut prendre jusqu'à 30 minutes pour instancier un nouveau point de terminaison à l’aide de vos modèles personnalisés. Lorsque l’état du déploiement devient **Terminer**, le point de terminaison est prêt à utiliser.

Une fois votre point de terminaison est déployé, le nom de point de terminaison s’affiche sous forme de lien. Cliquez sur le lien pour afficher des informations spécifiques à votre point de terminaison, tels que la clé de point de terminaison, les URL de point de terminaison et exemple de code.

## <a name="view-logging-data"></a>Afficher les données de journalisation

Données de journalisation soient disponibles en téléchargement sous **point de terminaison > détails**.

## <a name="next-steps"></a>Étapes suivantes

* Utilisez votre point de terminaison personnalisé avec le [Speech SDK](speech-sdk.md)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Préparer et tester vos données](how-to-custom-speech-test-data.md)
* [Inspectez vos données](how-to-custom-speech-inspect-data.md)
* [Évaluer vos données](how-to-custom-speech-evaluate-data.md)
* [Formation d’un modèle](how-to-custom-speech-train-model.md)
* [Déployer votre modèle](how-to-custom-speech-deploy-model.md)
