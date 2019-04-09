---
title: Déclenchement d’Azure Functions à l’aide de webhooks dans Azure IoT Central
description: Créez une application de fonction qui s’exécute à chaque fois qu’une règle est déclenchée dans Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 03/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0d92e9bdf8ec207e5ef0e3f891c162182b5a4fff
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264843"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Déclenchement d’Azure Functions à l’aide de webhooks dans Azure IoT Central

*Cette rubrique s’applique aux générateurs et aux administrateurs.*

Utilisez Azure Functions pour exécuter du code serverless sur la sortie de webhook à partir de règles IoT Central. Vous n’êtes pas obligé d’approvisionner une machine virtuelle ou publier une application web pour utiliser Azure Functions, mais au lieu de cela, vous pouvez exécuter ce code sans serveur. Utilisez Azure Functions pour transformer la charge utile du webhook avant de l’envoyer vers sa destination finale, par exemple une base de données SQL ou le service Event Grid.

## <a name="prerequisites"></a>Conditions préalables

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="how-to-connect-azure-functions"></a>Comment connecter Azure Functions

1. [Créer une nouvelle application de fonction dans le portail Azure](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Créer une nouvelle application de fonction dans le portail Azure](media/howto-trigger-azure-functions/createfunction.png)

2. Développez votre function app et sélectionnez le **+ bouton** en regard de fonctions. Si cette fonction est la première condition dans votre application de fonction, sélectionnez **dans portail** comme environnement de développement, puis sélectionnez **continuer**.

    ![Choisir la fonction personnalisée dans une application de fonction](media/howto-trigger-azure-functions/customfunction.png)

3. Choisissez **Webhook + API** modèle et sélectionnez **créer**. Cette rubrique utilise la fonction Azure avec .NET.

    ![Sélectionner un déclencheur de webhook générique](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. Dans votre nouvelle fonction, sélectionnez **<> / obtenir l’URL de fonction**, puis copiez et enregistrez la valeur. Vous utilisez cette valeur pour configurer le webhook.

    ![Obtenir l’URL de la fonction](media/howto-trigger-azure-functions/getfunctionurl.png)

4. Dans IoT Central, accédez à la règle que vous souhaitez connecter à votre application de fonction.

5. Ajoutez une action webhook. Entrez un **Nom d’affichage** et collez l’URL de fonction que vous avez copiée précédemment dans **URL de rappel**.

    ![Entrer l’URL de fonction dans le champ URL de rappel](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Enregistrez la règle. Lorsque la règle est déclenchée, le webhook appelle à présent l’application de fonction à exécuter. Dans votre function app, vous pouvez sélectionner **moniteur** pour afficher l’historique d’appel de la fonction. Vous pouvez utiliser App Insights ou la vue classique pour examiner l’historique.

    ![Analyser l’historique d’appels de la fonction](media/howto-trigger-azure-functions/monitorfunction.PNG)

Pour plus d’informations, consultez l’article Azure Functions sur la [création d’une fonction déclenchée par un webhook générique](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris à configurer et utiliser des webhooks, nous vous suggérons de découvrir comment [créer des workflows dans Microsoft Flow](howto-add-microsoft-flow.md).
