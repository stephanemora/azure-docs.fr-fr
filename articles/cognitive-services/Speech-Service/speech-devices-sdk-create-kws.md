---
title: Créer un mot déclencheur personnalisé - Services Speech
titleSuffix: Azure Cognitive Services
description: Votre appareil est toujours à l’écoute d’un mot déclencheur (ou d’une phrase). Quand l’utilisateur prononce le mot déclencheur, l’appareil envoie tous les sons suivants vers le cloud, jusqu’à ce que l’utilisateur arrête de parler. Pour différencier votre appareil et renforcer votre marque, vous pouvez personnaliser votre mot déclencheur.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 2280af4bf37fdb3cd12482da855f979a9180f0ec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65020543"
---
# <a name="create-a-custom-wake-word-by-using-the-speech-service"></a>Créer un mot déclencheur personnalisé à l’aide du service Speech

Votre appareil est toujours à l’écoute d’un mot déclencheur (ou d’une phrase). Par exemple, « Bonjour Cortana » est un mot déclencheur de l’assistant Cortana. Quand l’utilisateur prononce le mot déclencheur, l’appareil envoie tous les sons suivants vers le cloud, jusqu’à ce que l’utilisateur arrête de parler. Pour différencier votre appareil et renforcer votre marque, vous pouvez personnaliser votre mot déclencheur.

Dans cet article, vous allez apprendre à créer un mot déclencheur personnalisé pour votre appareil.

## <a name="choose-an-effective-wake-word"></a>Choisir un mot déclencheur efficace

Vous devez respecter les consignes suivantes lors du choix d’un mot déclencheur :

* Votre mot déclencheur doit être un mot ou une phrase en anglais. Le/la prononcer ne doit pas prendre plus de deux secondes.

* Il est préférable d’utiliser un mot de quatre à sept syllabes. Par exemple, « Hey, Computer » est un bon mot déclencheur, ce qui n’est pas le cas du mot « Hey » seul.

* Les mots déclencheurs doivent suivre les règles courantes de prononciation de la langue anglaise.

* Un mot unique ou même inventé qui suit les règles courantes de prononciation de la langue anglaise peut réduire les faux positifs. Par exemple, « computerama » pourrait être un bon mot déclencheur.

* Ne choisissez pas un mot courant, comme « eat » et « go » qui sont prononcés très fréquemment dans les conversations. Ces faux déclencheurs activeraient à tort votre appareil.

* Évitez d’utiliser un mot déclencheur qui a plusieurs prononciations. Sinon, les utilisateurs devront connaître la « bonne » prononciation pour obtenir une réponse de leur appareil. Par exemple, « 509 » peut se prononcer « five zero nine », « five oh nine » ou « five hundred and nine ». « R.E.I. » peut être prononcé « r-e-i » ou « ray ». « Live » peut être prononcé « /līv/ » ou « /liv/ ».

* N’utilisez pas de caractères spéciaux, de symboles ou de chiffres. Par exemple, « Go# » et « 20 + cats » ne sont pas de bons mots déclencheurs. Cependant, « go sharp » ou « twenty plus cats » peuvent fonctionner. Pour rendre évidente la bonne prononciation, vous pouvez utiliser les symboles de votre marque et vous servir du marketing et de la documentation.

> [!NOTE]
> Si vous choisissez un mot commercial comme mot déclencheur, vous devez être propriétaire de cette marque commerciale ou avoir l’autorisation de son propriétaire pour utiliser le mot. Microsoft n’est pas responsable des problèmes juridiques que pourrait provoquer votre choix de mot déclencheur.

## <a name="create-your-wake-word"></a>Créer votre mot déclencheur

Avant de pouvoir utiliser un mot déclencheur personnalisé avec votre appareil, vous devez en créer un avec le service de génération de mot déclencheur personnalisé de Microsoft. Une fois que vous avez fourni un mot déclencheur, le service génère un fichier que vous déployez sur votre kit de développement pour activer le mot déclencheur sur votre appareil.

1. Accédez au [portail du service Speech personnalisé](https://aka.ms/sdsdk-speechportal) et **connectez-vous**, ou, si vous n’avez pas d’abonnement vocal, choisissez [**Créer un abonnement**](https://go.microsoft.com/fwlink/?linkid=2086754)

    ![Le portail du service Speech personnalisé](media/speech-devices-sdk/wake-word-4.png)

1. À la page du [mot déclencheur personnalisé](https://aka.ms/sdsdk-wakewordportal), tapez le mot déclencheur de votre choix, puis cliquez sur **Ajouter un mot déclencheur**. Nous avons des [instructions](#choose-an-effective-wake-word) pour vous aider à choisir un mot clé efficace. Seule la langue en-US est prise en charge pour le moment.

    ![Entrez votre mot déclencheur](media/speech-devices-sdk/wake-word-5.png)

1. Trois prononciations possibles de votre mot déclencheur sont créées. Vous pouvez choisir toutes les prononciations que vous voulez. Puis sélectionnez **Envoyer** pour générer le mot déclencheur. Si vous souhaitez modifier le mot déclencheur, supprimez d’abord le mot existant : l’icône de suppression apparaît lorsque vous pointez sur la ligne de prononciation.

    ![Vérifiez votre mot déclencheur](media/speech-devices-sdk/wake-word-6.png)

1. La création du modèle peut prendre jusqu'à une minute. Vous êtes alors invité à télécharger le fichier.

    ![Téléchargez votre mot déclencheur](media/speech-devices-sdk/wake-word-7.png)

1. Enregistrez le fichier .zip sur votre ordinateur. Vous aurez besoin de ce fichier pour déployer votre mot déclencheur personnalisé dans le kit de développement.

## <a name="next-steps"></a>Étapes suivantes

Tester votre mot de mise en éveil personnalisé avec le [kit de développement logiciel (SDK) de démarrage rapide Speech](https://aka.ms/sdsdk-quickstart).
