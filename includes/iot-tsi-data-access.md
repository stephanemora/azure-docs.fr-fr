---
title: Fichier Include
description: Fichier Include
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: include
ms.date: 08/20/2018
ms.author: anshan
ms.custom: include file
ms.openlocfilehash: fb45ea02f365cf4e7b394e249f9b91a784e5469f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46369939"
---
## <a name="grant-data-access"></a>Accorder l’accès aux données

Effectuez les étapes suivantes pour accorder l’accès aux données à un utilisateur principal :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Recherchez votre environnement Time Series Insights. Tapez **Time Series** dans la zone de **recherche**. Sélectionnez **Environnement Time Series** dans les résultats de recherche. 

3. Sélectionnez votre environnement Time Series Insights dans la liste.

4. Sélectionnez **Stratégies d’accès aux données**, puis **+Ajouter**.
    ![Gérer la source Time Series Insights - Environnement](media/iot-tsi-data-access/getstarted-grant-data-access1.png)

5. Sélectionnez **Sélectionner un utilisateur**.  Recherchez le nom d’utilisateur ou l’adresse e-mail pour accéder à l’utilisateur que vous voulez ajouter. Cliquez sur **Sélectionner** pour confirmer la sélection. 

    ![Gérer la source Time Series Insights - Ajouter](media/iot-tsi-data-access/getstarted-grant-data-access2.png)

6. Sélectionnez **Sélectionner un rôle**. Choisissez le rôle d’accès approprié pour l’utilisateur :
    - Sélectionnez **Contributeur** si vous voulez autoriser l’utilisateur à changer les données de référence et à partager des requêtes et des perspectives enregistrées avec d’autres utilisateurs de l’environnement. 
    - Sinon, sélectionnez **Lecteur** pour autoriser l’utilisateur à interroger les données dans l’environnement et à enregistrer des requêtes personnelles (non partagées) dans l’environnement.

    Sélectionnez **OK** pour confirmer le choix du rôle.

    ![Gérer la source Time Series Insights - Sélectionner un utilisateur](media/iot-tsi-data-access/getstarted-grant-data-access3.png)

7. Sélectionnez **OK** dans la page **Sélectionner un rôle utilisateur**.

    ![Gérer la source Time Series Insights - Sélectionner un rôle](media/iot-tsi-data-access/getstarted-grant-data-access4.png)

8. La page **Stratégies d’accès aux données** répertorie les utilisateurs, et le ou les rôles pour chaque utilisateur.

    ![Gérer la source Time Series Insights - Résultats](media/iot-tsi-data-access/getstarted-grant-data-access5.png)