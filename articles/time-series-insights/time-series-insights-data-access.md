---
title: Configurer la sécurité pour accéder à la préversion d’Azure Time Series Insights et la gérer | Microsoft Docs
description: Cet article explique comment configurer la sécurité et les autorisations sous forme de stratégies d’accès de gestion et d’accès aux données pour sécuriser Azure Time Series Insights en préversion.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.custom: seodec18
ms.openlocfilehash: 9aea7a9c9dd96bf30ebb3def9354df9e4bd30114
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558514"
---
# <a name="grant-data-access-to-an-environment"></a>Accorder l’accès aux données dans un environnement

Cet article décrit les deux types de stratégies d’accès d’Azure Time Series Insights en préversion.

## <a name="grant-data-access"></a>Accorder l’accès aux données

Effectuez les étapes suivantes pour accorder l’accès aux données à un utilisateur principal.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
1. Recherchez votre environnement Time Series Insights. Entrez `Time Series` dans la zone **Recherche**. Sélectionnez **Environnement Time Series** dans les résultats de recherche.
1. Sélectionnez votre environnement Time Series Insights dans la liste.
1. Sélectionnez **Stratégies d’accès aux données**, puis **+Ajouter**.

    ![Data-access-one][1]

1. Choisissez **Sélectionner un utilisateur**. Recherchez le nom d’utilisateur ou l’adresse e-mail pour accéder à l’utilisateur que vous voulez ajouter. Cliquez sur **Sélectionner** pour confirmer la sélection.

    ![Data-access-two][2]

1. Choisissez **Sélectionner un rôle**. Choisissez le rôle d’accès approprié pour l’utilisateur :

    * Sélectionnez **Contributeur** si vous voulez autoriser l’utilisateur à changer les données de référence, et à partager des requêtes et des perspectives enregistrées avec d’autres utilisateurs de l’environnement.

    * Sinon, sélectionnez **Lecteur** pour autoriser l’utilisateur à interroger les données dans l’environnement et à enregistrer des requêtes personnelles non partagées dans l’environnement.

   Sélectionnez **OK** pour confirmer le choix du rôle.

    ![Data-access-three][3]

1. Sélectionnez **OK** dans la page **Sélectionner un rôle utilisateur**.

    ![Data-access-four][4]

1. Confirmez que la page **Stratégies d’accès aux données** répertorie les utilisateurs, et les rôles pour chaque utilisateur.

    ![Data-access-five][5]

## <a name="provide-guest-access-to-a-user-from-another-azure-active-directory-tenant"></a>Fournir l’accès invité à un utilisateur issu d’un autre locataire Azure Active Directory

