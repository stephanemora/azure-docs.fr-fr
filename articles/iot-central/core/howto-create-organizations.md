---
title: Gérer des organisations IoT Central | Microsoft Docs
description: Cet article explique comment créer et gérer une hiérarchie d’organisations afin de contrôler les utilisateurs qui peuvent accéder aux appareils de votre organisation IoT Central. Utilisez des organisations pour créer des applications IoT Central à plusieurs abonnés.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/20/2021
ms.topic: how-to
ms.openlocfilehash: e15e2b095cd6b6876728ff773a7b4617e325d77d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656756"
---
# <a name="manage-iot-central-organizations"></a>Gérer des organisations IoT Central

Les organisations vous permettent de définir une hiérarchie que vous utilisez pour gérer quels utilisateurs peuvent voir quels appareils dans votre application IoT Central. Le rôle de l’utilisateur détermine ses autorisations sur les appareils qu’il voit et les expériences auxquelles il peut accéder.

Les organisations sont hiérarchiques :

:::image type="content" source="media/howto-create-organization/organizations-hierarchy.png" alt-text="Diagramme montrant un exemple de hiérarchie d’organisations." border="false":::

Lorsque vous créez votre première hiérarchie d’organisations, vous voyez que le nom de votre application se trouve à la racine. Chaque organisation ajoutée devient une organisation enfant ou de nœud terminal. Les hiérarchies d’organisations utilisent l’héritage. Par exemple, dans la hiérarchie indiquée dans le diagramme précédent :

- Les utilisateurs de *Adatum Solar* ont accès aux appareils aux *États-Unis*, au *Canada*, en *Europe* et en *Amérique latine*.
- Les utilisateurs *Contoso* ont accès à tous les appareils de l’ensemble des organisations de la hiérarchie.

Les utilisateurs des organisations sœurs ne peuvent pas voir les appareils des autres utilisateurs.

Lorsque vous autorisez l’accès à votre application par les utilisateurs, plus l’accès qui leur est attribué est élevé dans la hiérarchie, plus ils peuvent en voir et en gérer. L’organisation contrôle les appareils que l’utilisateur peut voir. Le rôle définit ce qu’un utilisateur peut faire, par exemple en matière de création, de lecture et de suppression d’appareils.

La capture d’écran suivante montre une définition de hiérarchie d’organisations dans IoT Central :

:::image type="content" source="media/howto-create-organization/organizations-definition.png" alt-text="Capture d’écran de la définition de la hiérarchie d’organisations.":::

## <a name="create-a-hierarchy"></a>Créer une hiérarchie

Pour commencer à utiliser des organisations, vous devez définir la hiérarchie de votre organisation. Chaque organisation de la hiérarchie agit comme un conteneur logique dans lequel vous placez des appareils, enregistrez des tableaux de bord et des groupes d’appareils et invitez des utilisateurs. Pour créer vos organisations, accédez à la section **Administration** de votre application IOT central, sélectionnez l’onglet **Organisations**, puis sélectionnez **+ Nouveau** ou utilisez le menu contextuel d’une organisation existante. Pour créer une ou plusieurs organisations à la fois, sélectionnez **+ Ajouter une autre organisation** :

:::image type="content" source="media/howto-create-organization/create-organizations-hierarchy.png" alt-text="Capture d’écran montrant les options de création d’une hiérarchie d’organisation.":::

> [!TIP]
> La configuration initiale des organisations doit être effectuée par un membre du rôle **Administrateur d’application**.

Pour réattribuer une organisation à un nouveau parent, sélectionnez **Modifier**, puis choisissez un nouveau parent.

Pour supprimer une organisation, vous devez supprimer ou déplacer vers une autre organisation tous les éléments associés tels que les tableaux de bord, les périphériques, les utilisateurs, les groupes d’appareils et les travaux.

> [!TIP]
> Vous pouvez également utiliser l’API REST pour [créer et gérer des organisations](/rest/api/iotcentral/1.1-previewdataplane/organizations).

## <a name="assign-devices"></a>Attribuer des appareils

