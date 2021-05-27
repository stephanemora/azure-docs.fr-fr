---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: 08f23f483e8dc42a697a7a9196d35503b52f2af3
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110383748"
---
Les prérequis pour ce tutoriel sont les suivants :

* Un compte Azure incluant un abonnement actif. Si vous n’en avez pas déjà un, [créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    > [!NOTE]    
    > Vous aurez besoin d’un abonnement Azure dans lequel vous avez accès au rôle [Contributeur](../../../../../role-based-access-control/built-in-roles.md#contributor) et au rôle [Administrateur de l’accès utilisateur](../../../../../role-based-access-control/built-in-roles.md#user-access-administrator). Si vous ne disposez pas des autorisations appropriées, contactez l’administrateur de compte pour qu’il vous les octroie.
- [Visual Studio Code](https://code.visualstudio.com/), avec les extensions suivantes :
  * [Outils IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  
   > [!TIP]
   > Lors de l’installation d’Azure IoT Tools, vous pouvez être invité à installer Docker. N’hésitez pas à ignorer cette invite.
   * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
* [Python 3](https://www.python.org/downloads/) (3.6.9 ou version ultérieure), [Pip 3](https://pip.pypa.io/en/stable/installing/) et éventuellement [venv](https://docs.python.org/3/library/venv.html). 

> [!Important]
> Ce module Custom Vision ne prend en charge que les architectures **Intel x86 et amd64**. Vérifiez l’architecture de votre appareil périphérique avant de continuer.

## <a name="set-up-azure-resources"></a>Configurer les ressources Azure

[![Déployer sur Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](../../common-includes/azure-resources.md)]


