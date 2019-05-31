---
title: Configuration de l’application Azure FAQ | Microsoft Docs
description: Forum aux questions sur la Configuration de l’application Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: e321c0b473b110597b5b87a6e67666737116daa2
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393347"
---
# <a name="azure-app-configuration-faq"></a>Configuration de l’application Azure Forum aux questions

Cet article traite des questions fréquemment posées sur la Configuration de l’application Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>En quoi App Configuration est-il différent d'Azure Key Vault ?

Configuration de l’application est conçue pour un ensemble distinct de cas d’usage : il permet aux développeurs de gérer les paramètres d’application et de contrôler la disponibilité des fonctionnalités. Il vise à simplifier de nombreuses tâches de l’utilisation des données de configuration complexes.

Configuration de l’application prend en charge :

- Espaces de noms hiérarchique
- L’étiquetage
- Requêtes d’étendue
- Récupération par lots
- Opérations de gestion spécialisé
- Une interface utilisateur de gestion de la fonctionnalité

Configuration de l’application est complémentaire au coffre de clés et les deux doivent être utilisées côte à côte dans la plupart des déploiements d’applications.

## <a name="should-i-store-secrets-in-app-configuration"></a>Dois-je stocker des secrets dans la Configuration de l’application ?

Bien que la Configuration de l’application fournit une sécurité renforcée, Key Vault est toujours le meilleur emplacement pour stocker des secrets d’application. Key Vault permet le chiffrement au niveau du matériel, les stratégies d’accès granulaire et opérations de gestion, telles que la rotation de certificat.

## <a name="does-app-configuration-encrypt-my-data"></a>Configuration de l’application chiffre-t-il mes données ?

Oui. Configuration de l’application chiffre toutes les valeurs de clés qu’il contient, et il chiffre les communications de réseau. Les noms de clés sont utilisés en tant qu’index pour récupérer des données de configuration et ne sont pas chiffrées.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Comment dois-je stocker des configurations pour plusieurs environnements (test, intermédiaire, production et ainsi de suite) ?

Actuellement, vous pouvez contrôler qui a accès à la Configuration de l’application à un niveau par magasin. Utilisez un magasin distinct pour chaque environnement qui nécessite des autorisations différentes. Cette approche vous offre la meilleure isolation de sécurité.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quelles sont les méthodes recommandées pour utiliser la Configuration de l’application ?

Consultez [meilleures pratiques](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Combien coûte la Configuration de l’application ?

Le service est gratuit à utiliser pendant la préversion publique.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Comment puis-je signaler un problème ou donner une suggestion ?

Vous pouvez nous contacter directement sur [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Étapes suivantes

* [Sur la Configuration de l’application Azure](./overview.md)
