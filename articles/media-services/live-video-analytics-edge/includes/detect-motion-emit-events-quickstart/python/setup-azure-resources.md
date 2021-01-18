---
ms.openlocfilehash: 40d2f957ce115b43a1dcc138b86e05ec9cc47384
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060607"
---
Ce tutoriel nécessite les ressources Azure suivantes :

* IoT Hub
* Compte de stockage
* Compte Azure Media Services
* Machine virtuelle Linux dans Azure, avec le [runtime IoT Edge](../../../../../iot-edge/how-to-install-iot-edge.md) installé

Pour ce guide de démarrage rapide, nous vous recommandons d’utiliser le [script d’installation des ressources Live Video Analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) pour déployer les ressources nécessaires dans votre abonnement Azure. Pour ce faire, procédez comme suit :

1. Ouvrez [Azure Cloud Shell](https://shell.azure.com).
1. Si vous utilisez Cloud Shell pour la première fois, vous serez invité à sélectionner un abonnement pour créer un compte de stockage et un partage Microsoft Azure Files. Sélectionnez **Créer un stockage** pour créer un compte de stockage pour vos informations de session Cloud Shell. Ce compte de stockage est distinct du compte que le script créera pour être utilisé avec votre compte Azure Media Services.
1. Dans le menu déroulant situé sur le côté gauche de la fenêtre Cloud Shell, sélectionnez **Bash** comme environnement.

    ![Sélecteur d’environnement](../../../media/quickstarts/env-selector.png)
1. Exécutez la commande suivante :

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Si le script se termine correctement, toutes les ressources nécessaires doivent apparaître dans votre abonnement.
1. Une fois le script terminé, sélectionnez les accolades pour exposer la structure des dossiers. Quelques fichiers s’affichent dans le répertoire *~/clouddrive/lva-sample*. L’intérêt de ce guide de démarrage rapide est le suivant :

     * * **~/clouddrive/lva-sample/edge-deployment/.env** _ : ce fichier contient des propriétés que Visual Studio Code utilise pour déployer des modules sur un appareil de périphérie.
     _ * **~/clouddrive/lva-sample/appsetting.json** : Visual Studio Code utilise ce fichier pour exécuter l’exemple de code.
     
    Vous aurez besoin de ces fichiers quand vous configurerez votre environnement de développement dans Visual Studio Code dans la prochaine section. Vous pouvez les copier dans un fichier local pour l’instant.
    
    ![Paramètres de l’application](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> En cas de problèmes avec les ressources Azure créées, consultez notre _ *[guide de dépannage](../../../troubleshoot-how-to.md#common-error-resolutions)* * qui couvre les problèmes couramment rencontrés.