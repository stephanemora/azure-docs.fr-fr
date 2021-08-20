---
title: Contrôleurs SBC (Session Border Controller) certifiés pour le routage direct – Azure Communication Services
description: Liste des contrôleurs SBC certifiés pour le routage direct Azure Communication Services et limitations connues
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 7251e97209e9cca1b4c86a6ab8c829c8f0346525
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674896"
---
# <a name="list-of-session-border-controllers-certified-for-azure-communication-services-direct-routing"></a>Liste des contrôleurs SBC certifiés pour le routage direct Azure Communication Services
Ce document contient une liste de contrôleurs SBC certifiés pour le routage direct Azure Communication Services. Il comprend également les limitations connues.

Microsoft collabore avec les fournisseurs de contrôleurs SBC sélectionnés qui sont certifiés pour le routage direct Teams afin qu’ils puissent utiliser le routage direct Azure. Vous pouvez suivre la progression sur cette page. Les contrôleurs SBC, certifiés pour le routage direct Teams, sont susceptibles de fonctionner avec le routage direct Azure. Toutefois, nous vous encourageons à ne pas placer de charge de travail sur le contrôleur SBC avant qu’il apparaisse sur cette page. Nous ne prenons pas non plus en charge les contrôleurs SBC non certifiés. Bien que le routage direct Azure repose sur le même serveur principal que le routage direct Teams, il existe quelques différences. La certification couvre la validation complète du contrôleur SBC pour le routage direct Azure.

Microsoft travaille avec chaque fournisseur pour :
- Collaborer sur les protocoles d’interconnexion SIP.
- Effectuer des tests intensifs à l’aide d’un laboratoire tiers. Seuls les appareils qui réussissent les tests sont certifiés.
- Exécuter des tests quotidiens avec tous les appareils certifiés dans des environnements de production et de pré-production. La validation des appareils dans les environnements de pré-production garantit que les nouvelles versions du code Azure Communication Services dans le cloud fonctionnent avec les contrôleurs SBC certifiés.
- Établir un processus de support conjoint avec les fournisseurs SBC.

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

La dérivation du média n’est pas encore prise en charge par Azure Communication Services. Les médias préliminaires ne sont pas pris en charge par un client web.
Le tableau suivant répertorie les appareils certifiés pour le routage direct Azure Communication Services.

En cas de questions sur le programme de certification SBC pour le routage direct Communication Services, contactez acsdrcertification@microsoft.com.

## <a name="certified-sbc-vendors"></a>Fournisseurs de SBC certifiés

|Fournisseur|Produit|Version du logiciel|
|:--- |:--- |:--- 
|Metaswitch|Contrôleur Perimeta SBC|4,9|


Notez la certification accordée à une version principale. Cela signifie que les microprogrammes avec un nombre quelconque dans le microprogramme SBC suivant la version principale sont pris en charge.

## <a name="next-steps"></a>Étapes suivantes

### <a name="conceptual-documentation"></a>Documentation conceptuelle

- [Types de numéros de téléphone dans Azure Communication Services](./plan-solution.md)
- [Planifier le routage direct Azure](./sip-interface-infrastructure.md)
- [Associer le contrôleur de frontière de session et configurer le routage vocal](./direct-routing-provisioning.md)
- [Tarification](../pricing.md)

### <a name="quickstarts"></a>Démarrages rapides

- [Appel à un téléphone](../../quickstarts/voice-video-calling/pstn-call.md)
