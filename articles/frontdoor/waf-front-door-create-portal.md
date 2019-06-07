---
title: Créer une stratégie de pare-feu d’application web pour Azure porte d’entrée à l’aide du portail Azure
titlesuffix: Azure web application firewall
description: Découvrez comment créer une stratégie de pare-feu (WAF) d’application web à l’aide du portail Azure.
services: frontdoor
documentationcenter: na
author: KumudD
manager: twooley
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 15a80dac0e0601480e22ad960f2827f3d8f290c0
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479059"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>Créer une stratégie de pare-feu d’applications Web pour Azure porte d’entrée à l’aide du portail Azure

Cet article décrit comment créer une stratégie de pare-feu (WAF) d’applications web Azure de base et l’appliquer à un hôte de serveur frontal à Azure porte d’entrée.

## <a name="prerequisites"></a>Conditions préalables

Créez un profil Front Door en suivant les instructions décrites dans [Démarrage rapide : créer un profil Front Door](quickstart-create-front-door.md). 

## <a name="create-a-waf-policy"></a>Créer une stratégie de pare-feu d’applications Web

Commencez par créer une stratégie de pare-feu d’applications Web de base avec managé par défaut règle définie (DRS) à l’aide du portail. 

1. Dans l’angle supérieur gauche de l’écran, sélectionnez **créer une ressource**> Recherchez **WAF**> sélectionnez **pare-feu d’applications Web (version préliminaire)** > sélectionnez  **Créer**.
2. Dans le **notions de base** onglet de la **créer une stratégie de pare-feu d’applications Web** page, entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis **révision + créer**:

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | Abonnement            |Sélectionnez le nom de votre abonnement porte d’entrée.|
    | Groupe de ressources          |Sélectionnez le nom de votre groupe de ressources porte d’entrée.|
    | Nom de la stratégie             |Entrez un nom unique pour votre stratégie de pare-feu d’applications Web.|

   ![Créer une stratégie de pare-feu d’applications Web](./media/waf-front-door-create-portal/basic.png)

3. Dans le **Association** onglet de la **créer une stratégie de pare-feu d’applications Web** page, sélectionnez **ajouter un ordinateur hôte frontend**, entrez les paramètres suivants, puis sélectionnez **ajouter**:

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | Porte d’entrée              | Sélectionnez le nom de votre profil porte d’entrée.|
    | Hôte frontend           | Sélectionnez le nom de votre hôte de la porte d’entrée, puis sélectionnez **ajouter**.|
    
    > [!NOTE]
    > Si l’hôte de serveur frontal est associé à une stratégie de pare-feu d’applications Web, il est indiqué comme apparaît en grisé. Vous devez d’abord supprimer l’hôte du serveur frontal de la stratégie associée et associer de nouveau l’hôte du serveur frontal vers une nouvelle stratégie de pare-feu d’applications Web.
1. Sélectionnez **révision + créer**, puis sélectionnez **créer**.

## <a name="configure-waf-rules-optional"></a>Configurer des règles de pare-feu d’applications Web (facultatifs)

### <a name="change-mode"></a>Modifier le mode

Lorsque vous créez une stratégie de pare-feu d’applications Web, par le pare-feu d’applications Web par défaut stratégie se trouve dans **détection** mode. Dans **détection** mode, WAF ne bloque pas les demandes, au lieu de cela, les demandes correspondant aux règles de pare-feu d’applications Web sont enregistrées dans les journaux WAF.
Pour voir le pare-feu d’applications Web en action, vous pouvez modifier les paramètres du mode de **détection** à **prévention**. Dans **prévention** mode, demande que les règles de correspondance qui sont définis dans par défaut règle définie (DRS) sont bloqués et enregistrés à journaux WAF.

 ![Mode de stratégie de modification WAF](./media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Règles personnalisées

Vous pouvez créer une règle personnalisée en sélectionnant **ajouter une règle personnalisée** sous le **règles personnalisées** section. Cette action lance la page de configuration de règle personnalisée. Voici un exemple de configuration d’une règle personnalisée pour bloquer une demande si la chaîne de requête contient **blockme**.

![Mode de stratégie de modification WAF](./media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Ensemble de règles par défaut (DRS)

Gérés par Azure un ensemble de règles par défaut est activée par défaut. Pour désactiver une règle individuelle au sein d’un groupe de règles, développez les règles dans ce groupe de règles, sélectionnez le **case à cocher** devant le numéro de la règle, puis sélectionnez **désactiver** sous l’onglet ci-dessus. Pour modifier les types d’actions pour les règles individuelles au sein de la règle définie, activez la case à cocher devant le numéro de la règle, puis sélectionnez le **modifiez action** onglet ci-dessus.

 ![Modifier l’ensemble de règles de pare-feu d’applications Web](./media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [pare-feu d’applications web Azure](waf-overview.md).
- En savoir plus sur [porte d’entrée Azure](front-door-overview.md).
