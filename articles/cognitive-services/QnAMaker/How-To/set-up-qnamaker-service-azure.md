---
title: Configurer un service QnA Maker - QnA Maker
titleSuffix: Azure Cognitive Services
description: Avant de pouvoir créer des bases de connaissances QnA Maker, vous devez tout d’abord configurer un service QnA Maker dans Azure. Toute personne disposant d’autorisations pour créer des ressources dans un abonnement peut configurer un service QnA Maker.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 103da0c65bcf9b9bd24130082f844b9bd12a9a02
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389715"
---
# <a name="create-a-qna-maker-service"></a>Créer un service QnA Maker

Avant de pouvoir créer des bases de connaissances QnA Maker, vous devez tout d’abord configurer un service QnA Maker dans Azure. Toute personne disposant d’autorisations pour créer des ressources dans un abonnement peut configurer un service QnA Maker.

Cette configuration déploie plusieurs ressources Azure. Ensemble, ces ressources gèrent le contenu de la base de connaissances et fournissent des fonctionnalités de réponse aux questions via un point de terminaison.

1. Connectez-vous au [portail Azure](<https://portal.azure.com>).

2.  Cliquez sur **Ajouter une nouvelle ressource**, tapez « qna maker » dans la recherche et sélectionnez la ressource QnA Maker

    ![Créer un nouveau service QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource.png)

3.  Cliquez sur **Créer** après avoir lu les conditions générales.

    ![Créer un nouveau service QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

4. Dans **QnA Maker**, sélectionnez les niveaux et les régions appropriés.

    ![Créer un nouveau service QnA Maker](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Renseignez le **Nom** avec un nom unique pour identifier ce service QnA Maker. Ce nom identifie également le point de terminaison QnA Maker auquel vos bases de connaissances seront associées.
    * Choisissez **l’abonnement** dans lequel la ressource QnA Maker sera déployée.
    * Sélectionnez le **Niveau tarifaire de gestion** pour les services d’administration de QnA Maker (portail et API de gestion). Consultez [ici](https://aka.ms/qnamaker-pricing) plus d’informations sur la tarification des références SKU.
    * Créez un nouveau **Groupe de ressources** (recommandé) ou utilisez un groupe de ressources existant dans lequel déployer cette ressource QnA Maker.
    * Choisissez le **Niveau tarifaire de recherche** du service Recherche Azure. Si l’option de niveau Gratuit est grisée, cela signifie que vous disposez déjà d’un niveau Recherche Azure Gratuit déployé dans votre abonnement. Dans ce cas, vous devrez commencer par le niveau De base de la Recherche Azure. Consultez les détails sur la tarification de la Recherche Azure [ici](https://azure.microsoft.com/pricing/details/search/).
    * Choisissez **l’emplacement de recherche** où vous souhaitez que les données de Recherche Azure soient déployées. Les restrictions relatives à l’emplacement de stockage des données client indiquent l’emplacement que vous choisissez pour la Recherche Azure.
    * Attribuez un nom à votre App Service dans **Nom de l’application**.
    * Par défaut, App Service utilise le niveau Standard (S1). Vous pouvez modifier le plan après sa création. Pour plus d'informations, consultez [Tarification d’App Service](https://azure.microsoft.com/pricing/details/app-service/).
    * Choisissez **l’emplacement du site web** où App Service sera déployé.

        > [!NOTE]
        > L’emplacement de recherche peut être différent de l’emplacement du site web.

    * Choisissez si vous souhaitez activer **Application Insights** ou non. Si **Application Insights** est activé, QnA Maker collecte les données de télémétrie sur le trafic, les journaux de conversation et les erreurs.
    * Choisissez **l’emplacement d’Application Insights** où la ressource Application Insights sera déployée.

5. Une fois que tous les champs sont validés, vous pouvez cliquer sur **Créer** pour commencer le déploiement de ces services dans votre abonnement. L’exécution de cette opération nécessite quelques minutes.

6.  Une fois le déploiement terminé, vous verrez les ressources suivantes créées dans votre abonnement.

    ![Créer un nouveau service QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer et publier une base de connaissances](../Quickstarts/create-publish-knowledge-base.md)