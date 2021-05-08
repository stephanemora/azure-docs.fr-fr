---
title: Connecter des données Microsoft 365 Defender à Azure Sentinel | Microsoft Docs
description: Découvrez comment ingérer des données d’incidents, d’alertes et d’événements bruts de Microsoft 365 Defender dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 16cf1b89c2660d2505685fa931cc8b97ccb42a9b
ms.sourcegitcommit: 18cd3c1c8cc47258c6a1a04e0e03d6248c52ef24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107992287"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Connecter des données de Microsoft 365 Defender à Azure Sentinel

> [!IMPORTANT]
>
> **Microsoft 365 Defender** était anciennement connu sous le nom de **Protection Microsoft contre les menaces** ou **MTP**.
>
> **Microsoft Defender for Endpoint** était précédemment appelé **Microsoft Defender Advanced Threat Protection**, ou **MDATP**.
>
> Vous pouvez voir les anciens noms encore en cours d’utilisation pendant un certain temps.

## <a name="background"></a>Arrière-plan

Le connecteur [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) d’Azure Sentinel avec intégration d’incidents vous permet de diffuser l’ensemble des incidents et alertes M365D dans Azure Sentinel, et de garder les incidents synchronisés entre les deux portails. Les incidents M365D incluent l’ensemble de leurs alertes, entités et autres informations pertinentes. Ils sont enrichis par des alertes des services de composants de M365D **Microsoft Defender pour Endpoint**, **Microsoft Defender pour Identity**, **Microsoft defender pour Office 365** et **Microsoft Cloud App Security**, et regroupent ces alertes.

Le connecteur vous permet également de diffuser des événements de **repérage avancé** de Microsoft Defender pour point de terminaison vers Azure Sentinel. Vous pouvez ainsi copier des requêtes de repérage avancé Microsoft Defender pour point de terminaison dans Azure Sentinel, enrichir des alertes Sentinel avec des données d'événement brutes Microsoft Defender pour point de terminaison afin de fournir des insights supplémentaires, et stocker les journaux avec une rétention accrue dans Log Analytics.

Pour plus d’informations sur l’intégration d’incidents et la collecte d’événements de repérage avancé, consultez [Intégration de Microsoft 365 Defender avec Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

> [!IMPORTANT]
>
> Le connecteur Microsoft 365 Defender est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’une licence valide pour Microsoft 365 Defender, comme décrit dans [Prérequis pour Microsoft 365 Defender](/microsoft-365/security/mtp/prerequisites). 

- Vous devez être **administrateur général** ou **administrateur de la sécurité** dans Azure Active Directory.

## <a name="connect-to-microsoft-365-defender"></a>Se connecter à Microsoft 365 Defender

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données**, **Microsoft 365 Defender (préversion)** dans la galerie, puis **Ouvrir la page du connecteur**.

1. Sous **Configuration**, dans la section **Connecter les incidents et les alertes**, cliquez sur le bouton **Connecter les incidents et les alertes**.

1. Pour éviter la duplication des incidents, il est recommandé d’activer la case à cocher **Désactiver toutes les règles de création d’incident Microsoft pour ces produits**.

    > [!NOTE]
    > Lorsque vous activez le connecteur Microsoft 365 Defender, tous les connecteurs des composants M365D (mentionnés au début de cet article) sont automatiquement connectés en arrière-plan. Pour déconnecter l’un des connecteurs des composants, vous devez commencer par déconnecter le connecteur Microsoft 365 Defender.

1. Pour interroger les données d'incident de Microsoft 365 Defender, utilisez l'instruction suivante dans la fenêtre de requête :
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. Si vous souhaitez collecter des événements de repérage avancé de Microsoft Defender pour Endpoint, vous pouvez collecter les types d’événements suivants à partir de leurs tables de repérage avancé correspondantes.

    1. Activez les cases à cocher des tables contenant les types d’événements que vous souhaitez collecter :

       | Nom de la table | Types d’événements |
       |-|-|
       | DeviceInfo | Informations sur l’ordinateur (notamment les informations sur le système d’exploitation) |
       | DeviceNetworkInfo | Propriétés réseau des machines |
       | DeviceProcessEvents | Création de processus et événements associés |
       | DeviceNetworkInfo | Connexion réseau et événements associés |
       | DeviceFileEvents | Création de fichier, modification et autres événements du système de fichiers |
       | DeviceRegistryEvents | Création et modification des entrées du Registre |
       | DeviceLogonEvents | Connexions et autres événements d’authentification |
       | DeviceImageLoadEvents | Événements de chargement de DLL |
       | DeviceEvents | Types d’événements supplémentaires |
       | DeviceFileCertificateInfo | Informations de certificat de fichiers signés |
       |

    1. Cliquez sur **Appliquer les modifications**.

    1. Pour interroger les tables de recherche avancée de menaces dans Log Analytics, entrez le nom de la table de la liste ci-dessus dans la fenêtre de requête.

## <a name="verify-data-ingestion"></a>Vérifier l’ingestion de données

Le graphique de données de la page du connecteur indique que vous ingérez des données. Vous remarquerez qu’il affiche trois lignes, respectivement pour les incidents, les alertes et les événements, et que la ligne des événements est une agrégation de volume d’événements de toutes les tables activées. Une fois le connecteur activé, vous pouvez utiliser les requêtes KQL suivantes pour générer des graphiques plus spécifiques.

Utilisez la requête KQL suivante pour obtenir un graphique des incidents Microsoft 365 Defender entrants :

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

Utilises la requête KQL suivante pour générer un graphique du volume d’événements pour une seule table (remplacez la table *DeviceEvents* par la table requise de votre choix) :

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

Sous l’onglet **Étapes suivantes**, vous trouverez des classeurs, exemples de requêtes et modèles de règle d’analyse utiles qui ont été inclus. Vous pouvez les exécuter sur place ou les modifier et les enregistrer.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment intégrer des incidents de Microsoft 365 Defender et des données d’événement de repérage avancé de Microsoft Defender pour Endpoint dans Azure Sentinel, à l’aide du connecteur Microsoft 365 Defender. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](./tutorial-detect-threats-built-in.md).
