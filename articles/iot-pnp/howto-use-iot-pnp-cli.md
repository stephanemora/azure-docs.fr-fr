---
title: Utiliser l’extension Azure IoT pour Azure CLI afin d’interagir avec des appareils IoT Plug-and-Play en préversion | Microsoft Docs
description: Installez l’extension Azure IoT pour Azure CLI et utilisez-la pour interagir avec les appareils IoT Plug-and-Play connectés à mon hub IoT.
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: dadb1f044547acd6e5f0d274143123e89d7dae46
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475479"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Installer et utiliser l’extension Azure IoT pour Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) est un outil en ligne de commande open source et multiplateforme, destiné à la gestion des ressources Azure, telles qu’IoT Hub. Azure CLI est disponible sur Windows, Linux et MacOS. Azure CLI vous permet de gérer les ressources Azure IoT Hub, les instances du service de provisionnement des appareils et les hubs liés sans installer aucune extension.

L’extension Azure IoT pour Azure CLI est un outil en ligne de commande permettant d’interagir avec des appareils IoT Plug-and-Play en préversion et de les tester. Vous pouvez utiliser l’extension pour :

- Se connecter à un appareil.
- Afficher les données de télémétrie envoyées par l’appareil.
- Travailler avec les propriétés des appareils.
- Appeler les commandes d’appareil.

Cet article vous montre comment :

- Installer et configurer l’extension Azure IoT pour Azure CLI.
- Utiliser l’extension pour interagir avec vos appareils et les tester.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Installer l’extension Azure IoT pour Azure CLI

### <a name="step-1---install-the-azure-cli"></a>Étape 1 - Installer Azure CLI

Suivez les [instructions d’installation](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pour configurer Azure CLI dans votre environnement. Pour une expérience optimale, votre version d’Azure CLI doit être la version 2.9.1 ou ultérieure. Utilisez `az -–version` pour valider.

### <a name="step-2---install-iot-extension"></a>Étape 2 - Installer l’extension IoT

Le [Lisez-moi de l’extension IoT](https://github.com/Azure/azure-iot-cli-extension) décrit différentes manières d’installer l’extension. Le plus simple consiste à exécuter `az extension add --name azure-iot`. Après l’installation, vous pouvez utiliser `az extension list` pour valider les extensions actuellement installées ou `az extension show --name azure-iot` pour afficher les détails concernant l’extension IoT. Au moment de la rédaction de cet article, le numéro de version de l’extension est `0.9.7`.

Pour supprimer l’extension, vous pouvez utiliser `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Utiliser l’extension Azure IoT pour Azure CLI

### <a name="prerequisites"></a>Prérequis

Pour vous connecter à votre abonnement Azure, exécutez la commande suivante :

```azurecli
az login
```

Pour utiliser l’extension Azure IoT pour Azure CLI, vous avez besoin :

- Un IoT Hub Azure. Il existe de nombreuses façons d’ajouter un hub IoT à votre abonnement Azure, par exemple avec la [création d’un hub IoT via Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Vous avez besoin de la chaîne de connexion du hub IoT pour exécuter les commandes de l’extension Azure IoT. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Un appareil inscrit dans votre hub IoT. Vous pouvez utiliser la commande Azure CLI suivante pour inscrire un appareil et veiller à remplacer les espaces réservés `{YourIoTHubName}` et `{YourDeviceID}` par vos valeurs :

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

### <a name="interact-with-a-device"></a>Interagir avec un appareil

Vous pouvez utiliser l’extension pour afficher et interagir avec les appareils IoT Plug-and-Play connectés à un hub IoT. L’extension fonctionne avec le jumeau numérique qui représente l’appareil IoT Plug-and-Play.

#### <a name="list-devices"></a>Faire la liste des appareils

Listez tous les appareils figurant sur un hub IoT :

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

#### <a name="view-digital-twin"></a>Afficher le jumeau numérique

Affichez le jumeau numérique d’un appareil :

```azurecli
az iot pnp twin show -n {iothub_name} -d {device_id}
```

#### <a name="properties"></a>Propriétés

Définissez la valeur d’une propriété en lecture-écriture :

```azurecli
az iot pnp twin update --hub-name {iothub_name} --device-id {device_id} --json-patch '{"op":"add", "path":"/thermostat1/targetTemperature", "value": 54}'
```

#### <a name="commands"></a>Commandes

Appelez une commande :

```azurecli
az iot pnp twin invoke-command --cn getMaxMinReport -n {iothub_name} -d {device_id} --component-path thermostat1
```

#### <a name="digital-twin-events"></a>Événements de jumeau numérique

Surveillez tous les événements de jumeau numérique IoT Plug-and-Play à partir d’un appareil et d’une interface spécifiques en accédant au groupe de consommateurs du hub d’événements **$Default** :

```azurecli
az iot hub monitor-events -n {iothub_name} -d {device_id} -i {interface_id}
```

### <a name="manage-models-in-the-model-repository"></a>Gérer les modèles dans le référentiel modèle

Vous pouvez utiliser les commandes de référentiel de modèle CLI Azure pour gérer les modèles dans le référentiel.

#### <a name="create-model-repository"></a>Créer un référentiel de modèles

Créez un référentiel d’entreprise IoT Plug-and-Play pour votre locataire si vous êtes le premier utilisateur de votre locataire :

```azurecli
az iot pnp repo create
```

#### <a name="manage-model-repository-tenant-roles"></a>Gérer les rôles de locataire de référentiel de modèles

Créez une attribution de rôle pour un utilisateur ou un principal de service pour une ressource spécifique.

Par exemple, donnez à user@consoso.com le rôle de **ModelsCreator** pour le locataire :

```azurecli
az iot pnp role-assignment create --resource-id {tenant_id} --resource-type Tenant --subject-id {user@contoso.com} --subject-type User --role ModelsCreator
```

Ou donnez à user@consoso.com le rôle de **ModelAdministrator** pour un modèle spécifique :

```azurecli
az iot pnp role-assignment create --resource-id {model_id} --resource-type Model --subject-id {user@contoso.com} --subject-type User --role ModelAdministrator
```

#### <a name="create-a-model"></a>Créer un modèle

Créez un nouveau modèle dans le référentiel de l’entreprise :

```azurecli
az iot pnp model create --model {model_json or path_to_file}
```

#### <a name="search-a-model"></a>Rechercher un modèle

Répertorier les modèles correspondant à un mot clé spécifique :

```azurecli
az iot pnp model list -q {search_keyword}
```

#### <a name="publish-a-model"></a>Publier un modèle

Publiez un modèle d’appareil situé dans le référentiel de l’entreprise dans le référentiel public.

Par exemple, rendez public le modèle avec l’ID `dtmi:com:example:ClimateSensor;1`:

```azurecli
az iot pnp model publish --dtmi "dtmi:com:example:ClimateSensor;1"
```

Pour publier un modèle, les conditions suivantes doivent être remplies :

- Le locataire de l’entreprise ou de l’organisation doit être un partenaire Microsoft. 
- L'utilisateur ou le principal du service doit être membre du rôle d'éditeur du **Locataire** du référentiel.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à installer et à utiliser l’extension Azure IoT pour Azure CLI afin d’interagir avec vos appareils Plug-and-Play. À présent, nous vous invitons à découvrir comment utiliser l’[explorateur Azure IoT avec vos appareils](./howto-use-iot-explorer.md).
