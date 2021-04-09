---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: d06a6ecd8af16da3e6df21e984fbf6a727fbc27e
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105197"
---
### <a name="upload-your-model"></a>Charger votre modèle

Si vous ne disposez pas déjà d’un modèle Object Anchors, suivez les instructions de la section [Créer un modèle](../articles/object-anchors/quickstarts/get-started-model-conversion.md) pour en créer un. Ensuite, revenez ici.

Une fois votre HoloLens connecté au portail d’appareil Windows, procédez comme suit pour charger un modèle que l’application pourra utiliser :

1. Dans le portail d’appareil Windows, accédez à **Système > Explorateur de fichiers > LocalAppData**. Vous devriez voir votre application dans la liste des applications.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="Explorateur de fichiers":::

2. Ouvrez votre application, puis cliquez sur le dossier `LocalState`.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="Ouvrir le dossier LocalState":::

3. Chargez le fichier du modèle dans le dossier `LocalState`.

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="Charger le modèle dans le portail":::

    Lancez à nouveau l’application à partir de l’appareil HoloLens. Vous pouvez maintenant détecter les objets correspondant au modèle.