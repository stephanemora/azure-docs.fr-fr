---
title: Activer l’accès privé avec Private Link (préversion)
titleSuffix: Azure Digital Twins
description: Découvrez comment activer l’accès privé pour les solutions Azure Digital Twins avec Private Link.
author: baanders
ms.author: baanders
ms.date: 7/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q1
ms.openlocfilehash: 93435355a2d75a2346d076c44b377726789431a3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452332"
---
# <a name="enable-private-access-with-private-link-preview"></a>Activer l’accès privé avec Private Link (préversion)

Cet article décrit les différentes façons d'[activer une liaison privée avec un point de terminaison privé pour une instance Azure Digital Twins](concepts-security.md#private-network-access-with-azure-private-link-preview) (actuellement en préversion). La configuration d’un point de terminaison privé pour votre instance Azure Digital Twins vous permet de sécuriser cette instance et d’éliminer l’exposition publique, tout en évitant l’exfiltration de données à partir de votre [réseau virtuel Azure (VNet)](../virtual-network/virtual-networks-overview.md).

Les étapes décrites dans cet article sont les suivantes : 
1. Activer la liaison privée et configurer un point de terminaison privé pour une instance Azure Digital Twins.
1. Affichez, modifiez ou supprimez un point de terminaison privé d’une instance.
1. Désactiver ou activer des indicateurs d’accès au réseau public pour limiter l’accès de l’API aux connexions de liaison privées uniquement.

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir configurer un point de terminaison privé, vous avez besoin d’un [réseau virtuel Azure (VNet)](../virtual-network/virtual-networks-overview.md)  où le point de terminaison peut être déployé. Si vous ne disposez pas déjà d’un réseau virtuel, vous pouvez suivre l’un des [guides de démarrage rapide de réseau virtuel Azure](../virtual-network/quick-create-portal.md) pour en configurer un.

## <a name="add-a-private-endpoint-to-azure-digital-twins"></a>Ajouter un point de terminaison privé à Azure Digital Twins 

