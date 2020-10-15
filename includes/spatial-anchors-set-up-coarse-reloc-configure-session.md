---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76545202"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Configurer la session d’ancre spatiale cloud

Nous nous occuperons ensuite de la configuration de la session d’ancre spatiale cloud. Sur la première ligne, nous définissons le fournisseur de lecteur de la session. À partir de maintenant, toutes les ancres créées pendant la session seront associées à un ensemble de relevés de lecteur. Ensuite, nous instancions des critères de proximité de l’appareil et nous les initialisons en fonction des spécifications de l’application. Enfin, nous indiquons à la session d’utiliser les données du lecteur lors de la localisation des ancres en créant un observateur à partir de nos critères de proximité de l’appareil.