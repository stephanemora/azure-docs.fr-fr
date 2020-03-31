---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d06bda1826964b019edb156375885c7f389ca6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67177712"
---
Appliquez la procédure suivante pour afficher et mettre à jour la version du runtime utilisée actuellement par une application de fonction.

1. Dans le [portail Azure](https://portal.azure.com), accédez à une application de fonction.

1. Sous **Fonctionnalités configurées**, choisissez **Paramètres de l’application de fonction**.

    ![Sélectionnez Paramètres de l’application de fonction](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. Dans l’onglet **Paramètres de l’application de fonction**, trouvez la **Version du runtime**. Prenez note de la version spécifique du runtime ainsi que de la version majeure souhaitée. Dans l’exemple ci-dessous, la version est définie sur `~2`.

   ![Sélectionnez Paramètres de l’application de fonction](./media/functions-view-update-version-portal/function-app-view-version.png)

1. Pour conserver votre application de fonction sur la version 1.x du runtime, choisissez  **~1** sous **Version du runtime**. Ce commutateur est désactivé lorsque votre application comporte des fonctions.

1. Quand vous modifiez la version du runtime, revenez à l’onglet **Vue d’ensemble** et choisissez l’option **Redémarrer** pour redémarrer l’application.  L’application de fonction redémarre avec la version 1.x du runtime, et les modèles de la version 1.x sont utilisés lorsque vous créez des fonctions.