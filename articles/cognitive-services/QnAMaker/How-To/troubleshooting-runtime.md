---
title: Résolution des problèmes - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnAMaker comprend des composants hébergés dans le compte d’utilisateur Azure. Pour les besoins du débogage, les utilisateurs peuvent avoir à manipuler leurs ressources Azure QnAMaker ou à fournir à l’équipe du support technique QnAMaker des informations supplémentaires sur leur installation.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 94a3dfd9a3560321d7e2753ccd385fb1a5323107
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559923"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Conseils de dépannage pour prendre en charge le service et le runtime QnA Maker
QnAMaker comprend des composants hébergés dans le compte d’utilisateur Azure. Pour les besoins du débogage, les utilisateurs peuvent avoir à manipuler leurs ressources Azure QnAMaker ou à fournir à l’équipe du support technique QnAMaker des informations supplémentaires sur leur installation.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Comment obtenir les dernières mises à jour du runtime QnAMaker
Le runtime QnAMaker fait partie d’Azure App Service, qui est déployé quand vous [créez un service QnAMaker](./set-up-qnamaker-service-azure.md) dans le portail Azure. Des mises à jour du runtime sont effectuées régulièrement. QnA Maker App Service est en mode de mise à jour automatique, publication de l’extension de site d’avril 2019 (version 5+). Ce service a déjà été conçu pour éviter le moindre temps d’arrêt pendant les mises à niveau. Vous pouvez vérifier votre version actuelle à l’adresse https://www.qnamaker.ai/UserSettings. Si votre version est antérieure à la version 5.x, vous devez redémarrer l’App Service pour appliquer les dernières mises à jour apportées à votre installation QnAMaker.

1. Accédez à votre service QnAMaker (groupe de ressources) dans le [portail Azure](https://portal.azure.com).

    ![Groupe de ressources Azure QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Cliquez sur l’App Service et ouvrez la section Vue d’ensemble.

     ![App Service QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Redémarrez l'App Service. Cette opération ne prend que quelques secondes normalement. Notez que la build de vos applications/bots en aval sur ce service QnAMaker n’est pas disponible pour les utilisateurs finaux durant le redémarrage.

    ![Redémarrage de l'App Service QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Comment obtenir le nom d’hôte du service QnAMaker
Le nom d’hôte du service QnAMaker est utile pour le débogage quand vous contactez le support technique de QnAMaker ou UserVoice. Le nom d’hôte est une URL de cette forme : https:// *{nom_hôte}* .azurewebsites.net.
    
1. Accédez à votre service QnAMaker (groupe de ressources) dans le [portail Azure](https://portal.azure.com).

    ![Groupe de ressources Azure QnAMaker sur le portail Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Cliquez sur l'App Service.

     ![Sélectionner l’App Service QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. L’URL de nom d’hôte est indiquée dans la section Vue d’ensemble.

    ![Nom d’hôte QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Améliorer les questions de la base de connaissances avec l'apprentissage actif](./improve-knowledge-base.md)
