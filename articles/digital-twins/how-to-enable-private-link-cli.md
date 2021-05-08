---
title: Activer l’accès privé avec Private Link (préversion) - CLI
titleSuffix: Azure Digital Twins
description: Découvrez comment activer l’accès privé pour les solutions Azure Digital Twins avec Private Link, à l’aide d’Azure CLI.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c38db60c89b02e932c0a381daff1013b17008f54
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108208830"
---
# <a name="enable-private-access-with-private-link-preview-azure-cli"></a>Activer l’accès privé avec Private Link (préversion) : Azure CLI

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

Cet article décrit les différentes façons d'[activer une liaison privée avec un point de terminaison privé pour une instance Azure Digital Twins](concepts-security.md#private-network-access-with-azure-private-link-preview) (actuellement en préversion). La configuration d’un point de terminaison privé pour votre instance Azure Digital Twins vous permet de sécuriser cette instance et d’éliminer l’exposition publique, tout en évitant l’exfiltration de données à partir de votre [réseau virtuel Azure (VNet)](../virtual-network/virtual-networks-overview.md).

Cet article décrit le processus d’utilisation d’[Azure CLI](/cli/azure/what-is-azure-cli).

Les étapes décrites dans cet article sont les suivantes : 
1. Activer la liaison privée et configurer un point de terminaison privé pour une instance Azure Digital Twins.
1. Désactiver ou activer des indicateurs d’accès au réseau public pour limiter l’accès de l’API aux connexions de liaison privées uniquement.

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir configurer un point de terminaison privé, vous avez besoin d’un [réseau virtuel Azure (VNet)](../virtual-network/virtual-networks-overview.md)  où le point de terminaison peut être déployé. Si vous ne disposez pas déjà d’un réseau virtuel, vous pouvez suivre l’un des [démarrages rapides](../virtual-network/quick-create-portal.md) de réseau virtuel Azure pour en configurer un.

## <a name="manage-private-endpoints-for-an-azure-digital-twins-instance"></a>Gérer les points de terminaison privés d’une instance d’Azure Digital Twins 

Quand vous utilisez [Azure CLI](/cli/azure/what-is-azure-cli), vous pouvez configurer des points de terminaison privés avec Private Link sur une instance d’Azure Digital Twins déjà existante (vous ne pouvez pas les ajouter dans le cadre de la création de l’instance). Vous pouvez ensuite les voir et les gérer via des commandes CLI supplémentaires. 

