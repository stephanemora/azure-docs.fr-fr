---
title: Remplacer votre appareil Azure Stack Edge Pro | Microsoft Docs
description: Décrit comment obtenir un appareil Azure Stack Edge Pro de remplacement.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 2196c9463569dc43092b38de58e0103104efed0c
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443469"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Remplacer votre appareil Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment remplacer votre appareil Azure Stack Edge Pro. Un appareil de remplacement est nécessaire lorsque l’appareil existant présente une défaillance matérielle ou a besoin d’une mise à niveau. 


Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
>
> * Ouvrir un ticket de support de support pour problème matériel
> * Créer une commande pour obtenir un appareil de remplacement dans le portail Azure
> * Installer et activer l’appareil de remplacement
> * Retourner l’appareil d’origine

## <a name="open-a-support-ticket"></a>Ouverture d’un ticket de support

Si votre appareil existant présente une défaillance matérielle, ouvrez un ticket de support en effectuant les étapes suivantes :

1. Ouvrez un ticket de support auprès du Support Microsoft indiquant que vous souhaitez retourner l’appareil. Sélectionnez le type de problème **Matériel Azure Stack Edge Pro**, puis choisissez le sous-type **Problèmes matériels**.  

    ![Ouverture d’un ticket de support](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Un ingénieur du support Microsoft vous contactera pour déterminer si une unité de remplacement (FRU, Field Replacement Unit) peut résoudre le problème et est disponible pour cette instance. Si aucune FRU n’est disponible ou si l’appareil a besoin d’une mise à niveau matérielle, le service de support vous guidera pour passer une nouvelle commande et retourner votre ancien appareil.

## <a name="create-a-new-order"></a>Créer une commande

Créez une ressource pour l’activation de votre appareil de remplacement en suivant les étapes décrites dans [Créer une ressource](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).

> [!NOTE]
> L’activation d’un appareil de remplacement sur une ressource existante n’est pas prise en charge. La nouvelle ressource est considérée comme une nouvelle commande. La facturation débutera 14 jours après la date d’expédition de l’appareil.

## <a name="install-and-activate-the-replacement-device"></a>Installer et activer l’appareil de remplacement

Pour installer et activer l’appareil de remplacement, procédez comme suit :

1. [Installez votre appareil](azure-stack-edge-deploy-install.md).
2. [Activez votre appareil](azure-stack-edge-deploy-connect-setup-activate.md) par rapport à la nouvelle ressource que vous avez créée précédemment.

## <a name="return-your-existing-device"></a>Retourner votre appareil existant

Suivez toutes les étapes pour retourner l’appareil d’origine :

1. [Effacez les données sur l’appareil](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. [Initiez le retour de l’appareil](azure-stack-edge-return-device.md#initiate-device-return) pour l’appareil d’origine.
3. [Planifiez un enlèvement](azure-stack-edge-return-device.md#schedule-a-pickup).
4. Une fois que l’appareil est reçu chez Microsoft, vous pouvez [supprimer la ressource](azure-stack-edge-return-device.md#delete-the-resource) associée à l’appareil retourné.


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [retourner un appareil Azure Stack Edge Pro](azure-stack-edge-return-device.md).
