---
title: Mettre à niveau des clusters managés Azure Service Fabric
description: Découvrez les options de mise à niveau de votre cluster managé Azure Service Fabric.
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: b30f240325eda83428a19377e63d5a7f37f88169
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122865054"
---
# <a name="manage-service-fabric-managed-cluster-upgrades"></a>Gérer les mises à niveau des clusters managés Service Fabric

Un cluster Azure Service Fabric est une ressource qui vous appartient, mais qui est en partie gérée par Microsoft. Voici la façon de gérer quand et comment Microsoft met à jour votre runtime de cluster managé Azure Service Fabric.

## <a name="set-upgrade-mode"></a>Définir le mode de mise à niveau

Les clusters managés Azure Service Fabric sont définis par défaut pour recevoir des mises à niveau automatiques Service Fabric à mesure qu’elles sont publiées par Microsoft à l’aide d’une stratégie de [déploiement par vagues](#wave-deployment-for-automatic-upgrades). Vous pouvez également configurer les mises à niveau en mode manuel afin d’effectuer une sélection dans une liste de versions actuellement prises en charge. Vous pouvez configurer ces paramètres via le contrôle *Mises à niveau de Fabric* dans le portail Azure ou le paramètre `ClusterUpgradeMode` dans le modèle de déploiement de votre cluster.

## <a name="wave-deployment-for-automatic-upgrades"></a>Déploiement par vagues pour les mises à niveau automatiques

Avec le déploiement par vagues, vous pouvez créer un pipeline pour mettre à niveau vos clusters de test, de préproduction et de production en séquence, séparés par un « temps de cuisson » intégré pour valider les prochaines versions de Service Fabric avant que vos clusters de production ne soient mis à jour.

>[!NOTE]
>Par défaut, les clusters sont définis sur Vague 0.

Pour activer le déploiement par vagues à des fins de mise à niveau automatique, déterminez d’abord la vague à attribuer à votre cluster :

* **Vaque 0** (`Wave0`) : Les clusters sont mis à jour dès qu’une nouvelle build de Service Fabric est publiée.
* **Vague 1** (`Wave1`) : Les clusters sont mis à jour après la Vague 0 pour permettre une durée de baking. La vague 1 intervient après un minimum de 7 jours suivant la Vague 0.
* **Vague 2** (`Wave2`) : Les clusters sont mis à jour en dernier pour permettre une plus longue durée de baking. La vague 2 intervient après un minimum de 14 jours suivant la Vague 0.

## <a name="set-the-wave-for-your-cluster"></a>Définir la Vague de votre cluster

Vous pouvez définir votre cluster sur l’un des vagues disponibles par le biais du contrôle *Mises à niveau Fabric* dans le portail Azure ou du paramètre `ClusterUpgradeMode` dans le modèle de déploiement de votre cluster.

### <a name="azure-portal"></a>Portail Azure

À l’aide du portail Azure, vous choisissez entre les vagues automatiques disponibles lors de la création d’un cluster Service Fabric.

:::image type="content" source="./media/how-to-managed-cluster-upgrades/portal-new-cluster-upgrade-waves-setting.png" alt-text="Choisissez entre les différentes vagues disponibles lors de la création d’un cluster dans le portail Azure à partir des options « Avancées ».":::

Vous pouvez également basculer entre les mises à niveau automatiques disponibles à partir de la section **Mises à niveau Fabric** d’une ressource de cluster existante.

:::image type="content" source="./media/how-to-managed-cluster-upgrades/manage-upgrade-wave-settings.png" alt-text="Sélectionnez entre différentes vagues automatiques dans la section « Mises à niveau Fabric » de votre ressource de cluster dans le portail Azure.":::

### <a name="resource-manager-template"></a>Modèle Resource Manager

Pour modifier le mode de mise à niveau de votre cluster à l’aide d’un modèle Resource Manager, spécifiez *Automatic* (Automatique) ou *Manual* (Manuel) pour la propriété `ClusterUpgradeMode` de la définition de ressource *Microsoft.ServiceFabric/clusters*. Si vous choisissez des mises à niveau manuelles, définissez également `clusterCodeVersion` sur une [version de Fabric actuellement prise en charge](#query-for-supported-cluster-versions).

#### <a name="manual-upgrade"></a>Mise à niveau manuelle

```json
{
"apiVersion": "2021-05-01",
"type": "Microsoft.ServiceFabric/managedClusters",
"properties": {
        "ClusterUpgradeMode": "Manual",
        "ClusterCodeVersion": "8.0.514.9590"
        }
}
```

Une fois le déploiement du modèle réussi, les modifications apportées au mode de mise à niveau du cluster seront appliquées. Si votre cluster est en mode manuel, la mise à niveau du cluster démarre automatiquement.

Les stratégies d’intégrité des clusters (combinaison de l’intégrité des nœuds et de l’intégrité de toutes les applications exécutées dans le cluster) sont respectées pendant la mise à niveau. Si les stratégies d’intégrité des clusters ne sont pas respectées, la mise à niveau est annulée.

Si une restauration se produit, corrigez les problèmes à l’origine et initiez à nouveau la mise à niveau en suivant la procédure décrite précédemment.

#### <a name="automatic-upgrade-with-wave-deployment"></a>Mise à niveau automatique avec déploiement par vagues

Pour configurer les mises à niveau automatiques et le déploiement par vagues, vérifiez simplement que `ClusterUpgradeMode` est défini sur `Automatic` et que la propriété `clusterUpgradeCadence` est définie avec l’une des valeurs de vague répertoriées ci-dessus dans votre modèle Resource Manager.

```json
{
"apiVersion": "2021-05-01",
"type": "Microsoft.ServiceFabric/managedClusters",
"properties": {
        "ClusterUpgradeMode": "Automatic",
        "clusterUpgradeCadence": "Wave1",
        }  
}
```

Une fois que vous avez déployé le modèle mis à jour, votre cluster sera inscrit dans la vague spécifiée pour les mises à jour automatiques.

## <a name="query-for-supported-cluster-versions"></a>Requête sur les versions de cluster prises en charge

Vous pouvez utiliser l’[API REST Azure](/rest/api/azure/) pour répertorier toutes les versions du runtime Service Fabric ([clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list)) disponibles pour l’emplacement spécifié et votre abonnement.

Vous pouvez également référencer des [versions de Service Fabric](service-fabric-versions.md) pour plus d’informations sur les versions et les systèmes d’exploitation pris en charge.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/managedclusterVersions?api-version=2021-05-01

"value": [
  {
    "id": "subscriptions/eec8e14e-b47d-40d9-8bd9-23ff5c381b40/providers/Microsoft.ServiceFabric/locations/eastus2/environments/Windows/managedClusterVersions/7.2.477.9590",
    "name": "7.2.477.9590",
    "type": "Microsoft.ServiceFabric/locations/environments/managedClusterVersions",
    "properties": {
      "supportExpiryUtc": "2021-11-30T00:00:00",
      "osType": "Windows",
      "clusterCodeVersion": "7.2.477.9590"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/locations/eastus2/environments/Windows/managedClusterVersions/8.0.514.9590",
    "name": "8.0.514.9590",
    "type": "Microsoft.ServiceFabric/locations/environments/managedClusterVersions",
    "properties": {
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "osType": "Windows",
      "clusterCodeVersion": "8.0.514.9590"
    }
  }
]

```

La propriété `supportExpiryUtc` dans la sortie indique quand une version donnée expire ou a expiré. Les version les plus récentes ne seront pas associées à une date valide, mais plutôt une valeur *9999-12-31T23:59:59.9999999*, qui signifie simplement que la date d’expiration n’est pas encore définie.

## <a name="next-steps"></a>Étapes suivantes

* [Personnaliser votre configuration de cluster managé Service Fabric](how-to-managed-cluster-configuration.md)
* Découvrez les [mises à niveau de l’application](service-fabric-application-upgrade.md)

<!--Image references-->
[Upgrade-Wave-Settings]: ./media/how-to-managed-cluster-upgrades/manage-upgrade-wave-settings.png
[New-Cluster-Wave-Settings]: ./media/how-to-managed-cluster-upgrades/portal-new-cluster-upgrade-waves-setting.png
