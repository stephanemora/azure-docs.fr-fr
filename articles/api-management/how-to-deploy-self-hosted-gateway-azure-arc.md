---
title: Déployer une passerelle de gestion des API Azure sur Azure Arc
description: Activez Azure Arc pour déployer votre passerelle de gestion des API Azure auto-hébergée.
author: v-hhunter
ms.author: v-hhunter
ms.service: api-management
ms.topic: article
ms.date: 05/25/2021
ms.openlocfilehash: 71abc9acdcf8796591e7241a7fcfeded1cd3139a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112283120"
---
# <a name="deploy-an-azure-api-management-gateway-on-azure-arc-preview"></a>Déployer une passerelle de gestion des API Azure sur Azure Arc (préversion)

Avec l’intégration entre la Gestion des API Azure et [Azure Arc sur Kubernetes](../azure-arc/kubernetes/overview.md), vous pouvez déployer le composant de passerelle Gestion des API en tant qu’[extension dans un cluster Kubernetes avec Azure Arc](../azure-arc/kubernetes/extensions.md). 

Le déploiement de la passerelle Gestion des API sur un cluster Kubernetes avec Arc étend la prise en charge de la gestion des API aux environnements hybrides et à plusieurs clouds. Activez le déploiement à l’aide d’une extension de cluster pour assurer la cohérence de la gestion et de l’application des stratégies à votre cluster avec Arc.

[!INCLUDE [preview](./includes/preview/preview-callout-self-hosted-gateway-azure-arc.md)]

> [!NOTE]
> Vous pouvez également déployer la passerelle auto-hébergée [directement sur Kubernetes](./how-to-deploy-self-hosted-gateway-azure-kubernetes-service.md).

## <a name="prerequisites"></a>Prérequis

