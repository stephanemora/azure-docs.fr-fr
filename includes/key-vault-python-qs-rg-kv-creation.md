---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 59359d37fe347c8568c7944f75accdbc04cddb93
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967118"
---
1. Utilisez la commande `az group create` pour créer un groupe de ressources :

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    Si vous préférez, vous pouvez remplacer « eastus » par un emplacement plus proche de vous.

1. Utilisez `az keyvault create` pour créer le coffre de clés :

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    Remplacez `<your-unique-keyvault-name>` par un nom unique à l’échelle d’Azure. La pratique courante consiste à utiliser son nom ou le nom de son entreprise et à ajouter des chiffres ou des identificateurs. 

1. Créez une variable d’environnement qui fournit le nom du coffre de clés au code :

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```