---
title: Connecteur de gestion des services informatiques – Exportation sécurisée dans Azure Monitor – Configuration à l’aide du BMC
description: Cet article vous montre comment connecter vos produits/services ITSM à l’aide du BMC sur Exportation sécurisée dans Azure Monitor.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 7c50d533f95e1131e26ddd808c77023ae4591a26
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041703"
---
# <a name="connect-bmc-helix-to-azure-monitor"></a>Connecter BMC Helix à Azure Monitor

Les sections suivantes détaillent la connexion de votre produit BMC Helix à l’exportation sécurisée dans Azure.

## <a name="prerequisites"></a>Prérequis

Assurez-vous d’avoir respecté les prérequis suivants :

* Azure AD est inscrit.
* Vous disposez de la version prise en charge de BMC Helix Multi-Cloud Service Management (version 19.08 ou ultérieure).

## <a name="configure-the-bmc-helix-connection"></a>Configurer la connexion BMC Helix

1. Utilisez la procédure suivante dans l’environnement BMC Helix afin d’obtenir l’URI pour l’exportation sécurisée :

   1. Connectez-vous à Integration Studio.
   1. Recherchez le flux **Create Incident from Azure Alerts** (Créer un incident à partir des alertes Azure).
   1. Copiez l’URL du webhook.
   
   ![Capture d’écran de l’URL du Webhook dans Integration Studio.](media/itsmc-secure-webhook-connections-bmc/bmc-url.png)
   
2. Suivez les instructions correspondant à la version :
   * [Activation de l’intégration prédéfinie à Azure Monitor pour la version 20.02](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)
   * [Activation de l’intégration prédéfinie à Azure Monitor pour la version 19.11](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html)

3. Dans la partie de configuration de la connexion dans BMC Helix, accédez à votre instance BMC d’intégration et suivez ces instructions :

   1. Sélectionnez **Catalog**.
   2. Sélectionnez **Azure alerts**.
   3. Sélectionnez **connectors**.
   4. Sélectionnez **configuration**.
   5. Sélectionnez la configuration **add new connection**.
   6. Renseignez les informations suivantes dans la section de configuration :
      - **Name** : Indiquez ce qui vous convient.
      - **Authorization type**: **NONE**
      - **Description** : Indiquez ce qui vous convient.
      - **Site**: **Cloud**
      - **Number of instances**: **2**, la valeur par défaut.
      - **Check**: Sélectionné par défaut pour activer l’utilisation.
      - L’ID du locataire Azure et l’ID de l’application Azure sont extraits de l’application que vous avez définie précédemment.

![Capture d’écran illustrant la configuration BMC.](media/itsmc-secure-webhook-connections-bmc/bmc-configuration.png)
