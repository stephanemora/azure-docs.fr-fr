---
title: Azure Blockchain Service pris en charge les versions de comptabilité, mise à jour corrective et mise à niveau
description: Vue d’ensemble des versions prises en charge de registres dans Azure Blockchain Service, y compris les stratégies en matière de systèmes de mise à jour corrective et gérés par le système et gérées par l’utilisateur des mises à niveau.
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 53f65ec91a1e0f1e5a6322f0125bf83cd3e400b2
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399106"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versions de comptabilité Azure Blockchain Service prises en charge

Azure Blockchain Service utilise le Ethereum [Quorum](https://www.goquorum.com/developers) général conçu pour le traitement des transactions privés au sein d’un groupe de participants connus, identifié comme un consortium dans Azure Blockchain Service.

Actuellement, Azure Blockchain Service prend en charge [Quorum version 2.2.1](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) et [Gestionnaire de transactions Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Gestion des mises à jour et des mises à niveau

Le contrôle de version dans le Quorum est effectuée via une majeure, mineure et libère de correctif. Par exemple, si la version de Quorum est 2.0.1, type de version sont catégorisée comme suit :

|Major | Secondaire  | Correctif  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure Blockchain Service met automatiquement à jour les versions de correctifs de Quorum aux membres en cours d’exécution existants dans les 30 jours de la mise à disposition à partir de Quorum.

## <a name="availability-of-new-ledger-versions"></a>Disponibilité de nouvelles versions de livre

Azure Blockchain Service fournit les dernières versions majeure et mineure de la comptabilité de Quorum dans les 60 jours d’être disponibles auprès du fabricant de Quorum. Un maximum de quatre versions mineures sont fournis pour consortiums sélectionnables lors de la configuration d’un nouveau membre et un consortium. Version de la mise à niveau à partir de des principales ou secondaire n’est actuellement pas pris en charge.

## <a name="next-steps"></a>Étapes suivantes

[Limites dans le Service Azure Blockchain](limits.md)
