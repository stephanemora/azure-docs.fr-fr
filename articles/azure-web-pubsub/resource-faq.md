---
title: Questions fréquentes (FAQ) sur le service Azure Web PubSub
description: Réponses aux questions fréquentes sur le service Azure Web PubSub.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 04/26/2021
ms.openlocfilehash: e53823539194fe1082621f458e8b5b3d493c191d
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166883"
---
# <a name="azure-web-pubsub-service-faq"></a>Questions fréquentes (FAQ) sur le service Azure Web PubSub

Voici les questions fréquemment posées sur le service Azure Web PubSub. 

## <a name="is-azure-web-pubsub-service-ready-for-production-use"></a>Le service Azure Web PubSub est-il prêt pour une utilisation en production ?

Le service Azure Web PubSub est en état de préversion publique sans contrat de niveau de service validé. 

##  <a name="where-does-my-data-reside"></a>Où mes données résident-elles ?

Le service Azure Web PubSub fonctionne comme un service de processeur de données. Il ne stocke pas de contenu client et la résidence des données est incluse par défaut. Si vous utilisez le service Azure Web PubSub avec d’autres services Azure, comme le Stockage Azure pour les diagnostics, consultez [ce livre blanc](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) pour obtenir des conseils sur la façon de maintenir la résidence des données dans les régions Azure.