---
title: Réinitialisation et réactivation d’appareil AZure Stack Edge
description: Cet article décrit comment effacer les données de votre appareil Azure Stack Edge, puis réactiver celui-ci.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/05/2021
ms.author: alkohli
ms.openlocfilehash: 4afe7235cb4122e4c4fe7e26fff4b34504663892
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110460596"
---
# <a name="reset-and-reactivate-your-azure-stack-edge-device"></a>Réinitialiser et réactiver votre appareil Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment réinitialiser, reconfigurer et réactiver un appareil Azure Stack Edge si vous rencontrez des problèmes avec celui-ci ou si vous devez le redémarrer à neuf pour une raison quelconque.

Une fois que vous avez réinitialisé l’appareil pour supprimer les données, vous devez le réactiver en tant que nouvelle ressource. La réinitialisation d’un appareil a pour effet de supprimer sa configuration. Vous devez donc le reconfigurer via l’interface utilisateur web locale.

Par exemple, vous devrez peut-être déplacer une ressource Azure Stack Edge existante vers un nouvel abonnement. Pour ce faire :

1. Réinitialisez les données sur l’appareil en suivant les étapes décrites dans [Réinitialiser un appareil](#reset-device).
2. Créez une nouvelle ressource qui utilise le nouvel abonnement avec votre appareil existant, puis activez l’appareil. Suivez les étapes décrites dans [Réactiver l’appareil](#reactivate-device).

## <a name="reset-device"></a>Réinitialiser un appareil

Pour effacer les données des disques de données de votre appareil, vous devez réinitialiser votre appareil.

Avant de réinitialiser, créez une copie des données locales sur l’appareil si nécessaire. Vous pouvez copier les données de l’appareil dans un conteneur de Stockage Azure.

>[!IMPORTANT]
> La réinitialisation de votre appareil a pour effet d’effacer de façon irréversible l’ensemble des données et charges de travail locales de celui-ci. Ne réinitialisez votre appareil que si vous souhaitez le redémarrer à neuf.

Vous pouvez réinitialiser votre appareil via l’interface utilisateur web locale ou PowerShell. Pour obtenir des instructions concernant PowerShell, consultez [Réinitialiser votre appareil](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

## <a name="reactivate-device"></a>Réactiver l’appareil

Une fois l’appareil réinitialisé, vous devez le réactiver en tant que nouvelle ressource. Après avoir passé une nouvelle commande, vous devez reconfigurer, puis réactiver la nouvelle ressource.

Pour réactiver votre appareil existant, procédez comme suit :

1. Créez une nouvelle commande pour l’appareil existant en procédant de la manière décrite dans [Créer une nouvelle ressource](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource). Sous l’onglet **Adresse de livraison**, sélectionnez **J’ai déjà un appareil**.

   ![Ne spécifiez aucun nouvel appareil dans Adresse de livraison](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [Obtenez la clé d’activation](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#get-the-activation-key).

1. [Connectez-vous à l’appareil](azure-stack-edge-gpu-deploy-connect.md).

1. [Configurez le réseau pour l’appareil](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

1. [Configurez les paramètres de l’appareil](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).

1. [Configurez des certificats](azure-stack-edge-gpu-deploy-configure-certificates.md).

1. [Activez l’appareil](azure-stack-edge-gpu-deploy-activate.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Se connecter à un appareil Azure Stack Edge](azure-stack-edge-gpu-deploy-connect.md).
