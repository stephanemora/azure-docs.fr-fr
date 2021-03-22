---
title: 'Tutoriel : Créer une stratégie WAF pour Azure Front - Portail Azure'
description: Dans ce tutoriel, vous allez apprendre à créer une stratégie de pare-feu d’applications web (WAF) à l’aide du portail Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: victorh
ms.openlocfilehash: 8b1d1007e817bafe3d75f0f0d7c3fc6eb5470854
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729468"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Tutoriel : Créer une stratégie de pare-feu d’applications web dans Azure Front Door à l’aide du portail Azure

Ce tutoriel explique comment créer une stratégie Azure Web Application Firewall (WAF) simple et comment l’appliquer sur un hôte front-end dans Azure Front Door.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une stratégie de pare-feu d’applications web (WAF)
> * Associer la stratégie à un hôte front-end
> * Configurer des règles WAF

## <a name="prerequisites"></a>Prérequis

Créez un profil Front Door en suivant les instructions décrites dans [Démarrage rapide : créer un profil Front Door](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Créer une stratégie de pare-feu d’applications web

Commencez par créer une stratégie WAF de base avec un ensemble de règles managées par défaut à partir du portail. 

1. Dans l’angle supérieur gauche de l’écran, sélectionnez **Créer une ressource** > recherchez **WAF** > sélectionnez **Pare-feu d’applications web (préversion)** > sélectionnez **Créer**.
2. Sous l’onglet **De base** dans la page **Créer une stratégie WAF**, entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis sélectionnez **Vérifier + créer** :

    | Paramètre                 | Value                                              |
    | ---                     | ---                                                |
    | Abonnement            |Sélectionnez le nom de votre abonnement Front Door.|
    | Resource group          |Sélectionnez le nom de votre groupe de ressources Front Door.|
    | Nom de stratégie             |Entrez un nom unique pour votre stratégie WAF.|

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="Capture d’écran de la page Créer une stratégie WAF, avec le bouton Vérifier + créer et des zones de liste pour l’abonnement, le groupe de ressources et le nom de la stratégie." border="false":::

3. Sous l’onglet **Association** de la page **Créer une stratégie WAF**, sélectionnez **Ajouter un hôte frontend**, entrez les paramètres suivants, puis sélectionnez **Ajouter** :

    | Paramètre                 | Valeur                                              |
    | ---                     | ---                                                |
    | Front Door              | Sélectionnez le nom de votre profil Front Door.|
    | Hôte frontend           | Sélectionnez le nom de votre hôte Front Door, puis sélectionnez **Ajouter**.|
    
    > [!NOTE]
    > Si l’hôte front-end est associé à une stratégie WAF, il apparaît en grisé. Vous devez d’abord supprimer l’hôte front-end de la stratégie associée, puis réassocier l’hôte front-end à une nouvelle stratégie WAF.
1. Sélectionnez **Vérifier + créer**, puis sélectionnez **Créer**.

## <a name="configure-web-application-firewall-rules-optional"></a>Configurer des règles de pare-feu d’applications web (facultatif)

### <a name="change-mode"></a>Changer de mode

Quand vous créez une stratégie WAF, celle-ci est par défaut en mode **Détection**. En mode **Détection**, la stratégie WAF ne bloque aucune requête, mais elle enregistre les requêtes correspondant aux règles WAF dans les journaux WAF.
Pour voir le fonctionnement de WAF, passez du mode **Détection** au mode **Prévention**. En mode **Prévention**, les requêtes qui correspondent à des règles définies dans un ensemble de règles par défaut sont bloquées et enregistrées dans les journaux WAF.

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="Capture d’écran de la section Paramètres de stratégie. Le mode est défini sur Prévention." border="false":::

### <a name="custom-rules"></a>Règles personnalisées

Vous pouvez créer une règle personnalisée en sélectionnant **Ajouter une règle personnalisée** sous la section **Règles personnalisées**. Cette action ouvre la page de configuration d’une règle personnalisée. Voici un exemple de configuration d’une règle personnalisée qui bloque une requête si la chaîne de requête contient **blockme**.

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="Capture d’écran de la page de configuration des règles personnalisées montrant les paramètres d’une règle qui vérifie si la variable QueryString contient la valeur blockme." border="false":::

### <a name="default-rule-set-drs"></a>Ensemble de règles par défaut

L’ensemble de règles par défaut managées par Azure est activé par défaut. La version par défaut actuelle est DefaultRuleSet_1.0. Dans **Règles gérées** de WAF, **Attribuer**, le nouvel ensemble de règles Microsoft_DefaultRuleSet_1.1 est disponible dans la liste déroulante.

Pour désactiver une règle spécifique dans un groupe de règles, développez les règles de ce groupe de règles, sélectionnez la **case à cocher** devant le numéro de la règle, puis sélectionnez **Désactiver** sous l’onglet ci-dessus. Pour changer le type d’action associée à une règle dans l’ensemble de règles, sélectionnez la case à cocher devant le numéro de la règle, puis sélectionnez l’onglet **Changer l’action** ci-dessus.

 :::image type="content" source="../media/waf-front-door-create-portal/managed2.png" alt-text="Capture d’écran de la page Règles managées montrant un ensemble de règles, des groupes de règles, des règles, ainsi que les boutons Activer, Désactiver et Changer l’action. Une règle est vérifiée." border="false":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, supprimez le groupe de ressources et toutes les ressources associées.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur Azure Front Door](../../frontdoor/front-door-overview.md)