Une fois que vous avez défini la hiérarchie de votre organisation, attribuez vos appareils aux organisations. Chaque appareil appartenant à une seule organisation, choisissez l’organisation appropriée dans la hiérarchie.

Lorsque vous créez un nouveau périphérique dans votre application, attribuez-le à une organisation de votre hiérarchie :

:::image type="content" source="media/howto-create-organization/assign-device.png" alt-text="Capture d’écran montrant comment attribuer un appareil à une organisation.":::

Pour attribuer ou réattribuer un appareil existant à une organisation, sélectionnez l’appareil dans la liste des appareils, puis sélectionnez **Organisation** :

:::image type="content" source="media/howto-create-organization/change-device-organization.png" alt-text="Capture d’écran montrant comment changer l’organisation à laquelle un appareil est associé.":::

> [!TIP]
> Vous pouvez voir l’organisation à laquelle appartient un appareil dans la liste des appareils. Utilisez l’outil filtre dans la liste des appareils pour afficher les appareils d’une organisation en particulier.

Lorsque vous réattribuez un appareil à une autre organisation, les données de l’appareil restent avec l’organisation en fonction de l’heure d’ingestion des données. Par exemple :

- Un appareil fait partie de l’organisation **Contoso/Customer1** du premier au septième jour, puis est déplacé vers l’organisation **Contoso/Customer4** le huitième jour.
- Le neuvième jour, les utilisateurs **Contoso/Customer4** peuvent voir les données du huitième au neuvième jour.
- Le neuvième jour, les utilisateurs **Contoso/Customer1** peuvent voir les données du premier au neuvième jour.

### <a name="device-first-registration"></a>Inscription de l’appareil en premier

Les appareils peuvent s’inscrire automatiquement auprès de votre application IoT Central sans être ajoutés au préalable à la liste des appareils. Dans ce cas, IoT Central ajoute l’appareil à l’organisation racine dans la hiérarchie. Vous pouvez ensuite réattribuer l’appareil à une autre organisation.

