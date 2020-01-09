---
title: Utiliser l’extension Azure IoT pour Azure CLI afin d’interagir avec des appareils IoT Plug-and-Play en préversion | Microsoft Docs
description: Installez l’extension Azure IoT pour Azure CLI et utilisez-la pour interagir avec les appareils IoT Plug-and-Play connectés à mon hub IoT.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: f4e255309d32026b7f2bb4de67fce7ec37c753f6
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529573"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Installer et utiliser l’extension Azure IoT pour Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) est un outil en ligne de commande open source et multiplateforme, destiné à la gestion des ressources Azure, telles qu’IoT Hub. Azure CLI est disponible sur Windows, Linux et MacOS. Azure CLI est également préinstallé dans [Azure Cloud Shell](https://shell.azure.com). Azure CLI vous permet de gérer les ressources Azure IoT Hub, les instances du service de provisionnement des appareils et les hubs liés sans installer aucune extension.

L’extension Azure IoT pour Azure CLI est un outil en ligne de commande permettant d’interagir avec des appareils IoT Plug-and-Play en préversion et de les tester. Vous pouvez utiliser l’extension pour :

- Se connecter à un appareil.
- Afficher les données de télémétrie envoyées par l’appareil.
- Travailler avec les propriétés des appareils.
- Appeler les commandes d’appareil.

Cet article vous montre comment :

- Installer et configurer l’extension Azure IoT pour Azure CLI.
- Utiliser l’extension pour interagir avec vos appareils et les tester.
- Utiliser l’extension pour gérer les interfaces dans le référentiel de modèles.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Installer l’extension Azure IoT pour Azure CLI

### <a name="step-1---install-the-azure-cli"></a>Étape 1 - Installer Azure CLI

