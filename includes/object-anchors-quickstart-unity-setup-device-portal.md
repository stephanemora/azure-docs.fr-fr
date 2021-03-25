---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 44fd3b2bc89ac53e7a7c0293961098509d3f5c76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044580"
---
### <a name="set-up-the-windows-device-portal"></a>Configurer le portail d’appareil Windows

Pour vous connecter à votre HoloLens via WiFi, procédez comme suit :

1. [Connectez tout d’abord votre HoloLens au WiFi](https://docs.microsoft.com/hololens/hololens-network).

2. Recherchez ensuite l’adresse IP sur l’appareil sous **Paramètres > Réseau et Internet > WiFi > Options avancées**.

3. À partir d’un navigateur web sur votre PC, accédez à `https://<YOUR_HOLOLENS_IP_ADDRESS>`. Le navigateur affichera le message suivant : « Le certificat de sécurité de ce site web présente un problème ». Ce message apparaît parce que le certificat délivré au portail d’appareil est un certificat auto-signé. Vous pouvez ignorer l’erreur de certificat et continuer.

Pour plus d’informations sur la configuration du portail d’appareil Windows, consultez [cette page](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal).
