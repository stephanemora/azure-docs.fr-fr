---
title: Modification d’un modèle d’appareil dans une application Azure IoT Central | Microsoft Docs
description: Itérez sur vos modèles d’appareils sans affecter vos appareils connectés en direct.
author: dominicbetts
ms.author: dobett
ms.date: 04/26/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: fd6fb0cebe33d8511185c396c95faa8927941e6e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103973"
---
# <a name="edit-an-existing-device-template"></a>Modification d’un modèle d’appareil existant

*Cet article s’applique aux créateurs de solutions et aux développeurs d’appareils.*

Un modèle d’appareil comprend un modèle qui décrit les interactions de l’appareil avec IoT Central, définit ses fonctionnalités et spécifie le comportement d’IoT Central à son égard. Les appareils peuvent envoyer des données de télémétrie et des valeurs de propriété à IoT Central, qui peut en retour leur envoyer des mises à jour de propriétés et des commandes. IoT Central utilise également le modèle pour définir des interactions avec différentes fonctionnalités IoT Central, notamment les travaux, les règles et les exportations.

Les changements apportés au modèle dans un modèle d’appareil sont susceptibles d’affecter l’ensemble de l’application, y compris les appareils connectés. Si vous modifiez une fonctionnalité, les règles, exportations, groupes d’appareils et travaux qui l’utilisent risquent de se comporter de manière inattendue ou de ne plus fonctionner du tout. Supposons par exemple que vous supprimiez une définition de télémétrie d’un modèle :

- IoT Central n’est plus en mesure d’interpréter cette valeur. Il affiche les données de l’appareil qu’il ne peut pas interpréter avec le libellé **Données non modélisées** sur la page **Données brutes** de l’appareil.
- IoT Central n’inclut plus la valeur dans les exportations de données.

Pour éviter toute conséquence inattendue de la modification d’un modèle d’appareil, cet article donne des recommandations qui varient en fonction de l’étape du cycle de vie de développement. En général, plus vous vous trouvez tôt dans le cycle de vie, plus grande peut être votre tolérance à l’égard de la modification du modèle d’appareil.

Pour plus d’informations sur les modèles d’appareils et pour savoir comment en créer un, consultez [Présentation des modèles d’appareil](concepts-device-templates.md) et [Configuration d’un modèle d’appareil](howto-set-up-template.md).

## <a name="modify-a-device-template"></a>Modification d’un modèle d’appareil

Les modifications additives (par exemple l’ajout d’une fonctionnalité ou d’une interface à un modèle) constituent des changements non cassants. Vous pouvez apporter ce type de modifications à un modèle à n’importe quel moment du cycle de vie du développement.

Les changements cassants impliquent la suppression de certaines parties d’un modèle, ou la modification d’un nom de fonction ou d’un type de schéma. En conséquence de ces changements, différentes fonctionnalités d’application (règles, exportations ou tableaux de bord) peuvent afficher des messages d’erreur et cesser de fonctionner.

Dans les premiers stades de développement des appareils, alors que vous êtes toujours en phase de conception et de test du modèle, vous pouvez bénéficier d’une plus grande tolérance pour apporter directement des modifications à votre modèle d’appareil. Avant de connecter des appareils de production à un modèle d’appareil, vous pouvez modifier directement ce dernier. IoT Central applique automatiquement ces changements aux appareils quand vous publiez le modèle d’appareil.

Une fois que vous avez attaché des appareils de production à un modèle d’appareil, évaluez l’impact potentiel avant de modifier ce dernier. N’effectuez pas de changements cassants sur un modèle d’appareil en production. Pour apporter de telles modifications, créez une nouvelle version du modèle d’appareil. Testez-le, puis migrez vos appareils de production vers le nouveau modèle pendant un temps d’arrêt planifié.

## <a name="update-an-iot-edge-device-template"></a>Mise à jour d’un modèle d’appareil IoT Edge

Les modèles d’appareils IoT Edge contiennent en outre un _manifeste de déploiement_. Sur un appareil IoT Edge, le modèle regroupe les fonctionnalités dans des modules qui correspondent aux modules IoT Edge exécutés sur l’appareil. Le manifeste de déploiement consiste en un document JSON distinct qui indique à un appareil IoT Edge quels modules installer et comment les configurer. Les instructions qui s’appliquent aux modules du modèle d’appareil sont les mêmes que dans la section précédente. Par ailleurs, tous les modules définis dans le modèle d’appareil doivent être inclus dans le manifeste de déploiement. Si vous avez besoin de remplacer le manifeste de déploiement une fois le modèle d’appareil IoT Edge publié, vous devez créer une nouvelle version. Pour que les appareils IoT Edge reçoivent le nouveau manifeste de déploiement, migrez-les vers la nouvelle version du modèle.

