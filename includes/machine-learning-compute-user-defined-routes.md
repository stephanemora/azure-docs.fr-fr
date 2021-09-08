---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/12/2021
ms.author: larryfr
ms.openlocfilehash: c2ad7408f00d8abf4cb5afdbdba44af5e0779380
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122603965"
---
Lors de l’utilisation de l’__instance de calcul__ ou du __cluster de calcul__ d’Azure Machine Learning, autorisez le trafic entrant à partir de la Azure Batch et des services Azure Machine Learning. Lors de la création d’itinéraires définis par l’utilisateur pour ce trafic, vous pouvez utiliser soit les **adresses IP** soit les **balises de service** pour définir l’itinéraire de ce trafic.

> [!IMPORTANT]
> L’utilisation de balises de service avec des itinéraires définis par l’utilisateur est actuellement en version préliminaire et peut ne pas être entièrement prise en charge. Pour plus d’informations, consultez [Virtual network routing (routage de réseau virtuel)](/azure/virtual-network/virtual-networks-udr-overview#service-tags-for-user-defined-routes-preview).

# <a name="ip-address-routes"></a>[Itinéraires d’adresses IP](#tab/ipaddress)

Pour le service Azure Machine Learning, vous devez ajouter l’adresse IP pour les régions Azure __primaire__ et __secondaire__. Pour trouver la région secondaire, consultez [Assurer la continuité des activités et la récupération d’urgence à l’aide des régions jumelées Azure](/azure/best-practices-availability-paired-regions#azure-regional-pairs). Par exemple, si votre service Azure Machine Learning se trouve dans la région USA Est 2, la région secondaire correspond à USA Centre. 

Pour obtenir la liste des adresses IP du service Batch et du service Azure Machine Learning, utilisez l’une des méthodes suivantes :

* Téléchargez les [plages d’adresses IP Azure et les balises de service](https://www.microsoft.com/download/details.aspx?id=56519), et recherchez `BatchNodeManagement.<region>` et `AzureMachineLearning.<region>` dans le fichier, où `<region>` est votre région Azure.

* Utilisez [Azure CLI](/cli/azure/install-azure-cli) pour télécharger les informations. L’exemple suivant télécharge les informations d’adresse IP et filtre les informations pour la région USA Est 2 (primaire) et la région USA Centre (secondaire) :

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    # Get primary region IPs
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    # Get secondary region IPs
    az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
    ```

    > [!TIP]
    > Si vous utilisez les régions USA Virginie, USA Arizona ou les régions Chine Est 2, ces commandes ne retournent aucune adresse IP. Utilisez plutôt un des liens suivants pour télécharger une liste d’adresses IP :
    >
    > * [Plages d’adresses IP et étiquettes de service pour Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [Plages d’adresses IP et étiquettes de service pour Azure Chine](https://www.microsoft.com//download/details.aspx?id=57062)

> [!IMPORTANT]
> Les adresses IP peuvent changer au fil du temps.

Lors de la création de l’UDR, définissez le __Type de tronçon suivant__ sur __Internet__. L’illustration suivante propose un exemple de cette adresse IP basée sur l’UDR dans le portail Azure :

:::image type="content" source="./media/machine-learning-compute-user-defined-routes/user-defined-route.png" alt-text="Image d’une configuration d’itinéraire défini par l’utilisateur":::

# <a name="service-tag-routes"></a>[Itinéraires de balise du service](#tab/servicetag)

Créer des itinéraires définis par l’utilisateur pour les balises de service suivantes :

* `AzureMachineLearning`
* `BatchNodeManagement.<region>`, où se trouve`<region>` votre région Azure.

Les commandes suivantes illustrent l’ajout d’itinéraires pour ces balises de service :

```azurecli
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n AzureMLRoute --address-prefix AzureMachineLearning --next-hop-type Internet
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n BatchRoute --address-prefix BatchNodeManagement.westus2 --next-hop-type Internet
```

---

Pour plus d’informations sur la configuration de l’UDR, consultez [Définir l’itinéraire du trafic réseau à l’aide d’une table de routage](/azure/virtual-network/tutorial-create-route-table-portal).