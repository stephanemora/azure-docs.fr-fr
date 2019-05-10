---
title: Personnaliser l’interface utilisateur Azure IoT Central | Microsoft Docs
description: Comment personnaliser les liens d’aide et de thème pour votre application centrale Azure IoT
author: dominicbetts
ms.author: dobett
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0256396cd228898f3852772b113e6064a0656746
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237656"
---
# <a name="customize-the-azure-iot-central-ui"></a>Personnaliser l’interface utilisateur Azure IoT Central 

*Cet article s’applique aux administrateurs.*

IoT Central permet de personnaliser l’interface utilisateur de votre application en appliquant des thèmes personnalisés et en modifiant les liens d’aide pour pointer vers vos propres ressources d’aide personnalisée. La capture d’écran suivante montre une page en utilisant le thème standard :

![Thème standard de IoT Central](./media/howto-customize-ui/standard-ui.png)

La capture d’écran suivante montre une page à l’aide d’une capture d’écran personnalisé avec les éléments d’interface utilisateur personnalisés mis en surbrillance :

![Thème Central IoT personnalisé](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Créer le thème

Pour créer un thème personnalisé, accédez à la **personnaliser votre application** page dans le **Administration** section :

![Thèmes de IoT Central](./media/howto-customize-ui/themes.png)

Dans cette page, vous pouvez personnaliser les aspects suivants de votre application :

### <a name="application-logo"></a>Logo de l’application

Une image PNG, non supérieure à 1 Mo, avec un arrière-plan transparent. Ce logo s’affiche à gauche de la barre de titre d’application IoT Central.

Si votre image de logo inclut le nom de votre application, vous pouvez masquer le texte de nom d’application. Pour plus d’informations, consultez [gérer les paramètres de l’application](./howto-administer.md#manage-application-settings).

### <a name="browser-icon-favicon"></a>Icône de navigateur (favicon)

Une image PNG, ne dépasse pas 32 x 32 pixels, avec un arrière-plan transparent. Un navigateur web peut utiliser cette image dans la barre d’adresses, l’historique, signets, onglet de navigateur.

### <a name="browser-colors"></a>Couleurs du navigateur

Vous pouvez modifier la couleur de l’en-tête de page et la couleur utilisée pour les boutons et autres points importants à l’accentuation. Utilisez une valeur de couleur hexadécimal de six caractères au format `##ff6347`. Pour plus d’informations sur **valeur hexadécimale** notation de couleur, consultez [couleurs HTML](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Vous pouvez toujours revenir aux options par défaut sur le **personnaliser votre application** page.

### <a name="changes-for-operators"></a>Modifications pour les opérateurs

Si un administrateur crée un thème personnalisé, opérateurs et autres utilisateurs de votre application peuvent choisir n’est plus un thème dans **paramètres**.

## <a name="replace-help-links"></a>Remplacez les liens d’aide

Pour fournir des informations d’aide personnalisé à vos opérateurs et d’autres utilisateurs, vous pouvez modifier les liens de l’application **aide** menu.

Pour modifier les liens d’aide, accédez à la **personnaliser help** page dans le **Administration** section :

![Personnaliser les liens d’aide IoT Central](./media/howto-customize-ui/help-links.png)

Vous pouvez également ajouter de nouvelles entrées au menu Aide et supprimer des entrées par défaut :

![Aide personnalisée de IoT Central](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Vous pouvez toujours revenir aux liens d’aide par défaut sur le **personnaliser help** page.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à personnaliser l’interface utilisateur dans votre application IoT Central, voici quelques étapes suggérées :

> [!div class="nextstepaction"]
> [Administrer votre application](./howto-administer.md)
> [configurer le tableau de bord d’application](./howto-configure-homepage.md)