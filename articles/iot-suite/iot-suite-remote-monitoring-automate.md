---
title: Détecter des problèmes d’appareils dans la solution de surveillance à distance - Azure | Microsoft Docs
description: Ce didacticiel vous montre comment utiliser des règles et des actions pour détecter automatiquement les problèmes d’appareils liés au seuil dans la solution de surveillance à distance.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 5acf35ed19a5b6baa2885fd58cfb7fbbe1ac3cd8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33774650"
---
# <a name="detect-issues-using-threshold-based-rules"></a>Détecter les problèmes à l’aide de règles de seuil

Ce didacticiel montre les fonctionnalités du moteur de règles dans la solution de surveillance à distance. Pour présenter ces fonctionnalités, le didacticiel utilise un scénario dans l’application Contoso IoT.

Contoso a une règle qui génère une alerte critique lorsque la pression signalée par un appareil de **refroidissement** dépasse 250 psi. En tant qu’opérateur, vous souhaitez identifier les appareils de **refroidissement** avec des capteurs défectueux en recherchant des pics de pression initiale. Pour identifier ces appareils, vous créez une règle qui génère un avertissement lorsque la pression dépasse 150 psi.

Vous avez également été informé qu’une alerte critique doit être déclenchée quand l’humidité moyenne de l’appareil de **refroidissement** des 5 dernières minutes est supérieure à 80 % et que la température de l’appareil de **refroidissement** des 5 dernières minutes est supérieure à 75 degrés Fahrenheit (24 degrés Celsius).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

>[!div class="checklist"]
> * Afficher les règles dans votre solution
> * Créer une nouvelle règle
> * Créer une règle avec plusieurs conditions
> * Modifier une règle existante
> * Supprimer une règle

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel, vous avez besoin d’une instance déployée de la solution de surveillance à distance dans votre abonnement Azure.

Si vous n’avez pas encore déployé la solution de surveillance à distance, vous devez suivre le tutoriel [Déployer l’accélérateur de solution de surveillance à distance](iot-suite-remote-monitoring-deploy.md).

## <a name="view-the-rules-in-your-solution"></a>Afficher les règles dans votre solution

La page **Règles** de la solution affiche une liste de toutes les règles actuellement proposées :

![Page des règles et actions](media/iot-suite-remote-monitoring-automate/rulesactions_v2.png)

Pour afficher uniquement les règles qui s’appliquent aux appareils de **refroidissement**, appliquez un filtre :

![Filtrer la liste des règles](media/iot-suite-remote-monitoring-automate/rulesactionsfilter_v2.png)

Vous pouvez afficher plus d’informations sur une règle et modifier celle-ci lorsque vous la sélectionnez dans la liste :

![Afficher les détails de la règle](media/iot-suite-remote-monitoring-automate/rulesactionsdetail_v2.png)

Pour désactiver, activer ou supprimer une ou plusieurs règles, sélectionnez des règles dans la liste :

![Sélectionner plusieurs règles](media/iot-suite-remote-monitoring-automate/rulesactionsmultiselect_v2.png)

## <a name="create-a-new-rule"></a>Créer une nouvelle règle

Pour ajouter une nouvelle règle qui génère un avertissement lorsque la pression dans un appareil de **refroidissement** dépasse 150 psi, choisissez **Nouvelle règle** :

![Créer une règle](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_v2.png)

Utilisez les valeurs suivantes pour créer la règle :

| Paramètre          | Valeur                                 |
| ---------------- | ------------------------------------- |
| Nom de la règle        | Avertissement de refroidissement                       |
| Description      | Pression de refroidissement supérieure à 150 psi |
| Groupe d’appareils     | Groupe d’appareils de **refroidissement**             |
| Calcul      | Immédiat                               |
| Champ de condition 1| pression                              |
| Opérateur de condition 1 | Supérieur à                      |
| Valeur de condition 1    | 150                               |
| Niveau de gravité  | Avertissement                               |

Pour enregistrer la nouvelle règle, choisissez **Appliquer**.

Vous pouvez voir le moment où la règle est déclenchée dans la page **Règles** ou la page **Tableau de bord**.

## <a name="create-a-new-rule-with-multiple-conditions"></a>Créer une règle avec plusieurs conditions

Pour créer une règle avec plusieurs conditions qui génère une alerte critique quand l’humidité moyenne de l’appareil de **refroidissement** des 5 dernières minutes est supérieure à 80 % et que la température de l’appareil de **refroidissement** des 5 dernières minutes est supérieure à 75 degrés Fahrenheit (24 degrés Celsius), choisissez **Nouvelle règle** :

![Créer une règle avec plusieurs conditions](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_mult_v2.png)

Utilisez les valeurs suivantes pour créer la règle :

| Paramètre          | Valeur                                 |
| ---------------- | ------------------------------------- |
| Nom de la règle        | Humidité et température critiques de l’appareil de refroidissement    |
| Description      | L’humidité et la température sont critiques |
| Groupe d’appareils     | Groupe d’appareils de **refroidissement**             |
| Calcul      | Moyenne                               |
| Période      | 5.                                     |
| Champ de condition 1| humidité                              |
| Opérateur de condition 1 | Supérieur à                      |
| Valeur de condition 1    | 80                               |
| Niveau de gravité  | Critique                              |

Pour ajouter la deuxième condition, cliquez sur +Ajouter une condition.

![Créer la condition 2](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2.png)

Utilisez les valeurs suivantes pour la nouvelle condition :

| Paramètre          | Valeur                                 |
| ---------------- | ------------------------------------- |
| Champ de condition 2| température                           |
| Opérateur de condition 2 | Supérieur à                      |
| Valeur de condition 2    | 75                                |

Pour enregistrer la nouvelle règle, choisissez **Appliquer**.

Vous pouvez voir le moment où la règle est déclenchée dans la page **Règles** ou la page **Tableau de bord**.

## <a name="edit-an-existing-rule"></a>Modifier une règle existante

Pour modifier une règle existante, sélectionnez-la dans la liste des règles.

![Modifier une règle](media/iot-suite-remote-monitoring-automate/rulesactionsedit_v2.png)

<!--## Disable a rule

To temporarily switch off a rule, you can disable it in the list of rules. Choose the rule to disable, and then choose **Disable**. The **Status** of the rule in the list changes to indicate the rule is now disabled. You can re-enable a rule that you previously disabled using the same procedure.

![Disable rule](media/iot-suite-remote-monitoring-automate/rulesactionsdisable.png)

You can enable and disable multiple rules at the same time if you select multiple rules in the list.-->

<!--## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a montré comment effectuer les opérations suivantes :

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Afficher les règles dans votre solution
> * Créer une nouvelle règle
> * Modifier une règle existante
> * Supprimer une règle

La détection des problèmes à l’aide de règles de seuil n’ayant plus de secrets pour vous, nous vous suggérons de découvrir les opérations suivantes :

* [Gérer et configurer vos appareils](./iot-suite-remote-monitoring-manage.md)
* [Dépanner et corriger les problèmes d’un appareil](./iot-suite-remote-monitoring-maintain.md)
* [Tester votre solution avec des appareils simulés](iot-suite-remote-monitoring-test.md)

<!-- Next tutorials in the sequence -->