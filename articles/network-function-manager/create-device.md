---
title: 'Tutoriel : créer une ressource d’appareil pour Azure Network Function Manager'
description: Dans ce tutoriel, découvrez comment créer une ressource d’appareil pour Azure Network Function Manager.
author: cherylmc
ms.service: network-function-manager
ms.topic: tutorial
ms.date: 06/30/2021
ms.author: cherylmc
ms.openlocfilehash: 048b086c407f5fbdc6239aec9dc509aceda7c8c2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468175"
---
# <a name="tutorial-create-a-network-function-manager-device-resource-preview"></a>Tutoriel : créer une ressource d’appareil de Network Function Manager (préversion)

Dans ce tutoriel, vous allez créer une ressource d’**appareil** pour Azure Network Function Manager (NFM). La ressource d’appareil de Network Function Manager est liée à la ressource Azure Stack Edge. La ressource de l’appareil regroupe toutes les fonctions réseau déployées sur Azure Stack Edge et fournit des services courants pour le déploiement, la surveillance, la résolution des problèmes et les opérations de gestion cohérente pour toutes les fonctions réseau déployées sur Azure Stack Edge. Vous devez créer la ressource d’appareil de Network Function Manager avant de pouvoir déployer des fonctions réseau sur Azure Stack Edge.

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Vérifier les prérequis
> * Créer une ressource d’appareil
> * Obtenir une clé d'inscription
> * Inscrire l’appareil

## <a name="prerequisites"></a><a name="pre"></a>Configuration requise

Vérifier les conditions préalables :

* Vous avez rempli toutes les conditions préalables indiquées dans l’article [Vue d’ensemble](overview.md#prereq).
* Les autorisations appropriées sont affectées. Pour plus d’informations, consultez [Inscription et autorisations du fournisseur de ressources](overview.md#permissions).
* Passez en revue la section  [Disponibilité de la région](overview.md#regions)  avant de créer une ressource d’appareil.
* Vérifiez que vous pouvez vous connecter à distance à partir d’un client Windows au GPU Pro d’Azure Stack Edge via PowerShell. Pour plus d’informations, découvrez comment [Se connecter à l’interface PowerShell](../databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

## <a name="create-a-device-resource"></a><a name="create"></a>Créer une ressource d’appareil

Si vous disposez d’une ressource d’appareil Azure Network Function Manager existante, vous n’avez pas besoin d’en créer une. Ignorez cette section et accédez à la section [clé d’inscription](#key).

Pour créer une ressource d'**appareil**, procédez comme suit.

1. Connectez-vous au [portail Azure en version préliminaire](https://aka.ms/AzureNetworkFunctionManager) à l’aide de vos informations d’identification Microsoft Azure.

1. Sous l’onglet **Informations de base**, configurez les paramètres **Détails du projet** et **Détails de l’instance** de l’appareil.
   :::image type="content" source="./media/create-device/device-settings.png" alt-text="Capture d’écran des paramètres des appareils.":::

   Lorsque vous remplissez les champs, une coche verte apparaît lorsque les caractères que vous ajoutez sont validés. Certains détails sont remplis automatiquement, tandis que d’autres sont des champs personnalisables :

   * **Abonnement** : vérifiez que l’abonnement répertorié est approprié. Vous pouvez modifier des abonnements à l’aide de la liste déroulante.
   * **Groupe de ressources** : sélectionnez un groupe de ressources existant ou cliquez sur **Créer** pour en créer un.
   * **Région** : sélectionnez l’emplacement de l’appareil. Il doit se trouver dans une région prise en charge. Pour plus d’informations, consultez [Disponibilité des régions](overview.md#regions).
   * **Nom** : entrez le nom de votre appareil.
   * **Azure Stack Edge** : sélectionnez la ressource Azure Stack Edge que vous souhaitez inscrire en tant qu’appareil pour Azure Network Function Manager. La ressource ASE doit être à l’état **en ligne** pour qu’une ressource d’appareil soit correctement créée. Vous pouvez vérifier l’état de votre ressource ASE en accédant à la section **Propriétés** de la page de ressources Azure Stack Edge.
1. Sélectionnez **Vérifier + Créer** pour valider vos paramètres.
1. Une fois tous les paramètres vérifiés, sélectionnez **Créer**.
   
   >[!NOTE]
   >La ressource d’appareil de Network Function Manager peut être liée à une ressource Azure Stack Edge. Vous devez créer une ressource d’appareil NFM distincte pour chaque ressource Azure Stack Edge.
   >

## <a name="get-the-registration-key"></a><a name="key"></a>Recevoir la clé d’inscription

1. Une fois votre appareil correctement approvisionné, accédez au groupe de ressources dans lequel la ressource d’appareil est déployée.
1. Cliquez sur la ressource de l'**appareil**. Pour obtenir la clé d’inscription, cliquez sur **Obtenir la clé d’inscription**. Vérifiez que vous disposez des autorisations appropriées pour générer une clé d’inscription. Pour plus d’informations, consultez [Autorisations](overview.md#permissions).

   :::image type="content" source="./media/create-device/register-device.png" alt-text="Capture d’écran de la clé d’inscription." lightbox="./media/create-device/register-device.png":::
1. Prenez note de la clé d’inscription de l’appareil, qui sera utilisée dans les étapes suivantes.

   > [!IMPORTANT]
   > La clé d’inscription expire 24 heures après sa génération. Si votre clé d’inscription n’est plus active, vous pouvez générer une nouvelle clé d’inscription en répétant les étapes de cette section.
   >

## <a name="register-the-device"></a><a name="registration"></a>Inscrire l’appareil

Procédez comme suit pour inscrire la ressource de l’appareil avec Azure Stack Edge.

1. Pour inscrire l’appareil avec la clé d’inscription obtenue à l’étape précédente, [Connectez-vous au périphérique Azure Stack Edge via Windows PowerShell](../databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Une fois que vous disposez d’une connexion PowerShell (minishell minimale) à l’appliance, entrez la commande suivante avec la clé d’inscription de l’appareil obtenue à partir de l’étape précédente comme paramètre :
   ```powershell
   Invoke-MecRegister <device-registration-key>
   ```

1. Vérifiez que la ressource de l’appareil a l’état de l'**appareil = inscrit**.

   :::image type="content" source="./media/create-device/device-registered.png" alt-text="Capture d’écran de l’appareil inscrit." lightbox="./media/create-device/device-registered.png":::
 
## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer une fonction réseau](deploy-functions.md)
