---
title: Obtenir le Kit de développement logiciel (SDK) de dispositifs vocaux
titleSuffix: Azure Cognitive Services
description: Le service Speech fonctionne avec une large gamme d’appareils et de sources audio. Vous pouvez maintenant faire passer vos applications de reconnaissance vocale au niveau supérieur en y associant du matériel et des logiciels. Dans cet article, vous allez découvrir comment accéder au kit SDK Speech Devices et commencer à développer.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: f8861b581c0e02bb78d80817cb2242a38e8aac40
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733465"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Obtenir le kit de développement logiciel (SDK) Speech Devices de Cognitive Services

Le SDK Speech Devices est en préversion limitée et nécessite votre inscription au programme. Actuellement, Microsoft privilégie les grandes entreprises comme candidats pour l’accès à ce produit.

## <a name="request-access"></a>Demander l'accès

Pour accéder au SDK Speech Devices :

1. Accédez au [formulaire d’inscription](https://aka.ms/sdsdk-signup) du kit de développement logiciel (SDK) Microsoft Speech Devices.
1. Lisez le [contrat de licence](speech-devices-sdk-license.md).
1. Si vous acceptez les termes de ce contrat de licence, sélectionnez **J’accepte**.
1. Répondez aux questions du formulaire.
1. Envoyez le formulaire.
1. Si votre adresse e-mail ne fait pas déjà partie d’Azure Active Directory (Azure AD), vous recevez une invitation par e-mail semblable à l’exemple suivant quand votre accès a été approuvé. Si votre adresse e-mail figure déjà dans Azure AD, vous recevez un e-mail de l’équipe Microsoft Speech quand votre accès a été approuvé, et vous pouvez passer directement à la rubrique [Télécharger le SDK Speech Devices](#download-the-speech-devices-sdk).

## <a name="approval-e-mail"></a>E-mail d’approbation

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com>
Subject: You're invited to the Microsoft organization
```

![message électronique](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>Accepter l’accès

Effectuez les étapes suivantes pour joindre Azure AD avec l’adresse e-mail que vous avez fournie lors de l’inscription. Ce processus vous permet d’accéder au [site de téléchargement](https://shares.datatransfer.microsoft.com/) du SDK Speech Devices.

1. Dans l’e-mail que vous avez reçu, cliquez sur **Bien démarrer**. Si votre organisation est déjà un client Office 365, vous êtes invité à vous connecter et vous pouvez passer directement à l’étape 7.

2. Dans la fenêtre de navigateur qui s’ouvre, sélectionnez **Suivant**.

    ![Fenêtre d’authentification](media/speech-devices-sdk/get-sdk-2.png)

3. Créez un compte Microsoft si vous n’en avez pas. Entrez l’adresse e-mail à laquelle vous avez reçu l’e-mail d’invitation.

    ![Création d'un compte Microsoft](media/speech-devices-sdk/get-sdk-3.png)

4. Sélectionnez **Suivant** pour créer un mot de passe.

5. Quand vous êtes invité à vérifier votre adresse e-mail, récupérez le code de vérification mentionné dans l’e-mail d’invitation que vous avez reçu.

7. Collez ou tapez dans la boîte de dialogue le code de sécurité mentionné dans l’e-mail. Dans cet exemple, le code de sécurité est **8406**. Sélectionnez **Suivant**.

    ![Vérifier l’adresse e-mail](media/speech-devices-sdk/get-sdk-6.png)

8. Quand vous voyez l’application Panneau d’accès dans le navigateur, cela signifie que vous avez confirmé que votre adresse e-mail fait partie d’Azure AD. Vous avez à présent accès au site de téléchargement du kit de développement logiciel (SDK) Speech Devices.

## <a name="download-the-speech-devices-sdk"></a>Télécharger le kit de développement logiciel (SDK) Speech Devices

Accédez au [site de téléchargement du SDK Speech Devices](https://shares.datatransfer.microsoft.com/). Connectez-vous avec le compte Microsoft que vous avez créé précédemment.

![Site de téléchargement du SDK](media/speech-devices-sdk/get-sdk-7.png)

Pour télécharger le SDK Speech Devices, l’exemple de code associé et les documents de référence :

1. Téléchargez et installez l’outil Aspera Connect quand vous y êtes invité dans le navigateur.

    ![Télécharger Aspera Connect](media/speech-devices-sdk/get-sdk-8.png)

1. Sélectionnez **Oui** pour basculer les applications vers Aspera Connect.

    ![Basculer vers Aspera Connect](media/speech-devices-sdk/get-sdk-9.png)

1. Sélectionnez **Autoriser** pour confirmer le téléchargement des fichiers à l’aide d’Aspera Connect.

    ![Télécharger à l’aide d’Aspera Connect](media/speech-devices-sdk/get-sdk-10.png)

1. Fermez la fenêtre des transferts Aspera Connect une fois les fichiers téléchargés.

    ![Fenêtre des transferts Aspera Connect](media/speech-devices-sdk/get-sdk-11.png)

Par défaut, les fichiers sont téléchargés dans votre dossier **Téléchargements**. Vous pouvez maintenant vous déconnecter de ce site.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Prise en main du kit de développement logiciel (SDK) Speech Devices](speech-devices-sdk-qsg.md)
