---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 81c4e95660eb2875cd1b03bdfa670aeabadedc3f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104356"
---
### <a name="set-up-the-windows-device-portal"></a>Configurer le portail d’appareil Windows

Pour vous connecter à votre HoloLens via WiFi, procédez comme suit :

1. [Connectez tout d’abord votre HoloLens au WiFi](/hololens/hololens-network).

2. Recherchez ensuite l’adresse IP sur l’appareil sous **Paramètres > Réseau et Internet > WiFi > Options avancées**.

3. À partir d’un navigateur web sur votre PC, accédez à `https://<YOUR_HOLOLENS_IP_ADDRESS>`. Le navigateur affichera le message suivant : « Le certificat de sécurité de ce site web présente un problème ». Ce message apparaît parce que le certificat délivré au portail d’appareil est un certificat auto-signé. Vous pouvez ignorer l’erreur de certificat et continuer.

Pour plus d’informations sur la configuration du portail d’appareil Windows, consultez [cette page](/windows/mixed-reality/using-the-windows-device-portal).