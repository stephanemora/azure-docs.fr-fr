---
title: Utiliser des modèles d’application dans Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur, comment utiliser des ensembles d’appareils dans votre application Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a26f70c5a61f3855a3de991072a7e84103e87b69
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499218"
---
# <a name="use-application-templates"></a>Utiliser des modèles d’application

Cet article décrit comment, en tant que solution gestionnaire, pour créer et utiliser des modèles d’application.

Lorsque vous créez une application Azure IoT Central, vous avez le choix des exemples intégrés de modèle. Vous pouvez également créer vos propres modèles d’application à partir des applications IoT Central existantes. Vous pouvez ensuite utiliser vos propres modèles d’application lorsque vous créez des applications.

Lorsque vous créez un modèle d’application, il inclut les éléments suivants à partir de votre application existante :

- Par défaut application tableau de bord, y compris la mise en page du tableau de bord et toutes les vignettes que vous avez définies.
- Modèles de périphériques, y compris les mesures, les paramètres, les propriétés, les commandes et tableau de bord.
- Règles. Toutes les définitions de règle sont incluses. Toutefois, les actions, à l’exception des actions de messagerie, ne sont pas incluses.
- Jeux d’appareil, y compris leurs conditions et les tableaux de bord.

> [!WARNING]
> Si un tableau de bord inclut des vignettes qui affichent des informations sur des appareils spécifiques, puis affichent ces vignettes **la ressource demandée est introuvable** dans la nouvelle application. Vous devez reconfigurer ces vignettes pour afficher des informations sur les appareils dans votre nouvelle application.

Lorsque vous créez un modèle d’application, il n’inclut pas les éléments suivants :

- Appareils
- Utilisateurs
- Définitions des travaux
- Définitions d’exportation continue des données

Ajouter manuellement des ces éléments dans les applications créées à partir d’un modèle d’application.

## <a name="create-an-application-template"></a>Créer un modèle d’application

Pour créer un modèle d’application à partir d’une application IoT Central existante :

1. Accédez à la **Administration** section dans votre application.
1. Sélectionnez **exportation de modèle d’Application**.
1. Sur le **exporter des modèles d’Application** , entrez un nom et une description pour votre modèle.
1. Sélectionnez le **exporter** bouton permettant de créer le modèle d’application. Vous pouvez maintenant copier la **lien partageable** qui permet à un utilisateur de créer une nouvelle application à partir du modèle :

![Créer un modèle d’application](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Utiliser un modèle d’application

Pour utiliser un modèle d’application pour créer une application IoT Central, vous devez créé précédemment **lien partageable**. Coller le **lien partageable** dans la barre d’adresses de votre navigateur. Le **créer une application** page s’affiche avec votre modèle d’application personnalisée sélectionnée :

![Créer une application à partir d’un modèle](media/howto-use-app-templates/create-app.png)

Sélectionnez votre plan de paiement et renseignez les autres champs sur le formulaire. Puis sélectionnez **créer** pour créer une nouvelle application IoT Central à partir du modèle d’application.

## <a name="manage-application-templates"></a>Gérer les modèles d’application

Sur le **exporter des modèles d’Application** page, vous pouvez supprimer ou mettre à jour le modèle d’application.

Si vous supprimez un modèle d’application, vous pouvez ne plus utiliser le lien partageable généré précédemment à créer des applications.

Pour mettre à jour votre modèle d’application, modifiez le nom du modèle ou la description sur la **exporter des modèles d’Application** page. Puis sélectionnez le **exporter** bouton à nouveau. Cette action génère une nouvelle **lien partageable** et invalide précédent **lien partageable** URL.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment utiliser des modèles d’application, l’étape suivante suggérée consiste à apprendre comment [gérer IoT Central à partir du portail Azure](howto-manage-iot-central-from-portal.md)
