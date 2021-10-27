---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/26/2021
ms.author: larryfr
ms.openlocfilehash: 6ccdeea7b283ab4e4674e2a7959ead4553c43f2f
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130078019"
---
Lors de l’utilisation de l’__instance de calcul__ (avec une adresse IP publique) ou du __cluster de calcul__ d’Azure Machine Learning, autorisez le trafic entrant à partir de la gestion Azure Batch et des services Azure Machine Learning. L’instance de calcul sans adresse IP publique (préversion) ne nécessite pas cette communication entrante. Un groupe de sécurité réseau qui autorise ce trafic est créé dynamiquement pour vous, mais vous devrez peut-être également créer des itinéraires définis par l’utilisateur (UDR) si vous avez un pare-feu. Lors de la création d’un UDR pour ce trafic, vous pouvez utiliser des **adresses IP** ou des **étiquettes de service** pour router ce trafic.

> [!IMPORTANT]
> L’utilisation de balises de service avec des itinéraires définis par l’utilisateur est actuellement en version préliminaire et peut ne pas être entièrement prise en charge. Pour plus d’informations, consultez [Virtual network routing (routage de réseau virtuel)](../articles/virtual-network/virtual-networks-udr-overview.md#service-tags-for-user-defined-routes-preview).

> [!TIP]
> Bien qu’une instance de calcul sans adresse IP publique (fonctionnalité en préversion) n’ait pas besoin d’UDR pour ce trafic entrant, vous en aurez toujours besoin si vous utilisez également un cluster de calcul ou une instance de calcul avec une adresse IP publique.


# <a name="ip-address-routes"></a>[Itinéraires d’adresses IP](#tab/ipaddress)

Pour le service Azure Machine Learning, vous devez ajouter l’adresse IP pour les régions Azure __primaire__ et __secondaire__. Pour trouver la région secondaire, consultez [Assurer la continuité des activités et la récupération d’urgence à l’aide des régions jumelées Azure](../articles/best-practices-availability-paired-regions.md#azure-regional-pairs). Par exemple, si votre service Azure Machine Learning se trouve dans la région USA Est 2, la région secondaire correspond à USA Centre. 

Pour obtenir la liste des adresses IP du service Batch et Azure Machine Learning service, téléchargez les [plages d’adresses IP Azure et les balises de service](https://www.microsoft.com/download/details.aspx?id=56519), et recherchez `BatchNodeManagement.<region>` et `AzureMachineLearning.<region>` dans le fichier, où `<region>` est votre région Azure.

> [!IMPORTANT]
> Les adresses IP peuvent changer au fil du temps.

Lors de la création de l’UDR, définissez le __Type de tronçon suivant__ sur __Internet__. L’illustration suivante propose un exemple de cette adresse IP basée sur l’UDR dans le portail Azure :

:::image type="content" source="./media/machine-learning-compute-user-defined-routes/user-defined-route.png" alt-text="Image d’une configuration d’itinéraire défini par l’utilisateur":::

# <a name="service-tag-preview-routes"></a>[Itinéraires de balise de service (préversion)](#tab/servicetag)

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
