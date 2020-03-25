---
title: Vue d’ensemble de l’exemple de blueprint de zone d’accueil de migration du framework d’adoption du cloud
description: Vue d’ensemble et architecture de l’exemple de blueprint de zone d’accueil de migration du framework d’adoption du cloud pour Azure.
ms.date: 08/20/2019
ms.topic: sample
ms.openlocfilehash: 08171a39c83ea6822243d7239882a19b053f213e
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2020
ms.locfileid: "74545491"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Vue d’ensemble de l’exemple de blueprint de zone d’accueil de migration du framework d’adoption de Microsoft Cloud pour Azure.

Le blueprint de zone d’accueil de migration du framework d’adoption de Microsoft Cloud pour Azure est un ensemble d’infrastructures destinées à vous aider à configurer la migration de votre première charge de travail et à gérer vos ressources cloud en adéquation avec le framework d’adoption du cloud.

L’exemple de blueprint de [base du framework d’adoption du cloud](../caf-foundation/index.md) étend cet exemple.

## <a name="architecture"></a>Architecture

L’exemple de blueprint de zone d’accueil de migration du framework d’adoption du cloud déploie dans Azure des ressources d’infrastructure de base que les organisations peuvent utiliser pour préparer leur abonnement à accueillir la migration de machines virtuelles. Il aide également à mettre en place les contrôles de gouvernance nécessaires pour gérer leurs ressources cloud. Cet exemple déploie et applique des ressources, stratégies et modèles qui permettent à une organisation de commencer à utiliser Azure en toute confiance.

![Zone d’accueil de migration du framework d’adoption du cloud, image décrivant ce qui est installé conformément à ce que préconise le framework d’adoption du cloud en lien avec la zone d’accueil de migration ](../../media/caf-blueprints/caf-migration-landing-zone-architecture.png)

Cet environnement se compose de plusieurs services Azure utilisés pour fournir une gouvernance sécurisée, entièrement supervisée et prête pour les entreprises. Cet environnement comporte les éléments suivants :

- Une instance [Azure Key Vault](../../../../key-vault/key-vault-overview.md) destinée à héberger les secrets utilisés pour les certificats, clés et secret déployés dans l’environnement des services partagés
- [Log Analytics](../../../../azure-monitor/overview.md) est déployé pour s’assurer que l’ensemble des actions et services journalisent dans un emplacement central dès l’instant où vous démarrez votre migration.
- [Azure Security Center](../../../../security-center/security-center-intro.md) (version standard) assure la protection contre les menaces de vos charges de travail migrées.
- [Réseau virtuel Azure](../../../../virtual-network/virtual-networks-overview.md) fournissant un réseau isolé et des sous-réseaux pour votre machine virtuelle.
- [Projet Azure Migrate](../../../..//migrate/migrate-overview.md) pour la découverte et l’évaluation. Nous ajoutons les outils pour l’évaluation de serveur, la migration de serveur, l’évaluation de base de données et la migration de base de données.  


Tous ces éléments se conforment aux pratiques éprouvées publiés dans [Centre des architectures Azure - Architectures de référence](/azure/architecture/reference-architectures/).

> [!NOTE]
> Le blueprint de migration du framework d’adoption du cloud constitue une zone d’accueil pour vos charges de travail. Vous devez toujours effectuer l’évaluation et la migration de vos machines virtuelles/bases de données en plus de cette architecture de base.

Pour plus d’informations, voir [Framework d’adoption de Microsoft Cloud pour Azure – Migrer](/azure/architecture/cloud-adoption/migrate/).

## <a name="next-steps"></a>Étapes suivantes

Vous avez découvert la vue d’ensemble et l’architecture de l’exemple de blueprint de zone d’accueil de migration du framework d’adoption du cloud.

> [!div class="nextstepaction"]
> [Blueprint de zone d’accueil de migration du framework d’adoption du cloud – Étapes de déploiement](./deploy.md)

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).