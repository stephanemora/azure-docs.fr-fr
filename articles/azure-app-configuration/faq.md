---
title: Azure App Configuration – Questions fréquentes (FAQ) | Microsoft Docs
description: Questions fréquentes (FAQ) sur App Configuration
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: dbfb6a1c4c53b1bd255560e688d3dc0cf3835a3a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469638"
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

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Existe-t-il des limitations de taille sur les clés et les valeurs stockées dans App Configuration ?

Il existe une limite de 10 Ko pour un seul élément de clé-valeur.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Comment dois-je stocker les configurations pour plusieurs environnements (test, intermédiaire, production, etc) ?

Actuellement, vous pouvez contrôler qui a accès à App Configuration par magasin. Utilisez un magasin distinct pour chaque environnement qui nécessite des autorisations différentes. Il s’agit de l’approche qui offre la meilleure isolation sur le plan de la sécurité.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quels sont les modes d’utilisation recommandés d’App Configuration ?

Prenez connaissance des [bonnes pratiques](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Quel est le coût d’App Configuration ?

L’utilisation de ce service est gratuite pour la préversion publique.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Comment puis-je recevoir des annonces sur les nouvelles versions et d’autres informations relatives à App Configuration ?

Abonnez-vous à notre [référentiel d’annonces GitHub](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Comment puis-je signaler un problème ou faire une suggestion ?

Vous pouvez nous contacter directement sur [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Étapes suivantes

* [À propos d’Azure App Configuration](./overview.md)
