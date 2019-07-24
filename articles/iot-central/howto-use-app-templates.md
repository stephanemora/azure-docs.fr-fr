---
title: Utiliser des modèles Application dans Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur, comment utiliser des ensembles d’appareils dans votre application Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a26f70c5a61f3855a3de991072a7e84103e87b69
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66499218"
---
# <a name="use-application-templates"></a>Utiliser des modèles d’application

Cet article explique comment, en tant que gestionnaire de solution, créer et utiliser des modèles d’application.

Lorsque vous créez une application Azure IoT Central, vous avez un choix de plusieurs exemples de modèles intégrés. Vous pouvez également créer vos propres modèles d’application à partir des applications IoT Central existantes. Vous pouvez ensuite utiliser vos propres modèles d’application lorsque vous créez des applications.

Lorsque vous créez un modèle d’application, il comprend les éléments suivants de votre application existante :

- Le tableau de bord par défaut de l’application, dont la disposition et toutes les vignettes que vous avez définies.
- Les modèles d’appareil, dont les mesures, les paramètres, les propriétés, les commandes et le tableau de bord.
- Les règles. Toutes les définitions de règle sont incluses. Toutefois, les actions, à l’exception des actions de messagerie, ne sont pas incluses.
- Les ensembles d’appareils, dont leurs conditions et leurs tableaux de bord.

> [!WARNING]
> Si un tableau de bord inclut des vignettes qui affichent des informations d’appareils spécifiques, ces vignettes affichent alors **La ressource demandée est introuvable** dans la nouvelle application. Vous devez reconfigurer ces vignettes pour qu’elles affichent des informations sur les appareils de votre nouvelle application.

Lorsque vous créez un modèle d’application, il n’inclut pas les éléments suivants :

- Appareils
- Utilisateurs
- Définitions des travaux
- Définitions des exportations de données continues

Ajoutez ces éléments manuellement à toutes les applications créées depuis un modèle d’application.

## <a name="create-an-application-template"></a>Créer un modèle d’application

Pour créer un modèle d’application à partir d’une application IoT Central existante :

1. Accédez à la section **Administration** de votre application.
1. Sélectionnez **Exportation de modèle d’application**.
1. Sur la page **Exportation de modèle d’application**, entrez un nom et une description pour votre modèle.
1. Sélectionnez le bouton **Exporter** pour créer le modèle d’application. Vous pouvez désormais copier le **Lien de partage** pour permettre à quelqu’un de créer une application depuis le modèle :

![Créer un modèle d’application](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Utiliser un modèle d’application

Pour utiliser un modèle d’application afin de créer une application IoT Central, vous devez d’abord avoir créé un **Lien de partage**. Collez le **Lien de partage** dans la barre d’adresse de votre navigateur. La page **Créer une application** s’affiche avec les modèles d’application personnalisés sélectionnés :

![Créer une application à partir d’un modèle](media/howto-use-app-templates/create-app.png)

Sélectionnez votre plan de paiement et remplissez les autres champs du formulaire. Sélectionnez ensuite **Créer** pour créer une application IoT Central à partir d’un modèle d’application.

## <a name="manage-application-templates"></a>Gérer des modèles d’application

Sur la page **Exportation de modèle d’application**, vous pouvez supprimer ou mettre à jour le modèle d’application.

Si vous supprimez un modèle d’application, vous ne pouvez plus utiliser le lien de partage généré précédemment pour créer des applications.

Pour mettre à jour votre modèle d’application, modifiez le nom du modèle ou sa description sur la page **Exportation de modèle d’application**. Sélectionnez ensuite le bouton **Exporter**. Cette action génère un nouveau **Lien de partage** et rend non valide les **Liens de partage** précédents.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser des modèles d’application, l’étape suivante conseillée est d’apprendre à [Gérer des applications IoT Central à partir du portail Azure](howto-manage-iot-central-from-portal.md)
