---
title: Configurer des règles et des actions dans Azure IoT Central
description: Ce didacticiel vous montre, en tant que générateur, comment configurer des règles et des actions basées sur la télémétrie dans votre application Azure IoT Central.
services: iot-central
author: ankitgupta
ms.author: ankitgup
ms.date: 04/16/2018
ms.topic: tutorial
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 5ac19e0e25ea3e25ede4d87776c01f8bcaea4655
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202235"
---
# <a name="2---configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>2 - Configurer des règles et des actions pour votre appareil dans Azure IoT Central

Ce didacticiel vous montre, en tant que générateur, comment configurer des règles et des actions basées sur la télémétrie dans votre application Microsoft Azure IoT Central.

Dans ce didacticiel, vous créez une règle qui envoie un courrier électronique lorsque la température d’un climatiseur raccordé dépasse 90&deg; F.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une règle basée sur la télémétrie
> * Ajouter une action

## <a name="prerequisites"></a>Prérequis


Avant de commencer, vous devez effectuer le tutoriel [Définir un nouveau type d’appareil dans votre application](tutorial-define-device-type.md) pour créer le modèle d’appareil **Climatiseur raccordé** à utiliser.

## <a name="create-a-telemetry-based-rule"></a>Créer une règle basée sur la télémétrie

1. Pour ajouter une nouvelle règle basée sur la télémétrie pour votre application, dans le menu de navigation de gauche, choisissez **Device Explorer** :

    ![Page Device Explorer](media/tutorial-configure-rules/explorerpage.png)

    Vous voyez le modèle d’appareil **Climatiseur raccordé (1.0.0)** et l’appareil **Climatiseur raccordé-1** que vous avez créé dans le didacticiel précédent.

1. Pour commencer à personnaliser votre climatiseur raccordé, choisissez l’appareil que vous avez créé dans le didacticiel précédent :

    ![Page du climatiseur raccordé](media/tutorial-configure-rules/builderdevicelist.png)

1. Pour commencer à ajouter une règle dans la vue **Règles**, choisissez **Règles** :

    ![Vue Règles](media/tutorial-configure-rules/builderrulesview.png)

1. Dans ce didacticiel, vous ajoutez une règle de télémétrie basée sur un seuil. Pour commencer à créer une règle basée sur un seuil, choisissez **Nouvelle règle**, puis **Télémétrie**.

1. Pour définir votre règle, utilisez les informations du tableau suivant :

    | Paramètre     | Valeur                          |
    | ----------- | ------------------------------ |
    | NOM        | Température du climatiseur    |
    | Activer la règle | Il en va                             |
    | Condition   | Température supérieure à 90 |

    ![Condition de règle de température](media/tutorial-configure-rules/buildertemperaturerule.png)

## <a name="add-an-action"></a>Ajouter une action

Lorsque vous définissez une règle, vous définissez également une action à exécuter lorsque les conditions de la règle sont remplies. Dans ce didacticiel, vous ajoutez une action pour envoyer un courrier électronique sous forme de notification indiquant que la règle est déclenchée.

1. Pour ajouter une **Action**, faites défiler vers le bas le panneau **Configure Telemetry Rule** (Configurer une règle de télémétrie) et choisissez **+** en regard de **Actions**, puis **Courrier électronique** :

    ![Action de règle de température](media/tutorial-configure-rules/builderaddaction.png)

1. Pour définir votre action, utilisez les informations du tableau suivant :

    | Paramètre   | Valeur                          |
    | --------- | ------------------------------ |
    | À        | Votre adresse e-mail             |
    | Notes     | La température dans le climatiseur dépasse le seuil. |

    > [!NOTE]
    > Pour recevoir une notification par courrier électronique, l’adresse e-mail doit être un [ID d’utilisateur dans l’application](howto-administer.md), et l’utilisateur doit s’être connecté au moins une fois à l’application.

    ![Action de température du Générateur d’applications](media/tutorial-configure-rules/buildertemperatureaction.png)

1. Choisissez **Enregistrer**. Votre règle est répertoriée dans la page **Règles** :

    ![Règles du Générateur d’applications](media/tutorial-configure-rules/builderrules.png)

## <a name="test-the-rule"></a>Tester la règle

Peu après l’enregistrement de la règle, elle devient active. Lorsque les conditions définies dans la règle sont remplies, votre application envoie un message à l’adresse e-mail que vous avez spécifiée dans l’action.

![Action Courrier électronique](media/tutorial-configure-rules/email.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Créer une règle basée sur la télémétrie
> * Ajouter une action

Maintenant que vous avez défini une règle basée sur un seuil, l’étape suivante suggérée consiste à [Personnaliser les vues de l’opérateur](tutorial-customize-operator.md).

Pour en savoir plus sur les différents types de règles dans Azure IoT Central et comment paramétrer la définition de règle, consultez :
* [Créer une règle de télémétrie et configurer des notifications](howto-create-telemetry-rules.md).
* [Créer une règle d’événements et configurer des notifications](howto-create-event-rules.md).

<!-- Next tutorials in the sequence -->