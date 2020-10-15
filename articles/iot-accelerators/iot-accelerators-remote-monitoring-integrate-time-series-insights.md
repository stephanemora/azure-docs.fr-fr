---
title: Intégration de Time Series Insights à une solution de supervision à distance | Microsoft Docs
description: Dans cette procédure, vous allez apprendre à configurer Time Series Insights pour une solution de supervision à distance existante qui ne contient pas déjà Time Series Insights.
author: Philmea
manager: timlt
ms.author: philmea
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 8b6219c82dc23deb467e87a6866839b8eace8b5d
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072643"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Intégrer Azure Time Series Insights à la Supervision à distance

Azure Time Series Insights est un service entièrement managé d’analytique, de stockage et de visualisation pour la gestion des données chronologiques IoT dans le cloud. Vous pouvez utiliser Time Series Insights pour stocker et gérer des données chronologiques, explorer et visualiser plusieurs événements de façon simultanée, procéder à une analyse de la cause racine et comparer plusieurs sites et ressources.

L’accélérateur de solution de supervision à distance assure désormais le déploiement automatique et l’intégration à Time Series Insights. Dans cette procédure, vous allez apprendre à configurer Time Series Insights pour une solution de supervision à distance existante qui ne contient pas déjà Time Series Insights.

> [!NOTE]
> Time Series Insights n’est pas encore disponible dans le cloud Azure Chine. Les nouveaux déploiements d’accélérateur de solution de supervision à distance dans le cloud Azure Chine utilisent Cosmos DB pour l’intégralité du stockage.

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer cette procédure, vous devez avoir déjà déployé une solution de supervision à distance :

