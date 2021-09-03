---
title: Réglage intelligent - Azure Database pour PostgreSQL - Serveur flexible
description: Cet article décrit les fonctionnalités de réglage intelligent disponibles dans Azure Database pour PostgreSQL - Serveur flexible.
author: nathan-wisner-ms
ms.author: nathanwisner
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2021
ms.openlocfilehash: d16a1842c4dfed044a8ac865a3dcdcd6cbe05bf3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525010"
---
# <a name="perform-intelligent-tuning-on-your-postgresql-flex-server"></a>Effectuer un réglage intelligent pour PostgreSQL Flex - Serveur flexible

> [!IMPORTANT]
> Le serveur flexible Azure Database pour PostgreSQL est en préversion

> [!IMPORTANT]
> Le réglage intelligent est en préversion.

**S’applique à :** Azure Database pour PostgreSQL - Serveur flexible versions 11 et ultérieures

Les fonctionnalités de réglage intelligent d’Azure Database pour PostgreSQL - Serveur flexible permettent d’améliorer automatiquement les performances de vos bases de données. Avec notre service, le réglage intelligent ajuste automatiquement les éléments Checkpoint_completion_target, min_wal_size et max_wal_size en fonction des valeurs et des modèles d’utilisation. Notre service interroge les statistiques de votre base de données toutes les 30 minutes et effectue des ajustements constants afin d’optimiser vos performances sans aucune interaction de l’utilisateur.

## <a name="enabling-intelligent-tuning"></a>Activation du réglage intelligent

Le réglage intelligent étant une fonctionnalité avec option d’adhésion, elle n’est pas activée par défaut sur un serveur. Le réglage est disponible pour les bases de données singulières et n’est pas global et dès lors, l’activation d’une base de données ne l’active pas sur toutes les bases de données connectées.

### <a name="enable-intelligent-tuning-using-the-azure-portal"></a>Activer le réglage intelligent à l’aide du portail Azure

1. Connectez-vous au portail Azure, puis sélectionnez votre serveur Azure Database pour PostgreSQL.
2. Sélectionnez Paramètres du serveur dans la section Paramètres du menu.
3. Recherchez le paramètre Réglage intelligent.
4. Définissez la valeur sur True et enregistrez.

Autorisez jusqu’à 35 minutes de conservation pour le premier lot de données dans la base de données azure_sys.

## <a name="information-about-intelligent-tuning"></a>Informations relatives au réglage intelligent

Actuellement, le réglage intelligent s’articule sur trois fonctionnalités principales.

* Checkpoint_completion_target
* Min_wal_size
* Max_wal_size

Ces trois variables affectent essentiellement les éléments suivants :

* La durée des points de contrôle
* La fréquence des points de contrôle
* La durée des synchronisations

Le réglage intelligent fonctionne dans les deux sens, il tente de réduire les durées lors des charges de travail élevées et de les augmenter lors des segments inactifs. Ces règles permettent d’optimiser ces fonctionnalités pour permettre aux utilisateurs d’obtenir des résultats personnalisés pendant les périodes difficiles, sans mises à jour manuelles.

## <a name="limitations-and-known-issues"></a>Limitations et problèmes connus

* Les optimisations sont uniquement émises dans des plages spécifiques. Il est possible qu’aucune modification ne soit apportée.
* La fonctionnalité de réglage intelligent peut être retardée par les bases de données supprimées dans la requête, ce qui peut entraîner de légers retards en termes d’exécution des améliorations.
  
Actuellement, les optimisations portent uniquement sur les sections de stockage et seront par la suite étendues à d’autres catégories.
