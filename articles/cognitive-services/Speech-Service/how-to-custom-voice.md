---
title: Améliorer la synthèse avec Custom Voice – Service Speech
titleSuffix: Azure Cognitive Services
description: Custom Voice est un ensemble d’outils en ligne qui vous permet de créer une voix unique reconnaissable entre toutes pour votre marque. Pour commencer, vous n’avez besoin que de quelques fichiers audio et des transcriptions associées. Suivez les liens ci-dessous pour commencer à créer une expérience de reconnaissance vocale personnalisée.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: eff51c8568ce82c9d8d21bff7a2ba079c291679c
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007293"
---
# <a name="get-started-with-custom-voice"></a>Bien démarrer avec Custom Voice

[Custom Voice](https://aka.ms/customvoice) est un ensemble d’outils en ligne qui vous permettent de créer une voix unique reconnaissable entre toutes pour votre marque. Pour commencer, vous n’avez besoin que de quelques fichiers audio et des transcriptions associées. Suivez les liens ci-dessous pour commencer à créer une expérience de synthèse vocale personnalisée.

## <a name="whats-in-custom-voice"></a>Qu’y a-t-il dans Custom Voice ?

Avant de commencer avec Custom Voice, vous aurez besoin d’un compte Azure et d’un abonnement au service Speech. Une fois que vous aurez créé un compte, vous pourrez préparer vos données, entraîner et tester vos modèles, évaluer la qualité de la voix et en dernier lieu déployer votre modèle vocal personnalisé.

Le diagramme ci-dessous présente les étapes à suivre pour créer un modèle vocal personnalisé à partir du [portail Custom Voice](https://aka.ms/customvoice). Suivez les liens pour en savoir plus.

![Schéma de l’architecture Custom Voice](media/custom-voice/custom-voice-diagram.png)

1. [S’abonner et créer un projet](#set-up-your-azure-account) – Créez un compte Azure ainsi qu’un abonnement au service Speech. Cet abonnement unifié vous donne accès à la reconnaissance vocale, à la synthèse vocale, à la traduction vocale et au portail Custom Voice. Ensuite, servez-vous de votre abonnement Speech Services pour créer votre premier projet Custom Voice.

2. [Charger des données](how-to-custom-voice-create-voice.md#upload-your-datasets) – Chargez des données (audio et texte) via le portail Custom Voice ou l’API Custom Voice. Sur le portail, vous pouvez examiner et évaluer les scores de prononciation et les rapports signal/bruit. Pour plus d’informations, consultez [Guide pratique pour préparer des données pour Custom Voice](how-to-custom-voice-prepare-data.md).

3. [Entraîner votre modèle](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – Servez-vous de vos données pour créer un modèle vocal personnalisé pour la synthèse vocale. Vous pouvez entraîner un modèle dans différentes langues. Après l’entraînement, testez votre modèle. Si vous êtes satisfait du résultat, vous pouvez le déployer.

4. [Déployer votre modèle](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) – Créez un point de terminaison personnalisé pour votre modèle vocal et utilisez-le pour la synthèse vocale dans vos produits, outils et applications.

## <a name="custom-neural-voices"></a>Voix neurales personnalisées

Custom Voice prend actuellement en charge les niveaux standard et neuronal. La voix neuronale personnalisée permet aux utilisateurs de créer des modèles vocaux de grande qualité avec moins de données, et fournit des mesures pour vous aider à déployer une IA responsable. Nous vous recommandons d’utiliser la voix neuronale personnalisée pour créer des voix plus réalistes et développer ainsi des interfaces conversationnelles plus naturelles. Avec cette fonctionnalité, vos clients et utilisateurs finaux bénéficient en plus de la dernière technologie de synthèse vocale, dans une approche responsable. [Découvrez-en plus sur la voix neuronale personnalisée](https://aka.ms/CNV-Transparency-Note). 

> [!NOTE]
> Dans le cadre de son engagement en faveur de la conception d’une IA responsable, Microsoft a limité l’utilisation de la voix neuronale personnalisée. Vous pouvez accéder à la technologie uniquement après que vos applications ont été examinées et que vous vous êtes engagé à l’utiliser en conformité avec nos principes sur l’IA responsable. Renseignez-vous sur notre [stratégie de limitation de l’accès](https://aka.ms/gating-overview) et [demandez l’accès ici](https://aka.ms/customneural). Les [langues](language-support.md#customization) et les [régions](regions.md#custom-voices) prises en charge diffèrent pour la version standard et la version neuronale de Custom Voice. Renseignez-vous avant de commencer.  

## <a name="set-up-your-azure-account"></a>Configurer votre compte Azure

Vous avez besoin d’un abonnement au service Speech pour pouvoir créer un modèle personnalisé à partir du portail Custom Speech. Suivez ces instructions pour créer un abonnement Speech standard dans Azure. Si vous n’avez pas de compte Azure, vous pouvez vous inscrire pour en créer un.  

Une fois que vous avez créé un compte Azure et un abonnement Speech, vous devez vous connecter au portail Custom Voice et connecter votre abonnement.

1. Obtenez votre clé d’abonnement Speech sur le portail Azure.
2. Connectez-vous au [portail Custom Voice](https://aka.ms/custom-voice).
3. Sélectionnez votre abonnement et créez un projet Speech.
4. Si vous voulez basculer vers un autre abonnement Speech, utilisez l’icône représentant une roue dentée dans le volet de navigation du haut.

> [!NOTE]
> Pour pouvoir utiliser le service Speech, vous devez disposer d’une clé F0 ou S0 créée dans Azure. La voix neuronale personnalisée prend uniquement en charge le niveau S0. 

## <a name="how-to-create-a-project"></a>Comment créer un projet

Le contenu que représentent les données, les modèles, les tests et les points de terminaison sont organisées en **projets** sur le portail Custom Voice. Chaque projet est spécifique à un pays/langue et au genre de la voix que vous voulez créer. Par exemple, vous pouvez créer un projet de voix féminine pour les bots conversationnels de votre centre d’appels en anglais des États-Unis (« en-US »).

Pour créer votre premier projet, sélectionnez l’onglet **Text-to-Speech/Custom Voice**, puis cliquez sur **New Project**. Suivez les instructions fournies par l’Assistant pour créer votre projet. Une fois le projet créé, quatre onglets sont affichés : **Data**, **Training**, **Testing** et **Deployment**. Utilisez les liens fournis dans [Étapes suivantes](#next-steps) pour savoir comment utiliser chaque onglet.

> [!IMPORTANT]
> Le [portail Custom Voice](https://aka.ms/custom-voice) a été récemment mis à jour. Si vous avez créé antérieurement des données, modèles, tests et points de terminaison publiés sur le portail CRIS.ai ou à l’aide d’API, vous devez créer un projet sur le nouveau portail pour vous connecter à ces anciennes entités.

## <a name="how-to-migrate-to-custom-neural-voice"></a>Comment migrer vers la voix neuronale personnalisée

Si vous utilisez la version non neuronale (ou standard) de Custom Voice, envisagez de migrer vers la voix neuronale personnalisée immédiatement en suivant les étapes ci-dessous. Le passage à la voix neuronale personnalisée vous permet de créer des voix plus réalistes et de développer ainsi des interfaces conversationnelles encore plus naturelles. Avec Grâce à fonctionnalité, vos clients et utilisateurs finaux bénéficient en plus de la dernière technologie de synthèse vocale, dans une approche responsable. 

1. Renseignez-vous sur notre [stratégie de limitation de l’accès](https://aka.ms/gating-overview) et [demandez l’accès ici](https://aka.ms/customneural). Notez que l’accès au service de voix neuronale personnalisée est soumis à la seule discrétion de Microsoft en fonction de nos critères d’éligibilité. Les clients ne peuvent accéder à la technologie que lorsque leur application a été révisée et qu’ils s’engagent à l’utiliser en conformité avec nos [principes sur l’IA responsable](https://microsoft.com/ai/responsible-ai) et le [code de conduite](https://aka.ms/custom-neural-code-of-conduct). 
2. Une fois votre application approuvée, vous disposerez de l’accès à la fonctionnalité de formation « neuronale ». Veillez à vous connecter au [portail de Custom Voice](https://speech.microsoft.com/customvoice) en utilisant le même abonnement Azure que celui que vous fournissez dans votre application. 
    > [!IMPORTANT]
    > Pour protéger les voix professionnelles et empêcher la formation des modèles vocaux avec un enregistrement non autorisé ou sans l’accord de la voix professionnelle, nous avons besoin que le client charge une déclaration enregistrée de la voix professionnelle donnant son consentement. Quand vous préparez votre script d’enregistrement, veillez à inclure cette phrase. « Je, soussigné(e) [indiquez votre nom et votre prénom], avoir compris que les enregistrements de ma voix seront utilisés par [indiquez le nom de la société] pour créer et utiliser une version synthétique de ma voix. »
    > Cette phrase doit être chargée dans l’onglet **Voix professionnelle** en tant que fichier de consentement verbal. Elle servira à vérifier si les enregistrements dans vos jeux de données de formation sont réalisés par la personne qui a donné son consentement.
3. Une fois le modèle de voix neuronale personnalisée créé, déployez le modèle vocal sur un nouveau point de terminaison. Pour créer un nouveau point de terminaison Custom Voice personnalisé avec votre modèle vocal neuronal, accédez à **Synthèse vocale > Custom Voice > Déploiement**. Sélectionnez **Déployer le modèle** et entrez le **nom** et la **description** de votre point de terminaison personnalisé. Ensuite, sélectionnez le modèle de voix neuronale personnalisée que vous souhaitez associer à ce point de terminaison et confirmez le déploiement.  
4. Mettez à jour votre code dans vos applications si vous avez créé un nouveau point de terminaison avec un nouveau modèle. 

## <a name="next-steps"></a>Étapes suivantes

- [Préparer des données pour Custom Voice](how-to-custom-voice-prepare-data.md)
- [Créer une voix personnalisée Custom Voice](how-to-custom-voice-create-voice.md)
- [Guide : Enregistrer des échantillons vocaux](record-custom-voice-samples.md)
