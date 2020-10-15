---
title: Contrôle d’accès aux données pour la surveillance à distance - Azure | Microsoft Docs
description: Cet article fournit des informations sur la façon dont vous pouvez configurer les contrôles d’accès pour l’explorateur de données de télémétrie Time Series Insights dans l’accélérateur de solution de supervision à distance
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "65827198"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Configurer des contrôles d’accès pour l’explorateur de données de télémétrie Time Series Insights

Cet article fournit des informations sur la façon dont vous pouvez configurer les contrôles d’accès pour l’explorateur Time Series Insights dans l’accélérateur de solution de supervision à distance. Pour permettre aux utilisateurs de l’accélérateur de solution d’accéder à l’explorateur Time Series Insights, vous devez accorder à chacun d’entre eux l’accès aux données.

Les stratégies d’accès aux données accordent des autorisations pour générer des requêtes de données, manipuler des données de référence dans l’environnement, et des requêtes partagées enregistrées et des perspectives associées à l’environnement.

## <a name="grant-data-access"></a>Accorder l’accès aux données

Effectuez les étapes suivantes pour accorder l’accès aux données à un utilisateur principal :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Recherchez votre environnement Time Series Insights. Tapez **Time Series** dans la zone de **recherche**. Sélectionnez **Environnement Time Series** dans les résultats de recherche. 

3. Sélectionnez votre environnement Time Series Insights dans la liste.

4. Sélectionnez **Stratégies d’accès aux données**, puis **+Ajouter**.
    ![Gérer la source Time Series Insights - Environnement](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Sélectionnez **Sélectionner un utilisateur**.  Recherchez le nom d’utilisateur ou l’adresse e-mail pour accéder à l’utilisateur que vous voulez ajouter. Cliquez sur **Sélectionner** pour confirmer la sélection. 

    ![Gérer la source Time Series Insights - Ajouter](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Sélectionnez **Sélectionner un rôle**. Choisissez le rôle d’accès approprié pour l’utilisateur :
   - Sélectionnez **Contributeur** si vous voulez autoriser l’utilisateur à changer les données de référence et à partager des requêtes et des perspectives enregistrées avec d’autres utilisateurs de l’environnement. 
   - Sinon, sélectionnez **Lecteur** pour autoriser l’utilisateur à interroger les données dans l’environnement et à enregistrer des requêtes personnelles (non partagées) dans l’environnement.

     Sélectionnez **OK** pour confirmer le choix du rôle.

     ![Gérer la source Time Series Insights - Sélectionner un utilisateur](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Sélectionnez **OK** dans la page **Sélectionner un rôle utilisateur**.

    ![Gérer la source Time Series Insights - Sélectionner un rôle](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. La page **Stratégies d’accès aux données** répertorie les utilisateurs, et le ou les rôles pour chaque utilisateur.

    ![Gérer la source Time Series Insights - Résultats](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment sont accordés des contrôles d’accès pour l’explorateur Time Series Insights dans l’accélérateur de solution de supervision à distance.

Pour plus d’informations conceptuelles sur l’accélérateur de solution de supervision à distance, consultez [Architecture de la supervision à distance](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Pour plus d’informations sur la personnalisation de la solution de supervision à distance, consultez [Personnaliser et redéployer un microservice](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->