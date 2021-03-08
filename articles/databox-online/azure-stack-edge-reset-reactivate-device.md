---
title: Réinitialiser et réactiver votre appareil Azure Stack Edge Pro | Microsoft Docs
description: Cet article décrit comment effacer les données de votre appareil Azure Stack Edge Pro, puis réactiver celui-ci.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/01/2020
ms.author: alkohli
ms.openlocfilehash: 4026bac9818b14c33c05d99caff4052adad196c3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743222"
---
# <a name="reset-and-reactivate-your-azure-stack-edge-pro-device"></a>Réinitialiser et réactiver votre appareil Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment réinitialiser, reconfigurer et réactiver un appareil Azure Stack Edge Pro si vous rencontrez des problèmes avec celui-ci ou si vous devez le redémarrer à neuf pour une raison quelconque.

Une fois que vous avez réinitialisé l’appareil pour supprimer les données, vous devez le réactiver en tant que nouvelle ressource. La réinitialisation d’un appareil a pour effet de supprimer sa configuration. Vous devez donc le reconfigurer via l’interface utilisateur web locale.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
>
> * Effacer les données des disques de données sur l’appareil
> * Réactiver l’appareil en créant une nouvelle commande, en reconfigurant l’appareil et en l’activant

## <a name="reset-data-from-the-device"></a>Réinitialiser les données de l’appareil

Pour effacer les données des disques de données de votre appareil, vous devez réinitialiser votre appareil. 

Avant de réinitialiser, créez une copie des données locales sur l’appareil si nécessaire. Vous pouvez copier les données de l’appareil dans un conteneur de Stockage Azure.

>[!IMPORTANT]
> La réinitialisation de votre appareil a pour effet d’effacer de façon irréversible l’ensemble des données et charges de travail locales de celui-ci. Ne réinitialisez votre appareil que si vous souhaitez le redémarrer à neuf.

Vous pouvez réinitialiser votre appareil via l’interface utilisateur web locale ou PowerShell. Pour obtenir des instructions concernant PowerShell, consultez [Réinitialiser votre appareil](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).

[! INCLUDe] [Réinitialiser les données de l’appareil](../../includes/azure-stack-edge-device-reset.md)

## <a name="reactivate-device"></a>Réactiver l’appareil

Une fois l’appareil réinitialisé, vous devez le réactiver en tant que nouvelle ressource. Après avoir passé une nouvelle commande, vous devez reconfigurer, puis réactiver la nouvelle ressource.

Pour réactiver votre appareil existant, procédez comme suit :

1. Créez une nouvelle commande pour l’appareil existant en procédant de la manière décrite dans [Créer une nouvelle ressource](azure-stack-edge-gpu-deploy-prep?tabs=azure-portal#create-a-new-resource). Sous l’onglet **Adresse de livraison**, sélectionnez **J’ai déjà un appareil**.

   ![Ne spécifiez aucun nouvel appareil dans Adresse de livraison](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [Obtenez la clé d’activation](azure-stack-edge-gpu-deploy-prep?tabs=azure-portal#get-the-activation-key).

1. [Connectez-vous à l’appareil](azure-stack-edge-gpu-deploy-connect.md).

1. [Configurez le réseau pour l’appareil](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

1. [Configurez les paramètres de l’appareil](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).

1. [Configurez des certificats](azure-stack-edge-gpu-deploy-configure-certificates.md).

1. [Activez l’appareil](databox-online/azure-stack-edge-gpu-deploy-activate.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Se connecter à un appareil Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-connect.md).
