---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 13d4bb94d3efb574895df6cfdf240cdeec77e539
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070187"
---
Utilisez l’applet de commande Azure PowerShell [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) pour créer un coffre de clés dans le groupe de ressources de l’étape précédente. Vous devrez fournir certaines informations :

- Nom du coffre de clés : chaîne de 3 à 24 caractères qui ne peut contenir que des chiffres (0-9), des lettres (a-z, A-Z) et des traits d’union (-).

  > [!Important]
  > Chaque coffre de clés doit avoir un nom unique. Remplacez <your-unique-keyvault-name> par le nom de votre coffre de clés dans les exemples suivants.

- Groupe de ressources nommé : **myResourceGroup**.
- Localisation : **EastUS**.

```azurepowershell-interactive
New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "ContosoResourceGroup" -Location "East US"
```

La sortie de cette cmdlet affiche les propriétés du coffre de clés que vous venez de créer. Notez les deux propriétés ci-dessous :

- **Nom du coffre** : Nom que vous avez fourni au paramètre--name ci-dessus.
- **URI du coffre** : Dans l’exemple, il s’agit de https://&lt;your-unique-keyvault-name&gt;.vault.azure.net/. Les applications qui utilisent votre coffre via son API REST doivent utiliser cet URI.

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.
