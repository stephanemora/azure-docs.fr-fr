---
title: 'Portail Azure : Déployer un serveur FHIR open source pour Azure - API Azure pour FHIR'
description: Ce guide de démarrage rapide explique comment déployer le serveur FHIR open source de Microsoft à l’aide du portail Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 57ab6bca820c4c25a9a56e4a801aa7d917d317ec
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90978606"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Démarrage rapide : Déployer le serveur FHIR open source à l’aide du portail Azure

Dans ce guide de démarrage rapide, vous allez apprendre à déployer un serveur FHIR open source sur Azure à l’aide du portail Azure. Nous allons utiliser des liens de déploiement simplifié dans le [dépôt open source](https://github.com/Microsoft/fhir-server)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="github-open-source-repository"></a>Dépôt open source GitHub

Accédez à la [page de déploiement de GitHub](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md), puis recherchez le bouton Déployer sur Azure :

>[!div class="mx-imgBorder"]
>![Page de déploiement open source](media/quickstart-oss-portal/deployment-page-oss.png)

Cliquez sur le bouton de déploiement, et le portail Azure s’ouvre.

## <a name="fill-in-deployment-parameters"></a>Indiquer les paramètres de déploiement

Choisissez de créer un groupe de ressources, puis donnez-lui un nom. Les seuls autres paramètres nécessaires sont un nom pour le service et le mot de passe de l’administrateur SQL.

>[!div class="mx-imgBorder"]
>![Paramètres du déploiement personnalisé](media/quickstart-oss-portal/deployment-custom-parameters.png)

Après avoir indiqué les détails nécessaires, vous pouvez démarrer le déploiement.

## <a name="validate-fhir-server-is-running"></a>Vérifier que le serveur FHIR est en cours d’exécution

Une fois le déploiement effectué, vous pouvez faire pointer votre navigateur vers `https://SERVICENAME.azurewebsites.net/metadata` pour obtenir une déclaration de capacité. Il faut environ une minute au serveur pour répondre la première fois.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources contenant les ressources provisionnées, sélectionnez **Supprimer le groupe de ressources**, puis confirmez le nom du groupe de ressources à supprimer.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez déployé le serveur FHIR open source pour Azure de Microsoft sur votre abonnement. Pour savoir comment accéder à l’API FHIR à l’aide de Postman, effectuez le didacticiel Postman.
 
>[!div class="nextstepaction"]
>[Accédez à l’API FHIR à l’aide de Postman](access-fhir-postman-tutorial.md)