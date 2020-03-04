---
title: Configurer un service QnA Maker - QnA Maker
description: Avant de pouvoir créer des bases de connaissances QnA Maker, vous devez tout d’abord configurer un service QnA Maker dans Azure. Toute personne disposant d’autorisations pour créer des ressources dans un abonnement peut configurer un service QnA Maker.
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 663cbce0e096c6189d97cf7872d466383d272f06
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650416"
---
# <a name="manage-qna-maker-resources"></a>Gérer les ressources QnA Maker

Avant de pouvoir créer des bases de connaissances QnA Maker, vous devez tout d’abord configurer un service QnA Maker dans Azure. Toute personne disposant d’autorisations pour créer des ressources dans un abonnement peut configurer un service QnA Maker.

Veillez à bien assimiler les concepts suivants avant de créer votre ressource :

* [Ressources QnA Maker](../Concepts/azure-resources.md)
* [Création et publication de clés](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Créer un nouveau service QnA Maker

Cette procédure permet de créer les ressources Azure nécessaires pour gérer le contenu de la base de connaissances. Une fois ces étapes terminées, vous trouverez les clés _d’abonnement_ dans la page **Clés** de la ressource dans la portail Azure.

1. Connectez-vous au portail Azure et [créez une ressource QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker).

1. Sélectionnez **Créer** après avoir lu les conditions générales :

    ![Créer un nouveau service QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. Dans **QnA Maker**, sélectionnez les niveaux et les régions appropriés :

    ![Créer un service QnA Maker - Niveau tarifaire et régions](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Dans le champ **Nom**, renseignez un nom unique pour identifier ce service QnA Maker. Ce nom identifie également le point de terminaison QnA Maker auquel vos bases de connaissances seront associées.
    * Choisissez **l’abonnement** dans lequel la ressource QnA Maker sera déployée.
    * Sélectionnez le **Niveau tarifaire** pour les services d’administration de QnA Maker (portail et API de gestion). Voir [plus d’informations sur la tarification des références SKU](https://aka.ms/qnamaker-pricing).
    * Créez un nouveau **Groupe de ressources** (recommandé) ou utilisez un groupe de ressources existant dans lequel déployer cette ressource QnA Maker. QnA Maker crée plusieurs ressources Azure. Lorsque vous créez un groupe de ressources pour conserver ces ressources, vous pouvez facilement les rechercher, les gérer et les supprimer par le biais du nom du groupe de ressources.
    * Sélectionnez un **Emplacement du groupe de ressources**.
    * Choisissez le **Niveau tarifaire de recherche** du service Recherche cognitive Azure. Si l’option de niveau Gratuit est indisponible (semble grisée), cela signifie que vous disposez déjà d’un service gratuit déployé dans votre abonnement. Dans ce cas, vous devrez commencer par le niveau De base. Consultez les [Détails de la tarification du service Recherche cognitive Azure](https://azure.microsoft.com/pricing/details/search/).
    * Choisissez **l’emplacement de recherche** où vous souhaitez déployer les index du service Recherche cognitive Azure. Les restrictions relatives à l’emplacement de stockage des données client vous aideront à déterminer l’emplacement que vous choisissez pour la Recherche cognitive Azure.
    * Dans le champ **Nom de l’application**, entrez un nom pour votre instance Azure App Service.
    * Par défaut, App Service utilise le niveau Standard (S1). Vous pouvez modifier le plan après sa création. Consultez des informations supplémentaires sur [la tarification d’App Service](https://azure.microsoft.com/pricing/details/app-service/).
    * Choisissez **l’emplacement du site web** où App Service sera déployé.

        > [!NOTE]
        > **Emplacement de recherche** peut être différent de **Emplacement du site web**.

    * Choisissez si vous souhaitez activer **Application Insights** ou non. Si **Application Insights** est activé, QnA Maker collecte les données de télémétrie sur le trafic, les journaux d’activité de conversation et les erreurs.
    * Choisissez **l’emplacement d’Application Insights** où la ressource Application Insights sera déployée.
    * Pour réduire vos coûts, vous pouvez [partager](#configure-qna-maker-to-use-different-cognitive-search-resource) certaines des ressources Azure créées pour QnA Maker, mais pas toutes.

1. Une fois tous les champs validés, sélectionnez **Créer**. L’exécution de ce processus peut prendre plusieurs minutes.

1. Une fois le déploiement terminé, vous verrez les ressources suivantes créées dans votre abonnement :

   ![Service QnA Maker créé par une ressource](../media/qnamaker-how-to-setup-service/resources-created.png)

    La ressource avec le type _Cognitive Services_ a vos clés _d’abonnement_.

## <a name="find-subscription-keys-in-the-azure-portal"></a>Rechercher les clés d’abonnement dans le portail Azure

Vous pouvez afficher et réinitialiser vos clés d’abonnement à partir du portail Azure où la ressource QnA Maker a été créée.

1. Accédez à la ressource QnA Maker dans le portail Azure et sélectionnez la ressource qui a le type _Cognitive Services_ :

    ![Liste des ressources QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Accédez à **Clés** :

    ![Clé d’abonnement](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Rechercher des clés de point de terminaison dans le portail QnA Maker

Le point de terminaison se trouve dans la même région que la ressource, car les clés de point de terminaison sont utilisées pour appeler la base de connaissances.

Les clés de point de terminaison peuvent être gérées à partir du [portail QnA Maker](https://qnamaker.ai).

1. Connectez-vous au [portail QnA Maker](https://qnamaker.ai), accédez à votre profil, puis sélectionnez **Paramètres de service** :

    ![Clé de point de terminaison](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Affichez ou réinitialisez vos clés :

    > [!div class="mx-imgBorder"]
    > ![Gestionnaire de la clé de point de terminaison](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Actualisez vos clés si vous pensez qu’elles ont été compromises. Cette opération peut nécessiter des modifications correspondantes de votre code de bot ou d’application cliente.

### <a name="upgrade-qna-maker-sku"></a>Mettre à niveau la référence SKU de QnA Maker

Si vous souhaitez avoir plus de questions et de réponses dans votre base de connaissances, au-delà de votre niveau actuel, mettez à niveau votre niveau tarifaire du service QnA Maker.

Pour mettre à niveau la référence SKU de gestion de QnA Maker :

1. Accédez à votre ressource QnA Maker dans le portail Azure et sélectionnez **Niveau tarifaire**.

    ![Ressource QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Choisissez la référence SKU appropriée et appuyez sur **Sélectionner**.

    ![Tarification de QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>Mise à niveau du service d’application

 Lorsque votre base de connaissances doit répondre à un plus grand nombre de demandes de votre application client, mettez à niveau le niveau tarifaire d’App Service.

Vous pouvez faire [monter en puissance](https://docs.microsoft.com/azure/app-service/manage-scale-up) ou faire descendre en puissance le service d’application.

Accédez à la ressource App Service dans le portail Azure et sélectionnez l’option **Monter en puissance** ou **Descendre en puissance** en fonction des besoins.

![Mise à l'échelle du service d’application QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Mettre à niveau le service Recherche cognitive Azure

Si vous prévoyez de disposer de plusieurs bases de connaissances, mettez à niveau votre niveau tarifaire du service Recherche cognitive Azure.

Il n’est actuellement pas possible d’effectuer une mise à niveau sur place de la référence SKU de la recherche Azure. Toutefois, vous pouvez créer une ressource de recherche Azure avec la référence SKU souhaitée, restaurer les données vers la nouvelle ressource, puis la lier à la pile QnA Maker. Pour ce faire, procédez comme suit :

1. Créez une ressource de recherche Azure dans le portail Azure et sélectionnez la référence SKU souhaitée.

    ![Ressources de recherche QnA Maker Azure](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Restaurez les index de votre ressource de recherche Azure d’origine vers la nouvelle. Consultez [l’exemple de code de restauration de sauvegarde](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Une fois que les données sont restaurées, accédez à votre nouvelle ressource de recherche Azure, sélectionnez **Clés**, puis notez le **Nom** et la **Clé d’administration** :

    ![Clés de recherche QnA Maker Azure](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Pour lier la nouvelle ressource de recherche Azure à la pile QnA Maker, accédez à l’instance App Service de QnA Maker.

    ![Instance du service d’application QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Sélectionnez **Paramètres d'application** et modifiez les paramètres dans les champs **AzureSearchName** et **AzureSearchAdminKey** à partir de l’étape 3.

    ![Paramètre de service d’application QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Redémarrez l’instance App Service.

    ![Redémarrage de l’instance d’App Service de QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Téléchargement des dernières mises à jour du Runtime

Le runtime QnAMaker fait partie de l’instance Azure App Service, qui est déployé quand vous [créez un service QnAMaker](./set-up-qnamaker-service-azure.md) dans le portail Azure. Des mises à jour du runtime sont effectuées régulièrement. L’instance QnA Maker App Service est en mode de mise à jour automatique à partir de la publication de l’extension de site d’avril 2019 (version 5+). Cette mise à jour a déjà été conçue pour éviter le moindre temps d’arrêt pendant les mises à niveau.

Vous pouvez vérifier votre version actuelle à l’adresse https://www.qnamaker.ai/UserSettings. Si votre version est antérieure à la version 5.x, vous devez redémarrer App Service pour appliquer les dernières mises à jour :

1. Accédez à votre service QnAMaker (groupe de ressources) dans le [portail Azure](https://portal.azure.com).

    > [!div class="mx-imgBorder"]
    > ![Groupe de ressources Azure QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Sélectionnez l’instance d’App Service et ouvrez la section **Vue d’ensemble**.

    > [!div class="mx-imgBorder"]
    > ![Instance de l’App Service QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Redémarrez App Service. Le processus de mise à jour doit se terminer en quelques secondes. Les applications ou bots dépendants qui utilisent ce service QnAMaker ne sont pas disponibles pour les utilisateurs finaux durant le redémarrage.

    ![Redémarrage de l’instance d’App Service de QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Configurer QnA Maker pour utiliser une autre ressource de Recherche cognitive

Si vous créez un service QnA et ses dépendances (par exemple, la recherche) via le portail, un service de recherche est créé pour vous et lié au service QnA Maker. Une fois ces ressources créées, vous pouvez mettre à jour le paramètre App Service pour utiliser un service de recherche existant au préalable et supprimer celui que vous venez de créer.

La ressource **App Service** de QnA Maker utilise la ressource Recherche cognitive. Pour modifier la ressource Recherche cognitive qu’utilise QnA Maker, vous devez modifier le paramètre sur le Portail Azure.

1. Récupérez la **Clé d’administration** et le **Nom** de la ressource Recherche cognitive que QnA Maker doit utiliser.

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et recherchez l’**App Service** associé à votre ressource QnA Maker. Les deux portent le même nom.

1. Sélectionnez **Paramètres**, puis **Configuration**. Cela permet d’afficher tous les paramètres existants de l’App Service de QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran du portail Azure montrant les paramètres de configuration d’App Service](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Modifiez les valeurs des clés suivantes :

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Pour utiliser les nouveaux paramètres, vous devez redémarrer l’App Service. Sélectionnez **Vue d’ensemble**, puis **Redémarrer**.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de Portail Azure redémarrant App Service après modification des paramètres de configuration](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Si vous créez un service QnA par le biais de modèles Azure Resource Manager, vous pouvez créer toutes les ressources et contrôler la création de l’App Service pour utiliser un service de recherche existant.

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur [App Service](../../../app-service/index.yml) et le [service de recherche](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Créer et publier une base de connaissances](../Quickstarts/create-publish-knowledge-base.md)