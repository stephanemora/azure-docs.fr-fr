---
title: Connecter les alertes Azure Defender à Azure Sentinel
description: Découvrez comment connecter les alertes Azure Defender à partir d’Azure Security Center et les diffuser vers Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 07/08/2021
ms.author: yelevin
ms.openlocfilehash: 0d348231c28e33b7eaef97a468e7e8a6a8677c32
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525658"
---
# <a name="connect-azure-defender-alerts-from-azure-security-center"></a>Connecter les alertes Azure Defender à partir d’Azure Security Center

## <a name="background"></a>Arrière-plan

[Azure Defender](../security-center/azure-defender.md), la plateforme cloud intégrée de protection de charge de travail (CWPP) d’[Azure Security Center](../security-center/security-center-introduction.md), est un outil de gestion de la sécurité qui vous permet de détecter les menaces et d’y répondre rapidement sur les charges de travail cloud hybride. 

Ce connecteur vous permet de diffuser vos alertes de sécurité Azure Defender depuis Azure Security Center vers Azure Sentinel, afin que vous puissiez visualiser les alertes Defender, les analyser et y répondre, ainsi que les incidents qu’elles génèrent, dans un contexte de menace organisationnel plus large.

Comme Azure Defender lui-même est activé par abonnement, le connecteur Azure Defender est lui aussi activé ou désactivé séparément pour chaque abonnement.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

### <a name="alert-synchronization"></a>Synchronisation des alertes

- Lorsque vous connectez Azure Defender à Azure Sentinel, l’état des alertes Azure Defender qui sont ingérées dans Azure Sentinel est synchronisé entre les deux services. Ainsi, par exemple, lorsqu’une alerte est fermée dans Azure Defender, cette alerte s’affiche également comme étant fermée dans Azure Sentinel.

- La modification de l’état d’une alerte dans Azure Defender n’aura *pas* d’impact sur l’état des **incidents** Azure Sentinel qui contiennent l’alerte Azure Sentinel, mais uniquement celui de l’alerte elle-même.

### <a name="bi-directional-alert-synchronization"></a>Synchronisation bidirectionnelle des alertes

> [!IMPORTANT]
>
> - La fonctionnalité de **synchronisation bidirectionnelle des alertes** est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

- L’activation de la **synchronisation bidirectionnelle** synchronisera automatiquement l’état des alertes Azure Defender d’origine avec celui des incidents Azure Sentinel qui contiennent ces alertes. Ainsi, par exemple, lorsqu’un incident Azure Sentinel contenant une alerte Azure Defender est fermé, l’alerte originale correspondante sera automatiquement fermée dans Azure Defender.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel.

- Vous devez avoir le rôle Lecteur Sécurité dans les abonnements des journaux que vous diffusez.

- Vous devez activer au moins un plan **Azure Defender** dans Azure Security Center pour chaque abonnement pour lequel vous souhaitez activer le connecteur. Pour activer les plans Azure Defender sur un abonnement, vous devez avoir le rôle **Administrateur de la sécurité** pour cet abonnement.

-  Pour activer la synchronisation bidirectionnelle, vous devez avoir le rôle **Contributeur** ou **Administrateur de la sécurité** sur l’abonnement concerné.

## <a name="connect-to-azure-defender"></a>Connexion à Azure Defender

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données** dans le menu de navigation.

1. Dans la galerie des connecteurs de données, sélectionnez **Azure Defender**, puis cliquez sur **Ouvrir la page du connecteur** dans le volet d’informations.

1. Sous **Configuration**, vous verrez une liste des abonnements de votre locataire et l’état de leur connexion à Azure Defender. Sélectionnez le bouton bascule **État** à côté de chaque abonnement dont vous souhaitez diffuser les alertes dans Azure Sentinel. Si vous souhaitez connecter plusieurs abonnements à la fois, vous pouvez le faire en cochant les cases à côté des abonnements concernés, puis en sélectionnant le bouton **Connecter** dans la barre située au-dessus de la liste.

    > [!NOTE]
    > - Les cases à cocher et les boutons bascule **Connecter** ne sont actifs que sur les abonnements pour lesquels vous disposez des autorisations requises.
    > - Le bouton **Connecter** n’est actif que si la case d’au moins un abonnement a été cochée.

