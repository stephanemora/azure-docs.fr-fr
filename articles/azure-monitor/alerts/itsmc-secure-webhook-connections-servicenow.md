---
title: Connecteur de gestion des services informatiques – Exportation sécurisée dans Azure Monitor – Configuration à l’aide de ServiceNow
description: Cet article vous montre comment connecter vos produits/services ITSM à l’aide de ServiceNow sur Exportation sécurisée dans Azure Monitor.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: cf19911bd8126bfb73f848c086aa817a7d7adb00
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563689"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Connecter ServiceNow à Azure Monitor

Les sections suivantes fournissent des détails sur la connexion de votre produit ServiceNow et l’exportation sécurisée dans Azure.

## <a name="prerequisites"></a>Prérequis

Assurez-vous d’avoir respecté les prérequis suivants :

* Azure AD est inscrit.
* Vous avez la version prise en charge de la Gestion des événements ServiceNow – ITOM (version New York ou ultérieure).
* L’[application](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.3.1?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3DEvent%2520Management%2520Connectors&sl=sh) est installée sur l’instance ServiceNow.

## <a name="configure-the-servicenow-connection"></a>Configurer la connexion ServiceNow

1. Utilisez le lien https://(nom de l’instance).service-now.com/api/sn_em_connector/em/inbound_event?source=azuremonitor afin d’obtenir l’URI pour la définition d’exportation sécurisée.

2. Suivez les instructions correspondant à la version :
   * [Paris](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [New York](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/concept/azure-integration.html)
