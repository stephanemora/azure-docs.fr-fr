---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 7ac98f608cb1ca5bab590714623bc78036d46b81
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092995"
---
## <a name="configure-the-cloud-anchor-session"></a>Configurer la session d’ancrage cloud

Nous nous occuperons tout à l’heure de la configuration de la session d’ancrage cloud. Sur la première ligne, nous définissons le fournisseur de lecteur de la session. À partir de maintenant, toutes les ancres créées pendant la session seront associées à un ensemble de relevés de lecteur. Ensuite, nous instancions des critères de proximité de l’appareil et nous les initialisons en fonction des spécifications de l’application. Enfin, nous indiquons à la session d’utiliser les données du lecteur lors de la localisation des ancres en créant un observateur à partir de nos critères de proximité de l’appareil.