Vous pouvez utiliser soit le [Portail Azure](https://portal.azure.com) soit l’[interface de ligne de commande Azure](/cli/azure/what-is-azure-cli) pour activer Private Link avec un point de terminaison privé pour une instance Azure Digital Twins. 

Si vous souhaitez configurer Private Link dans le cadre de la configuration initiale de l’instance, vous devrez utiliser le Portail Azure. Si vous souhaitez activer Private Link sur une instance après sa création, vous pouvez utiliser le Portail Azure ou l’interface de ligne de commande Azure. L’une ou l’autre de ces méthodes de création donnera les mêmes options de configuration et le même résultat final pour votre instance.

Utilisez les onglets dans les sections ci-dessous pour sélectionner les instructions de votre expérience préférée.

>[!TIP]
> Vous pouvez également configurer un point de terminaison de liaison privé via le service de liaison privée, plutôt que par le biais de votre instance Azure Digital Twins. Vous obtenez également les mêmes options de configuration et le même résultat final.
>
> Pour plus d’informations sur la configuration des ressources Private Link, consultez la documentation relative à Private Link pour le [Portail Azure](../private-link/create-private-endpoint-portal.md), l’[interface de ligne de commande Azure](../private-link/create-private-endpoint-cli.md), [Azure Resource Manager](../private-link/create-private-endpoint-template.md) ou [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-during-instance-creation"></a>Ajouter un point de terminaison privé pendant la création de l’instance

Dans cette section, vous allez créer un point de terminaison privé avec un lien privé dans le cadre de la configuration initiale d’une instance Azure Digital Twins. Cette action peut uniquement être effectuée dans le Portail Azure.

# <a name="portal"></a>[Portail](#tab/portal)

Cette section décrit comment activer Private Link lors de la configuration d’une instance Azure Digital Twins dans le Portail Azure. 

Les options de liaison privée se trouvent dans l’onglet **Mise en réseau** de la configuration de l’instance.

1. Commencez la configuration d’une instance Azure Digital Twins dans le Portail Azure. Pour obtenir des instructions, consultez [Configurer une instance et l’authentification](how-to-set-up-instance-portal.md).
1. Lorsque vous atteignez l’onglet **Mise en réseau** de la configuration d’instance, vous pouvez activer des points de terminaison privés en sélectionnant l’option **Point de terminaison privé** comme **Méthode de connectivité**.

    Cette étape ajoute une section appelée **Connexions des points de terminaison privés**, dans laquelle vous pouvez configurer les détails de votre point de terminaison privé. Sélectionnez le bouton **Ajouter** pour continuer.
    
    :::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Capture d’écran du portail Azure montrant l’onglet mise en réseau d’une nouvelle instance Azure Digital Twins et mettant en évidence comment créer un point de terminaison privé. Le bouton « Ajouter » est en surbrillance." lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

1. Sur la page **Créer un point de terminaison privé** qui s’ouvre, entrez les informations relatives à un nouveau point de terminaison privé.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="Capture d’écran du portail Azure, affichant la page Créer un point de terminaison privé. Cette page contient les champs décrits ci-dessous.":::

    1. Spécifiez les détails de votre **abonnement** et de votre **groupe de ressources**. Définissez l’option **Emplacement** sur le même emplacement que celui du réseau virtuel que vous allez utiliser. Choisissez le **nom** du point de terminaison puis, pour **Sous-ressources cibles**, sélectionnez *API*.

    1. Sélectionnez ensuite le **réseau virtuel** et le **sous-réseau** que vous souhaitez utiliser pour déployer le point de terminaison.

    1. Pour terminer, indiquez si vous souhaitez **intégrer à une zone DNS privée**. Vous pouvez utiliser la valeur par défaut **Oui** ou, pour obtenir de l’aide sur cette option, vous pouvez suivre le lien du portail pour [en savoir plus sur l’intégration à une zone DNS privée](../private-link/private-endpoint-overview.md#dns-configuration).

    1. Après avoir défini les options de configuration, sélectionnez **OK** pour terminer.

1. Cela vous renverra à l’onglet **Mise en réseau** de la configuration de l’instance Azure Digital Twins. Vérifiez que votre nouveau point de terminaison est visible sous **connexions de point de terminaison privé**.
    
    :::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Capture d’écran du portail Azure montrant l’onglet Mise en réseau d’Azure Digital Twins avec un point de terminaison privé nouvellement créé" lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

1. Utilisez les boutons de navigation inférieurs pour poursuivre la configuration de l’instance.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Vous ne pouvez pas ajouter un point de terminaison Private Link pendant la création de l’instance à l’aide d’Azure CLI. 

Vous pouvez basculer vers le Portail Azure pour ajouter le point de terminaison lors de la création de l’instance, ou passer à la section suivante pour utiliser l’interface CLI afin d’ajouter un point de terminaison privé après la création de l’instance.

--- 

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Ajouter un point de terminaison privé à une instance existante

Dans cette section, vous allez activer Private Link avec un point de terminaison privé pour une instance d’Azure Digital Twins qui existe déjà.

# <a name="portal"></a>[Portail](#tab/portal)

1. Tout d’abord, accédez au [portail Azure](https://portal.azure.com) dans un navigateur. Affichez votre instance Azure Digital Twins en recherchant son nom dans la barre de recherche du portail.

1. Sélectionnez **Mise en réseau (préversion)** dans le menu de gauche.

1. Basculez vers l’onglet **Connexions des points de terminaison privés**.

1. Sélectionnez **Point de terminaison privé** pour ouvrir la boîte de dialogue **Créer un point de terminaison privé**.

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Capture d’écran du portail Azure montrant la page Mise en réseau pour une instance Azure Digital Twins existante et mettant en évidence comment créer des points de terminaison privés." lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1. Dans l’onglet  **De base** , entrez ou sélectionnez l’**abonnement** et le **groupe de ressources** de votre projet, puis le **nom** et la **région** de votre point de terminaison. La région doit être la même que celle du réseau virtuel que vous utilisez.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="Capture d’écran du portail Azure, affichant le premier onglet (De base) de la boîte de dialogue Créer un point de terminaison privé. Il contient les champs décrits ci-dessus.":::

    Quand vous avez terminé, sélectionnez le bouton **Suivant : Bouton Ressource >** pour accéder à l’onglet suivant.

1. Dans l’onglet **Ressource**, entrez ou sélectionnez ces informations : 
    * **Méthode de connexion** : Sélectionnez **Se connecter à une ressource Azure dans mon répertoire** pour rechercher votre instance Azure Digital Twins.
    * **Abonnement**: Entrez votre abonnement.
    * **Type de ressource** : Sélectionnez **Microsoft.DigitalTwins/digitalTwinsInstances**
    * **Ressource** : Sélectionnez le nom de votre instance Azure Digital Twins.
    * **Sous-ressource cible** : Sélectionnez **l’API**.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="Capture d’écran du portail Azure, affichant le deuxième onglet (Ressource) de la boîte de dialogue Créer un point de terminaison privé. Il contient les champs décrits ci-dessus.":::

    Quand vous avez terminé, sélectionnez le bouton **Suivant : Bouton Configuration >** pour accéder à l’onglet suivant.    

1. Dans l’onglet  **Configuration**, entrez ou sélectionnez les informations suivantes :
    * **Réseau virtuel** : Sélectionnez votre réseau virtuel.
    * **Sous-réseau** : Choisissez un sous-réseau de votre réseau virtuel.
    * **Intégrer à une zone DNS privée** : Indiquez si vous souhaitez **intégrer à une zone DNS privée**. Vous pouvez utiliser la valeur par défaut **Oui** ou, pour obtenir de l’aide sur cette option, vous pouvez suivre le lien du portail pour [en savoir plus sur l’intégration à une zone DNS privée](../private-link/private-endpoint-overview.md#dns-configuration).
    Si vous sélectionnez **Oui**, vous pouvez conserver les informations de configuration par défaut.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="Capture d’écran du portail Azure, affichant le troisième onglet (Configuration) de la boîte de dialogue Créer un point de terminaison privé. Il contient les champs décrits ci-dessus.":::

    Lorsque vous avez terminé, vous pouvez sélectionner le bouton **Vérifier + créer** pour terminer l’installation. 

1. Dans l’onglet **Vérifier + créer**, passez en revue vos sélections, puis cliquez sur le bouton  **Créer**. 

Une fois le déploiement du point de terminaison terminé, le point de terminaison devrait apparaître dans les connexions des points de terminaison privés de votre instance Azure Digital Twins.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Pour créer un point de terminaison privé et le lier à une instance Azure Digital Twins à l’aide d’Azure CLI, utilisez la commande [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest&preserve-view=true#az_network_private_endpoint_create). Identifiez l’instance d’Azure Digital Twins à l’aide de son ID complet dans le paramètre `--private-connection-resource-id`.

Voici un exemple qui utilise la commande pour créer un point de terminaison privé, avec uniquement les paramètres obligatoires.

```azurecli-interactive
az network private-endpoint create --connection-name <private-link-service-connection> --name <name-for-private-endpoint> --resource-group <resource-group> --subnet <subnet-ID> --private-connection-resource-id "/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<Azure-Digital-Twins-instance-name>" 
```

Pour obtenir la liste complète des paramètres obligatoires et facultatifs ainsi que d’autres exemples de création de points de terminaison privés, consultez la documentation de référence relative à [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest&preserve-view=true#az_network_private_endpoint_create).

--- 

## <a name="manage-private-endpoint-connections"></a>Gérer les connexions de point de terminaison privé

Dans cette section, vous allez voir comment afficher, modifier et supprimer un point de terminaison privé après sa création.

# <a name="portal"></a>[Portail](#tab/portal)

Une fois qu’un point de terminaison privé a été créé pour votre instance Azure Digital Twins, vous pouvez l’afficher sous l’onglet **Mise en réseau (préversion)** de votre instance Azure Digital Twins. Cette page affichera toutes les connexions de point de terminaison privées associées à l’instance.

:::image type="content" source="media/how-to-enable-private-link/view-endpoint-digital-twins.png" alt-text="Capture d’écran du Portail Azure montrant la page Mise en réseau pour une instance Azure Digital Twins existante avec un point de terminaison privé." lightbox="media/how-to-enable-private-link/view-endpoint-digital-twins.png":::


Sélectionnez le point de terminaison pour afficher ses informations en détail, apporter des modifications à ses paramètres de configuration ou supprimer la connexion.

>[!TIP]
> Le point de terminaison privé peut également s’afficher dans le centre de liaisons privées du portail Azure.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Une fois qu’un point de terminaison privé a été créé pour votre instance d’Azure Digital Twins, vous pouvez utiliser les commandes [az dt network private-endpoint connection](/cli/azure/dt/network/private-endpoint/connection?view=azure-cli-latest&preserve-view=true) pour continuer à gérer les **connexions** de point de terminaison privé de l’instance. Les opérations incluent :
* Afficher une connexion de point de terminaison privé
* Définir l’état de la connexion de point de terminaison privé
* Supprimer la connexion de point de terminaison privé
* Lister toutes les connexions de point de terminaison privé d’une instance

Pour plus d’informations et d’exemples, consultez la documentation de référence relative à [az dt network private-endpoint](/cli/azure/dt/network/private-endpoint?view=azure-cli-latest&preserve-view=true).

### <a name="get-additional-private-link-information"></a>Obtenir des informations supplémentaires sur Private Link

Vous pouvez obtenir des informations supplémentaires sur l’état de Private Link pour votre instance avec les commandes [az dt network private-link](/cli/azure/dt/network/private-link?view=azure-cli-latest&preserve-view=true). Les opérations incluent :
* Lister les liens privés associés à une instance d’Azure Digital Twins
* Afficher un lien privé associé à l’instance

Pour plus d’informations et d’exemples, consultez la documentation de référence relative à [az dt network private-link](/cli/azure/dt/network/private-link?view=azure-cli-latest&preserve-view=true).

--- 

## <a name="disable--enable-public-network-access-flags"></a>Activer/désactiver les indicateurs d’accès au réseau public

Vous pouvez configurer une instance Azure Digital Twins pour refuser toutes les configurations publiques et n’autoriser que les connexions établies par le biais de points de terminaison privés afin d’améliorer la sécurité réseau. Cette action s’effectue à l’aide d’un **indicateur d’accès public au réseau**. 

Cette stratégie vous permet de limiter l’accès de l’API aux connexions de liaison privées uniquement. Lorsque l’indicateur d’accès public au réseau est défini sur *désactivé*, tous les appels de l’API REST vers le plan de données de l’instance Azure Digital Twins à partir du cloud public retournent `403, Unauthorized`. Par ailleurs, lorsque la stratégie est définie sur *désactivé* et qu’une demande est effectuée via un point de terminaison privé, l’appel d’API échoue.

Vous pouvez mettre à jour la valeur de l’indicateur de réseau avec le [Portail Azure](https://portal.azure.com), [Azure CLI](/cli/azure/)ou l’[outil de commande ARMClient](https://github.com/projectkudu/ARMClient).

# <a name="portal"></a>[Portail](#tab/portal-2)

Pour désactiver ou activer l’accès public au réseau dans le [portail Azure](https://portal.azure.com), ouvrez le portail, puis accédez à votre instance Azure Digital Twins.

1. Sélectionnez **Mise en réseau (préversion)** dans le menu de gauche.

1. Sous l’onglet **Accès public**, dans **Autoriser l’accès public au réseau**, sélectionnez **Désactivé** ou **Tous les réseaux**.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Capture d’écran du portail Azure montrant la page Mise en réseau pour une instance Azure Digital Twins et mettant en évidence comment activer ou désactiver l’accès public" lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    Sélectionnez **Enregistrer**.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli-2)

Dans Azure CLI, vous pouvez désactiver ou activer l’accès réseau public en ajoutant un paramètre `--public-network-access` à la commande `az dt create`. Bien que cette commande permette également de créer une instance, vous pouvez l’utiliser pour modifier les propriétés d’une instance existante en lui fournissant le nom de cette dernière. (Pour plus d’informations sur cette commande, consultez sa [documentation de référence](/cli/azure/dt?view=azure-cli-latest&preserve-view=true#az_dt_create) ou les [instructions générales relatives à la configuration d’une instance d’Azure Digital Twins](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)).

Pour **désactiver** l’accès réseau public d’une instance d’Azure Digital Twins, utilisez le paramètre `--public-network-access` comme ceci :

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --public-network-access Disabled
```

Pour **activer** l’accès réseau public d’une instance où il est désactivé, utilisez la commande similaire suivante :

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --public-network-access Enabled
```

# <a name="armclient"></a>[ARMClient](#tab/arm-client-2)

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

---


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les liaisons privées pour Azure : 
* [Qu’est-ce que le service Azure Private Link ?](../private-link/private-link-service-overview.md)