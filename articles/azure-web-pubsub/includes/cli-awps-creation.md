---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 785114c797fba5c84e97f5a0d0cc1a34dc14d540
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121782444"
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

> [!Note]
> Étant donné que le service Web PubSub est toujours en préversion, vous avez peut-être remarqué qu’un avertissement s’affiche dans la console CLI, indiquant « La commande requiert l’extension webpubsub. Voulez-vous l’installer maintenant ? La commande continuera à s’exécuter après l’installation de l’extension. (O/N) ». Appuyez sur O pour installer l’extension.

La sortie de cette commande affiche les propriétés de la ressource que vous venez de créer. Notez les deux propriétés ci-dessous :

- **Nom de la ressource** : nom que vous avez fourni au paramètre `--name` ci-dessus.
- **Nom d’hôte** : dans l’exemple, le nom d’hôte est `<your-unique-resource-name>.webpubsub.azure.com/`.

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur cette nouvelle ressource.
