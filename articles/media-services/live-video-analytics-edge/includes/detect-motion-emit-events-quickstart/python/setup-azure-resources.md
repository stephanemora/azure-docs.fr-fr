---
ms.openlocfilehash: 67d90836c382728f989ab2cb4fde4d81bac9eb25
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691027"
---
Ce tutoriel nécessite les ressources Azure suivantes :

* IoT Hub
* Compte de stockage
* Compte Azure Media Services
* Machine virtuelle Linux dans Azure, avec le [runtime IoT Edge](../../../../../iot-edge/how-to-install-iot-edge-linux.md) installé

Pour ce guide de démarrage rapide, nous vous recommandons d’utiliser le [script d’installation des ressources Live Video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) pour déployer les ressources nécessaires dans votre abonnement Azure. Pour ce faire, procédez comme suit :

1. Ouvrez [Azure Cloud Shell](https://shell.azure.com).
1. Si vous utilisez Cloud Shell pour la première fois, vous serez invité à sélectionner un abonnement pour créer un compte de stockage et un partage Microsoft Azure Files. Sélectionnez **Créer un stockage** pour créer un compte de stockage pour vos informations de session Cloud Shell. Ce compte de stockage est distinct du compte que le script créera pour être utilisé avec votre compte Azure Media Services.
1. Dans le menu déroulant situé sur le côté gauche de la fenêtre Cloud Shell, sélectionnez **Bash** comme environnement.

    ![Sélecteur d’environnement](../../../media/quickstarts/env-selector.png)
1. Exécutez la commande suivante :

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Si le script se termine correctement, vous devez voir toutes les ressources nécessaires dans votre abonnement.
1. Une fois le script terminé, sélectionnez les accolades pour exposer la structure des dossiers. Quelques fichiers s’affichent dans le répertoire *~/clouddrive/lva-sample*. L’intérêt de ce guide de démarrage rapide est le suivant :

     * ***~/clouddrive/lva-sample/edge-deployment/.env*** : ce fichier contient des propriétés que Visual Studio Code utilise pour déployer des modules sur un appareil de périphérie.
     * ***~/clouddrive/lva-sample/appsetting.json*** : Visual Studio Code utilise ce fichier pour exécuter l’exemple de code.
     
    Vous aurez besoin de ces fichiers quand vous configurerez votre environnement de développement dans Visual Studio Code dans la prochaine section. Vous pouvez les copier dans un fichier local pour l’instant.
    
    ![Paramètres de l’application](../../../media/quickstarts/clouddrive.png)
    