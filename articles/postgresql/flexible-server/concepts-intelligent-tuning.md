---
title: Réglage intelligent – Azure Database pour PostgreSQL – Serveur flexible
description: Cet article décrit la fonctionnalité de réglage intelligent disponible dans Azure Database pour PostgreSQL – Serveur flexible.
author: nathan-wisner-ms
ms.author: nathanwisner
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2021
ms.openlocfilehash: ca4dbea3701007d29327b5f8f26e0566f77b0c13
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455907"
---
# <a name="perform-intelligent-tuning-in-azure-database-for-postgresql---flexible-server"></a>Effectuer un réglage intelligent dans Azure Database pour PostgreSQL – Serveur flexible

> [!IMPORTANT]
> Azure Database pour PostgreSQL – Serveur flexible et le réglage intelligent sont disponibles en préversion.

**S’applique à :** Azure Database pour PostgreSQL – Serveur flexible versions 11 et ultérieures.

La fonctionnalité de réglage intelligent d’Azure Database pour PostgreSQL – Serveur flexible permet d’améliorer automatiquement les performances de votre base de données. Le réglage intelligent ajuste automatiquement vos paramètres `checkpoint_completion_target`, `min_wal_size` et `max_wal_size` en fonction des modèles et valeurs d’utilisation. Il interroge les statistiques de votre base de données toutes les 30 minutes et effectue des ajustements constants pour optimiser les performances sans aucune interaction.

Le réglage intelligent étant une fonctionnalité avec option d’adhésion, elle n’est pas activée par défaut sur un serveur. Elle n’est disponible que pour certaines bases de données et n’est pas globale. Le fait de l’activer sur une base de données ne l’active pas sur toutes les bases de données connectées.

## <a name="enable-intelligent-tuning-by-using-the-azure-portal"></a>Activer le réglage intelligent à l’aide du portail Azure

1. Connectez-vous au portail Azure, puis sélectionnez votre serveur Azure Database pour PostgreSQL.
2. Dans la section **Paramètres** du menu, sélectionnez **Paramètres du serveur**.
3. Recherchez le paramètre Réglage intelligent.
4. Définissez la valeur sur **True**, puis sélectionnez **Enregistrer**.

Laissez jusqu’à 35 minutes pour que le premier lot de données soit conservé dans la base de données *azure_sys*.

## <a name="information-about-intelligent-tuning"></a>Informations relatives au réglage intelligent

Le réglage intelligent s’articule autour de trois paramètres principaux pour le moment : `checkpoint_completion_target`, `min_wal_size` et `max_wal_size`.

Ces trois paramètres concernent principalement : 

* La durée des points de contrôle.
* La fréquence des points de contrôle.
* La durée des synchronisations.

Le réglage intelligent fonctionne dans les deux sens. Il tente de réduire les durées pendant les charges de travail élevées et d’augmenter les durées pendant les segments inactifs. De cette façon, vous pouvez obtenir des résultats personnalisés pendant les périodes difficiles sans mises à jour manuelles.

## <a name="limitations-and-known-issues"></a>Limitations et problèmes connus

* Le réglage intelligent n’effectue des optimisations que dans des plages spécifiques. Il est possible que la fonctionnalité n’apporte aucun changement.
* Les bases de données supprimées dans la requête peuvent entraîner de légers retards dans l’exécution des améliorations par la fonctionnalité.
* Pour l’instant, la fonction n’effectue des optimisations que dans les sections de stockage.
