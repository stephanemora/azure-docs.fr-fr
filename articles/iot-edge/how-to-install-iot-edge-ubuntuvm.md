---
title: Exécuter Azure IoT Edge sur des Machines virtuelles Ubuntu | Microsoft Docs
description: Instructions de configuration d’Azure IoT Edge sur des Machines virtuelles de la Place de marché Azure Ubuntu 16.04.
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: gregman
ms.openlocfilehash: 84dd1b6d657dfe5061f53698114f3dc4e0197571
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510054"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Exécuter Azure IoT Edge sur des Machines virtuelles Ubuntu

Le runtime Azure IoT Edge est ce qui transforme un appareil en appareil IoT Edge. Le runtime peut être déployé sur un appareil de petite taille comme un Raspberry Pi ou de grande taille comme un serveur industriel. Une fois qu’un appareil est configuré avec le runtime IoT Edge, vous pouvez commencer à déployer une logique métier sur celui-ci à partir du cloud.

Pour en savoir plus sur le fonctionnement du runtime IoT Edge et les composants inclus, consultez [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Cet article présente les étapes à suivre pour exécuter le runtime Azure IoT Edge sur une Machine virtuelle Ubuntu 16.04, avec [l’offre préconfigurée d’Azure IoT Edge sur la Place de marché Azure Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm).

Au premier démarrage, Azure IoT Edge sur la Machine virtuelle Ubuntu préinstalle la dernière version du runtime Azure IoT Edge. Il comporte également un script visant à définir la chaîne de connexion, puis à redémarrer le runtime, qui peut être déclenché à distance sur le portail de la machine virtuelle Azure ou en ligne de commande Azure, ce qui permet de configurer et de connecter facilement l’appareil IoT Edge sans démarrer de session SSH ou Bureau à distance. Ce script attendra, pour définir la chaîne de connexion, que le client de IoT Edge soit entièrement installé ; il n’est donc pas nécessaire d’intégrer cet aspect à l’automatisation.

## <a name="deploy-from-the-azure-marketplace"></a>Effectuer un déploiement à partir de la Place de marché Azure

1. Accédez à l’offre [Azure IoT Edge sur Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) de la Place de marché ou effectuez une recherche « Azure IoT Edge sur Ubuntu » sur la [Place de marché Azure](https://azuremarketplace.microsoft.com/).
2. Sélectionnez **OBTENIR MAINTENANT**, puis **Continuer** sur la boîte de dialogue suivante.
3. Une fois sur le Portail Azure, sélectionnez **Créer** et suivez l’Assistant pour déployer la machine virtuelle.
    * Si c’est la première fois que vous testez une machine virtuelle, il est plus facile d’utiliser un mot de passe et d’activer la liaison SSH dans le menu du port d’entrée public.
    * Si votre charge de travail nécessite de nombreuses ressources, mettez à niveau la taille de machine virtuelle en ajoutant des processeurs ou de la mémoire.
4. Une fois la machine virtuelle déployée, configurez-la de sorte qu’elle se connecte à votre hub IoT :
    1. Copiez la chaîne de connexion de votre appareil IoT Edge créé dans votre hub IoT. si vous ne connaissez pas bien ce processus, vous pouvez suivre la procédure [Récupérer la chaîne de connexion sur le portail Azure](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal)
    1. Sélectionnez votre nouvelle ressource de machine virtuelle sur le Portail Azure et ouvrez l’option **Exécuter la commande**.
    1. Sélectionnez l’option **RunShellScript**.
    1. Exécutez le script ci-dessous dans la fenêtre Commande avec la chaîne de connexion de votre appareil : `/etc/iotedge/configedge.sh "{device_connection_string}"`.
    1. Sélectionnez **Exécuter**.
    1. Attendez quelques instants ; l’écran devrait alors présenter un message indiquant que la chaîne de connexion a été correctement définie.

## <a name="deploy-from-the-azure-portal"></a>Effectuer un déploiement à partir du portail Azure

Sur le Portail Azure, recherchez « Azure IoT Edge » et sélectionnez **Runtime Azure IoT Edge Ubuntu Server 16.04 LTS +** pour lancer le workflow de création de machines virtuelles. À partir de là, suivez les étapes 3 et 4 dans les instructions « Déployer à partir de la Place de marché Azure » ci-dessus.

## <a name="deploy-from-azure-cli"></a>Effectuer un déploiement sur Azure CLI

1. Si vous utilisez Azure CLI sur votre ordinateur de bureau, commencez par vous connecter :

   ```azurecli-interactive
   az login
   ```

1. Si vous avez plusieurs abonnements, sélectionnez celui que vous souhaitez utiliser :
   1. Répertoriez vos abonnements :

      ```azurecli-interactive
      az account list --output table
      ```

   1. Copiez le champ SubscriptionID de l’abonnement que vous souhaitez utiliser.

   1. Définissez votre abonnement de travail avec l’ID que vous venez de copier :

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Créez un groupe de ressources (ou spécifiez un groupe existant aux étapes suivantes) :

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Acceptez les conditions d’utilisation de la machine virtuelle. Si vous souhaitez afficher les termes du contrat d’abord, suivez les étapes dans [Déployer à partir de la Place de marché Azure](#deploy-from-the-azure-marketplace).

   ```azurecli-interactive
   az vm image terms accept --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. Créez une machine virtuelle :

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. Définissez la chaîne de connexion de l’appareil. Si vous ne connaissez pas bien ce processus, vous pouvez suivre la procédure [Récupérer la chaîne de connexion avec Azure CLI](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) :

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Si vous souhaitez établir une connexion SSH sur cette machine virtuelle après l’installation, utilisez publicIpAddress avec la commande : `ssh azureuser@{publicIpAddress}`.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un appareil IoT Edge approvisionné avec le runtime installé, vous pouvez [déployer des modules IoT Edge](how-to-deploy-modules-portal.md).

Si vous ne parvenez pas à installer correctement le runtime IoT Edge, consultez la page de [résolution des problèmes](troubleshoot.md).

Pour mettre à jour une installation existante vers la dernière version d’IoT Edge, voir [Mettre à jour le runtime et le démon de sécurité IoT Edge](how-to-update-iot-edge.md).

Si vous souhaitez ouvrir des ports pour accéder à la machine virtuelle via SSH ou d’autres connexions entrantes, reportez-vous à la documentation de machine virtuelle Azure sur [l’ouverture de ports et de points de terminaison sur une machine virtuelle Linux](../virtual-machines/linux/nsg-quickstart.md)
