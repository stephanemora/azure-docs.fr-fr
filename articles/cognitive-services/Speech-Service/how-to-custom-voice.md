---
title: Bien démarrer avec la voix neuronale personnalisée - Service Speech
titleSuffix: Azure Cognitive Services
description: La voix neuronale personnalisée est un ensemble d’outils en ligne qui vous permettent de créer une voix unique reconnaissable entre toutes pour votre marque. Pour commencer, vous n’avez besoin que de quelques fichiers audio et des transcriptions associées.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: trbye
ms.openlocfilehash: 4564a84c89737744abd6faefda0159edef96c5dc
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111962886"
---
# <a name="get-started-with-custom-neural-voice"></a>Bien démarrer avec la voix neuronale personnalisée

La [voix neuronale personnalisée](https://aka.ms/customvoice) est un ensemble d’outils en ligne qui vous permettent de créer une voix unique reconnaissable entre toutes pour votre marque. Pour commencer, vous n’avez besoin que de quelques fichiers audio et des transcriptions associées. Suivez les liens ci-dessous pour commencer à créer une expérience de synthèse vocale personnalisée. Consultez les [langues](language-support.md#customization) et [régions](regions.md#custom-voices) prises en charge pour la voix neuronale personnalisée.

> [!NOTE]
> Dans le cadre de son engagement en faveur de la conception d’une IA responsable, Microsoft a limité l’utilisation de la voix neuronale personnalisée. Vous pouvez accéder à la technologie uniquement après que vos applications ont été examinées et que vous vous êtes engagé à l’utiliser en conformité avec nos principes sur l’IA responsable. Renseignez-vous sur notre [stratégie de limitation de l’accès](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) et [demandez l’accès ici](https://aka.ms/customneural). 
 
## <a name="set-up-your-azure-account"></a>Configurer votre compte Azure

Un abonnement au service Speech est nécessaire pour pouvoir utiliser la voix neuronale personnalisée. Suivez ces instructions pour créer un abonnement Speech standard dans Azure. Si vous n’avez pas de compte Azure, vous pouvez vous inscrire pour en créer un.  

Une fois que vous avez créé un compte Azure et un abonnement au service Speech, vous avez besoin de vous connecter à Speech Studio pour connecter votre abonnement.

1. Obtenez votre clé d’abonnement Speech sur le portail Azure.
2. Connectez-vous à [Speech Studio](https://speech.microsoft.com), puis cliquez sur **Voix personnalisée**.
3. Sélectionnez votre abonnement et créez un projet Speech.
4. Si vous voulez basculer vers un autre abonnement Speech, utilisez l’icône représentant une roue dentée dans le volet de navigation du haut.

> [!NOTE]
> Pour pouvoir utiliser le service Speech, vous devez disposer d’une clé F0 ou S0 créée dans Azure. La voix neuronale personnalisée prend uniquement en charge le niveau S0. 

## <a name="create-a-project"></a>Création d’un projet

Les contenus comme les données, les modèles, les tests et les points de terminaison sont organisés en **projets** dans Speech Studio. Chaque projet est spécifique à un pays/langue et au genre de la voix que vous voulez créer. Par exemple, vous pouvez créer un projet de voix féminine pour les bots conversationnels de votre centre d’appels en anglais des États-Unis (« en-US »).

Pour créer votre premier projet, sélectionnez l’onglet **Synthèse vocale/Voix personnalisée**, puis cliquez sur **Créer un projet**. Suivez les instructions fournies par l’Assistant pour créer votre projet. Une fois que vous avez créé un projet, quatre onglets s’affichent : **Configurer les talents de voix**, **Préparer des données d’entraînement**, **Entraîner un modèle** et **Déployer le modèle**. Utilisez les liens fournis dans les [étapes suivantes](#next-steps) pour apprendre à utiliser chaque onglet.

## <a name="tips-for-creating-a-custom-neural-voice"></a>Conseils pour la création d’une voix neuronale personnalisée

La création d’une voix personnalisée nécessite un contrôle qualité minutieux à chaque étape, depuis la conception de la voix et la préparation des données, jusqu’au déploiement du modèle vocal sur votre système. Voici quelques étapes clés à effectuer lors de la création d’une voix neuronale personnalisée pour votre organisation. 

### <a name="persona-design"></a>Conception du personnage

Tout d’abord, concevez un personnage de la voix qui représente votre marque en utilisant un document bref qui définit des éléments comme les caractéristiques de la voix et son caractère inhérent. Ce personnage va vous aider à guider le processus de création d’un modèle vocal personnalisé, notamment la définition des scripts, la sélection du talent vocal, l’entraînement et le réglage vocal.

### <a name="script-selection"></a>Sélection du script
 
Sélectionnez soigneusement le script d’enregistrement pour représenter les scénarios utilisateur de votre voix. Par exemple, vous pouvez utiliser les expressions issues des conversations de bot comme script d’enregistrement si vous créez un bot de service client. Incluez différents types de phrases dans vos scripts, notamment des affirmations, des questions, des exclamations, etc.

### <a name="preparing-training-data"></a>Préparation des données d’entraînement

Nous vous recommandons de capturer les enregistrements audio dans un studio d’enregistrement de qualité professionnelle pour obtenir un rapport signal/bruit élevé. La qualité du modèle vocal dépend fortement de vos données d’entraînement. Il est nécessaire de faire attention à l’homogénéité du volume, au débit de parole, à la tonalité et à la cohérence dans la manière de s’exprimer.

Une fois les enregistrements prêts, suivez [Préparer des données d’entraînement](how-to-custom-voice-prepare-data.md) pour préparer les données d’entraînement dans le bon format.

### <a name="training"></a>Entrainement

Une fois que vous avez préparé les données d’entraînement, accédez à [Speech Studio](https://aka.ms/custom-voice) pour créer votre voix neuronale personnalisée. Vous avez besoin de sélectionner au moins 300 énoncés pour créer une voix neuronale personnalisée. Une série de vérifications de la qualité des données s’effectue automatiquement quand vous les chargez. Pour créer des modèles vocaux de haute qualité, vous devez corriger les erreurs et soumettre à nouveau les données.

### <a name="testing"></a>Test

Préparez des scripts de test pour votre modèle vocal qui couvrent les différents cas d’usage de vos applications. Il est recommandé d’utiliser des scripts au sein et en dehors du jeu de données d’entraînement afin de pouvoir tester la qualité plus largement pour différents contenus.

### <a name="tuning-and-adjustment"></a>Réglage et ajustement

Le style et les caractéristiques du modèle vocal entraîné dépendent du style et de la qualité des enregistrements du talent vocal utilisé pour l’entraînement. Néanmoins, plusieurs ajustements peuvent être effectués à l’aide de [SSML (Speech Synthesis Markup Language)](./speech-synthesis-markup.md?tabs=csharp) quand vous effectuez les appels d’API à votre modèle vocal pour générer une voix de synthèse. SSML correspond au langage de balisage utilisé pour communiquer avec le service TTS afin de convertir du texte en audio. Les ajustements incluent la modification de la hauteur de la voix, du débit, de l’intonation et la correction de la prononciation.  Si le modèle vocal est généré avec plusieurs styles, le langage SSML peut également être utilisé pour passer de l’un à l’autre.

## <a name="migrate-to-custom-neural-voice"></a>Migrer vers la voix neuronale personnalisée

Le niveau d’entraînement standard/non neuronal (statistiques paramétriques, concaténatif) de Custom Voice est en cours de dépréciation. L’annonce a été envoyée à tous les abonnements Speech existants avant le 28/02/2021. Pendant la période de dépréciation (du 1/3/2021 au 29/2/2024), les utilisateurs du niveau standard existants peuvent continuer à utiliser leurs modèles non neuronaux créés. Tous les nouveaux utilisateurs/nouvelles ressources de message doivent passer au niveau neural/voix neuronale personnalisée. Après le 29/2/2024, toutes les voix personnalisées standard/non-neuronales ne seront plus prises en charge. 

Si vous utilisez la version non neuronale/standard de Custom Voice, envisagez de migrer vers la voix neuronale personnalisée immédiatement en suivant les étapes ci-dessous. Le passage à la voix neuronale personnalisée vous permet de créer des voix plus réalistes et de développer ainsi des interfaces conversationnelles encore plus naturelles. Avec Grâce à fonctionnalité, vos clients et utilisateurs finaux bénéficient en plus de la dernière technologie de synthèse vocale, dans une approche responsable. 

1. Renseignez-vous sur notre [stratégie de limitation de l’accès](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) et [demandez l’accès ici](https://aka.ms/customneural). Notez que l’accès au service de voix neuronale personnalisée est soumis à la seule discrétion de Microsoft en fonction de nos critères d’éligibilité. Les clients ne peuvent accéder à la technologie que lorsque leur application a été révisée et qu’ils s’engagent à l’utiliser en conformité avec nos [principes sur l’IA responsable](https://microsoft.com/ai/responsible-ai) et le [code de conduite](/legal/cognitive-services/speech-service/tts-code-of-conduct?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 
2. Une fois votre application approuvée, vous disposerez de l’accès à la fonctionnalité de formation « neuronale ». Veillez à vous connecter à [Speech Studio](https://speech.microsoft.com) en utilisant le même abonnement Azure que celui que vous indiquez dans votre application. 
    > [!IMPORTANT]
    > Pour protéger le talent vocal et empêcher l’entraînement des modèles vocaux avec un enregistrement non autorisé ou sans l’accord du talent vocal, nous exigeons du client qu’il charge une déclaration enregistrée du talent vocal donnant son consentement. Quand vous préparez votre script d’enregistrement, veillez à inclure cette phrase. « Je, soussigné(e) [indiquez votre nom et votre prénom], avoir compris que les enregistrements de ma voix seront utilisés par [indiquez le nom de la société] pour créer et utiliser une version synthétique de ma voix. »
    > Cette phrase doit être chargée sous l’onglet **Configurer les talents de voix** sous forme de fichier de consentement verbal. Elle servira à vérifier si les enregistrements dans vos jeux de données de formation sont réalisés par la personne qui a donné son consentement.
3. Une fois le modèle de voix neuronale personnalisée créé, déployez le modèle vocal sur un nouveau point de terminaison. Pour créer un point de terminaison de voix personnalisée avec votre modèle vocal neuronal, accédez à **Synthèse vocale > Voix personnalisée > Déployer le modèle**. Sélectionnez **Déployer les modèles** et entrez le **nom** et la **description** de votre point de terminaison personnalisé. Ensuite, sélectionnez le modèle de voix neuronale personnalisée que vous souhaitez associer à ce point de terminaison et confirmez le déploiement.  
4. Mettez à jour votre code dans vos applications si vous avez créé un nouveau point de terminaison avec un nouveau modèle. 

## <a name="next-steps"></a>Étapes suivantes

- [Préparer des données pour la voix neuronale personnalisée](how-to-custom-voice-prepare-data.md)
- [Entraîner et déployer une voix neuronale personnalisée](how-to-custom-voice-create-voice.md)
- [Guide pratique pour enregistrer des échantillons vocaux](record-custom-voice-samples.md)