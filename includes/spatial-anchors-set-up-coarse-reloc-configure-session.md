---
author: msftradford
ms.author: parkerra
ms.date: 11/20/2020
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 2586f1a25cb5ed72ce8753efe8f10009ac85165d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131571505"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Configurer la session d’ancre spatiale cloud

Nous nous occuperons ensuite de la configuration de la session d’ancre spatiale cloud. Sur la première ligne, nous définissons le fournisseur de lecteur de la session. À partir de maintenant, toutes les ancres créées pendant la session seront associées à un ensemble de relevés de lecteur. Ensuite, nous instancions des critères de proximité de l’appareil et nous les initialisons en fonction des spécifications de l’application. Enfin, nous indiquons à la session d’utiliser les données du lecteur lors de la localisation des ancres en créant un observateur à partir de nos critères de proximité de l’appareil.
