---
title: Advanced Threat Protection pour Azure Cosmos DB
description: Découvrez comment Azure Cosmos DB assure le chiffrement des données au repos, et comment s’effectue son implémentation.
author: monhaber
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: v-mohabe
ms.custom: seodec18
ms.openlocfilehash: 02281a1cad9c7e6f9680441a699fa5d34558b890
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501587"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db"></a>Advanced Threat Protection pour Azure Cosmos DB

Advanced Threat Protection pour Azure Cosmos DB offre une couche supplémentaire de sécurité intelligente qui détecte les tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses des comptes Azure Cosmos DB. Cette couche de protection vous permet de traiter efficacement les menaces sans pour autant être un expert en sécurité et de les intégrer aux systèmes centraux de supervision de la sécurité.

Les alertes de sécurité sont déclenchées lorsque des anomalies se produisent dans l’activité. Ces alertes de sécurité sont intégrées avec  [Azure Security Center](https://azure.microsoft.com/services/security-center/) et sont également envoyées par e-mail aux administrateurs d’abonnement, avec les détails des activités suspectes et des recommandations sur la façon d’examiner et de corriger les menaces.

> [!NOTE]
>
> * Advanced Threat Protection pour Azure Cosmos DB n’est actuellement disponible que pour l’API SQL.
> * Advanced Threat Protection pour Azure Cosmos DB est actuellement disponible dans les régions de cloud souverain et Azure Government.

Pour une expérience d’investigation complète des alertes de sécurité, nous vous recommandons d’activer la [journalisation des diagnostics dans Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging), ce qui journalise les opérations sur la base de données elle-même, notamment les opérations CRUD sur l’ensemble des documents, conteneurs et bases de données.

## <a name="set-up-advanced-threat-protection"></a>Configurer Advanced Threat Protection

### <a name="set-up-atp-using-the-portal"></a>Configurer ATP à l’aide du portail

1. Lancez le Portail Azure à l’adresse   [https://portal.azure.com](https://portal.azure.com/).

2. À partir du compte Azure Cosmos DB, dans le menu **Paramètres**, sélectionnez **Sécurité avancée**.

    ![Configurer ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. Dans le panneau de configuration  **Sécurité avancée** :

    * Cliquez sur l’option **Advanced Threat Protection** pour la définir sur **On (Activé)** .
    * Cliquez sur  **Enregistrer**  pour enregistrer la stratégie Advanced Threat Protection, nouvelle ou mise à jour.   

### <a name="set-up-atp-using-rest-api"></a>Configurer ATP à l’aide de l’API REST

Utilisez les commandes de l’API REST pour créer, mettre à jour ou obtenir le paramètre Advanced Threat Protection pour un compte Azure Cosmos DB spécifique.

* [Advanced Threat Protection - Créer](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Advanced Threat Protection - Obtenir](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Configurer ATP à l’aide d’Azure PowerShell

Utilisez les cmdlets PowerShell suivantes :

* [Activer Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Obtenir Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Désactiver Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

## <a name="manage-atp-security-alerts"></a>Gérer les alertes de sécurité ATP

Quand des anomalies d’activité Azure Cosmos DB sont détectées, une alerte est déclenchée. Elle contient des informations sur l’événement de sécurité suspect. 

 Dans Azure Security Center, vous pouvez examiner et gérer vos [alertes de sécurité](../security-center/security-center-alerts-overview.md) actuelles.  Cliquez sur l’alerte spécifiée dans [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) pour voir les causes possibles et les actions recommandées afin d’examiner et d’atténuer la menace potentielle. L’image suivante représente un exemple d’informations d’alerte fournies dans Security Center.

 ![Détails de la menace](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

Une notification par e-mail est également envoyée avec les détails de l’alerte et les actions recommandées. L’image suivante représente un exemple d’alerte par e-mail.

 ![Détails de l’alerte](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Alertes ATP Cosmos DB

 Pour afficher la liste des alertes générées lors de la supervision des comptes Azure Cosmos DB, consultez la section [Alertes Cosmos DB](../security-center/security-center-alerts-data-services.md#cosmos-db) dans la documentation Security Center.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la  [Journalisation des diagnostics dans Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging#turn-on-logging-in-the-azure-portal)
* En savoir plus sur  [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)