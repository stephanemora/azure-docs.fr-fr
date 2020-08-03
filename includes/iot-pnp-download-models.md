---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 07/14/2020
ms.openlocfilehash: b47580ad3aa14cee3ea3658ecd3b921e8e738882
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351736"
---
## <a name="download-the-model"></a>Télécharger le modèle

Plus tard, vous utiliserez l’explorateur Azure IoT pour afficher l’appareil quand celui-ci se connecte à votre hub IoT. L’explorateur Azure IoT a besoin d’une copie locale du fichier de modèle qui correspond à l’**ID de modèle** que votre appareil envoie. Le fichier de modèle permet à l’explorateur Azure IoT d’afficher les données de télémétrie, les propriétés et les commandes que votre appareil implémente.

Si vous n’avez pas encore téléchargé les exemples de fichiers de modèle :

1. Créez un dossier nommé *models* sur votre ordinateur local.

1. Cliquez avec le bouton droit sur [TemperatureController.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) et enregistrez le fichier JSON dans le dossier *models*.

1. Cliquez avec le bouton droit sur [Thermostat.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json) et enregistrez le fichier JSON dans le dossier *models*.
