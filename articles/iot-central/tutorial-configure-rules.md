---
title: Configurer des règles et des actions dans Azure IoT Central
description: Ce didacticiel vous montre, en tant que générateur, comment configurer des règles et des actions basées sur la télémétrie dans votre application Azure IoT Central.
author: ankitgupta
ms.author: ankitgup
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: fbe9e1fbd0891f2f39b05fa7ba53653188ef8e03
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158072"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Didacticiel : Configurer des règles et des actions pour votre appareil dans Azure IoT Central

*Cet article s’applique aux opérateurs, aux créateurs et aux administrateurs.*

Dans ce didacticiel, vous créez une règle qui envoie un courrier électronique lorsque la température d’un climatiseur raccordé dépasse 90&deg; F.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une règle basée sur la télémétrie
> * Ajouter une action

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez effectuer le tutoriel [Définir un nouveau type d’appareil dans votre application](tutorial-define-device-type.md) pour créer le modèle d’appareil **Climatiseur raccordé** à utiliser.

## <a name="create-a-telemetry-based-rule"></a>Créer une règle basée sur la télémétrie

1. Pour ajouter une nouvelle règle basée sur la télémétrie pour votre application, dans le menu de navigation de gauche, choisissez **Device Explorer** :

    ![Page Device Explorer](media/tutorial-configure-rules/explorerpage1.png)

    Vous voyez le modèle d’appareil **Climatiseur raccordé (1.0.0)** et l’appareil **Climatiseur raccordé-1** que vous avez créé dans le didacticiel précédent.

2. Pour commencer à personnaliser votre climatiseur raccordé, choisissez l’appareil que vous avez créé dans le didacticiel précédent :

    ![Page du climatiseur raccordé](media/tutorial-configure-rules/builderdevicelist1.png)

3. Pour commencer à ajouter une règle dans la vue **Règles**, choisissez **Règles**, puis cliquez sur **Modifier le modèle** :

    ![Vue Règles](media/tutorial-configure-rules/builderedittemplate.png)

4. Pour créer une règle de télémétrie basée sur un seuil, cliquez sur **Nouvelle règle**, puis sur **Télémétrie**.

    ![Modifier un Modèle](media/tutorial-configure-rules/buildernewrule.png)

5. Pour définir votre règle, utilisez les informations du tableau suivant :

    | Paramètre                                      | Valeur                             |
    | -------------------------------------------- | ------------------------------    |
    | NOM                                         | Alerte de température du climatiseur |
    | Activer la règle pour tous les appareils de ce modèle | Il en va                                |
    | Activer la règle sur cet appareil                   | Il en va                                |
    | Condition                                    | Température supérieure à 90    |
    | Agrégation                                  | Aucun                              |

    ![Condition de règle de température](media/tutorial-configure-rules/buildertemperaturerule1.png)

## <a name="add-an-action"></a>Ajouter une action

Lorsque vous définissez une règle, vous définissez également une action à exécuter lorsque les conditions de la règle sont remplies. Dans ce didacticiel, vous ajoutez une action pour envoyer un courrier électronique sous forme de notification indiquant que la règle est déclenchée.

1. Pour ajouter une **Action**, **enregistrez** d’abord la règle, puis faites défiler vers le bas le panneau **Configurer une règle de télémétrie** et choisissez **+** en regard de **Actions**, puis **E-mail** :

    ![Action de règle de température](media/tutorial-configure-rules/builderaddaction1.png)

2. Pour définir votre action, utilisez les informations du tableau suivant :

    | Paramètre   | Valeur                          |
    | --------- | ------------------------------ |
    | À        | Votre adresse e-mail             |
    | Notes     | La température du climatiseur a dépassé le seuil. |

    > [!NOTE]
    > Pour recevoir une notification par courrier électronique, l’adresse e-mail doit être un [ID d’utilisateur dans l’application](howto-administer.md), et l’utilisateur doit s’être connecté au moins une fois à l’application.

    ![Action de température du Générateur d’applications](media/tutorial-configure-rules/buildertemperatureaction.png)

3. Choisissez **Enregistrer**. Votre règle est répertoriée dans la page **Règles** :

    ![Règles du Générateur d’applications](media/tutorial-configure-rules/builderrules1.png)

4. Choisissez **Terminé** pour quitter le mode **Modifier le modèle**.
 

## <a name="test-the-rule"></a>Tester la règle

Peu après l’enregistrement de la règle, elle devient active. Lorsque les conditions définies dans la règle sont remplies, votre application envoie un message à l’adresse e-mail que vous avez spécifiée dans l’action.

![Action Courrier électronique](media/tutorial-configure-rules/email.png)

> [!NOTE]
> Une fois le test terminé, désactivez la règle pour ne plus recevoir d’alertes dans votre boîte de réception. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Créer une règle basée sur la télémétrie
> * Ajouter une action

Maintenant que vous avez défini une règle basée sur un seuil, l’étape suivante suggérée consiste à [Personnaliser les vues de l’opérateur](tutorial-customize-operator.md).

Pour en savoir plus sur les différents types de règles dans Azure IoT Central et comment paramétrer la définition de règle, consultez :
* [Créer une règle de télémétrie et configurer des notifications](howto-create-telemetry-rules.md).
* [Créer une règle d’événements et configurer des notifications](howto-create-event-rules.md).

<!-- Next tutorials in the sequence -->
