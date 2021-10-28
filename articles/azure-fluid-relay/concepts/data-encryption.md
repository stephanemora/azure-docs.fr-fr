---
title: Chiffrement des données dans Relais Azure Fluid
description: Mieux comprendre le chiffrement des données dans le serveur Relais Fluid
author: hickeys
ms.author: hickeys
ms.date: 10/08/2021
ms.service: app-service
ms.topic: reference
ms.openlocfilehash: 8a818ea51e1d4a3f2df03ae1c4bafcf16da35b13
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261003"
---
# <a name="data-encryption-in-azure-fluid-relay"></a>Chiffrement des données dans Relais Azure Fluid

> [!NOTE]
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

Le serveur Relais Microsoft Azure Fluid tire parti de la fonctionnalité de chiffrement au repos d’[Azure Kubernetes](../../aks/enable-host-encryption.md), [Microsoft Azure Cosmos DB]/azure/cosmos-db/database-encryption-at-rest) et de [Stockage Blob Azure](../../storage/common/storage-service-encryption.md). La communication de service à service entre AFRS et ces ressources est chiffrée par TLS et intégrée à la limite du réseau virtuel Azure. Elle est protégée contre les interférences externes par les règles de sécurité réseau.

Le diagramme ci-dessous montre la manière dont le serveur Relais Azure Fluid est implémenté et gère le stockage des données à un niveau élevé.

:::image type="content" source="../images/data-encryption.png" alt-text="Diagramme de stockage des données dans Relais Azure Fluid":::

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="how-much-more-does-azure-fluid-relay-server-cost-if-encryption-is-enabled"></a>Combien coûte le serveur Relais Azure Fluid de plus si le chiffrement est activé ?

Le chiffrement au repos est activé par défaut. Aucun coût supplémentaire n’est facturé.

### <a name="who-manages-the-encryption-keys"></a>Qui gère les clés de chiffrement ?

Les clés sont gérées par Microsoft.

### <a name="how-often-are-encryption-keys-rotated"></a>À quelle fréquence les clés de chiffrement tournent-elles ?

Microsoft a constitué un ensemble de règles internes pour la rotation des clés de chiffrement. Celles-ci sont suivies par le serveur Relais Azure Fluid. Les règles spécifiques ne sont pas publiées. Microsoft publie le [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), qui est vu comme un sous-ensemble de règles internes et qui contient des bonnes pratiques utiles pour les développeurs.

### <a name="can-i-use-my-own-encryption-keys"></a>Puis-je utiliser mes propres clés de chiffrement ?

Non, cette fonctionnalité n’est pas encore disponible. Veillez à consulter de nouveau cette page pour obtenir des mises à jour supplémentaires. 

### <a name="what-regions-have-encryption-turned-on"></a>Dans quelles régions le chiffrement est-il activé ?

Le chiffrement est activé dans toutes les régions du serveur Relais Azure Fluid pour l’ensemble des données utilisateur.

### <a name="does-encryption-affect-the-performance-latency-and-throughput-slas"></a>Le chiffrement modifie-t-il les SLA en termes de débit et de latence des performances ?

R : Il n’affecte aucunement les SLA en termes de performances lorsque le chiffrement au repos est activé.

## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble de l’architecture de Relais Azure Fluid](architecture.md)
- [Contrat de jeton Relais Azure Fluid](../how-tos/fluid-json-web-token.md)
- [Authentification et autorisation dans votre application](authentication-authorization.md)