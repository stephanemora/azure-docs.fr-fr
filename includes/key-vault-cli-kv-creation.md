---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 2d3c7cdcebc244aefb6ef5b2ceb7b931344a8960
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593708"
---
Utilisez la commande Azure CLI [az keyvault create](/cli/azure/keyvault#az_keyvault_create) pour créer un coffre de clés dans le groupe de ressources de l’étape précédente. Vous devrez fournir certaines informations :

- Nom du coffre de clés : chaîne de 3 à 24 caractères qui ne peut contenir que des chiffres (0-9), des lettres (a-z, A-Z) et des traits d’union (-).

  > [!Important]
  > Chaque coffre de clés doit avoir un nom unique. Remplacez \<your-unique-keyvault-name\> par le nom de votre coffre de clés dans les exemples suivants.

- Groupe de ressources nommé : **myResourceGroup**.
- Localisation : **EastUS**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

La sortie de cette commande affiche les propriétés du coffre de clés que vous venez de créer. Notez les deux propriétés ci-dessous :

- **Nom du coffre** : Nom que vous avez fourni au paramètre--name ci-dessus.
- **URI du coffre** : Dans l’exemple, il s’agit de https://&lt;your-unique-keyvault-name&gt;.vault.azure.net/. Les applications qui utilisent votre coffre via son API REST doivent utiliser cet URI.

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.
