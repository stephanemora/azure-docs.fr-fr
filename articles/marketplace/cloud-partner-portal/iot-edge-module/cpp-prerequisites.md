---
title: Prérequis du module Azure IoT Edge | Microsoft Docs
description: Prérequis à la publication d’une offre de module IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: a4f1023bdf8a49fccbbda1fd0dc537f83a3acee1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60910334"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Prérequis à la publication de module IoT Edge

Cet article décrit les prérequis à la publication d’une nouvelle offre de module IoT Edge.  Si vous ne le n'avez pas déjà fait, passez en revue la [modules IoT Edge guide de publication](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Publication des conditions préalables

Pour publier un module IoT Edge sur la Place de marché Azure, vous devez satisfaire aux prérequis suivants :

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Accéder au [Portail Cloud Partner](https://cloudpartner.azure.com/). Pour plus d’informations, consultez le [Guide de publication sur AppSource et la Place de marché Azure](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Acceptation des [conditions de la Place de marché Azure](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Hébergez votre ressource technique de module IoT Edge dans un registre Azure Container Registry.  Pour plus d’informations, consultez [How to prepare your IoT Edge module technical asset](./cpp-create-technical-assets.md) (Comment préparer votre ressource technique de module IoT Edge).
- Préparez vos métadonnées de module IoT Edge. Par exemple, préparer les ressources suivantes :
    - Un titre
    - Une description (au format HTML)
    - Une image de logo (format PNG et tailles d’image fixes, y compris 40x40px, 90x90px, 115x115px, 255x115px)
    - Conditions d’utilisation et politique de confidentialité
    - Une configuration du module par défaut qui comprend : routes, propriétés souhaitées du jumeau, createOptions et variables d’environnement.
    - Documentation du module
    - Contacts du support


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez [préparé votre ressource technique du module IoT Edge](./cpp-create-technical-assets.md), vous serez prêt à [créer votre offre de module IoT Edge](./cpp-create-offer.md). 
