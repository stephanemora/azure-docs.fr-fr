---
title: Migrer une application Azure IoT Central v2 vers v3 | Microsoft Docs
description: Découvrez comment migrer votre application Azure IoT Central v2 vers la version 3.
author: troyhopwood
ms.author: troyhop
ms.date: 01/18/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 0868720668f5db09748e6976327f1500bc8a4781
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108733352"
---
# <a name="migrate-your-v2-iot-central-application-to-v3"></a>Migrer votre application IoT Central v2 vers v3

Actuellement, lorsque vous créez une application IoT Central, il s’agit d’une application v3. Si vous avez déjà créé une application, selon la date à laquelle vous l’avez créée, il peut s’agir d’une application v2. Cet article explique comment migrer une application v2 vers une application v3 pour vous assurer que vous utilisez les fonctionnalités d’IoT Central les plus récentes.

Pour savoir comment identifier la version d’une application IoT Central, consultez [À propos de votre application](howto-get-app-info.md).

Les étapes à suivre pour migrer une application de la version 2 à la version 3 sont les suivantes :

1. Créez une nouvelle application v3 à partir de l’application v2.
1. Configurez l’application v3.
1. Supprimez l’application v2.

## <a name="create-a-new-v3-application"></a>Créer une application v3

Utilisez l’Assistant de migration pour créer une application v3.

IoT Central ne prend pas en charge la migration vers une application v3 existante. Pour déplacer automatiquement les appareils existants, utilisez l’Assistant de migration pour créer votre application v3.

L’Assistant de migration :

- Crée une application v3.
- Vérifie la compatibilité de vos modèles d’appareils avec v3.
- Copie tous vos modèles d’appareil dans la nouvelle application v3.
- Copie tous les utilisateurs et attributions de rôles dans la nouvelle application v3.

> [!NOTE]
> Pour garantir la compatibilité des appareils avec v3, les types primitifs du modèle d’appareil deviennent des propriétés d’objet. Vous n’avez pas besoin d’apporter de modifications à votre code d’appareil.

Vous devez être Administrateur pour migrer une application vers v3.

1. Dans le menu **Administration**, sélectionnez **Migrer vers une application v3** :

    :::image type="content" source="media/howto-migrate/migrate-menu.png" alt-text="Capture d’écran montrant l’Assistant de migration d’application":::

1. Entrez un nouveau **nom d’application** et, éventuellement, modifiez l’**URL** générée automatiquement. L’URL ne peut pas être la même que celle de votre application v2 actuelle. Toutefois, vous pouvez modifier l’URL ultérieurement après avoir supprimé votre application v2.

1. Sélectionnez **Créer une nouvelle application v3**.

    :::image type="content" source="media/howto-migrate/create-app.png" alt-text="Capture d’écran montrant les options de l’Assistant de migration d’application":::

    Selon le nombre et la complexité de vos modèles d’appareils, ce processus peut prendre plusieurs minutes.

    > [!Warning]
    > La création de votre application v3 échoue si un modèle d’appareil contient des champs qui commencent par un chiffre ou qui contiennent l’un des caractères suivants (`+`, `*`, `?`, `^`, `$`, `(`, `)`, `[`, `]`, `{`, `}`, `|`, `\`). Le schéma de modèle d’appareil DTDL utilisé par les applications v3 n’autorise pas ces caractères. Mettez à jour votre modèle d’appareil pour résoudre ce problème avant de migrer vers v3.

1. Lorsque votre nouvelle application v3 est prête, sélectionnez **Ouvrir la nouvelle application** pour l’ouvrir.

    :::image type="content" source="media/howto-migrate/open-app.png" alt-text="Capture d’écran montrant l’Assistant de migration d’application après la migration de l’application":::

## <a name="configure-the-v3-application"></a>Configurer l’application v3

Après la création de votre application v3, apportez les modifications de configuration nécessaires avant de déplacer vos appareils de l’application v2 à l’application v3.

> [!TIP]
> Prenez un moment pour [vous familiariser avec la version 3](overview-iot-central-tour.md#navigate-your-application), car elle présente quelques différences par rapport à la version précédente.

Voici quelques étapes de configuration recommandées à prendre en compte :

- [Configurer les tableaux de bord](howto-add-tiles-to-your-dashboard.md)
- [Configurer une exportation de données](howto-export-data.md)
- [Configurer des règles et des actions](quick-configure-rules.md)
- [Personnaliser l’interface utilisateur de l’application](howto-customize-ui.md)

Lorsque votre application v3 est configurée pour répondre à vos besoins, vous êtes prêt à déplacer vos appareils de votre application v2 vers votre application v3.

## <a name="move-your-devices-to-the-v3-application"></a>Déplacer vos appareils vers l’application v3

Une fois cette étape terminée, tous vos appareils communiquent uniquement avec votre nouvelle application v3.

> [!IMPORTANT]
> Avant de déplacer vos appareils vers votre application v3, supprimez tous les appareils que vous avez créés dans l’application v3.

Cette étape déplace tous vos appareils existants vers votre nouvelle application v3. Les données de votre appareil ne sont pas copiées.

Sélectionnez **Déplacer tous les appareils** pour commencer à déplacer vos appareils. Cette étape peut prendre plusieurs minutes.

:::image type="content" source="media/howto-migrate/move-devices.png" alt-text="Capture d’écran montrant l’Assistant de migration d’application avec l’option Déplacer les appareils":::

Une fois le déplacement terminé, redémarrez tous vos appareils pour vous assurer qu’ils se connectent à la nouvelle application v3.

> [!WARNING]
> Ne supprimez pas votre application v3, car votre application v2 est désormais inutilisable.

## <a name="delete-your-old-v2-application"></a>Supprimer votre ancienne application v2

Une fois que vous avez vérifié que tout fonctionne comme prévu dans votre nouvelle application v3, supprimez votre ancienne application v2. Cette étape garantit que vous n’êtes pas facturé pour une application que vous n’utilisez plus.

> [!Note]
> Pour supprimer une application, vous devez disposer des autorisations permettant de supprimer des ressources de l’abonnement Azure que vous avez choisi lors de la création de l’application. Pour en savoir plus, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer l’accès aux ressources de votre abonnement Azure](../../role-based-access-control/role-assignments-portal.md).

1. Dans votre application v2, sélectionnez l’onglet **Administration** dans le menu.
2. Sélectionnez **Supprimer** pour supprimer définitivement votre application IoT Central. Cette option supprime définitivement toutes les données associées à cette application.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à migrer votre application, l’étape suivante suggérée consiste à passer en revue l’[interface utilisateur d’Azure IoT Central](overview-iot-central-tour.md) pour voir ce qui a changé dans la version 3.