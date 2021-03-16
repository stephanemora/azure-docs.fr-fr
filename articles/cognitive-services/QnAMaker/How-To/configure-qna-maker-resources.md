---
title: Configurer le service QnA Maker – QnA Maker
description: Ce document décrit les configurations avancées de vos ressources QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 3c6f75eafad51c99f60b78ce49862d2488d5926f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102230947"
---
# <a name="configure-qna-maker-resources"></a>Configurer des ressources QnA Maker

L’utilisateur peut configurer QnA Maker pour utiliser une autre ressource Recherche cognitive. Il peut également configurer les paramètres App service s’il utilise QnA Maker en disponibilité générale.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/v1)

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Configurer QnA Maker pour utiliser une autre ressource de Recherche cognitive

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

En savoir plus sur la configuration des [paramètres de l’application](../../../app-service/configure-common.md#configure-app-settings) App Service .

### <a name="get-the-latest-runtime-updates"></a>Téléchargement des dernières mises à jour du Runtime

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

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Configurer le paramètre d’inactivité d’App Service pour éviter le délai d’expiration

Le service d’application, qui sert le runtime de prédiction QnA Maker d’une base de connaissances publiée, a une configuration de délai d’inactivité, laquelle utilise par défaut le délai d’expiration automatique si le service est inactif. Pour QnA Maker, cela signifie que votre API generateAnswer de runtime des prédictions expire parfois après des périodes sans trafic.

Pour que l’application de point de terminaison de prédiction soit chargée même en l’absence de trafic, définissez la valeur d’inactivité sur Toujours activé.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez et sélectionnez le service d’application de votre ressource QnA Maker. Il aura le même nom que la ressource QnA Maker, mais il aura un **type** différent d’App Service.
1. Recherchez **Paramètres** puis sélectionnez **Configuration**.
1. Dans le volet de configuration, sélectionnez **Paramètres généraux**, recherchez **Toujours activé**, puis sélectionnez **Activé** comme valeur.

    > [!div class="mx-imgBorder"]
    > ![Dans le volet de configuration, sélectionnez **Paramètres généraux**, puis recherchez **Toujours activé**, et sélectionnez la valeur **Activé**.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Sélectionnez **Enregistrer** pour enregistrer la configuration.
1. Vous êtes invité à redémarrer l’application pour qu’elle utilise le nouveau paramètre. Sélectionnez **Continuer**.

En savoir plus sur la configuration des [Paramètres généraux](../../../app-service/configure-common.md#configure-general-settings) de l’App Service .

### <a name="business-continuity-with-traffic-manager"></a>Continuité d'activité avec Traffic Manager

L’objectif principal du plan de continuité d’activité est de créer un point de terminaison de base de connaissances résilient qui garantit qu’il n’y a aucun temps d’arrêt du bot ou de l’application qui la consomme.

> [!div class="mx-imgBorder"]
> ![Plan de continuité d’activité QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

L’idée générale représentée plus haut est la suivante :

1. Configurez deux [services QnA Maker](set-up-qnamaker-service-azure.md) parallèles dans des [régions Azure associées](../../../best-practices-availability-paired-regions.md).

1. [Sauvegardez](../../../app-service/manage-backup.md) votre service d'application QnA Maker principal et [restaurez-le](../../../app-service/web-sites-restore.md) dans la configuration secondaire. Ainsi, les deux configurations fonctionneront avec les mêmes nom d'hôte et clés.

1. Synchronisez les index de recherche Azure principal et secondaire. Utilisez l’exemple GitHub [ici](https://github.com/pchoudhari/QnAMakerBackupRestore) pour savoir comment sauvegarder/restaurer les index Azure.

1. Sauvegardez Application Insights avec [exportation continue](../../../azure-monitor/app/export-telemetry.md).

1. Une fois que les piles principale et secondaire ont été configurées, utilisez [Gestionnaire de trafic](../../../traffic-manager/traffic-manager-overview.md) pour configurer les deux points de terminaison et définir une méthode de routage.

1. Vous devez créer un certificat Transport Layer Security (TLS), anciennement Secure Sockets Layer (SSL), pour le point de terminaison de votre gestionnaire de trafic. [Liez le certificat TLS/SSL](../../../app-service/configure-ssl-bindings.md) dans vos services d’application.

1. Enfin, utilisez le point de terminaison du gestionnaire de trafic dans votre bot ou dans votre application.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/v2)

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>Configurer le service QnA Maker managé (préversion) pour utiliser une autre ressource Recherche cognitive

Si vous créez un service QnA managé (préversion) et ses dépendances (par exemple, la recherche) par le biais du portail, un service de recherche est créé pour vous et lié au service QnA Maker managé (préversion). Une fois ces ressources créées, vous pouvez mettre à jour le service de recherche sous l’onglet **Configuration**.

1. Accédez à votre service QnA Maker managé (préversion) dans le portail Azure.

1. Sélectionnez **Configuration**, puis le service Recherche cognitive Azure à lier à votre service QnA Maker managé (préversion).

    ![Capture d’écran de la page de configuration de QnA Maker managé (préversion)](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. Cliquez sur **Enregistrer**.

> [!NOTE]
> Si vous modifiez le service de recherche Azure associé à QnA Maker, vous allez perdre l’accès à toutes les bases de connaissances déjà présentes dans celui-ci. Veillez à exporter les bases de connaissances existantes avant de modifier le service de recherche Azure.

---
