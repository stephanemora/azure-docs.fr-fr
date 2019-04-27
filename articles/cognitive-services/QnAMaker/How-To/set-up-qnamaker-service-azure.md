---
title: Configurer un service QnA Maker - QnA Maker
titleSuffix: Azure Cognitive Services
description: Avant de pouvoir créer des bases de connaissances QnA Maker, vous devez tout d’abord configurer un service QnA Maker dans Azure. Toute personne disposant d’autorisations pour créer des ressources dans un abonnement peut configurer un service QnA Maker.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/02/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 90e7abf229e0ee1ca396150fee0c1f0970d257aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61372212"
---
# <a name="create-a-qna-maker-service"></a>Créer un service QnA Maker

Avant de pouvoir créer des bases de connaissances QnA Maker, vous devez tout d’abord configurer un service QnA Maker dans Azure. Toute personne disposant d’autorisations pour créer des ressources dans un abonnement peut configurer un service QnA Maker.

## <a name="create-a-new-service"></a>Créer un service

Cette procédure permet de déployer certaines ressources Azure. Ensemble, ces ressources gèrent le contenu de la base de connaissances et fournissent des fonctionnalités de réponse aux questions via un point de terminaison.

1. Connectez-vous au [Portail Azure](<https://portal.azure.com>).

1. Sélectionnez **ajouter une nouvelle ressource**et tapez « qna maker » dans la recherche, sélectionnez la ressource de QnA Maker

    ![Créer un service QnA Maker - Ajouter une nouvelle ressource](../media/qnamaker-how-to-setup-service/create-new-resource.png)

1. Sélectionnez **créer** après avoir lu les termes et conditions.

    ![Créer un nouveau service QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. Dans **QnA Maker**, sélectionnez les niveaux et les régions appropriés.

    ![Créer un service QnA Maker - Niveau tarifaire et régions](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Renseignez le **Nom** avec un nom unique pour identifier ce service QnA Maker. Ce nom identifie également le point de terminaison QnA Maker auquel vos bases de connaissances seront associées.
    * Choisissez **l’abonnement** dans lequel la ressource QnA Maker sera déployée.
    * Sélectionnez le **Niveau tarifaire de gestion** pour les services d’administration de QnA Maker (portail et API de gestion). Consultez [ici](https://aka.ms/qnamaker-pricing) plus d’informations sur la tarification des références SKU.
    * Créez un nouveau **Groupe de ressources** (recommandé) ou utilisez un groupe de ressources existant dans lequel déployer cette ressource QnA Maker. QnA Maker crée plusieurs ressources Azure ; Lorsque vous créez un groupe de ressources contenant ces ressources, vous pouvez facilement rechercher, gérer et supprimer ces ressources par le nom de groupe de ressources.
    * Choisissez le **Niveau tarifaire de recherche** du service Recherche Azure. Si l’option de niveau Gratuit est grisée, cela signifie que vous disposez déjà d’un niveau Recherche Azure Gratuit déployé dans votre abonnement. Dans ce cas, vous devrez commencer par le niveau De base de la Recherche Azure. Consultez les détails sur la tarification de la Recherche Azure [ici](https://azure.microsoft.com/pricing/details/search/).
    * Choisissez **l’emplacement de recherche** où vous souhaitez que les données de Recherche Azure soient déployées. Les restrictions relatives à l’emplacement de stockage des données client indiquent l’emplacement que vous choisissez pour la Recherche Azure.
    * Attribuez un nom à votre App Service dans **Nom de l’application**.
    * Par défaut, App Service utilise le niveau Standard (S1). Vous pouvez modifier le plan après sa création. Pour plus d'informations, consultez [Tarification d’App Service](https://azure.microsoft.com/pricing/details/app-service/).
    * Choisissez **l’emplacement du site web** où App Service sera déployé.

        > [!NOTE]
        > L’emplacement de recherche peut être différent de l’emplacement du site web.

    * Choisissez si vous souhaitez activer **Application Insights** ou non. Si **Application Insights** est activé, QnA Maker collecte les données de télémétrie sur le trafic, les journaux d’activité de conversation et les erreurs.
    * Choisissez **l’emplacement d’Application Insights** où la ressource Application Insights sera déployée.
    * Pour les mesures d’économies de coût, vous pouvez [partager](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) certains, mais pas toutes les ressources Azure créés pour QnA Maker. 

1. Une fois que tous les champs sont validés, vous pouvez sélectionner **créer** pour démarrer le déploiement de ces services dans votre abonnement. L’exécution de cette opération nécessite quelques minutes.

1. Une fois le déploiement terminé, vous verrez les ressources suivantes créées dans votre abonnement.

    ![Service QnA Maker créé par une ressource](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>Région du service de gestion

Le service de gestion de QnA Maker, utilisé uniquement pour le portail & pour le traitement de données initial, est disponible uniquement dans l’ouest des États-Unis. Les données client sont stockées dans ce service de l’ouest des États-Unis.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer et publier une base de connaissances](../Quickstarts/create-publish-knowledge-base.md)
