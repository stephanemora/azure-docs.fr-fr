---
title: Essayez le service Speech gratuitement
description: Découvrez comment vous pouvez essayer gratuitement le service Speech.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/17/2018
ms.author: v-jerkin
ms.openlocfilehash: ff9a258d42f25ded82545909cdeade119548148e
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325205"
---
# <a name="try-the-speech-service-for-free"></a>Essayez le service Speech gratuitement

La prise en main du service Speech est simple et économique. Un essai gratuit de 30 jours vous permet de découvrir les fonctionnalités du service et de décider s’il est adapté aux besoins de votre application.

Si vous avez besoin de plus de temps, inscrivez-vous pour un compte Microsoft Azure, fourni avec 200 $ de crédit de service que vous pouvez utiliser pour un abonnement au service Speech payant pendant 30 jours.

Enfin, le service Speech offre un niveau gratuit de faible volume permettant de développer des applications. Vous pouvez conserver cet abonnement gratuit même après l’expiration de votre crédit de service.

## <a name="free-trial"></a>Essai gratuit

L’essai gratuit de 30 jours vous donne accès au niveau tarifaire standard S0 pour une durée limitée. Pour vous inscrire pour un essai gratuit de 30 jours, procédez comme suit.

1. Accédez à la page [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

1. Accédez à l’onglet Speech et cliquez sur le bouton **Get API key** (Obtenir la clé API) en regard de « Services Speech ».

   ![Onglet Services Speech](media/index/try-speech-api-free-trial1.png)<br>
   ![Clé API](media/index/try-speech-api-free-trial2.png)

3. Acceptez les conditions et sélectionnez vos paramètres régionaux dans le menu déroulant.

   ![J’accepte les conditions](media/index/try-speech-api-free-trial3.png)

4. Connectez-vous à l’aide de votre compte Microsoft, Facebook, LinkedIn ou GitHub. Vous pouvez également vous inscrire pour obtenir un compte Microsoft gratuit :

    * Accédez au [portail du compte Microsoft](https://account.microsoft.com/account).
    * Cliquez sur **Se connecter avec Microsoft**.

    ![Se connecter](media/index/try-speech-api-free-trial4.png)

    * Lorsque vous êtes invité à vous connecter, cliquez sur « Créer un compte ».

    ![Créer un compte](media/index/try-speech-api-free-trial5.png)

    * Dans les étapes suivantes, entrez votre adresse de messagerie ou votre numéro de téléphone, assignez un mot de passe et suivez les instructions pour vérifier votre nouveau compte Microsoft.

Une fois connecté, votre essai gratuit démarre. La page web affichée répertorie tous les services Cognitive Services pour lesquels vous disposez actuellement d’un abonnement d’essai. Deux clés d’abonnement sont répertoriées en regard de « Services Speech ». Vous pouvez utiliser n’importe laquelle de ces clés dans vos applications.

> [!NOTE]
> Tous les abonnements d’essai gratuit se trouvent dans la région USA Ouest. Veillez à utiliser le point de terminaison correspondant à votre région lors de vos requêtes.

## <a name="new-azure-account"></a>Nouveau compte Azure

Les nouveaux comptes Azure reçoivent un crédit de service de 200 $ qui dure jusqu’à 30 jours. Ce crédit peut servir à découvrir plus avant le service Speech ou à commencer à développer des applications.

Pour vous inscrire pour un nouveau compte Azure, procédez comme suit.

1. Accédez à la [page de connexion Azure](https://azure.microsoft.com/free/ai/). 

1. Cliquez sur **Commencer gratuitement**.

    ![Commencer gratuitement](media/index/try-speech-api-new-azure1.png)

3. Connectez-vous à votre compte Microsoft. Si vous n’en avez pas :

    * Accédez au [portail du compte Microsoft](https://account.microsoft.com/account).
    * Cliquez sur **Se connecter avec Microsoft**.
    * Lorsque vous êtes invité à vous connecter, cliquez sur « Créer un compte ».
    * Dans les étapes suivantes, entrez votre adresse de messagerie ou votre numéro de téléphone, assignez un mot de passe et suivez les instructions pour vérifier votre nouveau compte Microsoft.

1. Entrez les autres informations requises pour vous inscrire pour un compte. Indiquez votre pays et votre nom, et fournissez un numéro de téléphone et une adresse électronique.

    ![Entrer les informations](media/index/try-speech-api-new-azure2.png)

    Vérifiez votre identité par téléphone et en fournissant un numéro de carte de crédit, puis acceptez le contrat utilisateur Azure. (Votre carte de crédit ne sera pas débitée.)

    ![Accepter le contrat](media/index/try-speech-api-new-azure3.png)

Votre compte Azure gratuit est créé. Suivez les étapes de la section suivante pour démarrer un abonnement au service Speech.

## <a name="create-a-speech-resource-in-azure"></a>Créer une ressource Speech dans Azure

Pour ajouter une ressource de service Speech à votre compte Azure, procédez comme suit.

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/) en utilisant votre compte Microsoft.

1. Cliquez sur **Créer une ressource** (l’icône **+** verte) dans la partie supérieure gauche du portail.

    ![Créer une ressource](media/index/try-speech-api-create-speech1.png)

1. Dans la fenêtre, recherchez Speech.

    ![Cliquer sur Speech](media/index/try-speech-api-create-speech2.png)

1. Dans les résultats de recherche, cliquez sur « Speech (préversion) ».

1. Cliquez sur le bouton **Créer** situé en bas du panneau Service Speech.

    ![Click Create](media/index/try-speech-api-create-speech3.png)

1. Dans le panneau Créer, entrez :

    * Un nom pour la nouvelle ressource. Ce nom vous permet de faire la distinction entre plusieurs abonnements au même service.
    * Choisissez l’abonnement Azure associé à la nouvelle ressource pour déterminer la façon dont les frais sont facturés.
    * Choisissez la région où la ressource sera utilisée. Actuellement, le service Speech est disponible dans les régions Asie Est, Europe Nord et USA Ouest.
    * Choisissez le niveau de tarification, soit F0 (abonnement gratuit limité) soit S0 (abonnement standard). Cliquez sur **Afficher tous les détails de la tarification** pour plus d’informations sur la tarification et les quotas d’utilisation pour chaque niveau.
    * Créez un nouveau groupe de ressources pour cet abonnement Speech ou affectez-le à un groupe existant. Les groupes de ressources vous permettent d’organiser vos différents abonnements Azure.
    * Pour accéder facilement à votre abonnement à l’avenir, cochez la case **Épingler au tableau de bord**.
    * Cliquez sur **Create** (Créer).

    ![Cliquez sur Créer dans le panneau](media/index/try-speech-api-create-speech4.png)

    La création et le déploiement de votre ressource Speech peuvent prendre quelques instants. Le panneau Démarrage rapide affiche des informations sur votre nouvelle ressource.

    ![Panneau Démarrage rapide](media/index/try-speech-api-create-speech5.png)

1. Cliquez sur le lien **Clés** sous l’étape 1 du panneau Démarrage rapide pour afficher les clés de votre abonnement. Chaque abonnement dispose de deux clés ; vous pouvez utiliser n’importe laquelle de ces clés dans votre application. Cliquez sur le bouton en regard de chaque clé pour la copier dans le Presse-papiers et la coller dans votre code.

> [!NOTE]
> Vous pouvez créer autant d’abonnements de niveau standard que vous le souhaitez dans une ou plusieurs régions. Toutefois, vous ne pouvez créer qu’un seul abonnement de niveau gratuit.

## <a name="next-steps"></a>Étapes suivantes

Effectuez l’un de nos Démarrages rapides de 10 minutes, ou consultez nos exemples de kit de développement logiciel (SDK).

> [!div class="nextstepaction"]
> [Démarrage rapide : reconnaissance vocale dans C#](quickstart-csharp-dotnet-windows.md)
> [Exemples du kit de développement logiciel (SDK) de Speech](speech-sdk.md#get-the-samples)
