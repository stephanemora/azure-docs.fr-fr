---
title: Configurer les paramètres d’application pour Azure Static Web Apps
description: Apprendre à configurer les paramètres d’application pour Azure Static Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 09/23/2021
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: a104860eb72a6376c2ab337f31bb06fd041f42a0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597091"
---
# <a name="configure-application-settings-for-azure-static-web-apps"></a>Configurer les paramètres d’application pour Azure Static Web Apps

Les paramètres d’application contiennent les paramètres de configuration de valeurs susceptibles d’être modifiées, comme les chaînes de connexion de base de données. L’ajout de paramètres d’application vous permet de modifier l’entrée de configuration de votre application, sans avoir à modifier le code de l’application.

Paramètres de l’application :

- Sont disponibles en tant que variables d’environnement pour l’API back-end d’une application web statique
- Peuvent être utilisés pour stocker les secrets utilisés dans la [configuration de l’authentification](key-vault-secrets.md)
- Sont chiffrés au repos
- Sont copiés dans les environnements [intermédiaire](review-publish-pull-requests.md) et de production
- Ne peut contenir que des caractères alphanumériques, `.` et `_`

> [!IMPORTANT]
> Les paramètres d’application décrits dans cet article s’appliquent uniquement à l’API back-end d’une application Azure Static Web Apps.
>
> Pour configurer les variables d’environnement requises pour générer votre application web frontale, consultez [Configuration de build](build-configuration.md#environment-variables).

## <a name="prerequisites"></a>Prérequis

- Une application Azure Static Web Apps
- [Azure CLI](/cli/azure/install-azure-cli) : obligatoire si vous utilisez la ligne de commande

## <a name="configure-api-application-settings-for-local-development"></a>Configurer des paramètres d’application d’API pour le développement local

Les API dans Azure Static Web Apps sont fournies par Azure Functions, ce qui vous permet de définir des paramètres d’application dans le fichier _local.settings.json_ lorsque vous exécutez l’application localement. Ce fichier définit les paramètres d’application dans la propriété `Values` de la configuration.

> [!NOTE]
> Le fichier _local.settings.json_ est utilisé uniquement pour le développement local. Utilisez le [portail Azure](https://portal.azure.com) pour configurer des paramètres d’application pour la production.

L’exemple suivant _local.settings.json_ montre comment ajouter une valeur pour `DATABASE_CONNECTION_STRING`.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

Les paramètres définis dans la propriété `Values` peuvent être référencés à partir du code en tant que variables d’environnement. Dans les fonctions Node.js, par exemple, ils sont disponibles dans l’objet `process.env`.

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

Le fichier `local.settings.json` n’est pas suivi par le référentiel GitHub, car les informations sensibles, comme les chaînes de connexion de base de données, sont souvent incluses dans le fichier. Étant donné que les paramètres locaux restent sur votre ordinateur, vous devez configurer manuellement vos paramètres dans Azure.

La configuration de vos paramètres est rarement effectuée et n’est pas nécessaire à chaque build.

## <a name="configure-application-settings"></a>Configurer les paramètres de l’application

Vous pouvez configurer les paramètres d’application via le Portail Azure ou avec l’interface de ligne de commande Azure.

### <a name="use-the-azure-portal"></a>Utiliser le portail Azure

Le portail Azure fournit une interface pour la création, la mise à jour et la suppression des paramètres d’application.

1. Accédez au [portail Azure](https://portal.azure.com).

1. Dans la barre de recherche, recherchez et sélectionnez **Static Web Apps**.

1. Cliquez sur l’option de **configuration** dans la barre latérale.

1. Sélectionnez l’environnement auquel vous souhaitez appliquer les paramètres d’application. Les environnements intermédiaires sont automatiquement créés quand une demande de tirage (pull request) est générée et sont promus en production une fois que la demande de tirage est fusionnée. Vous pouvez définir les paramètres d’application par environnement.

1. Cliquez sur le bouton **Ajouter** pour ajouter un nouveau paramètre d’application.

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Affichage de la configuration Azure Static Web Apps":::

1. Entrez un **nom** et une **valeur**.

1. Cliquez sur **OK**.

1. Cliquez sur **Enregistrer**.

### <a name="use-the-azure-cli"></a>Utiliser Azure CLI

Vous pouvez utiliser la commande `az rest` pour effectuer des chargements par lots de vos paramètres sur Azure. La commande accepte les paramètres d’application en tant qu’objets JSON dans une propriété parent appelée `properties`.

Le moyen le plus simple de créer un fichier JSON avec les valeurs appropriées consiste à créer une version modifiée de votre fichier _local.settings.json_.

1. Pour vous assurer que votre nouveau fichier contenant des données sensibles n’est pas exposé publiquement, ajoutez l’entrée suivante dans votre fichier _.gitignore_.

   ```bash
   local.settings*.json
   ```

1. Ensuite, faites une copie de votre fichier _local.settings.json_ et nommez-le _local.settings.properties.json_.

1. Dans ce nouveau fichier, supprimez toutes les autres données, à l’exception des paramètres d’application et renommez `Values` en `properties`.

   Votre fichier doit maintenant ressembler à l’exemple suivant :

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

1. Exécutez la commande suivante pour répertorier les applications web statiques de votre abonnement et afficher leurs détails.

    ```bash
    az staticwebapp list -o json
    ```

    Recherchez l’application web statique que vous souhaitez configurer et notez son identifiant.

1. Dans un terminal ou une ligne de commande, exécutez la commande suivante pour charger les paramètres. Remplacez `<YOUR_APP_ID>` par l’identifiant de l’application que vous avez récupéré à l’étape précédente.

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "<YOUR_APP_ID>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

  > [!IMPORTANT]
  > Le fichier « local.settings.properties.json » doit se trouver dans le même répertoire que celui où cette commande est exécutée. Ce fichier peut porter le nom que vous souhaitez. Ce nom n’a pas d’importance.

### <a name="view-application-settings-with-the-azure-cli"></a>Voir les paramètres d’application avec Azure CLI

Les paramètres d’application peuvent être consultés via l’interface de ligne de commande Azure.

- Dans un terminal ou une ligne de commande, exécutez la commande suivante. Veillez à remplacer l’espace réservé `<YOUR_APP_ID>` par votre valeur.

   ```bash
   az rest --method post --uri "<YOUR_APP_ID>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer des infrastructures frontales](front-end-frameworks.md)
