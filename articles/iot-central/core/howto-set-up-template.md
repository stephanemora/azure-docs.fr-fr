---
title: Définir un nouveau type d’appareil IoT dans Azure IoT Central | Microsoft Docs
description: Cet article vous explique comment créer un modèle d’appareil Azure IoT dans votre application Azure IoT Central. Vous définissez la télémétrie, l’état, les propriétés et les commandes pour votre type d’appareil.
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperf-fy21q1
- device-developer
ms.openlocfilehash: 59c95633322f279504ded61bbf02a7415b1337b9
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122527969"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Définir un nouveau type d’appareil IoT dans votre application Azure IoT Central

Un modèle d’appareil est un blueprint qui définit les caractéristiques et les comportements d’un type d’appareil se connectant à une [application Azure IoT Central](concepts-app-templates.md).

Cet article explique comment créer un modèle d’appareil dans IoT Central. Par exemple, vous pouvez créer un modèle d’appareil pour un capteur qui envoie de la télémétrie (p. ex. : la température) et des propriétés (p. ex. : l’emplacement). À partir de ce modèle d’appareil, un opérateur peut créer et connecter des appareils réels.

La capture d’écran suivante montre un exemple de modèle d’appareil :

:::image type="content" source="media/howto-set-up-template/device-template.png" alt-text="Capture d’écran montrant un modèle d’appareil.":::

Le modèle d’appareil comprend les sections suivantes :

- Modèle : Utilisez le modèle pour définir la manière dont votre appareil interagit avec votre application IoT Central. Chaque modèle possède un ID de modèle unique et définit les capacités de l’appareil. Les capacités sont regroupées en interfaces. Les interfaces vous permettent de réutiliser les composants entre les modèles ou d’utiliser l’héritage pour étendre l’ensemble des capacités.
- Propriétés de cloud : Utilisez les propriétés de cloud pour définir les informations concernant vos appareils que votre application IoT Central stocke. Par exemple, une propriété de cloud peut enregistrer la date de la dernière maintenance d’un appareil.
- Personnaliser : Utilisez les personnalisations pour modifier les capacités. Par exemple, spécifiez les valeurs de température minimale et maximale pour une propriété.
- Vues : Utilisez les vues pour visualiser les données de l’appareil et les formulaires pour gérer et contrôler un appareil.

Pour plus d’informations, consultez [Que sont les modèles d’appareil ?](concepts-device-templates.md).

## <a name="create-a-device-template"></a>Créer un modèle d’appareil

Plusieurs choix s’offrent à vous pour créer des modèles d’appareil :

