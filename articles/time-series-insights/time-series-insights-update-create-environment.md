---
title: Tutoriel Azure Time Series Insights (préversion) | Microsoft Docs
description: Découvrez Azure Time Series Insights (préversion)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/26/2018
ms.openlocfilehash: ed25d03f7c592476b9284790ac12f9954661a42b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872303"
---
# <a name="azure-time-series-insights-preview-tutorial"></a>Tutoriel Azure Time Series Insights (préversion)

Ce tutoriel vous guide tout au long du processus de création d’un environnement Azure Time Series Insights (préversion) contenant des données issues d’appareils simulés. Ce tutoriel vous montre comment effectuer les opérations suivantes :

* Créer un environnement Time Series Insights (préversion).
* Connecter l’environnement Time Series Insights (préversion) à un hub d’événements.
* Simuler un parc éolien pour transmettre des données en continu à l’environnement Time Series Insights (préversion).
* Effectuer une analyse de base des données.
* Définir un type et une hiérarchie de modèle Time Series, et les associer à vos instances.

## <a name="create-a-time-series-insights-preview-environment"></a>Créer un environnement Time Series Insights (préversion)

Cette section décrit comment créer un environnement Azure Time Series Insights (préversion) à l’aide du [portail Azure](https://portal.azure.com/).

1. Connectez-vous au portail Azure à l’aide de votre compte d’abonnement.
1. Cliquez sur **+ Créer une ressource** dans le coin supérieur gauche.
1. Sélectionnez la catégorie **Internet des objets**, puis **Time Series Insights**.

  ![tutorial-one][1]

1. Dans la page Environnement Time Series Insights, définissez les paramètres nécessaires et cliquez sur **Suivant : Source des événements**.

  ![tutorial-two][2]

1. Dans la page  **Source des événements**, définissez les paramètres nécessaires, puis cliquez sur **Vérifier + Créer**.

  ![tutorial-three][3]

1. Vérifiez tous les détails et cliquez sur **Créer** pour démarrer le provisionnement de votre environnement.

  ![tutorial-four][4]

1. Vous recevez une notification quand le déploiement est terminé.

  ![tutorial-five][5]

## <a name="send-events-to-your-tsi-environment"></a>Envoyer des événements à votre environnement Time Series Insights

Dans cette section, vous utilisez un simulateur d’éoliennes pour envoyer des événements à votre environnement Time Series Insights via un hub d’événements.

  1. Dans le portail Azure, accédez à votre ressource de hub d’événements et connectez-vous à votre environnement Time Series Insights. Découvrez [comment connecter votre ressource à un hub d’événements existant](./time-series-insights-how-to-add-an-event-source-eventhub.md).

  1. Dans la page de la ressource de hub d’événements, accédez à **Stratégies d’accès partagé**, puis à **RootManageSharedAccessKey**. Copiez la valeur **Clé primaire de la chaîne de connexion**  affichée ici.

      ![tutorial-six][6]

  1. Accédez à [https://tsiclientsample.azurewebsites.net/windFarmGen.html]( https://tsiclientsample.azurewebsites.net/windFarmGen.html). Cette application web simule des éoliennes.
  1. Collez la chaîne de connexion copiée à l’étape 3 dans **Chaîne de connexion du hub d'événements**

      ![tutorial-seven][7]

  1. Cliquez sur **Cliquer pour démarrer** afin d’envoyer (push) les événements à votre hub d’événements. À ce stade, un fichier nommé `instances.json` est téléchargé sur votre ordinateur. Enregistrez ce fichier, car vous en aurez besoin plus tard.

  1. Revenez à votre hub d’événements. Vous devez maintenant voir les nouveaux événements reçus par le hub.

     ![tutorial-eight][8]

## <a name="analyze-data-in-your-environment"></a>Analyser les données dans votre environnement

Dans cette section, vous effectuez l’analytique de base de vos données de série chronologique à l’aide de l’explorateur de mise à jour Time Series Insights.

  1. Accédez à l’explorateur de mise à jour Time Series Insights en cliquant sur l’URL dans la page des ressources sur le portail Azure.

      ![tutorial-nine][9]

  1. Dans l’explorateur, cliquez sur les nœuds **Instances non apparentées** pour afficher toutes les instances Time Series dans l’environnement.

     ![tutorial-ten][10]

  1. Dans ce tutoriel, nous allons analyser les données qui ont été envoyées u cours de la journée précédente. Pour cela, cliquez sur **Durées courtes** et sélectionnez l’option **Dernières 24 heures**.

     ![tutorial-eleven][11]

  1. Sélectionnez **Sensor_0** et choisissez **Show Avg Value** (Afficher la valeur moyenne) pour visualiser les données provenant de cette instance de série chronologique.

     ![tutorial-twelve][12]

  1. De la même façon, vous pouvez tracer des données d’autres instances de série chronologique pour effectuer l’analytique de base.

     ![tutorial-thirteen][13]

## <a name="define-a-type--hierarchy"></a>Définir un type et une hiérarchie 

Dans cette section, vous créez un type et une hiérarchie, puis vous les associez à vos instances de série chronologique. Découvrez-en plus sur les [modèles Time Series](./time-series-insights-update-tsm.md).

  1. Dans l’explorateur, cliquez sur l’onglet **Modèle** dans la barre de l’application.

     ![tutorial-fourteen][14]

  1. Dans la section Types, cliquez sur **+ Ajouter**. Vous pouvez ici créer un type de modèle Time Series.

     ![tutorial-fifteen][15]

  1. Dans l’éditeur de type, entrez un **Nom** et une **Description**, et créez des variables pour les valeurs **Moyenne**, **Min** et **Max** comme indiqué ci-dessous. Cliquez sur **Créer** pour enregistrer le type.

     ![tutorial-sixteen][16]

     ![tutorial-seventeen][17]

  1. Dans la section **Hiérarchies**, cliquez sur **+ Ajouter**. Vous pouvez ici créer une hiérarchie de modèle Time Series.

     ![tutorial-eighteen][18]

  1. Dans l’éditeur de hiérarchie, entrez un **Nom** et ajoutez des niveaux de hiérarchie comme indiqué ci-dessous. Cliquez sur **Créer** pour enregistrer la hiérarchie.

     ![tutorial-nineteen][19]

     ![tutorial-twenty][20]

  1. Dans la section **Instances**, sélectionnez une instance et cliquez sur **Modifier**. Vous pouvez ici associer un type et une hiérarchie à cette instance.

     ![tutorial-twenty-one][21]

  1. Dans l’éditeur d’instance, choisissez le type et la hiérarchie que vous avez définis aux étapes 3 et 5 ci-dessus.

     ![tutorial-twenty-two][22]

  1. Vous pouvez aussi faire cette opération pour toutes les instances en même temps en modifiant le fichier `instances.json` téléchargé précédemment. Dans ce fichier, remplacez les valeurs des champs **typeId** et **hierarchyId** par les ID obtenus aux étapes 3 et 5 ci-dessus.

  1. Dans la section **Instances**, cliquez sur **Charger JSON** et chargez le fichier `instances.json` modifié comme indiqué ci-dessous.

     ![tutorial-twenty-three][23]

  1. Accédez à l’onglet **Analytique** et actualisez votre navigateur. Vous devez maintenant voir toutes les instances qui sont associées au type et à la hiérarchie définis ci-dessus.

     ![tutorial-twenty-four][24]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :  

* Créer un environnement Time Series Insights (préversion).
* Connecter l’environnement Time Series Insights (préversion) à un hub d’événements.
* Simuler un parc éolien pour transmettre des données en continu à l’environnement Time Series Insights (préversion).
* Effectuer une analyse de base des données.
* Définir un type et une hiérarchie de modèle Time Series, et les associer à vos instances.

Maintenant que vous savez comment créer votre propre environnement de mise à jour Time Series Insights, découvrez plus en détail les concepts clés utilisés dans Time Series Insights.

En savoir plus sur la configuration du stockage Time Series Insights :

> [!div class="nextstepaction"]
> [Entrée et stockage de données Azure Time Series Insights (préversion)](./time-series-insights-update-storage-ingress.md)

En savoir plus sur les modèles Time Series :

> [!div class="nextstepaction"]
> [Modèles de données Azure Time Series Insights (préversion)](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/tutorial-one.png
[2]: media/v2-update-provision/tutorial-two.png
[3]: media/v2-update-provision/tutorial-three.png
[4]: media/v2-update-provision/tutorial-four.png
[5]: media/v2-update-provision/tutorial-five.png
[6]: media/v2-update-provision/tutorial-six.png
[7]: media/v2-update-provision/tutorial-seven.png
[8]: media/v2-update-provision/tutorial-eight.png
[9]: media/v2-update-provision/tutorial-nine.png
[10]: media/v2-update-provision/tutorial-ten.png
[11]: media/v2-update-provision/tutorial-eleven.png
[12]: media/v2-update-provision/tutorial-twelve.png
[13]: media/v2-update-provision/tutorial-thirteen.png
[14]: media/v2-update-provision/tutorial-fourteen.png
[15]: media/v2-update-provision/tutorial-fifteen.png
[16]: media/v2-update-provision/tutorial-sixteen.png
[17]: media/v2-update-provision/tutorial-seventeen.png
[18]: media/v2-update-provision/tutorial-eighteen.png
[19]: media/v2-update-provision/tutorial-nineteen.png
[20]: media/v2-update-provision/tutorial-twenty.png
[21]: media/v2-update-provision/tutorial-twenty-one.png
[22]: media/v2-update-provision/tutorial-twenty-two.png
[23]: media/v2-update-provision/tutorial-twenty-three.png
[24]: media/v2-update-provision/tutorial-twenty-four.png