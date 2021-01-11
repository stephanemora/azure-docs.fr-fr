---
title: Connecteur de gestion des services informatiques – Exportation sécurisée dans Azure Monitor – Configuration à l’aide de ServiceNow
description: Cet article vous montre comment connecter vos produits/services ITSM à l’aide de ServiceNow sur Exportation sécurisée dans Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: fc384ffbc246f5ce9fa84b8161cbc4a5226fa5c8
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835892"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Connecter ServiceNow à Azure Monitor

Les sections suivantes fournissent des détails sur la connexion de votre produit ServiceNow et l’exportation sécurisée dans Azure.

## <a name="prerequisites"></a>Prérequis

Assurez-vous d’avoir respecté les prérequis suivants :

* Azure AD est inscrit.
* Vous avez la version prise en charge de la Gestion des événements ServiceNow – ITOM (version Orlando ou ultérieure).

## <a name="configure-the-servicenow-connection"></a>Configurer la connexion ServiceNow

1. Utilisez le lien https://(nom de l’instance).service-now.com/api/sn_em_connector/em/inbound_event?source=azuremonitor afin d’obtenir l’URI pour la définition d’exportation sécurisée.

2. Suivez les instructions correspondant à la version :
   * [Paris](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [New York](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)