Au lieu de cela, vous pouvez utiliser la fonctionnalité d’importation CSV pour inscrire en bloc des appareils auprès de votre application et les attribuer à des organisations. Pour en savoir plus, consultez [Importer des appareils](howto-manage-devices.md#import-devices).

### <a name="gateways"></a>Passerelles

Vous attribuez des appareils de passerelle et de nœud terminal aux organisations. Vous n’êtes pas obligé d’attribuer une passerelle et ses appareils de nœud terminal associés à la même organisation. Si vous les attribuez à différentes organisations, il est possible qu’un utilisateur puisse voir la passerelle mais pas les appareils de nœud terminal ou les appareils de nœud terminal, mais pas la passerelle.

## <a name="roles"></a>Rôles

Lorsque vous créez la première organisation dans votre application, IoT Central ajoute trois nouveaux rôles dans votre application : **Administrateur de l’organisation**, **Opérateur de l’organisation** et **Observateur de l’organisation**. Ces rôles sont nécessaires, car un utilisateur de l’organisation ne peut pas accéder à certaines fonctionnalités de l’ensemble de l’application, telles que les plans de tarification, la personnalisation et les couleurs, les jetons d’API et les informations sur le groupe d’inscription de l’ensemble de l’application.

:::image type="content" source="media/howto-create-organization/organization-roles.png" alt-text="Capture d’écran montrant les trois rôles de l’organisation.":::

Vous pouvez utiliser ces rôles lorsque vous invitez des utilisateurs à une organisation dans votre application.

### <a name="custom-organization-roles"></a>Rôles d’organisation personnalisés

Pour créer un rôle personnalisé pour les utilisateurs de votre organisation, créez un nouveau rôle et choisissez le type de rôle **Organisation** :

:::image type="content" source="media/howto-create-organization/custom-organization-role.png" alt-text="Capture d’écran montrant comment créer un rôle d’organisation personnalisé.":::

Sélectionnez ensuite les autorisations pour le rôle :

:::image type="content" source="media/howto-create-organization/organization-role-permissions.png" alt-text="Capture d’écran montrant comment sélectionner des autorisations personnalisées pour un rôle organisationnel.":::

## <a name="invite-users"></a>Inviter des utilisateurs

Une fois que vous avez créé la hiérarchie de votre organisation et attribué des appareils aux organisations, invitez les utilisateurs à votre application et attribuez-les à des organisations.

Pour inviter un utilisateur, accédez à **Administration > Utilisateurs**. Entrez l’adresse e-mail, l’organisation à laquelle ils sont attribués et le ou les rôles dont l’utilisateur est membre. L’organisation que vous sélectionnez filtre la liste des rôles disponibles pour s’assurer que vous affectez l’utilisateur à un rôle valide :

:::image type="content" source="media/howto-create-organization/assign-user-organization.png" alt-text="Capture d’écran montrant comment attribuer un appareil à une organisation et à un rôle.":::

Vous pouvez attribuer le même utilisateur à plusieurs organisations. L’utilisateur peut avoir un rôle différent dans chaque organisation qui lui est attribuée :

| Nom | Role | Organisation |
| ---- | ---- | ------------ |
| user1@contoso.com | Administrateur de l’organisation | Intégrité de Contoso Inc/Lamna |
| user1@contoso.com | Observateur de l’organisation | Contoso Inc/Adatum Solar |

## <a name="use-organizations"></a>Utiliser des organisations

Une fois que vous avez créé la hiérarchie de votre organisation, vous pouvez utiliser des organisations dans des domaines de votre application, par exemple :

- [Tableaux de bord des organisations](howto-manage-dashboards.md) qui affichent des informations aux utilisateurs sur les appareils de leur organisation.
- [Groupes d’appareils](tutorial-use-device-groups.md) pour les appareils dans des organisations spécifiques.
- [Analyse](howto-create-analytics.md) pour les appareils dans des organisations spécifiques.
- [Travaux](howto-manage-devices-in-bulk.md#create-and-run-a-job) qui gèrent en bloc des appareils dans des organisations spécifiques.

## <a name="default-organization"></a>Organisation par défaut

Vous pouvez définir une organisation comme organisation par défaut à utiliser dans votre application. L’organisation par défaut devient l’option par défaut lorsque vous choisissez une organisation, par exemple lorsque vous ajoutez un nouvel utilisateur à votre application IoT Central.

Pour définir l’organisation par défaut, sélectionnez **Paramètres** dans la barre de menus supérieure :

:::image type="content" source="media/howto-create-organization/set-default-organization.png" alt-text="Capture d’écran montrant comment définir votre organisation par défaut.":::

> [!TIP]
> Il s’agit d’une préférence personnelle qui s’applique uniquement à vous.

## <a name="add-organizations-to-an-existing-application"></a>Ajouter des organisations à une application existante

Une application peut contenir des appareils, des utilisateurs et des expériences, tels que des tableaux de bord, des groupes d’appareils et des travaux, avant d’ajouter une hiérarchie d’organisation.

Lorsque vous commencez à ajouter des organisations, tous les appareils, utilisateurs et expériences existants restent associés à l’organisation racine dans l’application :

- Vous pouvez réattribuer des appareils à une nouvelle organisation.
- Vous pouvez attribuer des utilisateurs à une nouvelle organisation et les supprimer de la racine.
- Vous pouvez recréer une expérience, comme des tableaux de bord, des groupes d’appareils et des travaux et les associer à des organisations dans la hiérarchie.

## <a name="limits"></a>Limites

Les limites suivantes s’appliquent aux organisations :

- La hiérarchie ne peut pas avoir plus de cinq niveaux de profondeur.
- Le nombre total d’organisations ne peut pas être supérieur à 200. Chaque nœud de la hiérarchie est comptabilisé en tant qu’organisation.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer les organisations Azure IoT Central, l’étape suivante suggérée est d’apprendre à [exporter des données IoT vers des destinations cloud à l’aide de l’exportation de données](howto-export-data.md).
