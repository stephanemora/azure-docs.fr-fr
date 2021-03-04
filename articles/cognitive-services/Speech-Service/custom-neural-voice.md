---
title: Présentation de la voix neuronale personnalisée – Service Speech
titleSuffix: Azure Cognitive Services
description: La voix neuronale personnalisée est une fonctionnalité de synthèse vocale qui vous permet de créer une voix de synthèse personnalisée unique en son genre pour vos applications en fournissant vos propres données audio à titre d’échantillon.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: trbye
ms.openlocfilehash: 36885e4673b83d1db7972f03c4a6309f766206c5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713097"
---
# <a name="what-is-custom-neural-voice"></a>Qu’est-ce qu’une voix neuronale personnalisée ?

La voix neuronale personnalisée est une fonctionnalité de [synthèse vocale](./text-to-speech.md) (TTS) qui vous permet de créer une voix de synthèse personnalisée unique en son genre pour vos applications en fournissant vos propres données audio à titre d’échantillon. La synthèse vocale fonctionne en convertissant le texte en parole de synthèse à l’aide d’un modèle Machine Learning qui ressemble à la voix choisie. Grâce à l’[API REST](./rest-text-to-speech.md), vous pouvez permettre à vos applications de parler avec des [voix prédéfinies](./language-support.md#neural-voices) ou avec vos propres modèles [vocaux personnalisés](./how-to-custom-voice-prepare-data.md) développés par l’intermédiaire de la fonctionnalité de voix neuronale personnalisée. La voix neuronale personnalisée est basée sur la technologie de synthèse vocale neuronale qui crée une voix à la sonorité naturelle qui est souvent impossible à distinguer d’une voix humaine.
La voix réaliste et naturelle de la voix neuronale personnalisée peut représenter des marques, personnifier des machines et permettre aux utilisateurs d’interagir de manière naturelle avec les applications.

> [!NOTE]
> La fonctionnalité de voix neuronale personnalisée nécessite une inscription, et l’accès est limité en fonction des critères d’éligibilité et d’utilisation de Microsoft. Les clients qui souhaitent utiliser cette fonctionnalité sont tenus d’inscrire leurs cas d’usage par le biais du [formulaire d’admission](https://aka.ms/customneural).

## <a name="the-basics-of-custom-neural-voice"></a>Concepts de base de la voix neuronale personnalisée

La technologie de synthèse vocale neuronale sous-jacente utilisée pour la voix neuronale personnalisée est constituée de trois composants principaux : un analyseur de texte, un modèle acoustique neuronal et un vocodeur neuronal. Pour générer de la parole de synthèse naturelle à partir d’un texte, celui-ci est d’abord entré dans l’analyseur de texte, qui fournit une sortie sous forme de séquence de phonèmes. Un phonème est une unité sonore de base qui distingue un mot d’un autre dans une langue particulière. Une séquence de phonèmes définit les prononciations des mots fournis dans le texte. 

Ensuite, la séquence de phonèmes entre dans le modèle acoustique neuronal pour prédire les caractéristiques acoustiques qui définissent les signaux de parole, comme le timbre, le style de parole, la vitesse, les intonations et les modèles de stress. Enfin, le vocodeur neuronal convertit les caractéristiques acoustiques en ondes audibles afin de générer de la parole de synthèse.

![Image d’introduction pour la voix neuronale personnalisée.](./media/custom-voice/cnv-intro.png)

Les modèles vocaux de la synthèse vocale neuronale sont formés à l’aide de réseaux neuronaux profonds basés sur des échantillons d’enregistrement de voix humaines. Dans ce [blog](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-extends-support-to-15-more-languages-with/ba-p/1505911), nous décrivons le fonctionnement de la synthèse vocale neuronale avec des modèles de synthèse vocale neuronales de pointe. Le blog explique également comment un modèle de base universel peut être adapté avec moins de 2 heures de données vocales (ou moins de 2 000 énoncés enregistrés) d’un locuteur cible et apprendre à parler dans la voix de ce locuteur cible. Pour savoir comment un vocodeur neuronal est formé, consultez le [billet de blog](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860).

Grâce à la capacité de personnalisation de la voix neuronale personnalisée, vous pouvez adapter le moteur de synthèse vocale neuronale pour mieux répondre à vos scénarios utilisateur. Pour créer une voix neuronale personnalisée, utilisez [Speech Studio](https://speech.microsoft.com/customvoice) pour charger le fichier audio enregistré et les scripts correspondants, effectuer l’apprentissage du modèle et déployer la voix sur un point de terminaison personnalisé. Selon le cas d’usage, la voix neuronale personnalisée peut être utilisée pour convertir du texte en parole en temps réel (par exemple, dans un assistant virtuel intelligent) ou pour générer du contenu audio hors connexion (par exemple, dans un livre audio ou des instructions dans des applications d’e-Learning) avec le texte fourni par l’utilisateur. Ce contenu est disponible via l’[API REST](./rest-text-to-speech.md), le [Kit de développement logiciel (SDK) Speech](./get-started-text-to-speech.md?pivots=programming-language-csharp&tabs=script%2cwindowsinstall) ou un [portail web](https://speech.microsoft.com/audiocontentcreation).

## <a name="terms-and-definitions"></a>Termes et définitions

| **Terme**      | **Définition**                                                                                                                                                                                                                                                                                                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Modèle vocal   | Modèle de conversion de texte par synthèse vocale qui peut imiter les caractéristiques vocales uniques d’un locuteur cible. Un *modèle vocal* est également connu sous le nom de *police de la voix* ou de *voix de synthèse*. Un modèle vocal est un ensemble de paramètres en format binaire qui n’est pas lisible par l’homme et qui ne contient pas d’enregistrements audio. Il ne peut pas faire l’objet d’une ingénierie inverse pour déduire ou construire l’audio d’une voix humaine. |
| Voix professionnelle  | Personnes ou locuteurs cibles dont la voix est enregistrée et utilisée pour créer des modèles vocaux destinés à ressembler à la voix du professionnel.                                                                                                                                                                                                                                                   |
| Synthèse vocale standard  | Méthode standard ou « traditionnelle » de synthèse vocale qui décompose le langage parlé en bribes phonétiques afin qu’elles puissent être remixées et appariées à l’aide de méthodes classiques de programmation ou de statistiques.                                                                                                                                                                                                    |
| Synthèse vocale neuronale    | La synthèse vocale neuronale synthétise la parole à l’aide de réseaux neuronaux profonds qui ont « appris » la manière dont la phonétique est combinée en voix humaine naturelle, plutôt que d’utiliser des méthodes procédurales de programmation ou de statistiques. En plus des enregistrements d’une voix professionnelle cible, la synthèse vocale neuronale utilise une bibliothèque source/un modèle de base qui est construit avec des enregistrements vocaux de nombreux locuteurs différents.          |
| Données de formation | Jeu de données de formation de la voix neuronale personnalisée qui comprend les enregistrements audio de la voix professionnelle et les transcriptions de texte associées.                                                                                                                                                                                                                                                               |
| Utilisateur       | Personnage décrivant qui doit être cette voix. Une bonne conception de personnage guidera toute la création de la voix, qu’il s’agisse de choisir un modèle vocal disponible déjà créé ou de commencer de zéro en choisissant et en enregistrant une nouvelle voix professionnelle.                                                                                                |
| Script        | Un script est un fichier texte qui contient les énoncés que doit prononcer votre voix. (Le terme « *énoncés* » englobe les phrases complètes et les expressions plus courtes.)                                                                                                                                                                                                                               |

## <a name="responsible-use-of-ai"></a>Utilisation responsable de l’IA

Pour savoir comment utiliser la voix neuronale personnalisée de façon responsable, consultez la [note de transparence](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context). Les notes de transparence de Microsoft sont destinées à vous aider à comprendre le fonctionnement de notre technologie d’IA, les choix que les propriétaires de systèmes peuvent faire et qui influencent les performances et le comportement du système, et l’importance de penser à l’ensemble du système, y compris la technologie, les personnes et l’environnement.

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Custom Voice](how-to-custom-voice.md)
* [Créer et utiliser un point de terminaison Custom Voice](how-to-custom-voice-create-voice.md)