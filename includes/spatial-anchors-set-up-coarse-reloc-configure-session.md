---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76545202"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Configurer la session d’ancre spatiale cloud

Nous nous occuperons ensuite de la configuration de la session d’ancre spatiale cloud. Sur la première ligne, nous définissons le fournisseur de lecteur de la session. À partir de maintenant, toutes les ancres créées pendant la session seront associées à un ensemble de relevés de lecteur. Ensuite, nous instancions des critères de proximité de l’appareil et nous les initialisons en fonction des spécifications de l’application. Enfin, nous indiquons à la session d’utiliser les données du lecteur lors de la localisation des ancres en créant un observateur à partir de nos critères de proximité de l’appareil.