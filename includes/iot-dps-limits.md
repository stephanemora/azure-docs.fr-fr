---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 62f5c1a1f6184feddad7f2b57bcfc1c4bb5bc339
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279599"
---
Le tableau suivant répertorie les limites qui s’appliquent aux ressources du service d’approvisionnement d’appareil Azure IoT Hub :

| Ressource | Limite |
| --- | --- |
| Nombre maximal de services d’approvisionnement d’appareil par abonnement Azure | 10 |
| Nombre maximal d’inscriptions | 500 000 |
| Nombre maximal d’inscriptions | 500 000 |
| Nombre maximal de groupes d’inscription | 100 |
| Nombre maximal d’autorités de certification | 25 |

> [!NOTE]
> Vous pouvez contacter [Support Microsoft](https://azure.microsoft.com/support/options/) pour augmenter le nombre d’instances dans votre abonnement.

> [!NOTE]
> Vous pouvez contacter le [Support Microsoft](https://azure.microsoft.com/support/options/) pour augmenter le nombre d’abonnements et d’inscriptions sur votre service de provisionnement.

Le service d’approvisionnement d’appareil limite les demandes lorsque les quotas suivants sont dépassés :

| Limitation | Valeur par unité |
| --- | --- |
| Opérations | 200/min/service |
| Inscriptions d’appareil | 200/min/service |
| Opération d’interrogation des appareils | 5/10sec/appareil |
