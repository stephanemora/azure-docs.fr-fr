---
title: Création d’un mot déclencheur personnalisé | Microsoft Docs
description: Création d’un mot déclencheur personnalisé pour le Kit de développement logiciel (SDK) Speech Devices.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 2575ed24bb931ca4da05dd6663b976406af590e6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370700"
---
# <a name="create-a-custom-wake-word-using-speech-service"></a>Créer un mot déclencheur personnalisé à l’aide du service Speech

Votre appareil est toujours à l’écoute d’un mot déclencheur (ou d’une phrase). Par exemple, « Bonjour Cortana » est un mot déclencheur de l’assistant Cortana. Lorsque l’utilisateur prononce le mot déclencheur, l’appareil commence à envoyer tous les sons suivants vers le cloud jusqu'à ce que l’utilisateur arrête de parler. Pour différencier votre appareil et renforcer votre marque, vous pouvez personnaliser votre mot déclencheur.

Dans cet article, vous allez apprendre à créer un mot déclencheur personnalisé pour votre appareil.

## <a name="choosing-an-effective-wake-word"></a>Choix d’un mot déclencheur efficace

Vous devez respecter les consignes suivantes lors du choix d’un mot déclencheur.

* Votre mot déclencheur doit être un mot ou une phrase en anglais. Le/la prononcer ne doit pas prendre plus de deux secondes.

* Il est préférable d’utiliser un mot de 4 à 7 syllabes. Par exemple, « Hey, Computer » est un bon mot déclencheur, ce qui n’est pas le cas du mot « Hey » seul.

* Les mots déclencheurs doivent suivre les règles courantes de prononciation de la langue anglaise.

* Un mot unique ou composé qui suit les règles courantes de prononciation de la langue anglaise réduit les faux positifs. Par exemple, « computerama » est un bon mot déclencheur.

* Ne choisissez pas un mot courant, comme « eat » et « go » qui sont prononcés très fréquemment dans les conversations. Ces faux déclencheurs activeraient à tort votre appareil.

* Évitez d’utiliser un mot déclencheur qui a plusieurs prononciations. Sinon, les utilisateurs devront connaître la « bonne » prononciation pour obtenir une réponse de leur appareil. Par exemple, « 509 » peut se prononcer « five zero nine », « five oh nine » ou « five hundred and nine ». « R.E.I. » peut se prononcer « R E I » ou « Ray ». « Live » peut se prononcer [līv] ou [liv].

* N’utilisez pas de caractères spéciaux, de symboles ou de chiffres. Par exemple, « Go# » et « 20 + cats » ne sont pas de bons mots déclencheurs. Cependant, « go sharp » ou « twentyy plus cats » peuvent fonctionner. Pour rendre évidente la bonne prononciation, vous pouvez utiliser les symboles de votre marque et vous servir du marketing et de la documentation.

> [!NOTE]
> Si vous choisissez un mot commercial comme mot déclencheur, vous devez posséder cette marque commerciale ou avoir l’autorisation de son propriétaire pour l’utiliser. Microsoft n’est pas responsable des problèmes juridiques que pourrait provoquer votre choix de mot déclencheur.

## <a name="creating-your-wake-word"></a>Création de votre mot déclencheur

Avant de pouvoir utiliser un mot déclencheur personnalisé avec votre appareil, vous devez le créer à l’aide du service de génération de mot déclencheur personnalisé de Microsoft. Après avoir fourni un mot déclencheur, le service génère un fichier que vous déployez ensuite sur votre kit de développement pour activer ce mot déclencheur sur votre appareil.

1. Accédez au [portail Custom Speech Service](https://cris.ai/).

2. Créez un compte en utilisant l’adresse e-mail sur laquelle vous avez reçu l’invitation d’Azure Active Directory. 

    ![créer un compte](media/speech-devices-sdk/wake-word-1.png)
 
3.  Une fois connecté, remplissez le formulaire, puis cliquez sur **Start the journey** (Démarrer le parcours).

    ![connexion réussie](media/speech-devices-sdk/wake-word-3.png)
 
4. La page **Custom Wake Word** (Mot déclencheur personnalisé) n’est pas accessible au public, il n’existe donc aucun lien pour y accéder. À la place, cliquez sur ce lien ou collez-le : https://cris.ai/customkws.

    ![page masquée](media/speech-devices-sdk/wake-word-4.png)
 
6. Tapez le mot déclencheur de votre choix, puis **soumettez-le**.

    ![taper votre mot déclencheur](media/speech-devices-sdk/wake-word-5.png)
 
7. La création des fichiers peut nécessiter quelques minutes. Vous devez voir un cercle en rotation sur l’onglet de votre navigateur. Après quelques instants, une barre d’informations s’affiche et vous invite à télécharger un fichier `.zip`.

    ![réception du fichier .zip](media/speech-devices-sdk/wake-word-6.png)

8. Enregistrez le fichier `.zip` sur votre ordinateur. Vous avez besoin de ce fichier pour déployer le mot déclencheur personnalisé pour le kit de développement en suivant les instructions dans [Prise en main du Kit de développement logiciel (SDK) Speech Devices](speech-devices-sdk-qsg.md).

9. Vous pouvez maintenant **vous déconnecter**.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) et inscrivez-vous pour obtenir le kit de développement logiciel (SDK) Speech Devices.

> [!div class="nextstepaction"]
> [S’inscrire pour le kit de développement logiciel (SDK) Speech Devices](get-speech-devices-sdk.md)

