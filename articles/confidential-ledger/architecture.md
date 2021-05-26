---
title: Architecture du Registre confidentiel Azure
description: Architecture du Registre confidentiel Azure
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: b7403cec5df1ac5f7d5a313739f597494b7c48ee
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385528"
---
# <a name="architecture"></a>Architecture

Le Registre confidentiel Azure, un service d’API REST, permet aux utilisateurs d’interagir avec le registre via des appels d’API administratifs et fonctionnels.  Lorsque des données sont enregistrées dans le registre, elles sont envoyées aux nœuds de blockchain autorisés qui sont des réplicas sauvegardés en enclave. Les réplicas suivent un concept de consensus. Un utilisateur peut également récupérer les accusés de réception des données qui ont été validées dans le registre.

Il existe aussi une notion de consortium facultative qui prendra en charge la collaboration entre plusieurs parties à l’avenir.

## <a name="architecture-diagram"></a>Diagramme de l'architecture

Cette image fournit une vue d’ensemble de l’architecture du Registre confidentiel Azure et présente les utilisateurs du Registre confidentiel Azure qui interagissent avec les API Cloud pour un registre.

:::image type="content" source="./media/architecture-overview.png" alt-text="Présentation de l'architecture":::

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du Registre confidentiel Microsoft Azure](overview.md)
- [Authentification des nœuds du Registre confidentiel Azure](authenticate-ledger-nodes.md)