>[!TIP]
> Vous pouvez également configurer un point de terminaison de liaison privé via le service de liaison privée, plutôt que par le biais de votre instance Azure Digital Twins. Vous obtenez également les mêmes options de configuration et le même résultat final.
>
> Pour plus d’informations sur la configuration des ressources Private Link, consultez la documentation relative à Private Link pour le [portail Azure](../private-link/create-private-endpoint-portal.md), [Azure CLI](../private-link/create-private-endpoint-cli.md), les [modèles ARM](../private-link/create-private-endpoint-template.md) ou [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Ajouter un point de terminaison privé à une instance existante

Pour créer un point de terminaison privé et le lier à une instance d’Azure Digital Twins, utilisez la commande [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create). Identifiez l’instance d’Azure Digital Twins à l’aide de son ID complet dans le paramètre `--private-connection-resource-id`.

Voici un exemple qui utilise la commande pour créer un point de terminaison privé, avec uniquement les paramètres obligatoires.

```azurecli-interactive
az network private-endpoint create --connection-name {private_link_service_connection} -n {name_for_private_endpoint} -g {resource_group} --subnet {subnet_ID} --private-connection-resource-id "/subscriptions/{subscription_ID}/resourceGroups/{resource_group}/providers/Microsoft.DigitalTwins/digitalTwinsInstances/{Azure_Digital_Twins_instance_name}" 
```

Pour obtenir la liste complète des paramètres obligatoires et facultatifs ainsi que d’autres exemples de création de points de terminaison privés, consultez la documentation de référence relative à [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create).

### <a name="manage-private-endpoint-connections-on-the-instance"></a>Gérer les connexions de point de terminaison privé de l’instance

Une fois qu’un point de terminaison privé a été créé pour votre instance d’Azure Digital Twins, vous pouvez utiliser les commandes [az dt network private-endpoint connection](/cli/azure/dt/network/private-endpoint/connection) pour continuer à gérer les **connexions** de point de terminaison privé de l’instance. Les opérations incluent :
* Afficher une connexion de point de terminaison privé
* Définir l’état de la connexion de point de terminaison privé
* Supprimer la connexion de point de terminaison privé
* Lister toutes les connexions de point de terminaison privé d’une instance

Pour plus d’informations et d’exemples, consultez la documentation de référence relative à [az dt network private-endpoint](/cli/azure/dt/network/private-endpoint).

### <a name="manage-other-private-link-information-on-an-azure-digital-twins-instance"></a>Gérer d’autres informations Private Link sur une instance d’Azure Digital Twins

Vous pouvez obtenir des informations supplémentaires sur l’état de Private Link pour votre instance avec les commandes [az dt network private-link](/cli/azure/dt/network/private-link). Les opérations incluent :
* Lister les liens privés associés à une instance d’Azure Digital Twins
* Afficher un lien privé associé à l’instance

Pour plus d’informations et d’exemples, consultez la documentation de référence relative à [az dt network private-link](/cli/azure/dt/network/private-link).

## <a name="disable--enable-public-network-access-flags"></a>Activer/désactiver les indicateurs d’accès au réseau public

Vous pouvez configurer une instance Azure Digital Twins pour refuser toutes les configurations publiques et n’autoriser que les connexions établies par le biais de points de terminaison privés afin d’améliorer la sécurité réseau. Cette action s’effectue à l’aide d’un **indicateur d’accès public au réseau**. 

Cette stratégie vous permet de limiter l’accès de l’API aux connexions de liaison privées uniquement. Lorsque l’indicateur d’accès public au réseau est défini sur *désactivé*, tous les appels de l’API REST vers le plan de données de l’instance Azure Digital Twins à partir du cloud public retournent `403, Unauthorized`. Par ailleurs, lorsque la stratégie est définie sur *désactivé* et qu’une demande est effectuée via un point de terminaison privé, l’appel d’API échoue.

Cet article montre comment mettre à jour la valeur de l’indicateur réseau à l’aide d’[Azure CLI](/cli/azure/) ou de l’[outil en ligne de commande ARMClient](https://github.com/projectkudu/ARMClient). Pour savoir comment y parvenir via le portail Azure, consultez la [version dédiée au portail](how-to-enable-private-link-portal.md) de cet article.

### <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure

Dans Azure CLI, vous pouvez désactiver ou activer l’accès réseau public en ajoutant un paramètre `--public-network-access` à la commande `az dt create`. Bien que cette commande permette également de créer une instance, vous pouvez l’utiliser pour modifier les propriétés d’une instance existante en lui fournissant le nom de cette dernière. (Pour plus d’informations sur cette commande, consultez sa [documentation de référence](/cli/azure/dt#az_dt_create) ou les [instructions générales relatives à la configuration d’une instance d’Azure Digital Twins](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)).

Pour **désactiver** l’accès réseau public d’une instance d’Azure Digital Twins, utilisez le paramètre `--public-network-access` comme ceci :

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Disabled
```

Pour **activer** l’accès réseau public d’une instance où il est désactivé, utilisez la commande similaire suivante :

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Enabled
```

### <a name="usethe-armclientcommand-tool"></a>Utiliser l’outil en ligne de commande ARMClient 

Avec l’[outil de commande ARMClient](https://github.com/projectkudu/ARMClient), l’accès public au réseau est activé ou désactivé à l’aide des commandes ci-dessous. 

Pour **désactiver** l’accès réseau public :
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

Pour **activer** l’accès réseau public :  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les liaisons privées pour Azure : 
* [Qu’est-ce que le service Azure Private Link ?](../private-link/private-link-service-overview.md)