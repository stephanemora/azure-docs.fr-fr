---
title: Créer des mots clés personnalisés – Service Speech
titleSuffix: Azure Cognitive Services
description: Votre appareil est toujours à l’écoute d’un mot (ou d’une phrase) clé. Quand l’utilisateur prononce le mot clé, l’appareil envoie tous les sons suivants vers le cloud, jusqu’à ce que l’utilisateur arrête de parler. Pour différencier votre appareil et renforcer votre marque, vous pouvez personnaliser votre mot clé.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 46e9f8e660c3fd62807d630481e6b3057d2351a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717021"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Créer un mot clé personnalisé avec Speech Studio

Votre appareil est toujours à l’écoute d’un mot (ou d’une phrase) clé. Par exemple, « Bonjour Cortana » est un mot clé de l’assistant Cortana. Quand l’utilisateur prononce le mot clé, l’appareil envoie tous les sons suivants vers le cloud, jusqu’à ce que l’utilisateur arrête de parler. Pour différencier votre appareil et renforcer votre marque, vous pouvez personnaliser votre mot clé.

Dans cet article, vous allez apprendre à créer un mot clé personnalisé pour votre appareil.

## <a name="create-your-keyword"></a>Créer votre mot clé

Avant de pouvoir utiliser un mot clé personnalisé, vous devez créer un mot clé à l’aide de la page [Mot clé personnalisé](https://aka.ms/sdsdk-wakewordportal) sur [Speech Studio](https://aka.ms/sdsdk-speechportal). Une fois que vous avez fourni un mot clé, le logiciel génère un fichier que vous déployez sur votre appareil.

1. Accédez au [Speech Studio](https://aka.ms/sdsdk-speechportal) et **connectez-vous**, ou, si vous n’avez pas d’abonnement vocal, choisissez [**Créer un abonnement**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Sur la page [Mot clé personnalisé](https://aka.ms/sdsdk-wakewordportal), créez un **Nouveau projet**. 

1. Entrez un **Nom** et une **Description** facultative, puis sélectionnez la langue. Vous aurez besoin d’un projet par langue et la prise en charge est actuellement limitée à la variante en-US (anglais des États-Unis).

    ![Décrire votre projet de mot clé](media/custom-keyword/custom-kws-portal-new-project.png)

1. Sélectionnez votre projet dans la liste. 

    ![Sélectionner votre projet de mot clé](media/custom-keyword/custom-kws-portal-project-list.png)

1. Pour démarrer un nouveau modèle de mot clé, cliquez sur **Effectuer l’apprentissage du modèle**.

1. Entrez un **Nom** pour le modèle de mot clé et une **Description** facultative, saisissez le **Mot clé** de votre choix, puis cliquez sur **Suivant**. Nous avons des [instructions](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) pour vous aider à choisir un mot clé efficace.

    ![Entrez votre mot clé](media/custom-keyword/custom-kws-portal-new-model.png)

1. Le portail crée des propositions de prononciations pour votre mot clé. Écoutez chaque prononciation en cliquant sur les boutons de lecture, supprimez les coches en regard des prononciations incorrectes. Une fois que seules les bonnes prononciations sont cochées, cliquez sur **Effectuer l’apprentissage** pour commencer à générer le mot clé. 

    ![Vérifier votre mot clé](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. La génération du modèle peut prendre jusqu’à trente minutes. La liste de mots clés passe de **Traitement en cours** à **Terminé** lorsque le modèle est terminé. Vous pouvez ensuite télécharger le fichier.

    ![Vérifier votre mot clé](media/custom-keyword/custom-kws-portal-download-model.png)

1. Enregistrez le fichier .zip sur votre ordinateur. Vous aurez besoin de ce fichier pour déployer votre mot clé personnalisé sur votre appareil.

## <a name="next-steps"></a>Étapes suivantes

Testez votre mot clé personnalisé avec le [Démarrage rapide du Kit de développement logiciel (SDK) Speech](https://aka.ms/sdsdk-quickstart).
