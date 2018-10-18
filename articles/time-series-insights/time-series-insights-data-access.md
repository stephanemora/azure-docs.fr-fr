---
title: Configurer la sécurité pour accéder et gérer Azure Time Series Insights | Microsoft Docs
description: Cet article explique comment configurer la sécurité et les autorisations sous forme de stratégies d’accès de gestion et d’accès aux données pour sécuriser Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: c9bddf8e20524433b31793e277efd954a5d1320e
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423372"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Accorder l’accès aux données d’un environnement Time Series Insights à l’aide du portail Azure

Cet article décrit les deux types de stratégies d’accès de Time Series Insights.

## <a name="video"></a>Vidéo : 

### <a name="in-this-video-we-cover-creating-and-managing-access-policies-within-time-series-insights-br"></a>Dans cette vidéo, nous abordons la création et la gestion des stratégies d’accès dans Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/5zTIdyHMJW8]

Les environnements Time Series Insights comprennent deux types indépendants des stratégies d’accès :

* Stratégies d’accès de gestion
* Stratégies d’accès aux données

Ces deux stratégies accordent aux principaux Azure Active Directory (utilisateurs et applications) diverses autorisations sur un environnement spécifique. Les principaux (utilisateurs et applications) doivent appartenir à l’annuaire Active Directory (ou « locataire Azure ») associé à l’abonnement contenant l’environnement.

Les stratégies d’accès de gestion accordent des autorisations liées à la configuration de l’environnement, telles que la
*   création et la suppression de l’environnement, des sources d’événements, des ensembles de données de référence et la
*   gestion des stratégies d’accès aux données.

Les stratégies d’accès aux données accordent des autorisations pour générer des requêtes de données, manipuler des données de référence dans l’environnement, et des requêtes partagées enregistrées et des perspectives associées à l’environnement.

Les deux types de stratégies permettent de distinguer clairement l’accès à la gestion de l’environnement et l’accès aux données contenues dans l’environnement. Par exemple, vous pouvez configurer un environnement de sorte que le propriétaire/créateur de l’environnement soit supprimé de l’accès aux données. Par ailleurs, les utilisateurs et services qui sont autorisés à lire des données de l’environnement peuvent ne pas avoir accès à la configuration de l’environnement.

## <a name="grant-data-access"></a>Accorder l’accès aux données
Effectuez les étapes suivantes pour accorder l’accès aux données à un utilisateur principal :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Recherchez votre environnement Time Series Insights. Tapez **Time Series** dans la zone de **recherche**. Sélectionnez **Environnement Time Series** dans les résultats de recherche. 

3. Sélectionnez votre environnement Time Series Insights dans la liste.
   
4. Sélectionnez **Stratégies d’accès aux données**, puis **+Ajouter**.
  ![Gérer la source Time Series Insights - Environnement](media/data-access/getstarted-grant-data-access1.png)

5. Sélectionnez **Sélectionner un utilisateur**.  Recherchez le nom d’utilisateur ou l’adresse e-mail pour accéder à l’utilisateur que vous voulez ajouter. Cliquez sur **Sélectionner** pour confirmer la sélection. 

   ![Gérer la source Time Series Insights - Ajouter](media/data-access/getstarted-grant-data-access2.png)

6. Sélectionnez **Sélectionner un rôle**. Choisissez le rôle d’accès approprié pour l’utilisateur :
   - Sélectionnez **Contributeur** si vous voulez autoriser l’utilisateur à changer les données de référence et à partager des requêtes et des perspectives enregistrées avec d’autres utilisateurs de l’environnement. 
   - Sinon, sélectionnez **Lecteur** pour autoriser l’utilisateur à interroger les données dans l’environnement et à enregistrer des requêtes personnelles (non partagées) dans l’environnement.

   Sélectionnez **OK** pour confirmer le choix du rôle.

   ![Gérer la source Time Series Insights - Sélectionner un utilisateur](media/data-access/getstarted-grant-data-access3.png)

8. Sélectionnez **OK** dans la page **Sélectionner un rôle utilisateur**.

   ![Gérer la source Time Series Insights - Sélectionner un rôle](media/data-access/getstarted-grant-data-access4.png)

9. La page **Stratégies d’accès aux données** répertorie les utilisateurs, et le ou les rôles pour chaque utilisateur.

   ![Gérer la source Time Series Insights - Résultats](media/data-access/getstarted-grant-data-access5.png)

## <a name="provide-guest-access-to-a-user-from-another-aad-tenant"></a>Fournir l’accès invité à un utilisateur issu d’un autre locataire AAD

