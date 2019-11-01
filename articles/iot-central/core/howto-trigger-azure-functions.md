---
title: Déclenchement d’Azure Functions à l’aide de webhooks dans Azure IoT Central
description: Créez une application de fonction qui s’exécute à chaque fois qu’une règle est déclenchée dans Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a9590e5554956418859a07b43fb57724783343fe
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72942302"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Déclenchement d’Azure Functions à l’aide de webhooks dans Azure IoT Central

*Cette rubrique s’applique aux créateurs et aux administrateurs.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Utilisez Azure Functions pour exécuter du code serverless sur la sortie de webhook à partir de règles IoT Central. Vous ne devez pas provisionner une machine virtuelle ni publier une application web pour utiliser Azure Functions, mais au lieu de cela, vous pouvez exécuter ce code en mode serverless. Utilisez Azure Functions pour transformer la charge utile du webhook avant de l’envoyer vers sa destination finale, par exemple une base de données SQL ou le service Event Grid.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="how-to-connect-azure-functions"></a>Comment connecter Azure Functions

1. [Créez une application de fonction dans le portail Azure](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Créer une application de fonction dans le portail Azure](media/howto-trigger-azure-functions/createfunction.png)

2. Développez votre application de fonction, puis sélectionnez le **bouton +** en regard de Fonctions. Si cette fonction est la première de votre application de fonction, sélectionnez **Dans le portail** comme environnement de développement, puis sélectionnez **Continuer**.

    ![Choisir la fonction personnalisée dans une application de fonction](media/howto-trigger-azure-functions/customfunction.png)

3. Choisissez le modèle **Webhook + API**, puis sélectionnez **Créer**. Cette rubrique utilise une fonction Azure basée sur .NET.

    ![Sélectionner un déclencheur de webhook générique](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. Dans votre nouvelle fonction, sélectionnez **</> Obtenir l’URL de fonction**, puis copiez et enregistrez la valeur. Vous utilisez cette valeur pour configurer le webhook.

    ![Obtenir l’URL de la fonction](media/howto-trigger-azure-functions/getfunctionurl.png)

4. Dans IoT Central, accédez à la règle que vous souhaitez connecter à votre application de fonction.

5. Ajoutez une action webhook. Entrez un **Nom d’affichage** et collez l’URL de fonction que vous avez copiée précédemment dans **URL de rappel**.

    ![Entrer l’URL de fonction dans le champ URL de rappel](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Enregistrez la règle. À présent, quand la règle est déclenchée, le webhook demande l’exécution de l’application de fonction. Dans votre application de fonction, vous pouvez sélectionner **Superviser** pour voir l’historique des appels de la fonction. Vous pouvez utiliser App Insights ou la vue classique pour examiner l’historique.

    ![Analyser l’historique d’appels de la fonction](media/howto-trigger-azure-functions/monitorfunction.PNG)

Pour plus d’informations, consultez l’article Azure Functions sur la [création d’une fonction déclenchée par un webhook générique](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris à configurer et utiliser des webhooks, nous vous suggérons de découvrir comment [créer des workflows dans Microsoft Flow](howto-add-microsoft-flow.md).
