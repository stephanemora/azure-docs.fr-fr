---
title: Connexion des solutions Agari Phishing Defense et Agari Brand Protection à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur Agari Phishing Defense and Brand Protection pour extraire ses journaux dans Azure Sentinel. Affichez les données Agari dans des classeurs, créez des alertes et améliorez l’investigation.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 3684b5a8069a6b683ae562c527af89814362a7f9
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806984"
---
# <a name="connect-your-agari-phishing-defense-and-brand-protection-solutions-to-azure-sentinel"></a>Connexion des solutions Agari Phishing Defense et Agari Brand Protection à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Agari Phishing Defense and Brand Protection est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Le connecteur Agari Phishing Defense and Brand Protection vous permet de connecter facilement les journaux de vos solutions Brand Protection et Phishing Defense à Azure Sentinel. Vous pourrez ainsi afficher les données dans des classeurs, les interroger pour créer des alertes personnalisées et les incorporer pour améliorer l’investigation. Les solutions Agari s’intègrent à Azure Sentinel avec Azure Functions et l’API REST.

En outre, les clients Brand Protection et Phishing Defense peuvent tirer parti du partage du renseignement sur les menaces grâce à l’API Security Graph.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

Voici les éléments requis pour connecter les solutions Agari Phishing Defense et Agari Brand Protection à Azure Sentinel :

- Autorisations d’accès en lecture et en écriture à l’espace de travail Azure Sentinel.

- Autorisations d’accès en lecture aux clés partagées de l’espace de travail. [En savoir plus sur les clés d’espace de travail](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

- Autorisations d’accès en lecture et en écriture à Azure Functions pour créer une application de fonction. [En savoir plus sur Azure Functions](../azure-functions/index.yml).

- Votre **ID client** et vos **Clés secrètes** Agari (identiques dans toutes les solutions Agari). Pour obtenir des instructions, consultez le [site des développeurs Agari](https://developers.agari.com/agari-platform/docs/quick-start).

## <a name="configure-and-connect-agari-solutions"></a>Configuration et connexion de solutions Agari 

Les solutions Agari peuvent intégrer et exporter directement des journaux dans Azure Sentinel avec une application de fonction Azure.

> [!NOTE]
> Ce connecteur utilise Azure Functions pour se connecter aux solutions Agari et ainsi extraire les journaux dans Azure Sentinel. Il peut en résulter des coûts supplémentaires d’ingestion des données. Pour plus d’informations, consultez la page [Tarifs d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

1. **Collectez vos informations d’identification relatives à l’API Agari :** 

    Veillez à disposer de votre **ID client** et de vos **Clés secrètes** Agari. Vous trouverez des instructions sur le [site des développeurs Agari](https://developers.agari.com/agari-platform/docs/quick-start#generate-api-credentials).

1. **(Facultatif) Activez l’API Security Graph :** 

    L’application de fonction Agari permet de partager des informations sur les menaces avec Azure Sentinel via l’API Security Graph. Pour utiliser cette fonctionnalité, vous devez activer le [connecteur Sentinel Threat Intelligence Platforms](connect-threat-intelligence.md) et [inscrire une application](/graph/auth-register-app-v2) dans Azure Active Directory.

    À l’issue de ce processus, vous disposerez de trois informations à utiliser lors du déploiement de l’application de fonction ci-dessous : **l’ID de locataire Graph**, **l’ID de client Graph** et la **Clé secrète client Graph**.

1. **Déployez le connecteur et l’application de fonction Azure associée :** 

    1. Dans le portail Azure Sentinel, sélectionnez **Connecteurs de données**. Sélectionnez **Agari Phishing Defense and Brand Protection (préversion)** , puis **Ouvrir la page du connecteur**.

    1. Sous **Configuration**, copiez **l’ID d’espace de travail** et la **clé primaire** Azure Sentinel, puis mettez-les de côté.

    1. Sélectionnez **Déployer sur Azure**. (Vous devrez peut-être faire défiler l’écran pour trouver le bouton.)

    1. L’écran **Déploiement personnalisé** s’affiche.

        - Entrez votre **ID client** et votre **Clé secrète client** (clés secrètes) Agari.

        - Entrez votre **ID d’espace de travail** et votre **Clé d’espace de travail** (clé primaire) Azure Sentinel que vous avez copiés et mis de côté.

        - Sélectionnez **Vrai** ou **Faux** pour les solutions Agari pour lesquelles vous disposez d’abonnements actifs.

        - Si vous avez créé une application Azure pour partager des IdC avec Azure Sentinel à l’aide de l’API Security Graph, sélectionnez **Vrai** pour **Activer le partage Security Graph**, puis entrez **l’ID de locataire Graph**, **l’ID client Graph** et la **Clé secrète client Graph**.

    1. Sélectionnez **Revoir + créer**. Une fois la validation terminée, cliquez sur **Créer**.

1. **Attribuez les autorisations nécessaires à votre application de fonction :**

    Le connecteur Agari utilise une variable d’environnement pour stocker les horodateurs d’accès aux journaux. Des autorisations doivent être attribuées à l’identité affectée par le système pour que l’application puisse écrire dans cette variable.

    1. Sur le Portail Azure, accédez à **Application de fonction**.

    1. Dans le panneau **Application de fonction**, sélectionnez votre application de fonction dans la liste, puis **Identité** sous **Paramètres** dans le menu de navigation de l’application de fonction.

    1. Dans l’onglet **Affectée par le système**, définissez **État** sur **Activé**. 

    1. Sélectionnez **Enregistrer** : un bouton **Attributions de rôle Azure** s’affiche. Cliquez dessus.

    1. Sur l’écran **Attributions de rôle Azure**, sélectionnez **Ajouter une attribution de rôle**. Définissez la **Portée** sur **Abonnement**, sélectionnez votre abonnement dans la liste déroulante **Abonnement**, puis définissez **Rôle** sur **Propriétaire des données App Configuration**. 

    1. Sélectionnez **Enregistrer**.

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion établie, les données s’affichent dans **Journaux**, sous *CustomLogs*, dans les tables suivantes : 

- `agari_apdtc_log_CL`
- `agari_apdpolicy_log_CL`
- `agari_bpalerts_log_CL`

Pour interroger les données des solutions Agari, entrez l’un des noms de tables ci-dessus dans la fenêtre Requête.

Consultez l’onglet **Étapes suivantes** de la page du connecteur pour obtenir des exemples de requêtes utiles.

## <a name="validate-connectivity"></a>Valider la connectivité

Jusqu’à 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter les solutions Agari Phishing Defense et Agari Brand Protection à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.