Le terme « invité » ne correspond pas à un rôle de gestion. Il est utilisé pour désigner un compte qui a été invité par un autre locataire. Une fois que le compte a été invité dans l’annuaire du locataire, le même contrôle d’accès peut lui être appliqué, comme à tout autre compte, soit pour accorder l’accès de gestion dans un environnement TSI à l’aide du panneau Contrôle d’accès (IAM), soit pour accorder l’accès aux données de l’environnement via le panneau Stratégies d’accès aux données. Pour plus d’informations sur l’accès invité des locataires AAD, consultez [ce document](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Suivez ces étapes pour que l’accès invité à un environnement Time Series Insights soit accordé à un utilisateur AAD par un autre locataire :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Recherchez votre environnement Time Series Insights. Tapez **Time Series** dans la zone de **recherche**. Sélectionnez **Environnement Time Series** dans les résultats de recherche.

3. Sélectionnez votre environnement Time Series Insights dans la liste.

4. Sélectionnez **Stratégies d’accès aux données**, puis **+Inviter**.

    ![Gérer la source Time Series Insights - Inviter un utilisateur](media/data-access/getstarted-grant-data-access6.png)

5. Fournissez l’adresse e-mail de l’utilisateur que vous souhaitez inviter. Notez que cette adresse e-mail doit être associée à AAD. Vous pouvez inclure un message personnel dans l’invitation.

    ![Gérer la source Time Series Insights - Sélectionner un utilisateur](media/data-access/getstarted-grant-data-access7.png)

6. Une bulle de confirmation doit s’afficher à l’écran.

    ![Gérer la source Time Series Insights - Confirmer un utilisateur](media/data-access/getstarted-grant-data-access8.png)

7. Sélectionnez **Sélectionner un utilisateur**. Pour ajouter l’utilisateur que vous avez invité, recherchez son adresse e-mail. Cliquez sur **Sélectionner** pour confirmer la sélection.
  
    ![Gérer la source Time Series Insights - Confirmer un utilisateur](media/data-access/getstarted-grant-data-access9.png)

8. Sélectionnez **Sélectionner un rôle**. Choisissez le rôle d’accès approprié pour l’utilisateur invité :

    * Sélectionnez **Contributeur** si vous voulez autoriser l’utilisateur à changer les données de référence, et à partager des requêtes et des perspectives enregistrées avec d’autres utilisateurs de l’environnement.

    * Sinon, sélectionnez **Lecteur** pour autoriser l’utilisateur à interroger les données dans l’environnement et à enregistrer des requêtes personnelles (non partagées) dans l’environnement.

    Sélectionnez **OK** pour confirmer le choix du rôle.

    ![Gérer la source Time Series Insights - Sélectionner un rôle](media/data-access/getstarted-grant-data-access10.png)

9. Sélectionnez **OK** dans la page **Sélectionner un rôle utilisateur**.

10. La page **Stratégies d’accès aux données** liste les utilisateurs invités et leurs rôles.

    ![Gérer la source Time Series Insights - Confirmer un rôle](media/data-access/getstarted-grant-data-access11.png)

11. Maintenant, l’utilisateur invité doit effectuer certaines étapes pour accéder à l’environnement du locataire Azure dans lequel vous l’avez invité. Tout d’abord, il doit accepter l’invitation que vous venez de lui envoyer. Cette invitation est envoyée par e-mail à l’adresse que vous avez ajoutée à l’invitation à l’étape 5. Pour l’accepter, il doit cliquer sur « Commencer ».

    ![Gérer la source Time Series Insights - Inviter un utilisateur](media/data-access/getstarted-grant-data-access12.png)

12. Ensuite, l’utilisateur invité doit accepter les autorisations associées à l’organisation de l’administrateur.

    ![Gérer la source Time Series Insights - Accepter des autorisations](media/data-access/getstarted-grant-data-access13.png)

13. Lorsque l’utilisateur invité est connecté à l’adresse e-mail utilisée pour l’invitation, et lorsqu’il accepte celle-ci, il est dirigé vers insights.azure.com. Une fois sur ce site, il doit cliquer sur l’avatar à côté de l’e-mail, dans le coin supérieur droit de l’écran. 

    ![Gérer la source Time Series Insights - Accepter des autorisations](media/data-access/getstarted-grant-data-access14.png)

14. Ensuite, l’utilisateur invité doit sélectionner votre locataire Azure dans le menu déroulant de l’annuaire. Il s’agit du locataire pour lequel il a été invité. 

    ![Gérer la source Time Series Insights - Accepter des autorisations](media/data-access/getstarted-grant-data-access15.png)

15. Enfin, lorsque l’utilisateur invité sélectionne votre locataire, il voit l’environnement Time Series Insights auquel vous lui avez donné accès. Il doit désormais disposer de toutes les fonctionnalités associées au rôle que vous lui avez fourni à l’étape 8.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez le [Guide pratique pour ajouter une source d’événement Event Hub à votre environnement Azure Time Series Insights](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Envoyer des événements](time-series-insights-send-events.md) à la source d’événement.
* Affichez votre environnement dans [l’explorateur Time Series Insights](https://insights.timeseries.azure.com).
