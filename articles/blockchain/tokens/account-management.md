---
title: Gestion des comptes Azure Blockchain Tokens
description: La gestion des comptes Azure Blockchain Tokens vous permet de créer des groupes et de lier des comptes blockchain pour contrôler l’accès à Azure Blockchain Tokens.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: 91dcadd3c7704b7b8c6cab45005bc83e1bba18bb
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579772"
---
# <a name="azure-blockchain-tokens-account-management"></a>Gestion des comptes Azure Blockchain Tokens

[!INCLUDE [Preview note](./includes/preview.md)]

Pour une solution de blockchain, les utilisateurs peuvent avoir besoin de différents niveaux d’accès aux jetons créés avec le service Azure Blockchain Tokens. Dans la plupart des scénarios de blockchain, vous devez planifier et déployer différents comptes blockchain existant dans le registre. Vous devez également gérer l’accès entre participants. La gestion des comptes Azure Blockchain Tokens vous permet de créer des groupes et de lier des comptes blockchain pour contrôler l’accès à Azure Blockchain Tokens.

## <a name="blockchain-networks"></a>Réseaux blockchain

Azure Blockchain Tokens permet le déploiement et la gestion des jetons dans un ensemble de réseaux blockchain. Vous pouvez connecter un ou plusieurs registres blockchain au service.

## <a name="accounts"></a>Comptes

Pour les réseaux blockchain connectés à Azure Blockchain Tokens, le service crée et gère les paires de clés publique/privée du compte et effectue la signature et l’envoi des transactions. Azure Blockchain Tokens effectue également un mappage d’identité pour faire correspondre les comptes avec l’identité de clé publique sur le registre.

## <a name="groups"></a>Groupes

Les groupes vous permettent de gérer un grand nombre de comptes blockchain sur des réseaux connectés. Vous pouvez suivre et auditer les applications et les utilisateurs figurant dans l’annuaire qui ont la possibilité d’utiliser des comptes via les API Azure Blockchain Tokens. Par exemple, vous pouvez regrouper un ensemble de comptes représentant différents rôles ou activités, et accédant à des jetons blockchain.

Vous pouvez également associer un groupe à un utilisateur ou à un principal de service Azure Active Directory de sorte que ce principal dispose d’autorisations d’accès au groupe et à ses comptes associés.  

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les [modèles Azure Blockchain Tokens disponibles](templates.md).
