---
title: Gérer les mises à niveau des clusters Service Fabric
description: Gérer quand et comment le runtime de votre cluster Service Fabric est mis à jour
ms.topic: how-to
ms.date: 03/26/2021
ms.openlocfilehash: 98c3300e5cc51c32d894397839879e25190d979b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731159"
---
# <a name="manage-service-fabric-cluster-upgrades"></a>Gérer les mises à niveau des clusters Service Fabric

Un cluster Azure Service Fabric est une ressource qui vous appartient, mais qui est en partie gérée par Microsoft. Voici la façon de gérer quand et comment Microsoft met à jour votre cluster Azure Service Fabric.

Pour en savoir plus sur les concepts et processus de mise à niveau des clusters, consultez [Mettre à niveau et mettre à jour des clusters Azure Service Fabric](service-fabric-cluster-upgrade.md).

## <a name="set-upgrade-mode"></a>Définir le mode de mise à niveau

Vous pouvez définir votre cluster de façon à recevoir automatiquement les mises à niveau de Service Fabric dès qu’elles sont publiées par Microsoft, ou vous pouvez choisir manuellement parmi une liste de versions actuellement prises en charge en définissant le mode de mise à niveau de votre cluster. Pour ce faire, vous pouvez utiliser le contrôle *Mode de mise à niveau de Fabric* dans Portail Azure ou le paramètre `upgradeMode` dans le modèle de déploiement de votre cluster.

### <a name="azure-portal"></a>Portail Azure

À l’aide de Portail Azure, vous choisirez des mises à niveau automatiques ou manuelles lors de la création d’un cluster Service Fabric.

:::image type="content" source="media/service-fabric-cluster-upgrade/portal-new-cluster-upgrade-mode.png" alt-text="Choisissez entre les mises à niveau automatiques ou manuelles lors de la création d’un nouveau cluster dans Portail Azure à partir des options « Avancées ».":::

Vous pouvez également basculer entre les mises à niveau automatiques ou manuelles à partir de la section **Mises à niveau de Fabric** d’une ressource de cluster existante.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Sélectionnez des mises à niveau Automatiques ou Manuelles dans la section « Mises à niveau de Fabric » de votre ressource de cluster dans Portail Azure.":::

### <a name="manual-upgrades-with-azure-portal"></a>Mises à jour manuelles avec Portail Azure

Lorsque vous sélectionnez l’option de mise à niveau manuelle, il suffit de sélectionner une version dans la liste déroulante des versions disponibles, puis d’*enregistrer*. À partir de là, la mise à niveau du cluster est immédiatement lancée.

