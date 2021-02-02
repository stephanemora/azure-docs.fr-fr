---
title: Exposer des applications à Internet à l’aide d’Application Gateway et de Pare-feu Azure
description: Comment exposer des applications à Internet à l’aide d’Application Gateway et de Pare-feu Azure
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: d5bd62dad5be7f6a6df5b6b037e8eeae13ee48e3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98887081"
---
# <a name="expose-applications-to-the-internet-using-application-gateway-and-azure-firewall"></a>Exposer des applications à Internet à l’aide d’Application Gateway et de Pare-feu Azure

Ce document explique comment exposer des applications à Internet à l’aide d’Application Gateway et de Pare-feu Azure. Lorsqu’une instance de service Azure Spring Cloud est déployée dans votre réseau virtuel, les applications de l’instance de service sont accessibles uniquement dans le réseau privé. Pour rendre les applications accessibles sur Internet, vous devez effectuer une intégration avec **Azure Application Gateway** et, éventuellement, avec **Pare-feu Azure**.

## <a name="prerequisites"></a>Prérequis

- [Azure CLI version 2.0.4 ou ultérieure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="define-variables"></a>Définir des variables

Définissez des variables pour le groupe de ressources et le réseau virtuel que vous avez créés comme indiqué dans [Déployer Azure Spring Cloud dans un réseau virtuel Azure (injection de réseau virtuel)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md). Personnalisez les valeurs en fonction de votre environnement réel.

```
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_APP_PRIVATE_FQDN='my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="login-to-azure"></a>Connexion à Azure

Connectez-vous à Azure CLI et choisissez votre abonnement actif.

```
az login
az account set --subscription ${SUBSCRIPTION}
```

## <a name="create-network-resources"></a>Créer des ressources réseau

La **passerelle applicative Azure** qui sera créée rejoindra le même réseau virtuel que l’instance de service Azure Spring Cloud, ou un réseau virtuel qui lui est appairé. Commencez par créer un sous-réseau pour la passerelle applicative dans le réseau virtuel en utilisant `az network vnet subnet create`, puis créez une IP publique en tant que serveur frontal de la passerelle applicative en utilisant `az network public-ip create`.

```
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --address-prefix ${APPLICATION_GATEWAY_SUBNET_CIDR}
az network public-ip create \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-application-gateway"></a>Créer une passerelle Application Gateway

Créez une passerelle applicative en utilisant `az network application-gateway create` et spécifiez le nom de domaine complet (FQDN) privé de votre application en tant que serveurs dans le pool principal. Ensuite, mettez à jour le paramètre HTTP à l’aide de `az network application-gateway http-settings update` pour utiliser le nom d’hôte du pool principal.

```
APPLICATION_GATEWAY_NAME='my-app-gw'
az network application-gateway create \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Enabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --subnet ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --servers ${SPRING_APP_PRIVATE_FQDN}
az network application-gateway http-settings update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool true
```

La création de la passerelle d’application par Azure peut prendre jusqu’à 30 minutes. Après sa création, vérifiez l’intégrité du serveur principal à l’aide de `az network application-gateway show-backend-health`.  Il s’agit d’examiner si la passerelle applicative atteint votre application via son nom de domaine complet privé.

```
az network application-gateway show-backend-health \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP}
```

La sortie indique l’état d’intégrité du pool principal.

```
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="access-your-application-using-the-frontend-public-ip-of-the-application-gateway"></a>Accéder à votre application à l’aide de l’IP publique frontale de la passerelle applicative

Récupérez l’IP publique de la passerelle applicative en utilisant `az network public-ip show`.

```
az network public-ip show \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --query [ipAddress] \
    --output tsv
```

Copiez et collez l’adresse IP publique dans la barre d’adresse de votre navigateur.

  ![Application dans l’IP publique](media/spring-cloud-expose-apps-gateway-az-firewall/app-gateway-public-ip.png)

## <a name="see-also"></a>Voir aussi

- [Résolution des problèmes Azure Spring Cloud dans un réseau virtuel](spring-cloud-troubleshooting-vnet.md)
- [Responsabilités du client pour l’exécution d’Azure Spring Cloud dans un réseau virtuel](spring-cloud-vnet-customer-responsibilities.md)