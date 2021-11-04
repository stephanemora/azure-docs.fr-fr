---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 3671a5d221afa89c776dca1bc3a17b965b621c5b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423986"
---
Utilisez la commande [az webpubsub Create](/cli/azure/webpubsub#az_webpubsub_create) Azure CLI pour créer un Web PubSub dans le groupe de ressources à partir de l’étape précédente, en fournissant les informations suivantes :

- Nom de la ressource : chaîne de 3 à 24 caractères qui ne peut contenir que des chiffres (0-9), des lettres (a-z, A-Z) et des traits d’union (-)

  > [!Important]
  > Chaque ressource Web PubSub doit avoir un nom unique. Remplacez &lt;your-unique-keyvault-name&gt; par le nom de votre Web PubSub dans les exemples suivants.

- Groupe de ressources nommé : **myResourceGroup**.
- Localisation : **EastUS**.
- Référence SKU : **Gratuit_F1**

```azurecli-interactive
az webpubsub create --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --location "EastUS" --sku Free_F1
```

La sortie de cette commande affiche les propriétés de la ressource que vous venez de créer. Notez les deux propriétés ci-dessous :

- **Nom de la ressource** : nom que vous avez fourni au paramètre `--name` ci-dessus.
- **Nom d’hôte** : dans l’exemple, le nom d’hôte est `<your-unique-resource-name>.webpubsub.azure.com/`.

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur cette nouvelle ressource.
