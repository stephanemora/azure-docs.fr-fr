---
title: Exécuter Azure IoT Edge sur des machines virtuelles Windows Server | Microsoft Docs
description: Instructions pour la configuration d’Azure IoT Edge sur les machines virtuelles de la Place de marché Windows Server
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.openlocfilehash: 5f88a21efd04c9dd24fe31e925a3b911b5ec9df2
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77045896"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Exécuter Azure IoT Edge sur des machines virtuelles Windows Server

Le runtime Azure IoT Edge est ce qui transforme un appareil en appareil IoT Edge. Le runtime peut être déployé sur un appareil de petite taille comme un Raspberry Pi ou de grande taille comme un serveur industriel. Une fois qu’un appareil est configuré avec le runtime IoT Edge, vous pouvez commencer à déployer une logique métier sur celui-ci à partir du cloud.

Pour en savoir plus sur le fonctionnement du runtime IoT Edge et les composants inclus, consultez [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Cet article présente les étapes à suivre pour exécuter le runtime Azure IoT Edge sur une machine virtuelle Windows Server 2019, avec l’offre [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) de la Place de marché Azure. Suivez les instructions fournies dans [Installer le runtime Azure IoT Edge](how-to-install-iot-edge-windows.md) pour les autres versions de Windows.

## <a name="deploy-from-the-azure-marketplace"></a>Effectuer un déploiement à partir de la Place de marché Azure

1. Accédez à l’offre [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) de la Place de marché Azure ou effectuez une recherche sur « Windows Server » dans la [Place de marché Azure](https://azuremarketplace.microsoft.com/).
2. Sélectionnez **Obtenir maintenant**.
3. Dans **Abonnement logiciel**, recherchez « Windows Server 2019 Datacenter Server Core avec Containers », puis sélectionnez **Continuer** dans la boîte de dialogue suivante.
    * Vous pouvez également utiliser ces instructions pour les autres versions de Windows Server avec Containers.
4. Une fois sur le Portail Azure, sélectionnez **Créer** et suivez l’Assistant pour déployer la machine virtuelle.
    * Si c’est la première fois que vous testez une machine virtuelle, il est plus facile d’utiliser un mot de passe et d’activer RDP et SSH dans le menu du port d’entrée public.
    * Si votre charge de travail nécessite de nombreuses ressources, mettez à niveau la taille de machine virtuelle en ajoutant des processeurs ou de la mémoire.
5. Une fois la machine virtuelle déployée, configurez-la de sorte qu’elle se connecte à votre hub IoT :
    1. Copiez votre chaîne de connexion d’appareil à partir de votre appareil IoT Edge créé dans votre hub IoT. Consultez la procédure [Récupérer la chaîne de connexion sur le Portail Azure](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Sélectionnez votre nouvelle ressource de machine virtuelle sur le Portail Azure et ouvrez l’option **Exécuter la commande**.
    1. Sélectionnez l’option **RunPowerShellScript**.
    1. Copiez ce script dans la fenêtre Commande avec la chaîne de connexion de votre appareil :

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Exécutez le script pour installer le runtime IoT Edge et configurez votre chaîne de connexion en sélectionnant **Exécuter**.
    1. Au bout d’une ou deux minutes, vous devriez voir un message vous informant que le runtime Edge a bien été installé et provisionné.

## <a name="deploy-from-the-azure-portal"></a>Effectuer un déploiement à partir du portail Azure

1. Dans le portail Azure, recherchez « Windows Server », puis sélectionnez **Windows Server Datacenter 2019** pour commencer le processus de création de la machine virtuelle.
2. Dans **Sélectionner un abonnement logiciel**, choisissez « Windows Server 2019 Datacenter Server Core avec Containers », puis sélectionnez **Créer**.
3. Effectuez l’étape 5 décrite dans les instructions « Effectuer un déploiement à partir de la Place de marché Azure » ci-dessus.

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
   1. Exécutez cette commande avec l’ID copié :

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Créez un groupe de ressources (ou spécifiez un groupe existant aux étapes suivantes) :

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Créez une machine virtuelle :

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * Cette commande vous demandera un mot de passe. Toutefois, vous pouvez ajouter l’option `--admin-password` pour la définir plus facilement dans un script.
   * L’image Windows Server Core prend uniquement en charge la ligne de commande pour le Bureau à distance. Si vous souhaitez bénéficier de l’expérience complète, spécifiez `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` pour l’image.

1. Définissez la chaîne de connexion d’appareil (vous pouvez suivre la procédure [Récupérer la chaîne de connexion avec Azure CLI](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) si vous ne connaissez pas ce processus) :

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un appareil IoT Edge approvisionné avec le runtime installé, vous pouvez [déployer des modules IoT Edge](how-to-deploy-modules-portal.md).

Si vous ne parvenez pas à installer correctement le runtime Edge, consultez la page [Résolution des problèmes](troubleshoot.md).

Pour mettre à jour une installation existante vers la dernière version d’IoT Edge, voir [Mettre à jour le runtime et le démon de sécurité IoT Edge](how-to-update-iot-edge.md).

Pour plus d’informations sur l’utilisation des machines virtuelles Windows, consultez la [documentation sur les machines virtuelles Windows](https://docs.microsoft.com/azure/virtual-machines/windows/).

Si vous souhaitez établir une connexion SSH vers cette machine virtuelle après l’installation, suivez les instructions du guide [Installation d’OpenSSH pour Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) pour le bureau à distance ou PowerShell à distance.
