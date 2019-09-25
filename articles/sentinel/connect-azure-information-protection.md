---
title: Connexion des données Azure Information Protection à Azure Sentinel (préversion) | Microsoft Docs
description: Découvrez comment connecter les données Azure Information Protection dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2019
ms.author: cabailey
ms.openlocfilehash: 0614d24b19ef39cebdf4cb47fdd2d44470ea59c0
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067746"
---
# <a name="connect-data-from-azure-information-protection"></a>Connecter des données depuis Azure Information Protection

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez activer le streaming des informations de journalisation d’[Azure Information Protection](https://azure.microsoft.com/services/information-protection/) vers Azure Sentinel en configurant le connecteur de données Azure Information Protection. Azure Information Protection vous aide à contrôler et à sécuriser toutes vos données sensibles qui sont stockées dans le cloud ou localement.

Si la [création de rapports centralisés pour Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) est déjà configurée afin que les informations de journalisation de ce service soient stockées dans le même espace de travail Log Analytics que celui que vous utilisez pour Azure Sentinel, vous pouvez ignorer l’étape de configuration de ce connecteur de données. Les informations de journalisation d’Azure Information Protection sont déjà disponibles dans Azure Sentinel.

En revanche, si les informations de journalisation d’Azure Information Protection sont envoyées vers un espace de travail Log Analytics différent de celui que vous avez sélectionné pour Azure Sentinel, effectuez l’une des opérations suivantes :

- Changez l’espace de travail sélectionné dans Azure Sentinel.

- Changez l’espace de travail pour Azure Information Protection, en configurant ce connecteur de données.
    
    Si vous changez l’espace de travail, les nouvelles données de rapport pour Azure Information Protection seront désormais stockées dans l’espace de travail utilisé pour Azure Sentinel, et les données historiques ne seront pas disponibles dans Azure Sentinel. De plus, si l’espace de travail précédent était configuré pour des requêtes, alertes ou API REST personnalisées, celles-ci doivent être reconfigurées pour l’espace de travail Azure Sentinel afin de pouvoir les utiliser avec Azure Information Protection. Aucune reconfiguration n’est nécessaire pour les clients et services qui utilisent Azure Information Protection.

## <a name="prerequisites"></a>Prérequis

- L’un des rôles d’administrateur Azure AD suivants pour votre locataire : administrateur Azure Information Protection, administrateur de la sécurité ou administrateur général.
    
    > [!NOTE]
    > Vous ne pouvez pas utiliser le rôle d’administrateur Azure Information Protection si votre locataire se trouve sur la [plateforme d’étiquetage unifié](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).

- Les autorisations de lecture et d’écriture appropriées sur l’espace de travail Log Analytics que vous utilisez pour Sentinel et Azure Information Protection.

- Azure Information Protection a été ajouté au portail Azure. Si vous avez besoin d’aide pour cette étape, consultez [Ajouter Azure Information Protection au portail Azure](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal).

## <a name="connect-to-azure-information-protection"></a>Connexion à Azure Information Protection

Effectuez les étapes suivantes si vous n’avez pas encore configuré d’espace de travail Log Analytics pour Azure Information Protection ou si vous devez changer l’espace de travail dans lequel sont stockées les informations de journalisation d’Azure Information Protection. 

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données**, puis **Azure Information Protection**.

2. Dans le panneau **Azure Information Protection**, sélectionnez **Ouvrir la page du connecteur**.

3. Dans le panneau suivant, dans la section **Configuration**, sélectionnez **Azure Information Protection** pour accéder à l’**analytique Azure Information Protection**.

4. Dans la liste des espaces de travail disponibles, sélectionnez celui que vous utilisez actuellement pour Azure Sentinel. Si vous sélectionnez un autre espace de travail, les données de rapport d’Azure Information Protection ne seront pas transmises à Azure Sentinel.

5. Après avoir sélectionné un espace de travail, sélectionnez **OK**. Vous voyez normalement le connecteur **STATUS** passer à l’état **Connected** (Connecté).

6. Les données de rapport d’Azure Information Protection sont stockées dans la table **InformationProtectionLogs_CL** contenue dans l’espace de travail sélectionné. 
    
    Pour utiliser le schéma approprié dans Azure Monitor pour ces données de rapport, recherchez **InformationProtectionEvents**. Pour plus d’informations sur ces fonctions d’événement, consultez la section [Référence de schéma conviviale pour les fonctions d’événement](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) dans la documentation Azure Information Protection.

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure Information Protection à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