* [Déployer l’accélérateur de solution de supervision à distance](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Créer un groupe de consommateurs

Créez un groupe de consommateurs dédié dans votre hub IoT à utiliser pour diffuser en continu des données vers Time Series Insights.

> [!NOTE]
> Les groupes de consommateurs sont utilisés par les applications pour extraire des données de l’instance Azure IoT Hub. Chaque groupe de consommateurs peut contenir jusqu’à cinq consommateurs de sortie. Vous devez créer un autre groupe de consommateurs tous les cinq récepteurs de sortie, et vous pouvez créer jusqu’à 32 groupes de consommateurs.

1. Dans le portail Azure, cliquez sur le bouton Cloud Shell.

1. Exécutez la commande suivante pour créer un groupe de consommateurs. Utilisez le nom du hub IoT dans votre déploiement de surveillance à distance ainsi que le nom de votre déploiement de surveillance à distance comme nom du groupe de ressources :

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Déploiement de Time Series Insights

À présent, déployez Time Series Insights comme ressource supplémentaire dans votre solution de supervision à distance et connectez-le au hub IoT.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez **Créer une ressource** > **Internet des objets** > **Time Series Insights**.

    ![Nouvel environnement Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Pour créer votre environnement Time Series Insights, utilisez les valeurs dans le tableau suivant :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom de l’environnement | La capture d’écran suivante utilise le nom **contorosrmtsi**. Choisissez votre propre nom unique quand vous effectuez cette étape. |
    | Subscription | Sélectionnez votre abonnement Azure dans la liste déroulante. |
    | Resource group | **Utilisez la valeur existante**. Sélectionnez le nom de votre groupe de ressources existant pour la surveillance à distance. |
    | Location | Nous utilisons **USA Est**. Si possible, créez votre environnement dans la même région que votre solution de supervision à distance. |
    | Sku |**S1** |
    | Capacité | **1** |

    ![Créer un environnement Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Cliquez sur **Créer**. La création de l’environnement peut prendre quelques instants.

## <a name="create-event-source"></a>Créez la source d’événement

Créez une source d’événement à connecter à votre hub IoT. Vérifiez que vous utilisez le groupe de consommateurs créé dans les étapes précédentes. Time Series Insights exige que chaque service dispose d’un groupe de consommateurs dédié qui ne soit pas utilisé par un autre service.

1. Accédez à votre nouvel environnement Time Series Insights.

1. Sur la gauche, sélectionnez **Sources d’événements**.

    ![Afficher les sources d’événements](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Cliquez sur **Ajouter**.

    ![Ajouter une source d’événement](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Pour configurer votre hub IoT comme nouvelle source d’événement, utilisez les valeurs dans le tableau suivant :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom de la source d’événement | La capture d’écran suivante utilise le nom **contosorm-iot-hub**. Utilisez votre propre nom unique quand vous effectuez cette étape. |
    | Source | **IoT Hub** |
    | Option d’importation | **Utiliser Iot Hub à partir des abonnements disponibles** |
    | Identifiant d’abonnement | Sélectionnez votre abonnement Azure dans la liste déroulante. |
    | Nom du hub IoT | **contosorma57a6**. Utilisez le nom du hub IoT associé à votre solution de supervision à distance. |
    | Nom de la stratégie du hub IoT | **iothubowner** Vérifiez que la stratégie utilisée est une stratégie propriétaire. |
    | Clé de stratégie du hub IoT | Ce champ est rempli automatiquement. |
    | Groupe de consommateurs du hub IoT | **timeseriesinsights** |
    | Format de sérialisation de l’événement | **JSON**     | 
    | Nom de la propriété d’horodatage | Laisser vide |

    ![Créer une source d’événement](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Cliquez sur **Créer**.

## <a name="configure-the-data-access-policy"></a>Configuration de la stratégie d’accès aux données

Pour vous assurer que tous les utilisateurs qui ont accès à votre solution de supervision à distance sont en mesure d’explorer les données dans l’Explorateur Time Series Insights, ajoutez votre application et vos utilisateurs dans les stratégies d’accès aux données dans le portail Azure. 

1. Dans la liste de navigation, sélectionnez **Groupes de ressources**.

1. Choisissez le groupe de ressources **ContosoRM**.

1. Dans la liste des ressources Azure, choisissez **contosormtsi**.

1. Choisissez **Stratégies d’accès aux données** pour afficher la liste actuelle des attributions de rôles.

1. Choisissez **Ajouter** pour ouvrir le volet **Select User Rule** (Sélectionner une règle d’utilisateur).

   Si vous n’êtes pas autorisé à attribuer des rôles, l’option **Ajouter** ne s’affiche pas.

1. Dans la liste déroulante **Rôle**, sélectionnez un rôle, par exemple **Lecteur** et **Contributeur**.

1. Dans la liste **Sélectionner** , sélectionnez un utilisateur, un groupe ou une application. Si vous ne voyez pas le principal de sécurité dans la liste, vous pouvez saisir du texte dans la zone **Sélectionner** pour rechercher des noms d’affichage, des adresses de messagerie et des identificateurs d’objet dans le répertoire.

1. Sélectionnez **Enregistrer** pour créer l’attribution de rôle. Après quelques instants, le rôle est attribué au principal de sécurité dans les stratégies d’accès aux données.

> [!NOTE]
> Si vous devez accorder à des utilisateurs supplémentaires l’accès à l’Explorateur Time Series Insights, vous pouvez suivre ces étapes pour [accorder l’accès aux données](../time-series-insights/concepts-access-policies.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Configuration d’Azure Stream Analytics 

L’étape suivante consiste à configurer le microservice Azure Stream Analytics Manager pour interrompre l’envoi de messages à Cosmos DB et à les stocker uniquement dans Time Series Insights. Ignorez cette étape si vous souhaitez dupliquer vos messages dans Cosmos DB.

1. Dans la liste de navigation, sélectionnez **Groupes de ressources**.

1. Choisissez le groupe de ressources **ContosoRM**.

1. Dans la liste des ressources, recherchez le travail de diffusion en continu Azure Stream Analytics (ASA). Le nom de ressource commence par **streamingjobs-** .

1. En haut, cliquez sur le bouton pour arrêter les travaux de diffusion en continu ASA.

1. Modifiez la requête ASA, puis supprimez les clauses **SELECT**, **INTO** et **FROM** qui pointent vers le flux des messages dans Cosmos DB. Ces clauses doivent figurer en bas de la requête et ressembler à l’exemple suivant :

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. Redémarrez les travaux de diffusion en continu Azure Stream Analytics.

7. Extrayez les dernières modifications apportées au microservice Azure Stream Analytics Manager en tapant la commande suivante à l’invite de commandes :

.NET : 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java :

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Configuration du microservice Telemetry

Extrayez les dernières modifications apportées au microservice Telemetry en tapant la commande suivante à l’invite de commandes :

.NET :

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java :

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Facultatif]* Configuration de l’interface utilisateur web à lier à l’Explorateur Time Series Insights

Pour afficher facilement vos données dans l’Explorateur Time Series Insights, nous vous recommandons de personnaliser l’interface utilisateur pour la lier facilement à l’environnement. Pour ce faire, extrayez les dernières modifications apportées à l’interface utilisateur web à l’aide de la commande suivante :

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Configuration des variables d’environnement

Pour terminer l’intégration de Time Series Insights, vous devez configurer l’environnement de votre déploiement pour les microservices mis à jour.

### <a name="basic-deployments"></a>Déploiements de base

Configurez l’environnement de déploiement `basic` pour les microservices mis à jour.

1. Dans le portail Azure, cliquez sur l’onglet **Azure Active Directory** dans le panneau gauche.

1. Cliquez sur **Inscriptions d’applications**.

1. Recherchez votre application **ContosoRM** et cliquez dessus.

1. Accédez à **Paramètres** > **Clés**, puis créez une clé pour votre application. Veillez à copier la valeur de la clé à un emplacement sûr.

1. Extrayez le [dernier fichier yaml Docker Compose](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) à partir du référentiel GitHub à l’aide de la dernière balise. 

1. Connectez-vous à la machine virtuelle à l’aide du protocole SSH en suivant les étapes décrites dans l’article [Comment utiliser des clés SSH avec Windows sur Azure](../virtual-machines/linux/ssh-from-windows.md).

1. Une fois connecté, tapez `cd /app`.

1. Ajoutez les variables d’environnement suivantes à chaque microservice dans le fichier yaml Docker Compose et dans le script `env-setup` sur la machine virtuelle :

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Accédez au **service Telemetry** et modifiez également le fichier Docker Compose en ajoutant les variables d’environnement indiquées ci-dessus.

1. Accédez au **service ASA Manager** et modifiez le fichier Docker Compose en ajoutant `PCS_TELEMETRY_STORAGE_TYPE`.

1. Redémarrez les conteneurs Docker à l’aide de l’élément `sudo ./start.sh` à partir de la machine virtuelle.

> [!NOTE]
> La configuration des variables d’environnement ci-dessus est valable pour les versions de Surveillance à distance antérieures à 1.0.2

### <a name="standard-deployments"></a>Déploiements standard

Configurez l’environnement de déploiement `standard` pour les microservices mis à jour indiqués ci-dessus.

1. Sur la ligne de commande, exécutez `kubectl proxy`. Pour plus d’informations, consultez l’article [Accessing the Kubernetes API](https://kubernetes.io/docs/reference/access-authn-authz/#using-kubectl-to-start-a-proxy-server) (Accès à l’API Kubernetes).

1. Ouvrez la console de gestion Kubernetes.

1. Recherchez le mappage de configuration pour ajouter les nouvelles variables d’environnement suivantes pour TSI :

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Modifiez le fichier yaml de modèle pour un pod du service Telemetry :

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. Modifiez le fichier yaml de modèle pour un pod du service ASA Manager :

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir comment explorer vos données et diagnostiquer une alerte dans l’Explorateur Time Series Insights, consultez notre didacticiel sur la [réalisation d’une analyse de cause racine](iot-accelerators-remote-monitoring-root-cause-analysis.md).

* Pour apprendre à explorer et interroger des données dans l’Explorateur Time Series Insights, consultez la documentation relative à [l’Explorateur Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).