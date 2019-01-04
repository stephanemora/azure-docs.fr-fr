---
title: Se préparer à un changement d’adresse IP entrante – Azure App Service
description: Si votre adresse IP entrante va être modifiée, découvrez les choses à faire pour que votre application continue de fonctionner après la modification.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2a494b318011d601609033bc877134f0b0eeff09
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53249326"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Comment se préparer à un changement d’adresse IP entrante

Si vous avez reçu une notification indiquant que l’adresse IP entrante de votre application Azure App Service va être modifiée, suivez les instructions de cet article.

## <a name="determine-if-you-have-to-do-anything"></a>Déterminer si vous avez quelque chose à faire

* Option 1 : si votre application app Service ne dispose pas d’un domaine personnalisé, aucune action n’est requise.

* Option 2 : si seul un enregistrement CNAME (enregistrement DNS qui pointe vers un URI) est configuré dans votre portail d’inscription de domaine (fournisseur DNS tiers ou Azure DNS), aucune action n’est requise.

* Option 3 : si un enregistrement A (enregistrement DNS qui pointe directement vers votre adresse IP) est configuré dans votre portail d’inscription de domaine (fournisseur DNS tiers ou Azure DNS), remplacez l’adresse IP existante par la nouvelle. Vous trouverez la nouvelle adresse IP en suivant les instructions fournies dans la section suivante.

* Option 4 : si votre application se trouve derrière un équilibreur de charge, filtre IP ou tout autre mécanisme IP nécessitant l’adresse IP de votre application, remplacez l’adresse IP existante par la nouvelle. Vous trouverez la nouvelle adresse IP en suivant les instructions fournies dans la section suivante.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Trouver la nouvelle adresse IP entrante sur le Portail Azure

La nouvelle adresse IP entrante attribuée à votre application se trouve dans le portail, dans le champ **adresse IP virtuelle**. Cette nouvelle adresse IP ainsi que l’ancienne sont désormais connectées à votre application, et plus tard, l’ancienne sera déconnectée.

1.  Ouvrez le [portail Azure](https://portal.azure.com).

2.  Dans le menu de navigation de gauche, sélectionnez **App Services**.

3.  Sélectionnez votre application App Service dans la liste.

1.  Si l’application est une application de fonction, consultez [Function app inbound IP address](../azure-functions/ip-addresses.md#function-app-inbound-ip-address) (Adresse IP entrante de l’application de fonction).

4.  Sous l’en-tête **Paramètres**, cliquez sur **Propriétés** dans le volet de navigation de gauche, puis recherchez la section intitulée **Adresse IP virtuelle**.

5. Copiez l’adresse IP et reconfigurez votre enregistrement de domaine ou votre mécanisme d’IP.

## <a name="next-steps"></a>Étapes suivantes

Cet article a expliqué comment se préparer à un changement d’adresse IP initié par Azure. Pour plus d’informations sur les adresses IP dans Azure App Service, consultez [Inbound and Inbound IP addresses in Azure App Service](app-service-ip-addresses.md) (Entrées et adresses IP entrantes dans Azure App Service).
