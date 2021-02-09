---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 8f3031669723cd61715c12a42f99869ac0eaf3bc
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500324"
---
Avant de pouvoir déployer des machines virtuelles sur votre appareil Azure Stack Edge, vous devez configurer votre client pour qu’il se connecte à l’appareil via Azure Resource Manager sur Azure PowerShell. Pour obtenir des instructions détaillées, accédez à [Se connecter à Azure Resource Manager sur votre appareil Azure Stack Edge](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).

Assurez-vous que les étapes suivantes peuvent être utilisées pour accéder à l’appareil à partir de votre client. (Vous avez effectué cette configuration lors de la connexion à Azure Resource Manager. Vous vous contentez de vérifier que la configuration a réussi.) 

1. Vérifiez que la communication avec Azure Resource Manager est possible. Entrez :     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Appelez les API d’appareil local pour l’authentification. Entrez : 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Indiquez le nom d’utilisateur *EdgeARMuser* et le mot de passe utilisés pour vous connecter via Azure Resource Manager.

1. Si vous avez configuré **Calcul** pour Kubernetes, vous pouvez ignorer cette étape. Assurez-vous maintenant que vous avez activé une interface réseau pour le calcul. Dans votre interface utilisateur locale, accédez aux paramètres de **calcul**. Sélectionnez l’interface réseau que vous souhaitez utiliser pour créer un commutateur virtuel. Les machines virtuelles que vous créez seront attachées à un commutateur virtuel, lui-même attaché à ce port et au réseau associé. Choisissez un réseau qui correspond à l’adresse IP que vous allez utiliser pour la machine virtuelle.  

    ![Capture d’écran montrant comment activer les paramètres de calcul](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Activez le calcul sur l’interface réseau. Azure Stack Edge va ensuite créer et gérer un commutateur virtuel correspondant à cette interface réseau. N’entrez pas d’adresses IP spécifiques pour Kubernetes à ce stade. L’activation du calcul peut prendre plusieurs minutes.

    > [!NOTE]
    > Si vous créez des machines virtuelles GPU, sélectionnez une interface réseau connectée à Internet. Cela vous permet d’installer une extension GPU sur votre appareil.