1. Pour activer la synchronisation bidirectionnelle sur un abonnement, localisez l’abonnement dans la liste et choisissez **Activé** dans la liste déroulante de la colonne **Synchronisation bidirectionnelle (préversion)** . Pour activer la synchronisation bidirectionnelle sur plusieurs abonnements à la fois, cochez les cases correspondantes et sélectionnez le bouton **Activer la synchronisation bidirectionnelle** dans la barre située au-dessus de la liste.

    > [!NOTE]
    > - Les cases à cocher et les listes déroulantes ne sont actives que sur les abonnements pour lesquels vous disposez des [autorisations requises](#prerequisites).
    > - Le bouton **Activer la synchronisation bidirectionnelle** n’est actif que si la case d’au moins un abonnement a été cochée.

1. Dans la colonne **Plans Azure Defender** de la liste, vous pouvez voir si les plans Azure Defender sont activés sur votre abonnement (condition préalable à l’activation du connecteur). La valeur de cette colonne pour chaque abonnement sera soit vide (ce qui signifie qu’aucun plan Defender n’est activé), soit « Tous activés », soit « Certains activés ». Ceux qui indiquent « Certains activés » auront également un lien **Activer tout** que vous pourrez sélectionner et qui vous mènera au tableau de bord de configuration d’Azure Defender pour cet abonnement, où vous pourrez choisir les plans Defender à activer. Le bouton de lien **Activer Azure Defender pour tous les abonnements** dans la barre située au-dessus de la liste vous mènera à votre page de démarrage d’Azure Defender, où vous pouvez choisir sur quels abonnements activer Azure Defender.

    :::image type="content" source="./media/connect-azure-security-center/azure-defender-config.png" alt-text="Capture d’écran de la configuration du connecteur Azure Defender":::

1. Vous pouvez indiquer si vous voulez que les alertes d’Azure Defender génèrent automatiquement des incidents dans Azure Sentinel. Sous **Créer des incidents**, sélectionnez **Activé** pour activer la règle d’analyse par défaut qui [crée automatiquement des incidents à partir d’alertes](create-incidents-from-alerts.md). Vous pouvez ensuite modifier cette règle sous **Analytique** sous l’onglet **Règles actives**.

    > [!TIP]
    > Lorsque vous configurez des [règles d’analyse personnalisées](detect-threats-custom.md) pour des alertes provenant d’Azure Defender, tenez compte de la gravité de l’alerte pour éviter d’ouvrir des incidents pour des alertes d’information. 
    >
    > Les alertes d’information dans Azure Security Center ne représentent pas un risque de sécurité en soi et ne sont pertinentes que dans le contexte d’un incident existant et ouvert. Pour plus d’informations, consultez [Alertes et incidents de sécurité dans Azure Security Center](../security-center/security-center-alerts-overview.md).
    > 
    

## <a name="find-and-analyze-your-data"></a>Rechercher et analyser vos données

> [!NOTE]
> La synchronisation des alertes *dans les deux sens* peut prendre quelques minutes. Les modifications de l’état des alertes peuvent ne pas être affichées immédiatement.

- Les alertes Azure Defender sont stockées dans la table *SecurityAlert* de votre espace de travail Log Analytics.

- Pour interroger les alertes Azure Defender dans Log Analytics, copiez le code suivant dans votre fenêtre de requête comme point de départ :

    ```kusto
    SecurityAlert 
    | where ProductName == "Azure Security Center"
    ```

- Consultez l’onglet **Étapes suivantes** dans la page du connecteur pour obtenir d’autres exemples de requêtes utiles, des modèles de règles d’analyse et des recommandations de classeurs.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment connecter Azure Defender à Azure Sentinel et comment synchroniser les alertes entre eux. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](detect-threats-built-in.md).
- Écrivez vos propres règles pour [détecter les menaces](detect-threats-custom.md).