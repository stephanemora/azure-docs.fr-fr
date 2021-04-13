---
title: Fichier include
description: Fichier include
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e4f9fd537d7743a5bbb9d129b21c4bf0a529d32d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491088"
---
Quand vous exécutez l’exemple d’application d’appareil plus loin dans ce tutoriel, vous avez besoin des valeurs de configuration suivantes :

* Étendue de l’ID : dans votre application IoT Central, accédez à **Administration > Connexion de l’appareil**. Notez la valeur **Étendue de l’ID**.
* Clé primaire du groupe : Dans votre application IoT Central, accédez à **Administration > Connexion de l’appareil > SAS-IoT-Devices**. Notez la valeur **Clé primaire** de la signature d’accès partagé.

Utilisez Cloud Shell pour générer une clé d’appareil à partir de la clé primaire de groupe que vous avez récupérée :

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key --device-id sample-device-01 --pk <the group primary key value>
```

Notez la clé d’appareil générée. Vous l’utiliserez ultérieurement dans ce tutoriel.
