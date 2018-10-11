---
title: Créer un mot déclencheur personnalisé
description: Découvrez comment créer un mot déclencheur personnalisé pour le SDK Speech Devices.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 52f1d9cb242f140d485ccf7b39a55a46ee575a3d
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883616"
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

Avant de pouvoir utiliser un mot déclencheur personnalisé avec votre appareil, vous devez le créer à l’aide du service de génération de mot déclencheur personnalisé de Microsoft. Une fois que vous avez fourni un mot déclencheur, le service génère un fichier que vous déployez sur votre kit de développement pour activer ce mot déclencheur sur votre appareil.

1. Accédez au [portail du service Custom Speech](https://cris.ai/).

1. Créez un compte en utilisant l’adresse e-mail à laquelle vous avez reçu l’invitation d’Azure Active Directory. 

    ![Créer un nouveau compte](media/speech-devices-sdk/wake-word-1.png)
 
1.  Après vous être connecté, remplissez le formulaire, puis sélectionnez **Start my journey** (Démarrer mon parcours).

    ![Connexion réussie](media/speech-devices-sdk/wake-word-3.png)
 
1. La page **Custom Wake Word** (Mot déclencheur personnalisé) n’est pas accessible au public ; il n’existe donc aucun lien direct pour y accéder. La fonctionnalité Custom Speech nécessite un abonnement Azure, mais pas la fonctionnalité Custom Wake Word. Si vous recevez le message d’erreur **Aucun abonnement trouvé**, remplacez simplement **"Subscriptions?errorMessage=No%20Subscriptions%20found"** par "**customkws**" dans l’URL et appuyez sur Entrée. L’URL doit être l’une de celles-ci : https://westus.cris.ai/customkws, https://eastasia.cris.ai/customkws ou https://northeurope.cris.ai/customkws, en fonction de votre région.

    ![La page Custom Wake Word (Mot déclencheur personnalisé) est masquée](media/speech-devices-sdk/wake-word-4.png)
 
1. Tapez le mot déclencheur de votre choix, puis sélectionnez **Submit the word** (Soumettre le mot).

    ![Entrez votre mot déclencheur](media/speech-devices-sdk/wake-word-5.png)
 
1. La création des fichiers peut nécessiter quelques minutes. Vous devez voir un cercle en rotation dans la fenêtre de votre navigateur. Après quelques instants, une barre d’informations s’affiche et vous invite à télécharger un fichier .zip.

    ![Réception du fichier .zip](media/speech-devices-sdk/wake-word-6.png)

1. Enregistrez le fichier .zip sur votre ordinateur. Vous avez besoin de ce fichier pour déployer le mot déclencheur personnalisé dans le kit de développement. Pour déployer le mot déclencheur personnalisé, suivez les instructions fournies dans [ Prise en main du Kit de développement logiciel (SDK) Speech Devices](speech-devices-sdk-qsg.md).

1. Sélectionnez **Sign out** (Se déconnecter).

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) et inscrivez-vous pour obtenir le kit de développement logiciel (SDK) Speech Devices.

> [!div class="nextstepaction"]
> [S’inscrire pour le kit de développement logiciel (SDK) Speech Devices](get-speech-devices-sdk.md)

