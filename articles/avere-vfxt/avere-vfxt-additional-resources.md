---
title: Liens vers des informations supplémentaires sur Avere vFXT pour Azure
description: Liens vers des informations supplémentaires sur Avere vFXT pour Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: afba39d1af700650cfbf7226dff36729a76a0bda
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669786"
---
# <a name="additional-documentation"></a>Documentation complémentaire

Cet article fournit des liens vers de la documentation complémentaire sur l’interface de gestion du Panneau de configuration Avere et sujets connexes. 

## <a name="avere-cluster-documentation"></a>Documentation sur le cluster Avere

Vous trouverez de la documentation complémentaire sur le cluster Avere sur le site web <http://library.averesystems.com/>. Les documents suivants peuvent vous aider à comprendre comment utiliser les fonctionnalités du cluster et configurer ses paramètres. 

* Le [guide de création d’un cluster FXT](<http://library.averesystems.com/#fxt_cluster>) s’applique principalement aux clusters composés de nœuds matériels physiques, mais certaines informations de ce guide s’appliquent également aux clusters vFXT. En particulier, les sections suivantes peuvent être utiles aux administrateurs de nouveaux clusters vFXT :
  * La section [Customizing Support and Monitoring Settings](<http://library.averesystems.com/create_cluster/4_8/html/config_support.html#config-support>) explique comment personnaliser les paramètres de chargement des données de support et comment activer la supervision à distance. 
  * La section [Configuring VServers and Global Namespace](<http://library.averesystems.com/create_cluster/4_8/html/config_vserver.html#config-vserver>) fournit des informations sur la création d’un espace de noms accessible par les clients.
  * La section [Configuring DNS for the Avere cluster](<http://library.averesystems.com/create_cluster/4_8/html/config_network.html#dns-overview>) explique comment configurer un DNS en tourniquet (round-robin).
  * La section [Adding Backend Storage](<http://library.averesystems.com/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) décrit comment ajouter des systèmes de stockage principaux (« core filers »).

* Le [guide de configuration du cluster](<http://library.averesystems.com/#operations>) fournit des informations de référence complètes sur les paramètres et options d’un cluster Avere. Un cluster vFXT utilise seulement une partie de ces options, mais il partage la plupart des pages de configuration.

* Le [guide du tableau de bord](<http://library.averesystems.com/#operations>) explique comment utiliser les fonctionnalités de supervision du cluster situées dans le Panneau de configuration Avere.

## <a name="vfxt-creation-and-management-documentation"></a>Documentation sur la création et la gestion de vFXT

Un guide complet sur l’utilisation de vfxt.py, utilitaire pour créer et gérer des clusters cloud, est fourni sur <https://github.com/AvereSystems/vFXT.py/blob/master/docs/README.md>.  
