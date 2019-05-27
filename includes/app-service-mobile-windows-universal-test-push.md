---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 70e0d766fe3c9669912ce9f13f729fb1c757a26f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140497"
---
1. Cliquez avec le bouton droit sur le projet Windows Store, cliquez sur **Définir comme projet de démarrage**, puis appuyez sur la touche F5 pour exécuter l'application Windows Store.

    Une fois l'application démarrée, l'appareil est enregistré pour les notifications Push.
2. Arrêtez l’application Windows Store et répétez l’étape précédente pour l’application Windows Phone Store.

    À cette étape, les deux appareils sont enregistrés pour recevoir des notifications Push.

3. Exécutez à nouveau l’application Windows Store, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    Notez qu'une fois l'insertion terminée, l'application Windows Store et l'application Windows Phone reçoivent toutes les deux une notification Push de WNS. La notification est affichée sur le Windows Phone, même si l'application n'est pas en cours d'exécution.

    ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)