`Guest` ne correspond pas à un rôle de gestion. Il est utilisé pour désigner un compte qui a été invité par un autre locataire. Une fois que le compte invité est invité dans le répertoire du locataire, le même contrôle d’accès peut lui être appliqué comme pour tout autre compte. Vous pouvez accorder à l’administration l’accès à un environnement Time Series Insights en utilisant le Panneau Contrôle d’accès (IAM). Vous pouvez également autoriser l’accès aux données de l’environnement via le panneau Stratégies d’accès aux données. Pour plus d’informations sur l’accès invité Azure Active Directory (Azure AD), lisez [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le Portail Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Suivez ces étapes pour que l’accès invité à un environnement Time Series Insights soit accordé à un utilisateur Azure AD par un autre locataire.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
1. Recherchez votre environnement Time Series Insights. Entrez **Time Series** dans la zone **Recherche**. Sélectionnez **Environnement Time Series** dans les résultats de recherche.
1. Sélectionnez votre environnement Time Series Insights dans la liste.
1. Sélectionnez **Stratégies d’accès aux données**, puis **+Inviter**.

    ![Data-access-six][6]

1. Entrez l’adresse de messagerie de l’utilisateur que vous souhaitez inviter. Cette adresse e-mail doit être associée à Azure AD. Vous pouvez inclure un message personnel dans l’invitation.

    ![Data-access-seven][7]

1. Recherchez la bulle de confirmation qui s’affiche sur l’écran.

    ![Data-access-eight][8]

1. Choisissez **Sélectionner un utilisateur**. Pour ajouter l’utilisateur que vous avez invité, recherchez son adresse e-mail. Cliquez sur **Sélectionner** pour confirmer la sélection.

    ![Data-access-nine][9]

1. Choisissez **Sélectionner un rôle**. Choisissez le rôle d’accès approprié pour l’utilisateur invité :

    * Sélectionnez **Contributeur** si vous voulez autoriser l’utilisateur à changer les données de référence, et à partager des requêtes et des perspectives enregistrées avec d’autres utilisateurs de l’environnement.

    * Sinon, sélectionnez **Lecteur** pour autoriser l’utilisateur à interroger les données dans l’environnement et à enregistrer des requêtes personnelles non partagées dans l’environnement.

   Sélectionnez **OK** pour confirmer le choix du rôle.

    ![Data-access-ten][10]

1. Sélectionnez **OK** dans la page **Sélectionner un rôle utilisateur**.

1. Confirmez que la page **Stratégies d’accès aux données** liste l’utilisateur invité et les rôles pour chaque utilisateur invité.

    ![Data-access-eleven][11]

1. Maintenant, l’utilisateur invité doit suivre les étapes pour accéder à l’environnement du locataire Azure dans lequel vous l’avez invité. Tout d’abord, il doit accepter l’invitation que vous lui avez envoyé. Cette invitation est envoyée par e-mail à l’adresse que vous avez utilisée à l’étape 5. Ils sélectionnent **Commencer** pour accepter.

    ![Data-access-twelve][12]

1. Ensuite, l’utilisateur invité accepte les autorisations associées à l’organisation de l’administrateur.

    ![Data-access-thirteen][13]

1. Une fois que l’utilisateur invité est connecté à l’adresse e-mail que vous avez utilisée pour l’inviter et qu’il accepte l’invitation, il se rend sur insights.azure.com. Une fois sur ce site, il sélectionne l’avatar à côté de l’adresse e-mail, dans le coin supérieur droit de l’écran.

    ![Data-access-fourteen][14]

1. Ensuite, l’utilisateur invité sélectionne votre locataire Azure dans le menu déroulant de l’annuaire. C’est à ce locataire que vous avez invité l’utilisateur.

    ![Data-access-fifteen][15]

Lorsque l’utilisateur invité a sélectionne votre locataire, il voit l’environnement Time Series Insights auquel vous lui avez donné accès. Il dispose désormais de toutes les fonctionnalités associées au rôle que vous lui avez fourni à l’étape 8.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez le [Guide pratique pour ajouter une source d’événement Azure Event Hub](./time-series-insights-how-to-add-an-event-source-eventhub.md) à votre environnement Time Series Insights.
* Envoyez [des événements à la source d’événement](./time-series-insights-send-events.md).
* Accédez à [votre environnement dans l’explorateur Time Series Insights en préversion](./time-series-insights-update-explorer.md).

<!-- Images -->
[1]: media/data-access/data-access-one.png
[2]: media/data-access/data-access-two.png
[3]: media/data-access/data-access-three.png
[4]: media/data-access/data-access-four.png
[5]: media/data-access/data-access-five.png
[6]: media/data-access/data-access-six.png
[7]: media/data-access/data-access-seven.png
[8]: media/data-access/data-access-eight.png
[9]: media/data-access/data-access-nine.png
[10]: media/data-access/data-access-ten.png
[11]: media/data-access/data-access-eleven.png
[12]: media/data-access/data-access-twelve.png
[13]: media/data-access/data-access-thirteen.png
[14]: media/data-access/data-access-fourteen.png
[15]: media/data-access/data-access-fifteen.png