---
author: wesmc7777
ms.service: iot-dps
ms.topic: include
ms.date: 06/15/2021
ms.author: wesmc
ms.openlocfilehash: b3c218314f56fb53d0690ac0659192bb955e4301
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539000"
---
> [!NOTE]
> Certaines zones de ce service ont des limites ajustables. Cela est représenté dans les tableaux ci-dessous avec la colonne *Ajustable ?* . Quand la limite peut être réglée, la valeur *Ajustable ?* est *Oui*.
>
> Si votre entreprise nécessite de déclencher une limite ou un quota ajustable au-delà de la limite par défaut, vous pouvez demander des ressources supplémentaires en [ouvrant un ticket de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

Le tableau suivant répertorie les limites qui s’appliquent aux ressources du 	Service Azure IoT Hub Device Provisioning.

| Ressource | Limite | Ajustable ? |
| --- | --- | --- |
| Nombre maximal de services d’approvisionnement d’appareil par abonnement Azure | 10 | Oui |
| Nombre maximal d’inscriptions | 1 000 000 | Oui |
| Nombre maximal d’inscriptions individuelles | 1 000 000 | Oui |
| Nombre maximal de groupes d’inscription *(certificat X.509)* | 100 | Oui |
| Nombre maximal de groupes d’inscription *(clé symétrique)* | 100 | Non |
| Nombre maximal d’autorités de certification | 25 | Non |
| Nombre maximal de hubs IoT liés | 50 | Non |
| Taille maximale du message | 96 Ko| Non |

> [!TIP]
> Si la limite inconditionnelle sur les groupes d’inscription de clé symétrique est un problème, il est recommandé d’utiliser des inscriptions individuelles comme solution de contournement.

Le service de provisionnement des appareils a les limites de débit suivantes.

| Tarif | Valeur par unité | Ajustable ? |
| --- | --- | --- |
| Operations | 200/min/service | Oui |
| Inscriptions d’appareil | 200/min/service | Oui |
| Opération d’interrogation des appareils | 5/10 s/appareil | Non |

Chaque appel d’API sur DPS est facturable comme une seule *opération*. Cela comprend toutes les API de service et l’API d’inscription d’appareil. L’opération d’interrogation de l’inscription de l’appareil n’est pas facturée.