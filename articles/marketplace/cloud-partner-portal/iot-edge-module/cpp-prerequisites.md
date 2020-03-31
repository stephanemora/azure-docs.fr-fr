---
title: Prérequis du module Azure IoT Edge | Place de marché Azure
description: Prérequis à la publication d’une offre de module IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: b6e021fc452b45edd7b1be9fd5afd77b792b4853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286538"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Prérequis à la publication de module IoT Edge

Cet article décrit les prérequis à la publication d’une nouvelle offre de module IoT Edge.  Si ce n'est déjà fait, consultez le [Guide de publication des modules IoT Edge](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Publication des conditions préalables

Pour publier un module IoT Edge sur la Place de marché Azure, vous devez satisfaire aux prérequis suivants :

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Accéder au [Portail Cloud Partner](https://cloudpartner.azure.com/). Pour plus d’informations, consultez le [Guide de publication sur AppSource et la Place de marché Azure](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Acceptation des [conditions de la Place de marché Azure](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Hébergez votre ressource technique de module IoT Edge dans un registre Azure Container Registry.  Pour plus d’informations, consultez [How to prepare your IoT Edge module technical asset](./cpp-create-technical-assets.md) (Comment préparer votre ressource technique de module IoT Edge).
- Préparez vos métadonnées de module IoT Edge. Par exemple, préparez les ressources suivantes :
    - Un titre
    - Une description (au format HTML)
    - Une image de logo (format PNG et tailles d’image fixes, y compris 40x40px, 90x90px, 115x115px, 255x115px)
    - Conditions d’utilisation et politique de confidentialité
    - Une configuration du module par défaut qui comprend : routes, propriétés souhaitées du jumeau, createOptions et variables d’environnement.
    - Documentation relative au module
    - Contacts du support


## <a name="next-steps"></a>Étapes suivantes

Dès que vous aurez [préparé les ressources techniques de votre module IoT Edge](./cpp-create-technical-assets.md), vous serez prêt à [créer votre offre de module IoT Edge](./cpp-create-offer.md). 
