---
title: Fichier include
description: Fichier include
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 2a348827b1c992e0ef0a4592cc0b9c5c0fa0ca19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372014"
---
## <a name="about-cognitive-services-encryption"></a>À propos du chiffrement de Cognitive Services

Les données sont chiffrées et déchiffrées à l'aide du chiffrement [AES 256 bits](https://en.wikipedia.org/wiki/FIPS_140-2) certifié [FIPS 140-2](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard). Le chiffrement et le déchiffrement sont transparents, ce qui signifie que le chiffrement et l’accès sont gérés automatiquement. Comme vos données sont sécurisées par défaut, vous n’avez pas besoin de modifier votre code ou vos applications pour tirer parti du chiffrement.

## <a name="about-encryption-key-management"></a>À propos de la gestion des clés de chiffrement

Par défaut, votre abonnement utilise des clés de chiffrement gérées par Microsoft. Si vous utilisez un niveau tarifaire qui prend en charge les clés gérées par le client, les paramètres de chiffrement de votre ressource sont disponibles dans la section **Chiffrement** du [portail Azure](https://portal.azure.com), comme illustré ci-dessous.

![Afficher les paramètres de chiffrement](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

Pour les abonnements qui prennent uniquement en charge les clés de chiffrement gérées par Microsoft, la section **Chiffrement** n'est pas disponible.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Clés gérées par le client avec Azure Key Vault

Une option vous permet également de gérer votre abonnement avec vos propres clés. Les clés gérées par le client (CMK), également appelées BYOK (Bring Your Own Key), offrent plus de flexibilité pour créer, permuter, désactiver et révoquer des contrôles d'accès. Vous pouvez également effectuer un audit sur les clés de chiffrement utilisées pour protéger vos données.
