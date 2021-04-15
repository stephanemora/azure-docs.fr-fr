---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: f166413507afb9aff814eaddaade099d2e34ae68
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554353"
---
Avant de pouvoir déployer des machines virtuelles sur votre appareil Azure Stack Edge, vous devez configurer votre client pour qu’il se connecte à l’appareil via Azure Resource Manager sur Azure PowerShell. Pour obtenir des instructions détaillées, consultez [Se connecter à Azure Resource Manager sur votre appareil Azure Stack Edge](../articles/databox-online/azure-stack-edge-gpu-connect-resource-manager.md).

Assurez-vous de pouvoir utiliser les étapes suivantes pour accéder à l’appareil à partir de votre client. Vous avez déjà effectué cette configuration quand vous vous êtes connecté à Azure Resource Manager. Vous vérifiez maintenant que la configuration a réussi. 

1. Vérifiez que la communication Azure Resource Manager fonctionne en exécutant la commande suivante :     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Pour appeler les API de l’appareil local à des fins d’authentification, entrez : 

    `login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d`

    Indiquez le nom d’utilisateur *EdgeArmUser* et votre mot de passe pour vous connecter via Azure Resource Manager.

1. Si vous avez configuré le calcul pour Kubernetes, vous pouvez ignorer cette étape. Dans le cas contraire, vérifiez que vous avez activé une interface réseau pour le calcul en procédant comme suit : 

   a. Dans votre interface utilisateur locale, accédez aux paramètres **Calcul**.  
   b. Sélectionnez l’interface réseau que vous souhaitez utiliser pour créer un commutateur virtuel. Les machines virtuelles que vous créez seront attachées à un commutateur virtuel, lui-même attaché à ce port et au réseau associé. Choisissez un réseau qui correspond à l’adresse IP que vous allez utiliser pour la machine virtuelle.  

    ![Capture d’écran du volet des paramètres réseau de la configuration du calcul.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

   c. Sous **Activer pour le calcul** dans l’interface réseau, sélectionnez **Oui**. Azure Stack Edge crée et gère un commutateur virtuel correspondant à cette interface réseau. N’entrez pas d’adresses IP spécifiques pour Kubernetes à ce stade. L’activation du calcul peut prendre plusieurs minutes.

    > [!NOTE]
    > Si vous créez des machines virtuelles GPU, sélectionnez une interface réseau connectée à Internet. Cela vous permet d’installer une extension GPU sur votre appareil.