Les [stratégies d’intégrité des clusters](#custom-policies-for-manual-upgrades) (combinaison de l’intégrité des nœuds et de l’intégrité de toutes les applications exécutées dans le cluster) sont respectées pendant la mise à niveau. Si les stratégies d’intégrité des clusters ne sont pas respectées, la mise à niveau est annulée.

Une fois que vous avez corrigé les problèmes entraînant la restauration, vous devez initier à nouveau la mise à niveau en suivant la procédure décrite précédemment.

### <a name="resource-manager-template"></a>Modèle Resource Manager

Pour modifier le mode de mise à niveau de votre cluster à l’aide d’un modèle Resource Manager, spécifiez *Automatic* (Automatique) ou *Manual* (Manuel) pour la propriété `upgradeMode` de la définition de ressource *Microsoft.ServiceFabric/clusters*. Si vous choisissez des mises à niveau manuelles, définissez également `clusterCodeVersion` sur une [version de Fabric actuellement prise en charge](#query-for-supported-cluster-versions).

:::image type="content" source="./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG" alt-text="Capture d’écran montrant un modèle, qui est mis en retrait en texte clair pour refléter la structure. Les propriétés « clusterCodeVersion » et « upgradeMode » sont mis en évidence.":::

Une fois le déploiement du modèle réussi, les modifications apportées au mode de mise à niveau du cluster seront appliquées. Si votre cluster est en mode manuel, la mise à niveau du cluster démarre automatiquement.

Les [stratégies d’intégrité des clusters](#custom-policies-for-manual-upgrades) (combinaison de l’intégrité des nœuds et de l’intégrité de toutes les applications exécutées dans le cluster) sont respectées pendant la mise à niveau. Si les stratégies d’intégrité des clusters ne sont pas respectées, la mise à niveau est annulée.

Une fois que vous avez corrigé les problèmes entraînant la restauration, vous devez initier à nouveau la mise à niveau en suivant la procédure décrite précédemment.

## <a name="wave-deployment-for-automatic-upgrades"></a>Déploiement par vagues pour les mises à niveau automatiques

En mode de mise à niveau automatique, vous avez la possibilité d’activer votre cluster pour le déploiement par vagues. Avec le déploiement par vagues, vous pouvez créer un pipeline pour mettre à niveau vos clusters de test, de préproduction et de production en séquence, séparés par un « temps de cuisson » intégré pour valider les prochaines versions de Service Fabric avant que vos clusters de production ne soient mis à jour.

### <a name="enable-wave-deployment"></a>Activer le déploiement par vagues

> [!NOTE]
> Le déploiement par vagues requiert la version d’API `2020-12-01-preview` (ou ultérieure) pour votre ressource *Microsoft.ServiceFabric/clusters*.

Pour activer le déploiement par vagues pour la mise à niveau automatique, déterminez d’abord la vague à attribuer à votre cluster :

* **Vaque 0** (`Wave0`) : Les clusters sont mis à jour dès qu’une nouvelle build de Service Fabric est publiée. Destinée aux clusters de test/développement.
* **Vague 1** (`Wave1`) : Les clusters sont mis à jour une semaine (sept jours) après la publication d’une nouvelle build. Destinée aux clusters de préproduction et intermédiaires.
* **Vague 2** (`Wave2`) : Les clusters sont mis à jour 2 semaines (14 jours) après la publication d’une nouvelle build. Destinée aux clusters de production.

Ensuite, ajoutez simplement une propriété `upgradeWave` à votre modèle de ressource de cluster avec l’une des valeurs de vague indiquées ci-dessus. Assurez-vous que la version d’API de votre ressource de cluster est `2020-12-01-preview` ou une version ultérieure.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "fabricSettings": [...],
        "managementEndpoint": ...,
        "nodeTypes": [...],
        "provisioningState": ...,
        "reliabilityLevel": ...,
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
       ...
```

Une fois que vous avez déployé le modèle mis à jour, votre cluster sera inscrit dans la vague spécifiée pour la prochaine période de mise à niveau et après celle-ci.

Vous pouvez [vous inscrire aux notifications par e-mail](#register-for-notifications) avec des liens pour obtenir de l’aide en cas d’échec d’une mise à niveau du cluster.

### <a name="register-for-notifications"></a>Inscription à des notifications

Vous pouvez vous inscrire aux notifications en cas d’échec de la mise à niveau d’un cluster. Un e-mail sera envoyé à vos adresses e-mail désignées avec des détails supplémentaires sur l’échec de la mise à niveau et des liens vers de l’aide supplémentaire.

> [!NOTE]
> L’inscription au déploiement par vagues n’est pas nécessaire pour recevoir des notifications en cas d’échec de la mise à niveau.

Pour vous inscrire aux notifications, ajoutez une section `notifications` à votre modèle de ressource de cluster et désignez une ou plusieurs adresses e-mail (*receivers*) pour recevoir les notifications :

```json
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
        "notifications": [
        {
            "isEnabled": true,
            "notificationCategory": "WaveProgress",
            "notificationLevel": "Critical",
            "notificationTargets": [
            {
                "notificationChannel": "EmailUser",
                "receivers": [
                    "devops@contoso.com"
                ]
            }]
        }]
```

Une fois que vous avez déployé votre modèle mis à jour, vous êtes inscrit aux notifications d’échec de mise à niveau.

## <a name="custom-policies-for-manual-upgrades"></a>Stratégies personnalisées pour les mises à niveau manuelles

Vous pouvez spécifier des stratégies d’intégrité personnalisées pour les mises à niveau manuelles de cluster. Ces stratégies sont appliquées chaque fois que vous sélectionnez une nouvelle version du runtime, ce qui déclenche le lancement de la mise à niveau de votre cluster par le système. Si vous ne remplacez pas les stratégies, les valeurs par défaut sont utilisées.

Vous pouvez spécifier les stratégies d’intégrité personnalisées ou vérifier les paramètres actuels dans la section **Mises à niveau de Fabric** de votre ressource de cluster dans Portail Azure en sélectionnant l’option *Personnalisée* pour **Stratégie de mise à niveau**.

:::image type="content" source="./media/service-fabric-cluster-upgrade/custom-upgrade-policy.png" alt-text="Sélectionnez l’option de stratégie de mise à niveau « Personnalisée » dans la section « Mises à niveau de Fabric » de votre ressource de cluster dans Portail Azure afin de définir des stratégies d’intégrité personnalisées lors de la mise à niveau.":::

## <a name="query-for-supported-cluster-versions"></a>Requête sur les versions de cluster prises en charge

Vous pouvez utiliser l’[API REST Azure](/rest/api/azure/) pour répertorier toutes les versions du runtime Service Fabric ([clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list)) disponibles pour l’emplacement spécifié et votre abonnement.

Vous pouvez également référencer des [versions de Service Fabric](service-fabric-versions.md) pour plus d’informations sur les versions et les systèmes d’exploitation pris en charge.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2018-02-01

"value": [
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
    "name": "5.0.1427.9490",
    "type": "Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.0.1427.9490",
      "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
    "name": "5.1.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.1.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
    "name": "4.4.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "4.4.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Linux"
    }
  }
]
}
```

La propriété `supportExpiryUtc` dans la sortie indique quand une version donnée expire ou a expiré. Les version les plus récentes ne seront pas associées à une date valide, mais plutôt une valeur *9999-12-31T23:59:59.9999999*, qui signifie simplement que la date d’expiration n’est pas encore définie.


## <a name="next-steps"></a>Étapes suivantes

* [Gérer les mises à niveau de Service Fabric](service-fabric-cluster-upgrade-version-azure.md)
* Personnaliser les [paramètres de votre cluster Service Fabric](service-fabric-cluster-fabric-settings.md)
* [Effectuez un scale-in et un scale-out de votre cluster](service-fabric-cluster-scale-in-out.md)
* Découvrez les [mises à niveau de l’application](service-fabric-application-upgrade.md)


<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
