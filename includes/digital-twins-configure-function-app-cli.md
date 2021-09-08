---
author: baanders
description: inclure un fichier décrivant comment configurer une fonction Azure pour fonctionner avec les instructions Azure Digital Twins - CLI
ms.service: digital-twins
ms.topic: include
ms.date: 7/20/2021
ms.author: baanders
ms.openlocfilehash: fffa878ca69b0704417c7db7492eb5523217e4e3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801306"
---
Exécutez les commandes suivantes dans [Azure Cloud Shell](https://shell.azure.com) ou une [interface locale d’Azure CLI](/cli/azure/install-azure-cli).

#### <a name="assign-an-access-role"></a>Attribuer un rôle d’accès

La fonction Azure nécessite un jeton de porteur pour transmission. Pour vous assurer que le jeton du porteur est transmis, accordez à l’Application de fonction le rôle de **Propriétaire des données Azure Digital Twins** pour votre instance Azure Digital Twins, ce qui permettra à l’application de fonction d’effectuer des activités de plan de données sur l’instance.

[!INCLUDE [digital-twins-permissions-required.md](digital-twins-permissions-required.md)]

1. Utilisez la commande suivante pour consulter les détails de l’[identité managée par le système](../articles/active-directory/managed-identities-azure-resources/overview.md) de votre fonction. Prenez note du champ `principalId` dans la sortie. Vous utiliserez cet ID pour faire référence à la fonction afin de pouvoir lui accorder des autorisations à l’étape suivante.

    ```azurecli-interactive 
    az functionapp identity show --resource-group <your-resource-group> --name <your-function-app-name> 
    ```

    >[!NOTE]
    > Si le résultat est vide au lieu d’afficher les détails d’une identité, créez une autre identité managée par le système pour la fonction à l’aide de cette commande :
    > 
    >```azurecli-interactive    
    >az functionapp identity assign --resource-group <your-resource-group> --name <your-function-app-name>  
    >```
    >
    > La sortie affiche alors les détails de l’identité, notamment la valeur `principalId` nécessaire pour la prochaine étape. 

1. Utilisez la `principalId` valeur dans la commande suivante afin d’affecter à la fonction le rôle de **Propriétaire des données Azure Digital Twins** pour votre instance Azure Digital Twins.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

#### <a name="configure-application-settings"></a>Configurer les paramètres de l’application

Enfin, rendez l’URL de votre **instance Azure Digital Twins** accessible à votre fonction en lui définissant une [variable d’environnement](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal#use-application-settings).

> [!TIP]
> L’URL de l’instance Azure Digital Twins est créée en ajoutant *https://* au début du nom d’hôte de votre instance. Pour afficher le nom d’hôte, ainsi que toutes les propriétés de votre instance, exécutez `az dt show --dt-name <your-Azure-Digital-Twins-instance>`.

La commande suivante définit une variable d’environnement pour l’URL d’instance que votre fonction utilisera chaque fois qu’elle aura besoin d’accéder à l’instance.

```azurecli-interactive 
az functionapp config appsettings set --resource-group <your-resource-group> --name <your-function-app-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```