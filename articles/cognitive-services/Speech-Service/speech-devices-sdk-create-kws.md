---
title: Créer un mot clé personnalisé – Service de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Votre appareil est toujours à l’écoute d’un mot (ou d’une phrase) clé. Quand l’utilisateur prononce le mot clé, l’appareil envoie tous les sons suivants vers le cloud, jusqu’à ce que l’utilisateur arrête de parler. Pour différencier votre appareil et renforcer votre marque, vous pouvez personnaliser votre mot clé.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: erhopf
ms.openlocfilehash: 9a5b5de71ee290b39603968cf4309171689e22e4
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184841"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>Créer un mot clé personnalisé à l’aide du service Speech

Votre appareil est toujours à l’écoute d’un mot (ou d’une phrase) clé. Par exemple, « Bonjour Cortana » est un mot clé de l’assistant Cortana. Quand l’utilisateur prononce le mot clé, l’appareil envoie tous les sons suivants vers le cloud, jusqu’à ce que l’utilisateur arrête de parler. Pour différencier votre appareil et renforcer votre marque, vous pouvez personnaliser votre mot clé.

Dans cet article, vous allez apprendre à créer un mot clé personnalisé pour votre appareil.

## <a name="choose-an-effective-keyword"></a>Choisir un mot clé efficace

Vous devez respecter les consignes suivantes lors du choix d’un mot clé :

* Votre mot clé doit être un mot ou une phrase en anglais. Le/la prononcer ne doit pas prendre plus de deux secondes.

* Il est préférable d’utiliser un mot de quatre à sept syllabes. Par exemple, « Hey, Computer » est un bon mot clé, ce qui n’est pas le cas du mot « Hey » seul.

* Les mots clés doivent suivre les règles courantes de prononciation de la langue anglaise.

* Un mot unique ou même inventé qui suit les règles courantes de prononciation de la langue anglaise peut réduire les faux positifs. Par exemple, « computerama » pourrait être un bon mot clé.

* Ne choisissez pas un mot courant, comme « eat » et « go » qui sont prononcés très fréquemment dans les conversations. Ces faux déclencheurs activeraient à tort votre appareil.

* Évitez d’utiliser un mot clé qui a plusieurs prononciations. Sinon, les utilisateurs devront connaître la « bonne » prononciation pour obtenir une réponse de leur appareil. Par exemple, « 509 » peut se prononcer « five zero nine », « five oh nine » ou « five hundred and nine ». « R.E.I. » peut être prononcé « r-e-i » ou « ray ». « Live » peut être prononcé « /līv/ » ou « /liv/ ».

* N’utilisez pas de caractères spéciaux, de symboles ou de chiffres. Par exemple, « Go# » et « 20 + cats » ne sont pas de bons mots clés. Cependant, « go sharp » ou « twenty plus cats » peuvent fonctionner. Pour rendre évidente la bonne prononciation, vous pouvez utiliser les symboles de votre marque et vous servir du marketing et de la documentation.

> [!NOTE]
> Si vous choisissez un mot commercial comme mot clé, vous devez être propriétaire de cette marque commerciale ou avoir l’autorisation de son propriétaire pour utiliser le mot. Microsoft n’est pas responsable des problèmes juridiques que pourrait provoquer votre choix de mot clé.

## <a name="create-your-keyword"></a>Créer votre mot clé

Avant de pouvoir utiliser un mot clé personnalisé, vous devez créer un mot clé à l’aide de la page [Mot clé personnalisé](https://aka.ms/sdsdk-wakewordportal) sur [Speech Studio](https://aka.ms/sdsdk-speechportal). Une fois que vous avez fourni un mot clé, le logiciel génère un fichier que vous déployez sur votre appareil.

1. Accédez au [Speech Studio](https://aka.ms/sdsdk-speechportal) et **connectez-vous**, ou, si vous n’avez pas d’abonnement vocal, choisissez [**Créer un abonnement**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Dans la page [Mot clé personnalisé](https://aka.ms/sdsdk-wakewordportal), tapez le mot clé de votre choix, puis cliquez sur **Ajouter un mot clé**. Nous avons des [instructions](#choose-an-effective-keyword) pour vous aider à choisir un mot clé efficace. La prise en charge est actuellement limitée à la langue en-US.

    ![Entrez votre mot clé](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. Le portail crée des propositions de prononciations pour votre mot clé. Écoutez chaque prononciation en cliquant sur les boutons de lecture, supprimez les coches en regard des prononciations incorrectes. Une fois que seules les bonnes prononciations sont cochées, sélectionnez **Envoyer** pour commencer à générer le mot clé. Si vous souhaitez modifier le mot clé, supprimez le mot clé existant en cliquant sur le bouton Supprimer à droite de la ligne lorsque vous pointez dessus.

    ![Vérifier votre mot clé](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. La génération du modèle peut prendre jusqu’à une minute. Vous êtes ensuite invité à télécharger le fichier.

    ![Télécharger votre mot clé](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. Enregistrez le fichier .zip sur votre ordinateur. Vous aurez besoin de ce fichier pour déployer votre mot clé personnalisé sur votre appareil.

## <a name="next-steps"></a>Étapes suivantes

Testez votre mot clé personnalisé avec le [Démarrage rapide du Kit de développement logiciel (SDK) Speech](https://aka.ms/sdsdk-quickstart).
