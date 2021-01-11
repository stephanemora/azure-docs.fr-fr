---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: f2443765ecc9116193cefbc729ced25fa5657e59
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763424"
---
Avant de pouvoir déployer des machines virtuelles sur votre appareil Azure Stack Edge, vous devez configurer votre client pour qu’il se connecte à l’appareil via Azure Resource Manager sur Azure PowerShell. Pour obtenir des instructions détaillées, accédez à [Se connecter à Azure Resource Manager sur votre appareil Azure Stack Edge](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).


Assurez-vous de pouvoir effectuer les étapes suivantes pour accéder à l’appareil à partir de votre client (vous avez effectué cette configuration lors de la connexion à Azure Resource Manager, vous vérifiez simplement que la configuration est correcte) : 

1. Vérifiez que la communication avec Azure Resource Manager est possible. Tapez :     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Appelez les API d’appareil local pour l’authentification. Tapez : 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Indiquez le nom d’utilisateur *EdgeARMuser* et le mot de passe utilisés pour vous connecter via Azure Resource Manager.

1. Si vous avez configuré **Calcul** pour Kubernetes, vous pouvez ignorer cette étape. Assurez-vous maintenant que vous avez activé une interface réseau pour le calcul. Dans l’interface utilisateur locale, accédez aux paramètres **Calcul**. Sélectionnez l’interface réseau que vous allez utiliser pour créer un commutateur virtuel. Les machines virtuelles que vous créez seront attachées à un commutateur virtuel, lui-même attaché à ce port et au réseau associé. Choisissez un réseau qui correspond à l’adresse IP que vous allez utiliser pour la machine virtuelle.  

    ![Activer les paramètres de calcul 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Activez le calcul sur l’interface réseau. Azure Stack Edge va ensuite créer et gérer un commutateur virtuel correspondant à cette interface réseau. N’entrez pas d’adresses IP spécifiques pour Kubernetes à ce stade. L’activation du calcul peut prendre plusieurs minutes.

    > [!NOTE]
    > Si vous créez des machines virtuelles GPU, sélectionnez une interface réseau connectée à Internet. Cela vous permet d’installer l’extension GPU sur votre appareil.


1. Activer le rôle de machine virtuelle à partir du portail Azure. Cette étape crée un abonnement unique pour votre appareil et qui est utilisé pour créer des machines virtuelles via les API locales de l’appareil. 

    1. Pour activer le rôle de machine virtuelle, dans le portail Azure, accédez à la ressource Azure Stack Edge de votre appareil Azure Stack Edge. Accédez à **Computing en périphérie > Machines virtuelles**.

        ![Ajouter une image de machine virtuelle 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

    1. Sélectionnez **Machines virtuelles** pour accéder à la page **Vue d’ensemble**. **Activez** la gestion cloud des machines virtuelles.
        ![Ajouter une image de machine virtuelle 2](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)