Suivez les [instructions d’installation](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pour configurer Azure CLI dans votre environnement. Pour utiliser toutes les commandes ci-dessous, votre version d’Azure CLI doit être la version 2.0.73 ou ultérieure. Utilisez `az -–version` pour valider.

### <a name="step-2---install-iot-extension"></a>Étape 2 - Installer l’extension IoT

Le [Lisez-moi de l’extension IoT](https://github.com/Azure/azure-iot-cli-extension) décrit différentes manières d’installer l’extension. Le plus simple consiste à exécuter `az extension add --name azure-cli-iot-ext`. Après l’installation, vous pouvez utiliser `az extension list` pour valider les extensions actuellement installées ou `az extension show --name azure-cli-iot-ext` pour afficher les détails concernant l’extension IoT. Pour supprimer l’extension, vous pouvez utiliser `az extension remove --name azure-cli-iot-ext`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Utiliser l’extension Azure IoT pour Azure CLI

### <a name="prerequisites"></a>Conditions préalables requises

Pour vous connecter à votre abonnement Azure, exécutez la commande suivante :

```cmd/sh
az login
```

> [!NOTE]
> Si vous utilisez Azure Cloud Shell, vous êtes automatiquement connecté et vous n’avez pas besoin d’exécuter la commande précédente.

Pour utiliser l’extension Azure IoT pour Azure CLI, vous avez besoin :

- Un IoT Hub Azure. Il existe de nombreuses façons d’ajouter un hub IoT à votre abonnement Azure, par exemple avec la [création d’un hub IoT via Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Vous avez besoin de la chaîne de connexion du hub IoT pour exécuter les commandes de l’extension Azure IoT. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

    > [!NOTE]
    > Dans le cadre de la préversion publique, les fonctionnalités IoT Plug-and-Play sont disponibles uniquement sur les hubs IoT créés dans les régions **USA Centre**, **Europe Nord** et **Japon Est**.

- Un appareil inscrit dans votre hub IoT. Vous pouvez utiliser la commande Azure CLI suivante pour inscrire un appareil et veiller à remplacer les espaces réservés `{YourIoTHubName}` et `{YourDeviceID}` par vos valeurs :

    ```cmd/sh
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Certaines commandes ont besoin de la chaîne de connexion pour le référentiel de modèles d’une entreprise. Un référentiel de modèles pour votre entreprise est créé lors de votre première [intégration au portail Azure Certified pour IoT](howto-onboard-portal.md). Un tiers peut partager sa chaîne de connexion de référentiel de modèles avec vous pour vous permettre d’accéder à ses interfaces et modèles.

### <a name="interact-with-a-device"></a>Interagir avec un appareil

Vous pouvez utiliser l’extension pour afficher et interagir avec les appareils IoT Plug-and-Play connectés à un hub IoT. L’extension fonctionne avec le jumeau numérique qui représente l’appareil IoT Plug-and-Play.

#### <a name="list-devices-and-interfaces"></a>Lister les appareils et les interfaces

Listez tous les appareils figurant sur un hub IoT :

```cmd/sh
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Listez toutes les interfaces inscrites par un appareil IoT Plug-and-Play :

```cmd/sh
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Propriétés

Listez toutes les propriétés et les valeurs de propriété pour une interface sur un appareil :

```cmd/sh
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Définissez la valeur d’une propriété en lecture-écriture :

```cmd/sh
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Un exemple de fichier de charge utile pour définir la propriété **name** de l’interface **sensor** d’un appareil sur **Contoso** ressemble à ce qui suit :

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>Commandes

Listez toutes les commandes pour une interface sur un appareil :

```cmd/sh
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Sans le paramètre `--repo-login`, cette commande utilise le référentiel de modèles public.

Appelez une commande :

```cmd/sh
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Événements de jumeau numérique

Surveillez tous les événements de jumeau numérique IoT Plug-and-Play à partir d’un appareil et d’une interface spécifiques en accédant au groupe de consommateurs du hub d’événements **$Default** :

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Surveillez tous les événements de jumeau numérique IoT Plug-and-Play à partir d’un appareil et d’une interface spécifiques en accédant à un groupe de consommateurs spécifique :

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Gérer les interfaces dans un référentiel de modèles

Les commandes suivantes utilisent le référentiel de modèles IoT Plug-and-Play public. Pour utiliser un référentiel de modèles d’entreprise, ajoutez l’argument `--login` avec votre chaîne de connexion de référentiel de modèles.

Listez les interfaces figurant dans le référentiel de modèles IoT Plug-and-Play public :

```cmd/sh
az iot pnp interface list
```

Montrez une interface figurant dans le référentiel de modèles IoT Plug-and-Play public :

```cmd/sh
az iot pnp interface show --interface {YourInterfaceId}
```

Créez une interface dans votre référentiel de modèles d’entreprise IoT Plug-and-Play :

```cmd/sh
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Vous ne pouvez pas créer directement une interface dans le référentiel de modèles public.

Mettez à jour une interface dans votre référentiel de modèles d’entreprise IoT Plug-and-Play :

```cmd/sh
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Vous ne pouvez pas mettre à jour directement une interface dans le référentiel de modèles public.

Publiez une interface à partir de votre référentiel de modèles d’entreprise IoT Plug-and-Play dans le référentiel de modèles public. Cette opération rend l’interface immuable :

```cmd/sh
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Seuls les partenaires Microsoft peuvent publier des interfaces dans le référentiel de modèles public.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Gérer les modèles de capacité d’appareil dans un référentiel de modèles

Les commandes suivantes utilisent le référentiel de modèles IoT Plug-and-Play public. Pour utiliser un référentiel de modèles d’entreprise, ajoutez l’argument `--login` avec votre chaîne de connexion de référentiel de modèles.

Listez les modèles de capacité d’appareil dans le référentiel de modèles public IoT Plug-and-Play :

```cmd/sh
az iot pnp capability-model list
```

Montrez un modèle de capacité d’appareil dans le référentiel de modèles public IoT Plug-and-Play :

```cmd/sh
az iot pnp capability-model show --model {YourModelID}
```

Créez un modèle de capacité d’appareil dans un référentiel de modèles d’entreprise IoT Plug-and-Play :

```cmd/sh
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Vous ne pouvez pas créer directement un modèle dans le référentiel de modèles public.

Mettez à jour un modèle de capacité d’appareil dans le référentiel de modèles d’entreprise IoT Plug-and-Play :

```cmd/sh
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Vous ne pouvez pas mettre à jour directement un modèle dans le référentiel de modèles public.

Publiez un modèle de capacité d’appareil à partir de votre référentiel de modèles d’entreprise IoT Plug-and-Play dans le référentiel de modèles public. Cette opération rend le modèle immuable :

```cmd/sh
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Seuls les partenaires Microsoft peuvent publier des modèles dans le référentiel de modèles public.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article de procédure, vous avez appris à installer et à utiliser l’extension Azure IoT pour Azure CLI afin d’interagir avec vos appareils Plug-and-Play. L’étape suivante suggérée consiste à apprendre à [Gérer des modèles](./howto-manage-models.md).
