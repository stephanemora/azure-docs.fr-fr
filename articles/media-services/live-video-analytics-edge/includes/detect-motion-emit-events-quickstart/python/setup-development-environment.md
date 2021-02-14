---
ms.openlocfilehash: 94111ae3b02da897b9544adaad384fd281a91b02
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99531451"
---
1. Clonez le dépôt à partir de cet emplacement : https://github.com/Azure-Samples/live-video-analytics-iot-edge-python.
1. Dans Visual Studio Code, ouvrez le dossier dans lequel le dépôt a été téléchargé.
1. Dans Visual Studio Code, accédez au dossier *src/cloud-to-device-console-app*. À partir de là, créez un fichier et nommez-le *appsettings.json*. Ce fichier contiendra les paramètres nécessaires à l’exécution du programme.
1. Copiez le contenu du fichier *~/clouddrive/lva-sample/appsettings.json* que vous avez généré précédemment dans ce guide de démarrage rapide.

    Le texte doit ressembler à la sortie suivante.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Accédez au dossier *src/edge*, puis créez un fichier nommé  *.env*.
1. Copiez le contenu du fichier */clouddrive/lva-sample/edge-deployment/.env*. Le texte doit ressembler au code suivant.

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"      
    ```
    