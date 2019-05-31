---
title: Visualiser les données à partir de l’Explorateur de données Azure à l’aide de Grafana
description: Dans ce guide pratique, vous allez apprendre à configurer Azure Data Explorer en tant que source de données pour Grafana, puis à visualiser les données d’un exemple de cluster.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: 135f8f1c9c352f9d2307a8bf9ad1bec892aac179
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399915"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualiser des données Azure Data Explorer dans Grafana

Grafana est une plateforme d’analytique qui vous permet d’interroger et de visualiser des données, puis de créer et partager des tableaux de bord selon vos visualisations. Grafana propose un *plug-in* Azure Data Explorer, qui vous permet de vous connecter à des données et de les visualiser à partir d’Azure Data Explorer. Dans cet article, vous allez apprendre à configurer Azure Data Explorer en tant que source de données pour Grafana, puis à visualiser les données d’un exemple de cluster.

## <a name="prerequisites"></a>Conditions préalables

Pour suivre ce guide pratique, vous avez besoin des éléments suivants :

* [Grafana version 5.3.0 ou ultérieure](https://docs.grafana.org/installation/) pour votre système d’exploitation

* Le [plug-in Azure Data Explorer](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) pour Grafana

* Un cluster qui inclut l’exemple de données StormEvents. Pour plus d’informations, consultez [Démarrage rapide : Créer un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md) et [Ingérer des exemples de données dans Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-data-source"></a>Configurer la source de données

Effectuez les étapes suivantes pour configurer Azure Data Explorer en tant que source de données pour Grafana. Nous allons détailler ces étapes dans cette section :

1. Créez un principal de service Azure Active Directory (Azure AD) Le principal de service est utilisé par Grafana pour accéder au service Azure Data Explorer.

1. Ajoutez le principal de service Azure AD au rôle *observateurs* dans la base de données Azure Data Explorer.

1. Spécifiez les propriétés de connexion Grafana selon les informations du principal de service Azure AD, puis testez la connexion.

### <a name="create-a-service-principal"></a>Créer un principal du service

Vous pouvez créer le principal de service dans le [portail Azure](#azure-portal) ou à l’aide de la ligne de commande [Azure CLI](#azure-cli). Quelle que soit la méthode que vous utilisez, après la création, vous obtenez des valeurs pour quatre propriétés de connexion que vous utiliserez lors des étapes ultérieures.

#### <a name="azure-portal"></a>Portail Azure

1. Pour créer le principal de service, suivez les instructions données dans la [documentation du portail Azure](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. Dans la section [Affecter l’application à un rôle](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role), affectez le type de rôle **Lecteur** à votre cluster Azure Data Explorer.

    1. Dans la section [Obtenir les valeurs pour la connexion](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in), copiez les trois valeurs de propriété décrites dans les étapes : **ID de répertoire** (ID de locataire), **ID d’application** et **Mot de passe**.

1. Dans le portail Azure, sélectionnez **Abonnements**, puis copiez l’ID de l’abonnement dans lequel vous avez créé le principal de service.

    ![ID d’abonnement - portail](media/grafana/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. Créer un principal de service. Définissez une étendue appropriée et le type de rôle `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourGrafana}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Pour plus d’informations, consultez [Créer un principal de service Azure avec Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

1. La commande retourne un jeu de résultats comme le suivant. Copiez les trois valeurs de propriété : **appID**, **mot de passe** et **locataire**.

    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourGrafana}:{PortNumber}",
      "name": "https://{UrlToYourGrafana}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Obtenez la liste de vos abonnements.

    ```azurecli
    az account list --output table
    ```

    Copiez l’ID d’abonnement approprié.

    ![ID d’abonnement - CLI](media/grafana/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Ajouter le principal de service au rôle observateurs

Maintenant que vous avez un principal de service, ajoutez-le au rôle *observateurs* dans la base de données Azure Data Explorer. Vous pouvez effectuer cette tâche sous **Autorisations** dans le portail Azure ou sous **Requête** en utilisant une commande de gestion.

#### <a name="azure-portal---permissions"></a>Portail Azure - Autorisations

1. Dans le portail Azure, accédez à votre cluster Azure Data Explorer.

1. Dans la section **Vue d’ensemble**, sélectionnez la base de données contenant l’exemple de données StormEvents.

    ![Sélectionner la base de données](media/grafana/select-database.png)

1. Sélectionnez **Autorisations**, puis **Ajouter**.

    ![Autorisations de base de données](media/grafana/database-permissions.png)

1. Sous **Ajouter des autorisations de base de données**, sélectionnez le rôle **Observateur**, puis **Sélectionner les principaux**.

    ![Ajouter des autorisations de base de données](media/grafana/add-permission.png)

1. Recherchez le principal de service que vous avez créé (l’exemple montre le principal **mb-grafana**). Sélectionnez le principal, puis **Sélectionner**.

    ![Gérer les autorisations dans le portail Azure](media/grafana/new-principals.png)

1. Sélectionnez **Enregistrer**.

    ![Gérer les autorisations dans le portail Azure](media/grafana/save-permission.png)

#### <a name="management-command---query"></a>Commande de gestion - Requête

1. Dans le portail Azure, accédez à votre cluster Azure Data Explorer, puis sélectionnez **Requête**.

    ![Interroger](media/grafana/query.png)

1. Exécutez la commande suivante dans la fenêtre de requête. Utiliser l’ID d’application et l’ID de locataire à partir du portail Azure ou de l’interface CLI.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    La commande retourne un jeu de résultats comme le suivant. Dans cet exemple, la première ligne concerne un utilisateur existant dans la base de données et la deuxième le principal de service qui vient d’être ajouté.

    ![Jeu de résultats](media/grafana/result-set.png)

### <a name="specify-properties-and-test-the-connection"></a>Spécifier des propriétés et tester la connexion

Maintenant que le principal de service est affecté au rôle *observateurs*, vous pouvez spécifier des propriétés dans votre instance de Grafana, puis tester la connexion à Azure Data Explorer.

1. Dans Grafana, dans le menu de gauche, sélectionnez l’icône d’engrenage, puis **Sources de données**.

    ![Sources de données](media/grafana/data-sources.png)

1. Sélectionnez **Ajouter une source de données**.

1. Dans la page **Sources de données/Nouveau**, entrez un nom pour la source de données, puis sélectionnez le type **Source de données Azure Data Explorer**.

    ![Nom et type de la connexion](media/grafana/connection-name-type.png)

1. Entrez le nom de votre cluster au format https://{ClusterName}.{Region{Region}.kusto.windows.net. Entrez les autres valeurs à partir du portail Azure ou de l’interface CLI. Consultez le tableau situé sous l’image suivante pour obtenir un mappage.

    ![Propriétés de connexion](media/grafana/connection-properties.png)

    | Interface utilisateur de Grafana | Portail Azure | Azure CLI |
    | --- | --- | --- |
    | ID d’abonnement | ID D’ABONNEMENT | SubscriptionId |
    | ID client | ID du répertoire | locataire |
    | ID de client | ID de l'application | appId |
    | Clé secrète client | Mot de passe | password |
    | | | |

1. Sélectionnez **Enregistrer et tester**.

    Si le test est réussi, passez à la section suivante. Si vous rencontrez des problèmes, vérifiez les valeurs que vous avez spécifiées dans Grafana, puis vérifiez les étapes précédentes.

## <a name="visualize-data"></a>Visualiser les données

Maintenant que vous avez fini de configurer Azure Data Explorer en tant que source de données pour Grafana, le moment est venu de visualiser les données. Nous allons vous montrer un exemple très simple, mais sachez que les possibilités qui s’offrent à vous sont bien plus grandes. Nous vous recommandons de consulter [Écrire des requêtes pour Azure Data Explorer](write-queries.md) pour obtenir d’autres exemples de requêtes à exécuter sur l’exemple de jeu de données.

1. Dans Grafana, dans le menu de gauche, sélectionnez l’icône plus (+), puis **Tableau de bord**.

    ![Créer un tableau de bord](media/grafana/create-dashboard.png)

1. Sous l’onglet **Ajouter**, sélectionnez **Graphe**.

    ![Ajouter un graphe](media/grafana/add-graph.png)

1. Dans le panneau du graphe, sélectionnez **Titre du panneau**, puis **Modifier**.

    ![Modifier le panneau](media/grafana/edit-panel.png)

1. Au bas du panneau, sélectionnez **Source de données**, puis sélectionnez la source de données que vous avez configurée.

    ![Sélectionnez la source de données](media/grafana/select-data-source.png)

1. Dans le volet de requête, copiez la requête suivante, puis sélectionnez **Exécuter**. La requête compartimente le nombre d’événements par jour pour l’exemple de jeu de données.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Exécuter une requête](media/grafana/run-query.png)

1. Le graphe ne montre aucun résultat car sa portée se limite par défaut aux données des six dernières heures. Dans le menu supérieur, sélectionnez **6 dernières heures**.

    ![Six dernières heures](media/grafana/last-six-hours.png)

1. Spécifiez une plage personnalisée qui couvre l’année 2007, année incluse dans notre exemple de jeu de données StormEvents. Sélectionnez **Appliquer**.

    ![Plage de dates personnalisée](media/grafana/custom-date-range.png)

    Le graphe montre maintenant les données de 2007, compartimentées par jour.

    ![Graphe terminé](media/grafana/finished-graph.png)

1. Dans le menu supérieur, sélectionnez l’icône Enregistrer : ![Icône Enregistrer](media/grafana/save-icon.png).

## <a name="next-steps"></a>Étapes suivantes

* [Écrire des requêtes pour l’Explorateur de données Azure](write-queries.md)

* [Tutoriel : Visualiser des données Azure Data Explorer dans Power BI](visualize-power-bi.md)