---
author: vimeht
ms.author: vimeht
ms.date: 7/8/2021
ms.topic: include
ms.service: iot-hub-device-update
ms.openlocfilehash: d7970fff815449adf6412c2748e22b2d471e52b0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645470"
---
 > [!NOTE]
 > Quand une ressource ou une opération donnée n’a pas de limites ajustables, les limites par défaut et maximales sont les mêmes.
 > Lorsque la limite peut être ajustée, les tables incluent des valeurs différentes pour les en-têtes Limite par défaut et Limite maximale. La limite peut être augmentée au-dessus de la limite par défaut, mais pas au-delà de la limite maximale.
 > Si vous souhaitez élever la limite ou le quota au-dessus de la limite par défaut, ouvrez une [ demande de service clientèle en ligne](https://azure.microsoft.com/support/options/).


Ce tableau indique les limites de Device Update pour IoT Hub dans Azure Resource Manager :

| Ressource |  Limite par défaut | Limite maximale | Ajustable ? |
| --- | --- | --- | --- |
| Comptes par abonnement | 2 | 25 | Oui |
| Instances par compte | 2 | 25 | Oui |
| Longueur du nom de compte | Minimum : 3 <br/> Maximum : 24 | Minimum : 3 <br/> Maximum : 24 | No |
| Longueur du nom de l’instance | Minimum : 3 <br/> Maximum : 36 | Minimum : 3 <br/> Maximum : 36 | No |



Ce tableau fournit les différentes limites associées aux opérations de Device Update pour IoT Hub :

| Opération |  Limite par défaut | Limite maximale | Ajustable ? |
| --- | --- | --- | --- |
| Nombre d’appareils par instance | 10 000 | 10 000 | No |
| Nombre de fournisseurs de mises à jour par instance | 25 | 25 | Non |
| Nombre de noms de mises à jour par fournisseur et par instance | 25 | 25 | Non |
| Nombre de versions de mise à jour par fournisseur et nom de mise à jour par instance | 100 | 100 | Non |
| Nombre total de mises à jour par instance | 100 | 100 | Non |
| Taille maximale du fichier de mise à jour unique | 2 Go | 2 Go | No |
| Taille combinée maximale de tous les fichiers dans une seule opération d’importation | 2 Go | 2 Go | No |
| Nombre de groupes d’appareils par instance | 75 | 75 | Non |
