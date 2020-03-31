---
title: Créer un cluster privé avec Azure Red Hat OpenShift 3.11 | Microsoft Docs
description: Créer un cluster privé avec Azure Red Hat OpenShift 3.11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro, openshift, cluster privé, red hat
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399266"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Créer un cluster privé avec Azure Red Hat OpenShift 3.11

> [!IMPORTANT]
> Les clusters privés Azure Red Hat OpenShift (ARO) sont disponibles uniquement en préversion privée dans la région USA Est 2. L’acceptation de la préversion privée se fait sur invitation uniquement. Inscrivez votre abonnement avant d’essayer d’activer cette fonctionnalité.

Les clusters privés offrent les avantages suivants :

* Les clusters privés n’exposent pas les composants du plan de contrôle du cluster (tels que les serveurs d’API) sur une adresse IP publique.
* Le réseau virtuel d’un cluster privé est configurable par les clients, ce qui vous permet de configurer la mise en réseau afin d’autoriser le peering avec d’autres réseaux virtuels, y compris les environnements ExpressRoute. Vous pouvez également configurer un DNS personnalisé sur le réseau virtuel pour qu’il s’intègre aux services internes.

## <a name="before-you-begin"></a>Avant de commencer

> [!NOTE]
> Cette fonctionnalité nécessite la version 2019-10-27-preview de l’API HTTP ARO. Elle n’est pas encore prise en charge dans Azure CLI.

Les champs de l’extrait de configuration suivant sont nouveaux et doivent être inclus dans la configuration de votre cluster. `managementSubnetCidr` doit se trouver dans le réseau virtuel du cluster et est utilisé par Azure pour gérer le cluster.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Un cluster privé peut être déployé à l’aide des exemples de scripts fournis ci-dessous. Une fois le cluster déployé, exécutez la commande `cluster get` et consultez la propriété `properties.FQDN` pour déterminer l’adresse IP privée du serveur d’API OpenShift.

Le réseau virtuel du cluster aura été créé avec des autorisations afin que vous puissiez le modifier. Vous pouvez ensuite configurer la mise en réseau pour accéder au réseau virtuel (ExpressRoute, VPN, appairage de réseaux virtuels) en fonction de vos besoins.

Si vous changez les serveurs de noms DNS sur le réseau virtuel du cluster, vous devez émettre une mise à jour sur le cluster avec la propriété `properties.RefreshCluster` définie sur `true` afin que les machines virtuelles puissent être réinitialisées. Cette mise à jour leur permet de récupérer les nouveaux noms de port.

## <a name="sample-configuration-scripts"></a>Exemples de scripts de configuration

Utilisez les exemples de scripts fournis dans cette section pour configurer et déployer votre cluster privé.

### <a name="environment"></a>Environnement

Renseignez les variables d’environnement ci-dessous comme si vous utilisiez vos propres valeurs.

> [!NOTE]
> La localisation doit être définie sur `eastus2`, car il s’agit de la seule localisation prise en charge pour les clusters privés.

``` bash
export CLUSTER_NAME=
export LOCATION=eastus2
export TOKEN=$(az account get-access-token --query 'accessToken' -o tsv)
export SUBID=
export TENANT_ID=
export ADMIN_GROUP=
export CLIENT_ID=
export SECRET=
```

### <a name="private-clusterjson"></a>private-cluster.json

À l’aide des variables d’environnement définies ci-dessus, voici un exemple de configuration de cluster avec un cluster privé activé.

```json
{
   "location": "$LOCATION",
   "name": "$CLUSTER_NAME",
   "properties": {
       "openShiftVersion": "v3.11",
       "networkProfile": {
           "vnetCIDR": "10.0.0.0/8",
           "managementSubnetCIDR" : "10.0.1.0/24"
       },
       "authProfile": {
           "identityProviders": [
               {
                   "name": "Azure AD",
                   "provider": {
                       "kind": "AADIdentityProvider",
                       "clientId": "$CLIENT_ID",
                       "secret": "$SECRET",
                       "tenantId": "$TENANT_ID",
                       "customerAdminGroupID": "$ADMIN_GROUP"
                   }
               }
           ]
       },
       "masterPoolProfile": {
           "name": "master",
           "count": 3,
           "vmSize": "Standard_D4s_v3",
           "osType": "Linux",
           "subnetCIDR": "10.0.0.0/24",
           "apiProperties": {
                "privateApiServer": true
            }
       },
       "agentPoolProfiles": [
           {
               "role": "compute",
               "name": "compute",
               "count": 1,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           },
           {
               "role": "infra",
               "name": "infra",
               "count": 3,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           }
       ],
       "routerProfiles": [
           {
               "name": "default"
           }
       ]
   }
}
```

## <a name="deploy-a-private-cluster"></a>Déployer un cluster privé

Après avoir configuré votre cluster privé avec les exemples de scripts ci-dessus, exécutez la commande suivante pour le déployer.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’accès à la console OpenShift, consultez [Guide pas à pas de la console web](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).