Pour plus d’informations, consultez [Manifestes de déploiement IoT Edge et modèles d’appareils IoT Central](concepts-iot-edge.md#iot-edge-deployment-manifests-and-iot-central-device-templates).

### <a name="edit-and-publish-actions"></a>Modification et publication d’actions

Les actions suivantes sont utiles pour modifier un modèle d’appareil :

- _Enregistrez._ . Lorsque vous modifiez une partie de votre modèle d’appareil, les modifications sont enregistrées dans un brouillon auquel vous pouvez revenir. Elles n’affectent pas encore les appareils connectés. Les appareils créés à partir de ce modèle ne reçoivent pas les modifications enregistrées tant que vous ne l’avez pas publié.
- _Publication_. Lorsque vous publiez le modèle d’appareil, les modifications enregistrées sont appliquées aux instances existantes de l’appareil. Les nouvelles instances utilisent toujours le dernier modèle publié.
- _Versionnage d’un modèle_. Quand vous versionnez un modèle d’appareil, un nouveau modèle est créé avec toutes les dernières modifications enregistrées. Les instances existantes de l’appareil ne sont pas affectées par les modifications apportées à la nouvelle version. Pour plus d’informations, consultez [Versionnage d’un modèle d’appareil](#version-a-device-template).
- _Versionnage d’une interface_. Quand vous versionnez une interface, une nouvelle interface est créée avec toutes les dernières fonctionnalités enregistrées. Il est possible de réutiliser une interface dans plusieurs emplacements d’un modèle. De ce fait, la modification d’une référence à une interface se répercute sur tous les emplacements du modèle qui utilisent l’interface. Lorsque vous versionnez une interface, ce comportement change. En effet, la nouvelle version constitue alors une interface distincte. Pour plus d’informations, consultez [Versionnage d’une interface](#version-an-interface).
- _Migration d’un appareil_. Lorsque vous migrez un appareil, l’instance de l’appareil bascule d’un modèle d’appareil à un autre. La migration peut entraîner un bref délai le temps que IoT Central traite les modifications. Pour plus d’informations, consultez [Migration d’un appareil entre différentes versions](#migrate-a-device-across-versions).

### <a name="version-numbers"></a>Numéros de version

Les modèles d’appareil et les interfaces ont des numéros de version. Des numéros de version différents permettent aux modèles ou aux interfaces de partager une valeur `@id`, tout en fournissant un historique des mises à jour. Les numéros de version ne sont incrémentés que si vous choisissez de créer une version du modèle ou de l’interface, ou si vous modifiez délibérément le numéro de version. Changez le numéro de version lorsque vous apportez une modification majeure à un modèle ou à une interface.

L’extrait suivant montre le modèle d’appareil pour un appareil à thermostat. Le modèle d’appareil a une seule interface. Vous pouvez voir le numéro de version (`1`) à la fin du champ `@id`.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    // ...
  ]
}
```

Pour afficher ces informations dans l’interface utilisateur d’IoT Central, dans l’éditeur de modèle d’appareil, sélectionnez **Afficher l’identité** :

:::image type="content" source="media/howto-edit-device-template/view-identity.png" alt-text="Afficher l’identité d’une interface pour voir le numéro de version":::

## <a name="version-a-device-template"></a>Contrôler la version d’un modèle d’appareil

Pour contrôler la version d’un modèle d’appareil :

1. Accédez à la page **Modèles d’appareil**.
1. Sélectionnez le modèle d’appareil que vous souhaitez versionner.
1. Sélectionnez **Versionner** en haut de la page et donnez un nouveau nom au modèle. IoT Central suggère un nouveau nom que vous pouvez modifier.
1. Sélectionnez **Create** (Créer).

Vous avez créé un nouveau modèle doté d’une identité unique qui n’est attaché à aucun appareil existant.

## <a name="version-an-interface"></a>Contrôler la version d’une interface

Pour contrôler la version d’une interface :

1. Accédez à la page **Modèles d’appareil**.
1. Sélectionnez le modèle d’appareil en mode brouillon.
1. Sélectionnez l’interface publiée que vous souhaitez versionner et modifier.
1. Sélectionnez **Versionner** en haut de la page de l’interface.
1. Sélectionnez **Create** (Créer).

Vous avez créé une nouvelle interface doté d’une identité unique qui n’est pas synchronisé avec la version précédente de l’interface.

## <a name="migrate-a-device-across-versions"></a>Migrer un appareil vers d’autres versions

Vous pouvez créer plusieurs versions d’un modèle d’appareil. Au fil du temps, vous vous retrouvez avec plusieurs appareils connectés utilisant ces modèles d’appareils. Vous pouvez migrer les appareils d’une version de votre modèle d’appareil vers une autre. Les étapes suivantes décrivent le processus de migration d’un appareil :

1. Accédez à la page **Appareils**.
1. Sélectionnez l’appareil que vous devez migrer vers une autre version.
1. Choisissez **Migrer** :

    :::image type="content" source="media/howto-edit-device-template/migrate-device.png" alt-text="Choisissez l’option de démarrage de la migration d’un appareil":::

1. Sélectionnez le modèle d’appareil associé à la version vers laquelle vous voulez migrer l’appareil, puis sélectionnez **Migrer**.

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes un opérateur ou un créateur de solution, l’étape suivante suggérée consiste à [apprendre à gérer vos appareils](./howto-manage-devices.md).

Si vous êtes un développeur d’appareils, nous vous suggérons de vous renseigner sur les [appareils Azure IoT Edge et Azure IoT Central](./concepts-iot-edge.md).