* [Connectez votre cluster Kubernetes](../azure-arc/kubernetes/quickstart-connect-cluster.md) dans [une région Azure Arc prise en charge](../azure-arc/kubernetes/overview.md#supported-regions).
* Installez l’extension Azure CLI `k8s-extension` :

    ```azurecli
    az extension add --name k8s-extension
    ```
    Si vous avez déjà installé le module `k8s-extension`, mettez-le à jour vers la dernière version :

    ```azurecli
    az extension update --name k8s-extension
    ```
* [Créer une instance du service Gestion des API Azure](./get-started-create-service-instance.md).
* [Approvisionnez une ressource de passerelle dans votre instance Gestion des API Azure](./api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-api-management-gateway-extension-using-azure-cli"></a>Déployer l’extension de la passerelle Gestion des API à l’aide d’Azure CLI

1. Dans le portail Azure, accédez à votre instance APIM.
1. Sélectionnez **Passerelles** dans le menu de navigation latéral.
1. Sélectionnez et ouvrez votre ressource de passerelle approvisionnée dans la liste.
1. Dans votre ressource de passerelle approvisionnée, cliquez sur **Déploiement** dans le menu de navigation latéral.
1. Notez les valeurs de **Jeton** et **URL de configuration** pour l’étape suivante.
1. Dans Azure CLI, déployez l’extension de passerelle à l’aide de la commande `az k8s-extension create`. Renseignez les valeurs `token` et `configuration URL`.
    * L’exemple suivant utilise la configuration d’extension `service.type='LoadBalancer'`. Voir plus de [configurations d’extension disponibles](#available-extension-configurations).

    ```azurecli
    az k8s-extension create --cluster-type connectedClusters --cluster-name <cluster-name> \
      --resource-group <rg-name> --name <extension-name> --extension-type Microsoft.ApiManagement.Gateway \
      --scope namespace --target-namespace <namespace> \
      --configuration-settings gateway.endpoint='<Configuration URL>' \
      --configuration-protected-settings gateway.authKey='<token>' \
      --configuration-settings service.type='LoadBalancer' --release-train preview
    ```

    > [!TIP]
    > L’indicateur `-protected-` pour `authKey` est facultatif mais recommandé. 

1. Vérifiez l’état du déploiement à l’aide de la commande CLI suivante :
    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <rg-name> --name <extension-name>
    ```
1. Revenez à la liste **Passerelles** pour vérifier que l’état de la passerelle affiche une coche verte avec un nombre de nœuds. Cet état signifie que les pods de passerelle auto-hébergés déployés :
    * Communiquent avec succès avec le service Gestion des API.
    * Ont une « pulsation » normale.

## <a name="deploy-the-api-management-gateway-extension-using-azure-portal"></a>Déployer l’extension de la passerelle Gestion des API à l’aide du portail Azure

1. Dans le portail Azure, accédez à votre cluster connecté Azure Arc.
1. Dans le menu de gauche, sélectionnez **Extensions (préversion)**  >  **+ Ajouter** > **Passerelle de gestion des API (préversion)** .
1. Sélectionnez **Create** (Créer).
1. Dans la fenêtre **Installer la passerelle Gestion des API**, configurez l’extension de passerelle :
    * Sélectionnez l’abonnement et le groupe de ressources de votre instance Gestion des API.
    * Dans **Détails de la passerelle**, sélectionnez l’**Instance Gestion des API** et le **Nom de la passerelle**. Entrez une étendue **Espace de noms** pour votre extension et éventuellement un certain nombre de **Réplicas**, s’ils sont pris en charge dans votre niveau de service Gestion des API.
    * Dans **Configuration de Kubernetes**, sélectionnez la configuration par défaut ou une configuration différente pour votre cluster. Pour plus d’options, consultez [Configurations d’extension disponibles](#available-extension-configurations).

    :::image type="content" source="./media/how-to-deploy-self-hosted-gateway-azure-arc/deploy-gateway-extension-azure-arc.png" alt-text="Capture d’écran du déploiement de l’extension dans le portail Azure":::

1. Sous l’onglet **Supervision**, activez éventuellement la supervision pour télécharger les demandes de suivi des métriques vers la passerelle et le serveur principal. Si vous l’activez, sélectionnez un espace de travail **Log Analytics** existant.
1. Sélectionnez **Vérifier + installer**, puis **Installer**.

## <a name="available-extension-configurations"></a>Configurations d’extension disponibles

Les configurations d’extension suivantes sont **obligatoires**.

| Paramètre | Description |
| ------- | ----------- | 
| `gateway.endpoint` | URL de configuration du point de terminaison de la passerelle. |
| `gateway.authKey` | Jeton pour l’accès à la passerelle. | 
| `service.type` | Configuration du service Kubernetes pour la passerelle : `LoadBalancer`, `NodePort` ou `ClusterIP`. |

### <a name="log-analytics-settings"></a>Paramètres de Log Analytics

Pour activer la supervision de la passerelle auto-hébergée, configurez les paramètres Log Analytics suivants :

| Paramètre | Description |
| ------- | ----------- | 
| `monitoring.customResourceId` | ID de ressource Azure Resource Manager pour l’instance Gestion des API. |
| `monitoring.workspaceId` | ID d’espace de travail Log Analytics. | 
| `monitoring.ingestionKey` | Secret avec clé d’ingestion de Log Analytics. |

> [!NOTE]
> Si vous n’avez pas activé Log Analytics : 
> 1. Parcourez le guide de démarrage rapide [Créer un espace de travail Log Analytics](../azure-monitor/logs/quick-create-workspace.md). 
> 1. Découvrez où trouver les [paramètres de l’agent log Analytics](../azure-monitor/agents/log-analytics-agent.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la passerelle auto-hébergée, consultez [Vue d’ensemble de la passerelle auto-hébergée Gestion des API](self-hosted-gateway-overview.md).
* Découvrez toutes les [Extensions Kubernetes avec Azure Arc](../azure-arc/kubernetes/extensions.md). 
* En savoir plus sur [Kubernetes avec Azure Arc](../azure-arc/kubernetes/overview.md).
