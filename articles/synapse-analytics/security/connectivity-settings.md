---
title: Paramètres de connectivité d’Azure Synapse
description: Un article qui vous montre comment configurer les paramètres de connectivité dans Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 03/15/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ce1a4808833cbd897da17f9ad75af346538d23d1
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472778"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Paramètres de connectivité d’Azure Synapse Analytics

Cet article détaille les paramètres de connectivité dans Azure Synapse Analytics et comment les configurer le cas échéant.


## <a name="connection-policy"></a>Stratégie de connexion
La stratégie de connexion pour Synapse SQL dans Azure Synapse Analytics est définie sur *Par défaut*. Vous ne pouvez pas la modifier dans Azure Synapse Analytics. Vous pouvez en apprendre plus sur la façon dont cela affecte les connexions à Synapse SQL dans Azure Synapse Analytics [ici](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy). 

## <a name="minimal-tls-version"></a>Version TLS minimale
Synapse SQL dans Azure Synapse Analytics autorise les connexions avec toutes les versions TLS. Vous ne pouvez pas définir la version TLS minimale pour Synapse SQL dans Azure Synapse Analytics.

## <a name="next-steps"></a>Étapes suivantes

Créer un [espace de travail Azure Synapse](./synapse-workspace-ip-firewall.md)
