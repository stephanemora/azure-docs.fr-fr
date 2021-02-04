---
author: msftradford
ms.author: parkerra
ms.date: 01/28/2021
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 386c2f8a7cc32cf65381d9d62e2e6940754e3606
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214641"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>Configurer le fournisseur de lecteur d’empreintes digitales

Nous allons commencer par créer et configurer un fournisseur de lecteur d’empreintes digitales. Le fournisseur de lecture d’empreintes digitales se charge de lire les informations des capteurs spécifiques à la plateforme sur votre appareil, et de les convertir en une représentation commune consommée par la session d’ancre spatiale cloud.

> [!IMPORTANT]
> [Vérifiez dans cette page](../articles/spatial-anchors/concepts/coarse-reloc.md#platform-availability) si les capteurs que vous activez sont disponibles sur votre plateforme.