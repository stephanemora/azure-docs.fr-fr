---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 43c8c578587c65b6e0317caf77ff2d0604cb4fa3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "102044572"
---
### <a name="build-and-deploy-the-app"></a>Génération et déploiement de l’application

Ouvrez le fichier `.sln` généré par Unity. Modifiez la configuration de build comme suit.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-build-config.png" alt-text="configuration de build":::

Ensuite, vous devez configurer l’**adresse IP de l’ordinateur distant** pour déployer et déboguer l’application.

Cliquez avec le bouton droit sur le projet App et sélectionnez **Propriétés**. Dans la page de propriétés, sélectionnez **Propriétés de configuration -> Débogage**. Remplacez la valeur **Nom de l’ordinateur** par l’adresse IP de votre appareil HoloLens, puis cliquez sur **Appliquer**.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-remote-debug.png" alt-text="débogage à distance":::

Fermez la page de propriétés. Cliquez sur **Ordinateur distant**. L’application doit commencer à compiler et à déployer sur votre appareil distant. Assurez-vous que votre appareil est actif.
