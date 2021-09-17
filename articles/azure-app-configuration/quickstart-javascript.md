---
title: Démarrage rapide pour utiliser Azure App Configuration avec des applications JavaScript | Microsoft Docs
description: Dans ce guide de démarrage rapide, créez une application Node.js avec Azure App Configuration pour centraliser le stockage et la gestion des paramètres d’application en dehors de votre code.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: quickstart
ms.date: 07/12/2021
ms.author: drewbat
ms.openlocfilehash: 850771db454f854243ac9ba242d02ed1911f255c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785189"
---
# <a name="quickstart-create-a-javascript-app-with-azure-app-configuration"></a>Démarrage rapide : Créer une application JavaScript avec Azure App Configuration

Dans ce guide de démarrage rapide, vous utilisez Azure App Configuration pour centraliser le stockage et la gestion des paramètres d’une application à l’aide de la [bibliothèque de client App Configuration pour JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/appconfiguration/app-configuration/README.md).

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
- [Versions LTS de Node.js](https://nodejs.org/en/about/releases/). Pour plus d’informations sur l’installation de Node.js soit directement sur Windows, soit à l’aide du Sous-système Windows pour Linux (WSL), consultez [Prise en main de Node.js](/windows/dev-environment/javascript/nodejs-overview)

## <a name="create-an-app-configuration-store"></a>Créer un magasin App Configuration

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Sélectionnez **Explorateur de configurations** > **Créer** > **Clé-valeur** pour ajouter les paires clé-valeur suivantes :

    | Clé | Valeur |
    |---|---|
    | TestApp:Settings:Message | Data from Azure App Configuration |

    Laissez **Étiquette** et **Type de contenu** vides pour l’instant.

8. Sélectionnez **Appliquer**.

## <a name="setting-up-the-nodejs-app"></a>Configurer l’application Node.js

1. Dans ce tutoriel, vous allez créer un répertoire pour le projet nommé *app-configuration-quickstart*.

    ```console
    mkdir app-configuration-quickstart
    ```

1. Basculez vers le répertoire *app-configuration-quickstart*.

    ```console
    cd app-configuration-quickstart
    ```

1. Installez la bibliothèque de client Azure App Configuration à l’aide de la commande `npm install`.

    ```console
    npm install @azure/app-configuration
    ```

1. Créez un fichier appelé *app.js* dans le répertoire *app-configuration-quickstart*, puis ajoutez le code suivant :

   ```javascript
   const appConfig = require("@azure/app-configuration");
   ```

## <a name="configure-your-connection-string"></a>Configurer votre chaîne de connexion

1. Définissez une variable d’environnement nommée **AZURE_APP_CONFIG_CONNECTION_STRING** et affectez-lui la valeur de la clé d’accès à votre magasin App Configuration. Sur la ligne de commande, exécutez la commande suivante :

    ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    ### <a name="command-line"></a>[Ligne de commande](#tab/command-line)

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    ### <a name="macos"></a>[macOS](#tab/macOS)
    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

    ---

2. Redémarrez l’invite de commandes pour que la modification soit prise en compte. Imprimez la valeur de la variable d’environnement pour confirmer qu’elle est correctement définie.

## <a name="connect-to-an-app-configuration-store"></a>Se connecter à un magasin App Configuration

L’extrait de code suivant crée une instance d’**AppConfigurationClient** à l’aide de la chaîne de connexion stockée dans vos variables d’environnement.

```javascript
const connection_string = process.env.AZURE_APP_CONFIG_CONNECTION_STRING;
const client = new appConfig.AppConfigurationClient(connection_string);
```

## <a name="get-a-configuration-setting"></a>Obtenir un paramètre de configuration

L’extrait de code suivant récupère un paramètre de configuration par nom de clé (`key`). La clé indiquée dans cet exemple a été créée dans les étapes précédentes de cet article.

```javascript
async function run() {
  
  let retrievedSetting = await client.getConfigurationSetting({
    key: "TestApp:Settings:Message"
  });

  console.log("Retrieved value:", retrievedSetting.value);
}

run().catch((err) => console.log("ERROR:", err));
```

## <a name="build-and-run-the-app-locally"></a>Générer et exécuter l’application localement

1. Exécutez la commande suivante pour exécuter l’application Node.js :

   ```powershell
   node app.js
   ```
1. Vous devriez voir la sortie suivante s'affiche dans l'invite de commandes :

   ```powershell
   Retrieved value: Data from Azure App Configuration
   ```
## <a name="clean-up-resources"></a>Nettoyer les ressources


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un magasin App Configuration et appris à accéder aux paires clé/valeur à partir d’une application Node.js.

Pour obtenir des exemples de code supplémentaires, visitez :

> [!div class="nextstepaction"]
> [Exemples pour la bibliothèque de client Azure App Configuration](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)
