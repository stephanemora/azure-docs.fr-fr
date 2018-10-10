---
title: Supervision des menaces dans le stockage Azure
description: Configurez la protection avancée contre les menaces dans le stockage Azure pour détecter les anomalies dans l’activité de votre compte et vous alerter sur les tentatives d’accès à votre compte potentiellement dangereuses.
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 09/24/2018
ms.author: ronmat
ms.manager: shaik
ms.openlocfilehash: 8b2ca2d5d6418d68cab847df80fc437e468249ed
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995638"
---
# <a name="azure-storage-advanced-threat-protection"></a>Protection avancée contre les menaces dans le stockage Azure

La protection avancée contre les menaces dans le stockage Azure détecte les anomalies dans l’activité de votre compte et vous alerte sur les tentatives d’accès à votre compte potentiellement dangereuses. Cette couche de protection vous permet de traiter efficacement les menaces sans pour autant être un expert en sécurité ni avoir besoin de gérer des systèmes de supervision de la sécurité.

Les menaces sont signalées en fonction des alertes de sécurité définies qui se déclenchent quand des anomalies sont détectées dans l’activité. Ces alertes intégrées à [Azure Security Center](https://azure.microsoft.com/services/security-center/) fournissent des détails sur l’activité suspecte, ainsi que des suggestions pour examiner et corriger les menaces. 

> [!NOTE]
> La protection avancée contre les menaces dans le stockage Azure est disponible uniquement pour le service BLOB. Les alertes de sécurité sont intégrées à Azure Security Center et sont envoyées par e-mail aux administrateurs de l’abonnement.

La protection avancée contre les menaces dans le stockage Azure ingère les données des journaux de diagnostic relatives aux requêtes de lecture, d’écriture et de suppression envoyées au service BLOB en vue de détecter les menaces potentielles. Pour examiner les alertes notifiées par la protection avancée contre les menaces, vous devez [configurer les journaux de diagnostic](storage-monitor-storage-account.md#configure-logging) de manière à activer tous les niveaux de journaux pour le service BLOB.

## <a name="set-up-advanced-threat-protection-in-the-portal"></a>Configurer la protection avancée contre les menaces dans le portail

1. Lancez le portail Azure sur [https://portal.azure.com](https://portal.azure.com/).

2. Accédez à la page de configuration du compte de stockage Azure que vous voulez protéger. Dans la page **Paramètres**, sélectionnez **Protection avancée contre les menaces**.

3. Dans le panneau de configuration **Protection avancée contre les menaces**
    * Activez (**ON**) l’option *Protection contre les menaces* avancée
    * Cliquez sur **Enregistrer** pour enregistrer la stratégie de protection avancée contre les menaces, nouvelle ou mise à jour.

![Activer la protection avancée contre les menaces dans le stockage Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

## <a name="explore-anomalies"></a>Explorer les anomalies

Quand des anomalies d’activité du stockage sont détectées, vous recevez une notification par e-mail qui contient des informations sur l’événement de sécurité suspect. Les détails de l’événement fournis sont les suivants :

* nature de l’anomalie
* nom du compte de stockage
* type du stockage
* heure de l’événement

L’e-mail fournit également des détails sur les causes possibles et les actions recommandées pour examiner et atténuer la menace potentielle.

![E-mail d’alerte envoyé par la protection avancée contre les menaces dans le stockage Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Vous pouvez examiner et gérer vos alertes de sécurité actuelles à partir de la [vignette Alertes de sécurité](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts) dans Azure Security Center. Cliquez sur une alerte pour afficher les détails et les actions recommandées pour examiner la menace actuelle et atténuer les menaces futures.

![E-mail d’alerte envoyé par la protection avancée contre les menaces dans le stockage Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Alertes de collection

Les alertes sont générées en cas de détection de tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes de stockage. Ces événements peuvent déclencher les alertes suivantes :

* **Accès à partir d’un emplacement inhabituel** : cette alerte est déclenchée en cas de changement dans le modèle d’accès à un compte de stockage. Cela peut être le cas, par exemple, quand quelqu’un a accédé à un compte de stockage à partir d’un emplacement géographique inhabituel. Dans certains cas, l’alerte détecte une action légitime (une nouvelle application ou opération de maintenance du développeur). Dans d’autres cas, l’alerte détecte une action malveillante (ancien employé, attaquant externe, etc.).

* **Extraction de données inhabituelle** : cette alerte est déclenchée en cas de changement dans le modèle d’extraction des données à partir d’un compte de stockage. Cela peut être le cas, par exemple, quand quelqu’un a accédé à une quantité inhabituelle de données dans un compte de stockage. Dans certains cas, l’alerte détecte une action légitime (activité de maintenance). Dans d’autres cas, l’alerte détecte une action malveillante (exfiltration/violation de données, transfert de données non autorisé).

* **Accès anonyme inhabituel** : cette alerte est déclenchée en cas de changement dans le modèle d’accès à un compte de stockage. Cela peut être le cas, par exemple, quand quelqu’un a accédé anonymement à un compte de stockage. Dans certains cas, l’alerte détecte un accès légitime par le biais d’un accès en lecture public. Dans d’autres cas, l’alerte détecte un accès non autorisé qui exploite l’accès en lecture public à un conteneur et ses objets blob.

* **Suppression inattendue** : cette alerte est déclenchée quand une ou plusieurs opérations de suppression inattendue se produisent dans un compte de stockage, sur la base de l’analyse de l’historique du compte de stockage. Cela peut être le cas, par exemple, quand quelqu’un a effectué une opération *DeleteBlob* en utilisant une nouvelle application et une nouvelle adresse IP. Dans certains cas, l’alerte détecte une action légitime (l’administrateur a utilisé un autre navigateur lors d’un déplacement professionnel). Dans d’autres cas, l’alerte détecte une action malveillante (un attaquant a supprimé des données). 
 
* **Changement d’autorisation d’accès** : cette alerte est déclenchée en cas de changement inattendu de l’autorisation d’accès à un compte de stockage. Cela peut être le cas, par exemple, quand quelqu’un a changé l’autorisation d’accès à un compte de stockage en utilisant une nouvelle application et une nouvelle adresse IP. Dans certains cas, l’alerte détecte une action légitime (l’administrateur a utilisé un autre navigateur lors d’un déplacement professionnel). Dans d’autres cas, l’alerte détecte une action malveillante (par exemple, un attaquant a élevé les privilèges d’un compte auquel il a réussi à accéder). 

* **Chargement d’un package de service cloud Azure** : cette alerte est déclenchée en cas de chargement inattendu d’un package de service cloud Azure (fichier *.cspkg*) dans un compte de stockage. Cela peut être le cas, par exemple, quand quelqu’un a chargé un fichier *.cspkg* à partir d’une nouvelle adresse IP. Dans certains cas, l’alerte détecte une action légitime. Dans d’autres cas, l’alerte détecte une action malveillante (par exemple, un package de service cloud a été chargé en vue du déploiement d’un service malveillant).    
   

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [journaux dans les comptes de stockage Azure ](/rest/api/storageservices/About-Storage-Analytics-Logging)

* En savoir plus sur [Azure Security Center](../../security-center/security-center-intro.md)