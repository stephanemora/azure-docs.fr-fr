---
title: Exécuter Azure IoT Edge sur les Machines virtuelles Windows Server | Microsoft Docs
description: Instructions de configuration Azure IoT Edge sur des Machines virtuelles place de marché Windows Server
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: gregman
ms.openlocfilehash: be7479d3f042d6e64428a07e0509907b78595200
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159779"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Exécuter Azure IoT Edge sur les Machines virtuelles Windows Server
Le runtime Azure IoT Edge est ce qui transforme un appareil en appareil IoT Edge. Le runtime peut être déployé sur un appareil de petite taille comme un Raspberry Pi ou de grande taille comme un serveur industriel. Une fois qu’un appareil est configuré avec le runtime IoT Edge, vous pouvez commencer à déployer une logique métier sur celui-ci à partir du cloud.

Pour en savoir plus sur le fonctionnement du runtime IoT Edge et les composants inclus, consultez [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Cet article répertorie les étapes pour exécuter le runtime Azure IoT Edge sur une machine virtuelle Windows Server 2019 en utilisant le [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview) offre place de marché Azure. Suivez les instructions de [installer le runtime Azure IoT Edge](how-to-install-iot-edge-windows.md) sur Windows pour une utilisation avec d’autres versions.

> [!NOTE]
> Le runtime IoT Edge sur Windows Server est dans [version préliminaire publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-from-the-azure-marketplace"></a>Effectuer un déploiement à partir de la Place de marché Azure
1.  Accédez à la [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview) offre place de marché Azure ou en effectuant une recherche sur « Windows Server » [place de marché Azure](https://azuremarketplace.microsoft.com/)
2.  Sélectionnez **obtenir maintenant** 
3.  Dans **abonnement logiciel**, recherchez « 2019 Datacenter Server Core avec les conteneurs Windows Server », puis sélectionnez **continuer** sur la boîte de dialogue suivante.
    * Vous pouvez également utiliser ces instructions pour les autres versions de Windows Server avec des conteneurs
4.  Une fois sur le Portail Azure, sélectionnez **Créer** et suivez l’Assistant pour déployer la machine virtuelle. 
    *   Si elle est la première fois que tester une machine virtuelle, il est plus facile à utiliser un mot de passe et pour activer RDP et SSH dans le menu de port entrant public. 
    *   Si votre charge de travail nécessite de nombreuses ressources, mettez à niveau la taille de machine virtuelle en ajoutant des processeurs ou de la mémoire.
5.  Une fois la machine virtuelle déployée, configurez-la de sorte qu’elle se connecte à votre hub IoT :
    1.  Copiez la chaîne de connexion de votre appareil IoT Edge créé dans votre hub IoT (vous pouvez suivre le guide pratique [Inscrire un nouvel appareil Azure IoT Edge sur le Portail Azure](how-to-register-device-portal.md) si vous ne connaissez pas ce processus).
    1.  Sélectionnez votre nouvelle ressource de machine virtuelle sur le Portail Azure et ouvrez l’option **Exécuter la commande**.
    1.  Sélectionnez le **RunPowerShellScript** option
    1.  Copiez ce script dans la fenêtre de commande avec la chaîne de connexion de votre appareil : 
        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```
    1.  Exécutez le script pour installer le runtime Edge et définir votre chaîne de connexion en sélectionnant **exécuter**
    1.  Après une minute ou deux, vous devriez voir un message que le runtime Edge a été installé et configuré correctement.


## <a name="deploy-from-the-azure-portal"></a>Déployer à partir du portail Azure
1. À partir du portail Azure, recherchez « Windows Server » et sélectionnez **Windows Server Datacenter 2019** pour commencer le flux de travail de création de machine virtuelle. 
2. À partir de **sélectionner un abonnement logiciel** choisissez « 2019 Datacenter Server Core avec les conteneurs Windows Server », puis sélectionnez **créer**
3. Suivez les instructions de l’étape 5 dans le « déployer à partir de la place de marché Azure » ci-dessus.

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
   1. Exécutez cette commande avec l’ID que vous avez copié :
    
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
   * Cette commande vous demandera un mot de passe, mais vous pouvez ajouter l’option `--admin-password` pour définir plus facilement dans un script
   * L’image Windows Server Core a commande prise en charge uniquement avec le Bureau à distance de la ligne, spécifiez si vous souhaitez que l’expérience de bureau complète, `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` en tant que l’image

1. Définissez la chaîne de connexion d’appareil (vous pouvez suivre le guide pratique [Inscrire un nouvel appareil Azure IoT Edge avec Azure CLI](how-to-register-device-cli.md) si vous ne connaissez pas ce processus) :

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un appareil IoT Edge approvisionné avec le runtime installé, vous pouvez [déployer des modules IoT Edge](how-to-deploy-modules-portal.md).

Si vous rencontrez des problèmes avec le runtime Edge installation correctement, consultez la [dépannage](troubleshoot.md) page.

Pour mettre à jour une installation existante vers la dernière version d’IoT Edge, voir [Mettre à jour le runtime et le démon de sécurité IoT Edge](how-to-update-iot-edge.md).

En savoir plus sur l’utilisation de machines virtuelles de Windows à le [documentation sur les Machines virtuelles de Windows](https://docs.microsoft.com/azure/virtual-machines/windows/).

Si vous souhaitez SSH sur cette machine virtuelle après l’installation, suivez les [Installation d’OpenSSH pour Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) guide à l’aide de powershell à distance ou de bureau à distance.
