---
title: Azure App Configuration – Questions fréquentes (FAQ) | Microsoft Docs
description: Questions fréquentes (FAQ) sur App Configuration
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393347"
---
# <a name="azure-app-configuration-faq"></a>Azure App Configuration – Questions fréquentes (FAQ)

Cet article traite les questions fréquentes sur Azure App Configuration.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>En quoi App Configuration est-il différent d’Azure Key Vault ?

App Configuration a été conçu pour des cas d’usage bien distincts : il aide les développeurs à gérer les paramètres d’application et à contrôler la disponibilité des fonctionnalités. Il vise à simplifier de nombreuses tâches impliquant l’utilisation de données de configuration complexes.

App Configuration prend en charge :

- Les espace de noms hiérarchiques
- L’étiquetage
- Le requêtes étendues
- La récupération par lots
- Les opérations de gestion spécialisées
- Une interface utilisateur de gestion des fonctionnalités

App Configuration et Key Fault sont complémentaires. D’ailleurs, il est conseillé d’utiliser les deux côte à côte dans la plupart des déploiements d’applications.

## <a name="should-i-store-secrets-in-app-configuration"></a>Dois-je stocker les secrets dans App Configuration ?

Même si App Configuration offre une sécurité renforcée, Key Vault reste le meilleur emplacement de stockage pour les secrets d’application. Key Vault propose un chiffrement au niveau du matériel, des stratégies d’accès granulaires ainsi que des opérations de gestion, comme la rotation des certificats.

## <a name="does-app-configuration-encrypt-my-data"></a>Mes données sont-elles chiffrées par App Configuration ?

Oui. App Configuration chiffre toutes les valeurs de clés qu’il renferme, de même que les communications réseau. Les noms de clés servent d’index pour récupérer les données de configuration et ne sont pas chiffrés.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Comment dois-je stocker les configurations pour plusieurs environnements (test, intermédiaire, production, etc) ?

Actuellement, vous pouvez contrôler qui a accès à App Configuration par magasin. Utilisez un magasin distinct pour chaque environnement qui nécessite des autorisations différentes. Il s’agit de l’approche qui offre la meilleure isolation sur le plan de la sécurité.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quels sont les modes d’utilisation recommandés d’App Configuration ?

Prenez connaissance des [bonnes pratiques](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Quel est le coût d’App Configuration ?

L’utilisation de ce service est gratuite pour la préversion publique.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Comment puis-je signaler un problème ou faire une suggestion ?

Vous pouvez nous contacter directement sur [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Étapes suivantes

* [À propos d’Azure App Configuration](./overview.md)
