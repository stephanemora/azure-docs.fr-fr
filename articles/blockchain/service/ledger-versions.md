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
ms.openlocfilehash: 63c9a8b9e266dacbb0fb6faba50fb44ac9a4b46e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027884"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versions de comptabilité Azure Blockchain Service prises en charge

Azure Blockchain Service utilise le Ethereum [Quorum](https://github.com/jpmorganchase/quorum/wiki) général conçu pour le traitement des transactions privés au sein d’un groupe de participants connus, identifié comme un consortium dans Azure Blockchain Service.

Actuellement, Azure Blockchain Service prend en charge [Quorum version 2.2.1](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) et [Gestionnaire de transactions Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Gestion des mises à jour et des mises à niveau

Le contrôle de version dans le Quorum est effectuée via une version majeure, version majeure intermédiaire et une point mineur de version. Par exemple, si le Quorum version 2.0.1, 2 est une version majeure, 0 est une version majeure de point, et 1 est la version de point mineur. Le service effectue automatiquement les versions mineures de point de comptabilité. Actuellement, les versions majeure et principales point la mise à niveau ne sont pas pris en charge.

## <a name="availability-of-new-ledger-versions"></a>Disponibilité de nouvelles versions de livre

Azure Blockchain Service fournit les dernières versions de la comptabilité dans les 60 jours d’être disponibles auprès du fabricant du grand livre. Un maximum de quatre versions principales point sont fournis pour consortiums sélectionnables lors de la configuration d’un nouveau membre et un consortium.

## <a name="next-steps"></a>Étapes suivantes

[Limites dans le Service Azure Blockchain](limits.md)
