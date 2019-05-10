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
ms.openlocfilehash: 3181609bf34a04de4e31b73429f9bc5fa3fe3408
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411895"
---
# <a name="azure-app-configuration-faq"></a>Configuration de l’application Azure Forum aux questions

Cet article traite de fréquentes des questions sur la Configuration de l’application Azure.

## <a name="how-is-app-configuration-different-from-key-vault"></a>Comment diffère de Configuration d’application Key Vault ?

Configuration de l’application est conçue pour un ensemble distinct de cas d’usage : il permet aux développeurs de gérer les paramètres d’application et de contrôler la disponibilité des fonctionnalités. Il vise à simplifier de nombreuses tâches de l’utilisation des données de configuration complexes. Il prend en charge l’espace de noms hiérarchique, l’étiquetage, les requêtes étendues, récupération par lots et opérations de gestion spécialisé et les interfaces utilisateur. Configuration de l’application est complémentaire au coffre de clés et les deux doivent être utilisées côte à côte dans la plupart des déploiements d’applications.

## <a name="should-i-store-secrets-in-app-configuration"></a>Dois-je stocker des secrets dans la Configuration de l’application ?

Configuration de l’application fournit une sécurité renforcée, Key Vault est toujours le meilleur emplacement pour stocker des secrets d’application. Il fournit le chiffrement au niveau du matériel, les stratégies d’accès granulaire et les opérations de gestion telles que la rotation de certificat.

## <a name="does-app-configuration-encrypt-my-data"></a>Configuration de l’application chiffre-t-il mes données ?

Oui. Configuration de l’application chiffre toutes les valeurs de clés qu’il contient et la communication réseau. Les noms de clés sont utilisés en tant qu’index pour récupérer des données de configuration et ne sont pas chiffrés.

## <a name="does-app-configuration-support-azure-virtual-network-vnet"></a>Configuration de l’application prend-elle en charge le réseau virtuel Azure (VNET) ?

Pas encore. Prise en charge de réseau virtuel est prévue pour la disponibilité générale.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Comment dois-je stocker des configurations pour plusieurs environnements (test, intermédiaire, production et ainsi de suite) ?

Actuellement, vous pouvez contrôler qui a accès à la Configuration de l’application à un niveau par magasin. Vous devez utiliser un magasin distinct pour chaque environnement qui nécessite des autorisations différentes. Cette approche vous offre la meilleure isolation de sécurité.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Quelles sont les méthodes recommandées pour utiliser la Configuration de l’application ?

Consultez [meilleures pratiques](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Combien coûte la Configuration de l’application ?

Le service est gratuit à utiliser pendant la préversion publique.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Comment puis-je signaler un problème ou donner une suggestion ?

Vous pouvez nous contacter directement sur [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Étapes suivantes

* [Sur la Configuration de l’application Azure](./overview.md)
