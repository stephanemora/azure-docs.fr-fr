---
title: Utiliser la solution de supervision à distance pour détecter des problèmes d’appareil – Azure | Microsoft Docs
description: Ce tutoriel montre comment utiliser des règles et des actions pour détecter automatiquement les problèmes d’appareils liés au seuil dans la solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 695d1b1124c042b61837f0a1257ac2a201ae4860
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91539399"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>Tutoriel : Détecter des problèmes liés aux appareils connectés à votre solution de supervision

Dans ce tutoriel, vous configurez l’accélérateur de solution de supervision à distance pour détecter les problèmes liés à vos appareils IoT connectés. Pour détecter les problèmes liés à vos appareils, vous ajoutez des règles qui génèrent des alertes sur le tableau de bord de la solution.

Pour présenter les règles et alertes, le tutoriel utilise un condenseur simulé. L’appareil de refroidissement est géré par une entreprise appelée Contoso et est connecté à l’accélérateur de solution de supervision à distance. Contoso a déjà une règle qui génère une alerte critique lorsque la pression signalée par un appareil de refroidissement dépasse 298 psi. En tant qu’opérateur chez Contoso, vous souhaitez identifier les appareils de refroidissement avec des capteurs défectueux en recherchant des pics de pression initiale. Pour identifier ces appareils, vous ajoutez une règle qui génère une alerte d’avertissement lorsque la pression dans le refroidisseur dépasse 150 psi.

Vous devez également créer une alerte critique pour un refroidisseur lorsque, au cours des cinq dernières minutes, l’humidité moyenne dans l’appareil était supérieure à 80 % et la température de l’appareil était supérieure à 75 degrés Fahrenheit (24 degrés Celsius).

Dans ce tutoriel, vous allez :

>[!div class="checklist"]
> * Afficher les règles dans votre solution
> * Créer une règle
> * Créer une règle avec plusieurs conditions
> * Modifier une règle existante
> * Activer et désactiver les règles de commutateur

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="review-the-existing-rules"></a>Réviser les règles existantes

La page **Règles** de l’accélérateur de solution affiche une liste de toutes les règles actuellement proposées :

[![Page de règles](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

Pour afficher uniquement les règles qui s’appliquent aux appareils de refroidissement, appliquez un filtre. Vous pouvez afficher plus d’informations sur une règle et modifier celle-ci lorsque vous la sélectionnez dans la liste :

[![Afficher les détails de la règle](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>Créer une règle

Pour ajouter une nouvelle règle qui génère un avertissement lorsque la pression dans un appareil de refroidissement dépasse 150 psi, choisissez **Nouvelle règle**. Utilisez les valeurs suivantes pour créer la règle :

| Paramètre          | Valeur                                 |
| ---------------- | ------------------------------------- |
| Nom de la règle        | Avertissement de refroidissement                       |
| Description      | Pression de refroidissement supérieure à 150 psi |
| Groupe d’appareils     | Groupe d’appareils de **refroidissement**             |
| Calcul      | Immédiat                               |
| Champ de condition 1| pression                              |
| Opérateur de condition 1 | Supérieur à                      |
| Valeur de condition 1    | 150                               |
| Niveau de gravité  | Avertissement                               |

[![Créer la règle d’avertissement](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-expanded.png#lightbox)

Pour enregistrer la nouvelle règle, cliquez sur **Appliquer**.

Vous pouvez voir le moment où la règle est déclenchée dans la page **Règles** ou la page **Tableau de bord** :

[![Règle d’avertissement déclenchée](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-expanded.png#lightbox)

## <a name="create-an-advanced-rule"></a>Créer une règle avancée

Pour créer une règle avec plusieurs conditions qui génère une alerte critique quand l’humidité moyenne de l’appareil de refroidissement des cinq dernières minutes est supérieure à 80 % et que la température moyenne de l’appareil de refroidissement est supérieure à 75 degrés Fahrenheit (24 degrés Celsius), choisissez **Nouvelle règle**. Utilisez les valeurs suivantes pour créer la règle :

| Paramètre          | Valeur                                 |
| ---------------- | ------------------------------------- |
| Nom de la règle        | Humidité et température critiques de l’appareil de refroidissement    |
| Description      | L’humidité et la température sont critiques |
| Groupe d’appareils     | Groupe d’appareils de **refroidissement**             |
| Calcul      | Average                               |
| Période      | 5                                     |
| Champ de condition 1| humidité                              |
| Opérateur de condition 1 | Supérieur à                      |
| Valeur de condition 1    | 80                                |
| Niveau de gravité  | Critique                              |

[![Créer une règle à plusieurs conditions règle - première partie](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-expanded.png#lightbox)

Pour ajouter la seconde condition, cliquez sur « + Ajouter une condition ». Utilisez les valeurs suivantes pour la nouvelle condition :

| Paramètre          | Valeur                                 |
| ---------------- | ------------------------------------- |
| Champ de condition 2| température                           |
| Opérateur de condition 2 | Supérieur à                      |
| Valeur de condition 2    | 75                                |

[![Créer une règle à plusieurs conditions règle - deuxième partie](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-expanded.png#lightbox)

Pour enregistrer la nouvelle règle, cliquez sur **Appliquer**.

Vous pouvez voir le moment où la règle est déclenchée dans la page **Règles** ou la page **Tableau de bord** :

[![Déclenchement d’une règle à plusieurs conditions](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-expanded.png#lightbox)

## <a name="edit-an-existing-rule"></a>Modifier une règle existante

Pour modifier une règle existante, sélectionnez-la dans la liste des règles et cliquez sur **Modifier** :

[![Modifier une règle](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-expanded.png#lightbox)

## <a name="disable-a-rule"></a>Désactiver une règle

Pour désactiver temporairement une règle, vous pouvez la désactiver dans la liste des règles. Sélectionnez la règle à désactiver, puis choisissez **Désactiver**. L’**état** de la règle dans la liste change pour indiquer que la règle est désactivée. Vous pouvez réactiver une règle que vous avez précédemment désactivée à l’aide de la même procédure.

[![Désactiver une règle](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

Vous pouvez activer et désactiver plusieurs règles en même temps en sélectionnant plusieurs règles dans la liste.

## <a name="delete-a-rule"></a>Supprimer une règle

Pour supprimer définitivement une règle, vous pouvez la supprimer de la liste de règles. Sélectionnez la règle à supprimer, puis choisissez **Supprimer**.

[![Capture d’écran montrant la page « Règles » avec la fenêtre « Supprimer la règle » mise en évidence.](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-expanded.png#lightbox)

Après avoir confirmé la suppression de la règle, vous avez la possibilité de supprimer toutes les alertes associées à la règle à partir de la page **Maintenance**.

[![Suppression de règle](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-expanded.png#lightbox)

Vous ne pouvez supprimer qu’une règle à la fois.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a montré comment utiliser la page **Règles** dans l’accélérateur de solution de supervision à distance pour créer et gérer des règles qui déclenchent des alertes dans la solution. Pour découvrir comment utiliser l’accélérateur de solution pour gérer et configurer vos appareils connectés, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Configurer et gérer les appareils connectés à votre solution de supervision](iot-accelerators-remote-monitoring-manage.md)