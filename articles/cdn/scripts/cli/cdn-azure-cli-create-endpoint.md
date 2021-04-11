---
title: Créer un profil et un point de terminaison Azure Content Delivery Network (CDN) avec Azure CLI
description: Exemples de scripts Azure CLI pour créer un profil Azure CDN, un point de terminaison, un groupe d’origine, une origine et un domaine personnalisé.
author: asudbring
ms.author: allensu
manager: danielgi
ms.date: 03/09/2021
ms.topic: sample
ms.service: azure-cdn
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce01c1f851a5dbaedc85d848b12bf0b0831a16ef
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104723457"
---
# <a name="create-an-azure-cdn-profile-and-endpoint-using-the-azure-cli"></a>Créer un profil et un point de terminaison Azure CDN avec Azure CLI

En guise d’alternative au portail Azure, vous pouvez utiliser ces exemples de scripts Azure CLI pour gérer les opérations CDN suivantes :

- Créer un profil CDN
- Créer un point de terminaison CDN.
- Créer un groupe d’origine CDN et en faire le groupe par défaut
- Créer une origine CDN
- Créer un domaine personnalisé et activer le protocole HTTPS

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-scripts"></a>Exemples de scripts

Si n’avez pas encore de groupe de ressources pour votre profil CDN, créez-en un avec la commande `az group create` :

```azurecli
# Create a resource group to use for the CDN.
az group create --name MyResourceGroup --location eastus

```

Le script Azure CLI suivant crée un profil CDN et un point de terminaison CDN :

```azurecli
# Create a CDN profile.
az cdn profile create --resource-group MyResourceGroup --name MyCDNProfile --sku Standard_Microsoft

# Create a CDN endpoint.
az cdn endpoint create --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --origin www.contoso.com

```

Le script Azure CLI suivant crée un groupe d’origine CDN, définit le groupe d’origine par défaut pour un point de terminaison, et crée une nouvelle origine :

```azurecli
# Create an origin group.
az cdn origin-group create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyOriginGroup --origins origin-0

# Make the origin group the default group of an endpoint.
az cdn endpoint update --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --default-origin-group MyOriginGroup
                           
# Create another origin for an endpoint.
az cdn origin create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name origin-1 --host-name example.contoso.com

```

Le script Azure CLI suivant crée un domaine personnalisé CDN et active le protocole HTTPS. Avant de pouvoir associer un domaine personnalisé à un point de terminaison Azure CDN, vous devez créer un enregistrement de nom canonique (CNAME) avec Azure DNS ou votre fournisseur DNS pointant vers votre point de terminaison CDN. Pour plus d’informations, voir [Créer un enregistrement CNAME DNS](../../../cdn/cdn-map-content-to-custom-domain.md#create-a-cname-dns-record).

```azurecli
# Associate a custom domain with an endpoint.
az cdn custom-domain create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain --hostname www.example.com

# Enable HTTPS on the custom domain.
az cdn custom-domain enable-https --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain

```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois que vous avez fini d’exécuter les exemples de script, utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées.

```azurecli
# Delete the resource group.
az group delete --name MyResourceGroup

```

## <a name="azure-cli-commands-used-in-this-article"></a>Commandes Azure CLI utilisées dans cet article

- [az cdn endpoint create](/cli/azure/cdn/endpoint#az_cdn_endpoint_create)
- [az cdn endpoint update](/cli/azure/cdn/endpoint#az_cdn_endpoint_update)
- [az cdn origin create](/cli/azure/cdn/origin#az_cdn_origin_create)
- [az cdn origin-group create](/cli/azure/cdn/origin-group#az_cdn_origin_group_create)
- [az cdn profile create](/cli/azure/cdn/profile#az_cdn_profile_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az cdn custom-domain create](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_create)
- [az cdn custom-domain enable-https](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_enable_https)
