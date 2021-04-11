---
title: Diagramme d’architecture de référence | Place de marché Azure
description: Explique comment créer un diagramme d’architecture de référence pour une offre sur la Place de marché commerciale Microsoft.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: stmummer
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 02/18/2021
ms.openlocfilehash: 66e4d498ff7584188d680e35c89c6f10cc43c9cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604489"
---
# <a name="reference-architecture-diagram"></a>Diagramme de l’architecture de référence

Le diagramme d’architecture de référence est un modèle qui représente l’infrastructure sur laquelle repose votre offre. Pour les solutions Azure IP, le diagramme doit également montrer comment votre offre utilise les services cloud de Microsoft conformément aux conditions techniques de la co-vente IP. Il n’est pas conçu pour évaluer la qualité de l’architecture, mais pour illustrer comment votre solution utilise les services Microsoft.

Le diagramme d’architecture de référence peut être créé à l’aide de plusieurs outils. Nous vous recommandons Microsoft Visio, car il contient plusieurs stencils qui représentent les modèles d’architecture Azure.

Un point de départ utile pour créer des diagrammes d’architecture de référence consiste à tirer parti des [modèles d’architecture Azure](/azure/architecture/browse/).

## <a name="typical-components-of-a-reference-architecture-diagram"></a>Composants types d’un diagramme d’architecture de référence

- Services cloud qui hébergent votre offre et interagissent avec elle, notamment ceux qui consomment des ressources Azure
- Connexions de données, couches de données et services de données consommés par votre offre
- Services cloud utilisés pour contrôler la sécurité, l’authentification et les utilisateurs de l’offre
- Interfaces utilisateur et autres services qui exposent l’offre aux utilisateurs finaux
- Connectivité hybride ou locale et intégrations, ou une combinaison des deux

Cette capture d’écran montre un exemple de diagramme d’architecture de référence.

[![Cette image est un exemple de diagramme d’architecture de co-vente.](./media/co-sell/co-sell-arch-diagram.png)](./media/co-sell/co-sell-arch-diagram.png#lightbox)

Cet exemple de diagramme d’architecture de référence concerne un chatbot industriel vertical qui peut être intégré à des sites intranet pour permettre de prévoir des scénarios de demande via un algorithme Machine Learning Cette solution utilise des données de chaîne d’approvisionnement et de planification de fabrication de différents systèmes ERP. Le chatbot est conçu pour répondre aux questions concernant le moment où un vendeur peut confirmer les dates de livraison possibles d’une commande.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer la co-vente pour une offre de la Place de marché commerciale](commercial-marketplace-co-sell.md)