- Concevez le modèle d’appareil dans l’interface graphique utilisateur d’IoT Central.
- Importez un modèle d’appareil à partir du [catalogue d’appareils certifiés Azure pour l’IoT](https://aka.ms/iotdevcat). Si vous le souhaitez, personnalisez le modèle d’appareil selon vos besoins dans IoT Central.
- Lorsque l’appareil se connecte à IoT Central, demandez-lui d’envoyer l’ID du modèle qu’il implémente. IoT Central utilise cet ID du modèle pour récupérer le modèle dans le référentiel de modèles et créer un modèle d’appareil. Ajoutez au modèle d’appareil les propriétés de cloud, les personnalisations et les vues dont votre application IoT Central a besoin.
- Lorsque l’appareil se connecte à IoT Central, laissez IoT Central [générer automatiquement une définition de modèle d’appareil](#autogenerate-a-device-template) à partir des données envoyées par l’appareil.
- Créez un modèle d’appareil à l’aide de la [version 2 du langage DTDL (Digital Twin Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Importez manuellement le modèle d’appareil dans votre application IoT Central. Ajoutez ensuite les propriétés de cloud, les personnalisations et les vues dont votre application IoT Central a besoin.
- Vous pouvez également ajouter des modèles d’appareil à une application IoT Central à l’aide de l’[API REST](/learn/modules/manage-iot-central-apps-with-rest-api/) ou de l’interface [CLI](howto-manage-iot-central-from-cli.md).

> [!NOTE]
> Dans chaque cas, le code de l’appareil doit implémenter les capacités définies dans le modèle. L’implémentation du code de l’appareil n’est pas concernée par les sections Propriétés de cloud, Personnalisations et Vues du modèle d’appareil.

Cette section vous montre comment importer un modèle d’appareil à partir du catalogue et comment le personnaliser à l’aide de l’interface graphique utilisateur d’IoT Central. Cet exemple utilise le modèle d’appareil **ESP32-Azure IoT Kit** du catalogue d’appareils :

1. Pour ajouter un nouveau modèle d’appareil, sélectionnez **+ Nouveau** dans la page **Modèles d’appareil**.
1. Dans la page **Sélectionner le type**, faites défiler jusqu’à la vignette **ESP32-Azure IoT Kit** dans la section **Utiliser un modèle d’appareil préconfiguré**.
1. Sélectionnez la vignette **ESP32-Azure IoT Kit**, puis sélectionnez **Suivant : Vérification**).
1. Dans la page **Vérifier**, sélectionnez **Créer**.
Le nom du modèle que vous avez créé est **Sensor Controller**. Le modèle de capacité comprend des composants tels que **Sensor Controller**, **SensorTemp** et **Device Information interface**. Les composants définissent les fonctionnalités d’un appareil ESP32, telles que la télémétrie, les propriétés et les commandes.

:::image type="content" source="media/howto-set-up-template/device-template.png" alt-text="Capture d’écran d’un modèle d’appareil Contrôleur de capteur.":::

## <a name="autogenerate-a-device-template"></a>Générer automatiquement un modèle d’appareil

Vous pouvez également créer automatiquement un modèle d’appareil à partir d’un appareil connecté qui n’est pas encore affecté à un modèle d’appareil. IoT Central utilise la télémétrie et les valeurs de propriété que l’appareil envoie pour déduire un modèle d’appareil.

> [!NOTE]
> Actuellement, cette fonctionnalité d’évaluation ne peut pas utiliser la télémétrie et les propriétés des composants. Elle peut uniquement générer des capacités à partir de la télémétrie et des propriétés de la racine.

Les étapes suivantes montrent comment utiliser cette fonctionnalité :

1. Connectez votre appareil à IoT Central, et commencez à envoyer les données. Lorsque les données apparaissent dans la vue **Données brutes**, sélectionnez **Créer le modèle automatiquement** dans la liste déroulante **Gérer le modèle** :

    :::image type="content" source="media/howto-set-up-template/infer-model-1.png" alt-text="Capture d’écran montrant les données brutes d’un appareil non assigné.":::

1. Sur la page **Aperçu des données**, apportez toutes les modifications nécessaires aux données brutes, puis sélectionnez **Créer le modèle** :

    :::image type="content" source="media/howto-set-up-template/infer-model-2.png" alt-text="Capture d’écran montrant la modification de l’aperçu des données qui vous permet de modifier les données qu’IoT Central utilise pour générer le modèle d’appareil.":::

1. IoT Central génère un modèle basé sur le format de données indiqué sur la page Aperçu **des données** et lui affecte l’appareil. Vous pouvez apporter d’autres modifications au modèle d’appareil, par exemple en le renommant ou en ajoutant des capacités, sur la page **Modèles d’appareil** :

    :::image type="content" source="media/howto-set-up-template/infer-model-3.png" alt-text="Capture d’écran montrant comment renommer le modèle d’appareil généré automatiquement.":::

## <a name="manage-a-device-template"></a>Gérer un modèle d’appareil

Vous pouvez renommer ou supprimer un modèle à partir de la page d’éditeur du modèle.

Une fois que vous avez défini le modèle, vous pouvez le publier. Tant que le modèle n’est pas publié, vous ne pouvez pas y connecter un appareil et il n’apparaît pas sur la page **Appareils**.

Pour en savoir plus sur la modification et le contrôle de version des modèles d’appareils, consultez [Modifier un modèle d’appareil existant](howto-edit-device-template.md).

## <a name="models"></a>Modèles

Le modèle définit la manière dont votre appareil interagit avec votre application IoT Central. Personnalisez votre modèle avec davantage de capacités, ajoutez des interfaces pour hériter des capacités ou ajoutez de nouveaux composants basés sur d’autres interfaces.

Pour créer un modèle d’appareil, plusieurs possibilités s’offrent à vous :

- Utilisez IoT Central pour créer un modèle personnalisé en partant de zéro.
- Importez un modèle DTDL à partir d’un fichier JSON. Un concepteur d’appareils peut avoir utilisé Visual Studio Code pour créer un modèle d’appareil pour votre application.
- Sélectionnez l’un des appareils dans le catalogue d’appareils. Cette option importe le modèle d’appareil que le fabricant a publié pour cet appareil. Un modèle d’appareil importé de cette façon est automatiquement publié.

1. Pour afficher l’identifiant du modèle, sélectionnez l’interface racine dans le modèle et sélectionnez **Modifier l’identité** :

    :::image type="content" source="media/howto-set-up-template/view-id.png" alt-text="Capture d’écran montrant l’identifiant du modèle pour l’interface racine du modèle d’appareil.":::

1. Pour afficher l’identifiant du composant, sélectionnez **Modifier l’identité** sur n’importe quelle interface de composant du modèle.

Pour en savoir plus, consultez le [guide de modélisation d’IoT Plug-and-Play](../../iot-pnp/concepts-modeling-guide.md).

### <a name="interfaces-and-components"></a>Interfaces et composants

Pour afficher et gérer les interfaces dans votre modèle d’appareil :

1. Accédez à la page **Modèles d’appareil** et sélectionnez le modèle d’appareil que vous avez créé. Les interfaces sont répertoriées dans la section **Modèles** du modèle d’appareil. La capture d’écran suivante montre un exemple de l’interface racine de **Contrôleur de capteur** dans un modèle d’appareil :

    :::image type="content" source="media/howto-set-up-template/device-template.png" alt-text="Capture d’écran montrant l’interface racine d’un modèle"::: 

1. Sélectionnez le bouton de sélection pour ajouter une interface ou un composant hérité à l’interface racine. Pour en savoir plus sur les interfaces et les composants, consultez la section [Plusieurs composants](../../iot-pnp/concepts-modeling-guide.md#multiple-components) dans le guide de modélisation.

    :::image type="content" source="media/howto-set-up-template/add-interface.png" alt-text="Ajout d’une interface ou d’un composant":::

1. Pour exporter un modèle ou une interface, sélectionnez **Exporter**.

1. Pour afficher ou modifier le langage DTDL d’une interface ou d’une capacité, sélectionnez **Modifier le langage DTDL**.

### <a name="capabilities"></a>Fonctionnalités

Sélectionnez **+ Ajouter une capacité** pour ajouter une capacité à une interface ou à un composant. Par exemple, vous pouvez ajouter la capacité **Température cible** à un composant **SensorTemp**.

:::image type="content" source="media/howto-set-up-template/add-capability.png" alt-text="Ajout d’une capacité":::

#### <a name="telemetry"></a>Télémétrie

Les données de télémétrie sont un flux de valeurs envoyées à partir de l’appareil, généralement par un capteur. Par exemple, un capteur peut indiquer la température ambiante comme indiqué ci-dessous :

:::image type="content" source="media/howto-set-up-template/telemetry.png" alt-text="Ajout de télémétrie":::

Le tableau suivant décrit les paramètres de configuration d’une fonctionnalité de données de télémétrie :

| Champ | Description |
| ----- | ----------- |
| Nom d’affichage | Nom complet de la valeur de télémétrie utilisée sur les affichages et les formulaires. |
| Nom | Nom du champ dans le message de données de télémétrie. IoT Central génère une valeur pour ce champ à partir du nom d’affichage, mais vous pouvez choisir votre propre valeur si nécessaire. Ce champ doit être alphanumérique. |
| Type de fonctionnalité | Données de télémétrie. |
| Type sémantique | Type sémantique des données de télémétrie, telles que la température, l’état ou l’événement. Le choix du type sémantique détermine lequel des champs suivants est disponible. |
| schéma | Type de données de télémétrie, tel que double, chaîne ou vecteur. Les options disponibles sont déterminées par le type sémantique. Le schéma n’est pas disponible pour les types sémantiques d’événement et d’état. |
| severity | Disponible uniquement pour le type sémantique d’événement. Les gravités sont **Erreur**, **Information** ou **Avertissement**. |
| Valeurs d’état | Disponible uniquement pour le type sémantique d’état. Définissez les valeurs d’état possibles, chacune ayant un nom d’affichage, un nom, un type d’énumération et une valeur. |
| Unité | Unité pour la valeur des données de télémétrie, telles que **mph**, **%** ou **&deg;C**. |
| Unités d'affichage | Unité d’affichage à utiliser sur les affichages et les formulaires. |
| Commentaire | Commentaires sur la fonctionnalité de données de télémétrie. |
| Description | Description de la fonctionnalité de données de télémétrie. |

#### <a name="properties"></a>Propriétés

Les propriétés représentent des valeurs à un moment donné. Vous pouvez définir des propriétés accessibles en écriture à partir d’IoT Central.
Par exemple, un appareil peut utiliser une propriété accessible en écriture pour permettre à un opérateur de définir la température cible, comme indiqué ci-dessous :

:::image type="content" source="media/howto-set-up-template/property.png" alt-text="Ajout d’une propriété":::

Le tableau suivant décrit les paramètres de configuration d’une fonctionnalité de données de propriété :

| Champ | Description |
| ----- | ----------- |
| Nom d’affichage | Nom complet de la valeur de propriété utilisée sur les affichages et les formulaires. |
| Nom | Nom de la propriété. IoT Central génère une valeur pour ce champ à partir du nom d’affichage, mais vous pouvez choisir votre propre valeur si nécessaire. Ce champ doit être alphanumérique. |
| Type de fonctionnalité | Propriété. |
| Type sémantique | Type sémantique de la propriété, telles que la température, l’état ou l’événement. Le choix du type sémantique détermine lequel des champs suivants est disponible. |
| schéma | Type de données de propriété, tel que double, chaîne ou vecteur. Les options disponibles sont déterminées par le type sémantique. Le schéma n’est pas disponible pour les types sémantiques d’événement et d’état. |
| Accessible en écriture | Si la propriété n’est pas inscriptible, l’appareil peut signaler des valeurs de propriété à IoT Central. Si la propriété est inscriptible, l’appareil peut signaler des valeurs de propriétés à IoT Central, et IoT Central peut envoyer des mises à jour de propriétés à l’appareil.
| severity | Disponible uniquement pour le type sémantique d’événement. Les gravités sont **Erreur**, **Information** ou **Avertissement**. |
| Valeurs d’état | Disponible uniquement pour le type sémantique d’état. Définissez les valeurs d’état possibles, chacune ayant un nom d’affichage, un nom, un type d’énumération et une valeur. |
| Unité | Unité pour la valeur des propriétés, telles que **mph**, **%** ou **&deg;C**. |
| Unités d'affichage | Unité d’affichage à utiliser sur les affichages et les formulaires. |
| Commentaire | Commentaires sur la fonctionnalité de propriété. |
| Description | Description de la fonctionnalité de propriété. |

#### <a name="commands"></a>Commandes

Vous pouvez appeler des commandes d’appareil à partir d’IoT Central. Les commandes transmettent éventuellement des paramètres à l’appareil et reçoivent une réponse de ce dernier. Par exemple, vous pouvez appeler une commande pour redémarrer un appareil en 10 secondes, comme indiqué ci-dessous :

:::image type="content" source="media/howto-set-up-template/command.png" alt-text="Ajout de commandes":::

Le tableau suivant décrit les paramètres de configuration d’une fonctionnalité de commande :

| Champ | Description |
| ----- | ----------- |
| Nom d’affichage | Nom complet de la commande utilisée sur les affichages et les formulaires. |
| Nom | Nom de la commande. IoT Central génère une valeur pour ce champ à partir du nom d’affichage, mais vous pouvez choisir votre propre valeur si nécessaire. Ce champ doit être alphanumérique. |
| Type de fonctionnalité | Commande. |
| Mettre en file d'attente si hors connexion | Si cette option est activée, vous pouvez appeler la commande même si l’appareil est hors connexion. Si elle n’est pas activée, vous pouvez uniquement appeler la commande lorsque l’appareil est en ligne. |
| Commentaire | Commentaires sur la fonctionnalité de commande. |
| Description | Description de la fonctionnalité de commande. |
| Requête | Si cette option est activée, il s’agit d’une définition du paramètre de requête, notamment le nom, le nom d’affichage, le schéma, l’unité et l’unité d’affichage. |
| response | Si cette option est activée, il s’agit d’une définition de la réponse de commande, notamment le nom, le nom d’affichage, le schéma, l’unité et l’unité d’affichage. |

Pour en savoir plus sur la façon dont les appareils implémentent les commandes, consultez [Charges utiles de télémétrie, de propriétés et de commandes > Commandes et commandes de longue durée](concepts-telemetry-properties-commands.md#commands).

#### <a name="offline-commands"></a>Commandes hors connexion

Vous pouvez choisir des commandes de mise en file d’attente si un appareil est actuellement hors connexion en activant l’option **Mettre en file d’attente si hors connexion** pour une commande dans le modèle d’appareil.

:::image type="content" source="media/howto-set-up-template/offline-commands.png" alt-text="Ajout de commandes hors connexion":::

Cette option utilise des messages IoT Hub cloud-à-appareil pour envoyer les notifications aux appareils. Pour plus d’informations, consultez l’article IoT Hub [Envoyer des messages cloud-à-appareil](../../iot-hub/iot-hub-devguide-messages-c2d.md).

Messages cloud-à-appareil :

- Notifications unidirectionnelles de votre solution vers l’appareil.
- Remise des messages au moins une fois. IoT Hub conserve les messages cloud-à-appareil dans des files d’attente par appareil, garantissant ainsi la résilience face aux défaillances de connectivité et des appareils.
- Exiger que l’appareil implémente un gestionnaire de messages pour traiter le message cloud-à-appareil.

> [!NOTE]
> Cette option est disponible uniquement dans l’interface utilisateur web d’IoT Central. Ce paramètre n’est pas inclus si vous exportez un modèle ou un composant à partir du modèle d’appareil.

## <a name="cloud-properties"></a>Propriétés cloud

Utilisez les propriétés de cloud pour stocker des informations sur les appareils dans IoT Central. Les propriétés de cloud ne sont jamais envoyées à un appareil. Par exemple, vous pouvez utiliser les propriétés de cloud pour stocker le nom du client qui a installé l’appareil ou la date de la dernière maintenance de l’appareil.

:::image type="content" source="media/howto-set-up-template/cloud-properties.png" alt-text="Ajout de propriétés de cloud"::: 

Le tableau suivant illustre les paramètres de configuration d’une propriété de cloud :

| Champ | Description |
| ----- | ----------- |
| Nom d’affichage | Nom complet de la valeur de propriété cloud utilisée sur les affichages et les formulaires. |
| Nom | Nom de la propriété de cloud. IoT Central génère une valeur pour ce champ à partir du nom d’affichage, mais vous pouvez choisir votre propre valeur si nécessaire. |
| Type sémantique | Type sémantique de la propriété, telles que la température, l’état ou l’événement. Le choix du type sémantique détermine lequel des champs suivants est disponible. |
| schéma | Type de données de propriété de cloud, tel que double, chaîne ou vecteur. Les options disponibles sont déterminées par le type sémantique. |

## <a name="customizations"></a>Personnalisations

Utilisez des personnalisations quand vous devez modifier un composant importé ou ajouter des fonctionnalités propres à IoT Central à une capacité. Par exemple, vous pouvez modifier le nom d’affichage et les unités d’une propriété comme indiqué ci-dessous :

:::image type="content" source="media/howto-set-up-template/customize.png" alt-text="Procédure de personnalisation":::

Le tableau suivant illustre les paramètres de configuration pour les personnalisations :

| Champ | Description |
| ----- | ----------- |
|Nom complet | Remplacement du nom d’affichage du modèle. |
|Type de sémantique | Remplacement du type sémantique du modèle. |
|Unité | Remplacement de l’unité du modèle. |
|Unité d'affichage | Remplacement du modèle. |
|Commentaire | Remplacement du modèle. |
|Description | Remplacement du modèle. |
|Couleur | Option spécifique à IoT Central. |
|Valeur min | Définition de la valeur minimale. Option spécifique à IoT Central. |
|Valeur max | Définition de la valeur maximale. Option spécifique à IoT Central. |
|Nombre de décimales | Option spécifique à IoT Central. |
|Valeur initiale | Valeur « Commandes » uniquement spécifique à IoT Central. Valeur par défaut du paramètre. |

## <a name="views"></a>Les vues

Les vues vous permettent de définir des vues et des formulaires qui permettent à un opérateur de surveiller un appareil et d’interagir avec lui. Les vues utilisent des visualisations telles que des graphiques pour afficher la télémétrie et les valeurs des propriétés.

La génération de vues par défaut permet de visualiser rapidement les informations importantes de votre appareil. Les trois vues par défaut sont les suivantes :

### <a name="default-views"></a>Vues par défaut

- **Commandes** : affiche des commandes d’appareil et permet à votre opérateur de les répartir sur votre appareil.
- **Vue d’ensemble** : affiche la télémétrie de l’appareil sous la forme de graphiques et de métriques.
- **À propos de** : affiche des informations sur l’appareil et ses propriétés.

Une fois que vous avez sélectionné **Générer les vues par défaut**, elles sont automatiquement ajoutées sous la section **Vues** de votre modèle d’appareil.

### <a name="custom-views"></a>Vues personnalisées

Ajoutez des affichages à un modèle d’appareil pour permettre aux opérateurs de visualiser un appareil à l’aide de graphiques et de métriques. Vous pouvez ajouter vos propres vues personnalisées à un modèle d’appareil.

Pour ajouter un affichage au modèle d’appareil :

1. Accédez à votre modèle d’appareil et sélectionnez **Vues**.
1. Sélectionnez **Visualisation de l’appareil**.
1. Entrez un nom pour votre affichage dans **Nom de l’affichage**.
1. Sélectionnez **Commencer par un contrôle** sous Ajouter des vignettes et choisissez le type de contrôle pour votre vignette. Ensuite, sélectionnez **Ajouter une vignette** ou faites glisser le contrôle et déposer-le sur le canevas. Pour configurer la vignette, sélectionnez l’icône en forme d’engrenage.

:::image type="content" source="media/howto-set-up-template/start-visual.png" alt-text="Commencer par un contrôle"::: 

:::image type="content" source="media/howto-set-up-template/tile.png" alt-text="Configurer une vignette"::: 

Pour tester votre vue, sélectionnez **Configurer l’aperçu d’appareil**. Cette fonctionnalité vous permet de voir la vue telle qu’un opérateur la voit après sa publication. Utilisez cette fonctionnalité pour vérifier que vos vues affichent les données qui conviennent. Choisissez parmi les options suivantes :

- Aucun appareil en préversion
- Le véritable appareil de test que vous avez configuré pour votre modèle d’appareil
- Un appareil existant dans votre application, à l’aide de l’ID de l’appareil

### <a name="forms"></a>Formulaires

Ajoutez des formulaires à un modèle d’appareil pour permettre aux opérateurs de gérer un appareil en affichant et en définissant des propriétés. Les opérateurs peuvent uniquement modifier des propriétés de cloud et des propriétés d’appareil inscriptibles. Vous pouvez avoir plusieurs formulaires pour un modèle d’appareil.

1. Après avoir sélectionné le nœud **Vues**, sélectionnez la vignette **Modification des données de l’appareil et du cloud** pour ajouter une nouvelle vue.

1. Remplacez le nom du formulaire par **Gérer l’appareil**.

1. Sélectionnez les propriétés cloud **Customer Name** et **Last Service Date** ainsi que la propriété **Target Temperature**. Sélectionnez ensuite **Ajouter une section**.

1. Sélectionnez **Enregistrer** pour enregistrer votre nouveau formulaire.

:::image type="content" source="media/howto-set-up-template/form.png" alt-text="Configurer un formulaire":::

## <a name="publish-a-device-template"></a>Publier un modèle d’appareil

Avant de pouvoir connecter un appareil qui implémente votre modèle d’appareil, vous devez publier votre modèle d’appareil.

Pour publier un modèle d’appareil, accédez à votre modèle d’appareil et sélectionnez **Publier**.

Une fois que vous avez publié un modèle d’appareil, un opérateur peut accéder à la page **Appareils** et ajouter des appareils, réels ou simulés, qui utilisent votre modèle d’appareil. Vous pouvez continuer à modifier et à enregistrer votre modèle d’appareil au fur et à mesure que vous apportez des modifications. Quand vous souhaitez envoyer ces modifications à l’opérateur pour qu’elles s’affichent sous la page **Appareils**, vous devez systématiquement sélectionner **Publier**.

## <a name="next-steps"></a>Étapes suivantes

L’étape suivante suggérée est de savoir comment [apporter des modifications à un modèle d’appareil existant](howto-edit-device-template.md).
