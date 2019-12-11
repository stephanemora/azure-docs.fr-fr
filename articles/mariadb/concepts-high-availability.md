---
title: Haute disponibilité - Azure Database for MariaDB
description: Cette rubrique fournit des informations de haute disponibilité lors de l’utilisation d’Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 797a94a561351ac7f5317f2f215b56f6944c023f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772524"
---
# <a name="high-availability-concepts-in-azure-database-for-mariadb"></a>Concepts de haute disponibilité dans Azure Database for MariaDB
Le service Azure Database for MariaDB fournit un haut niveau de disponibilité garanti. Le contrat de niveau de service (SLA) est de 99,99 % en disponibilité générale. Il n’existe pratiquement aucun temps d’arrêt d’application lors de l’utilisation de ce service.

## <a name="high-availability"></a>Haute disponibilité
Le modèle de haute disponibilité (HA) est basé sur des mécanismes de basculement intégrés quand une interruption au niveau du nœud se produit. Une interruption au niveau du nœud peut se produire en raison d’une défaillance matérielle ou en réponse à un déploiement de service.

À tout moment, les changements apportés à un serveur de base de données Azure Database for MariaDB se produisent dans le contexte d’une transaction. Les modifications sont enregistrées de manière synchrone dans Stockage Azure lorsque la transaction est validée. En cas d’interruption au niveau du nœud, le serveur de base de données crée automatiquement un nouveau nœud et associe le stockage de données au nouveau nœud. Toutes les connexions actives sont supprimées et aucune transaction séquentielle n’est validée.

## <a name="application-retry-logic-is-essential"></a>La logique de nouvelle tentative d’application est essentielle
Il est important que des applications de base de données MariaDB soient conçues pour détecter et relancer les connexions interrompues et les transactions ayant échoué. Lorsque l’application lance une nouvelle tentative, la connexion de l’application est redirigée de façon transparente vers l’instance nouvellement créée, qui remplace l’instance qui a échoué.

En interne dans Azure, une passerelle est utilisée pour rediriger les connexions vers la nouvelle instance. Lors d’une interruption, tout le processus de basculement prend généralement quelques dizaines de secondes. Étant donné que la redirection est gérée en interne par la passerelle, la chaîne de connexion externe reste la même pour les applications clientes.

## <a name="scaling-up-or-down"></a>Augmentation ou diminution d’échelle
Comme pour le modèle de haute disponibilité, quand un serveur Azure Database for MariaDB est mis à l’échelle, une nouvelle instance de serveur de la taille spécifiée est créée. Le stockage de données existant est détaché de l’instance d’origine et attaché à la nouvelle instance.

Pendant l’opération de mise à l’échelle, une interruption se produit pour les connexions de base de données. Les applications clientes sont déconnectées, et les transactions non validées en cours sont annulées. Une fois que l’application cliente réessaie d’établir la connexion ou établit une nouvelle connexion, la passerelle dirige la connexion vers l’instance qui vient d’être dimensionnée.

## <a name="next-steps"></a>Étapes suivantes
- Vous trouverez une vue d’ensemble du service dans la page  [Vue d’ensemble d’Azure Database for MariaDB](overview.md)
