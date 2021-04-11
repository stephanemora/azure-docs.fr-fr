---
title: À propos de l’intégration de Forescout
description: L’intégration d’Azure Defender pour IoT avec la plateforme Forescout offre un nouveau niveau de visibilité, de surveillance et de contrôle centralisés pour le paysage IoT et OT.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 07e5187970d193502b95b49c5517a8e3824767be
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784065"
---
# <a name="about-the-forescout-integration"></a>À propos de l’intégration de Forescout

Azure Defender pour IoT offre une plateforme de cybersécurité pour le partage de connexion Internet (ICS) et l’Internet des objets (IoT), créée par des experts de l’équipe Blue Team disposant d’une solide expérience dans la défense d’infrastructure nationale critique. Defender pour IoT est la seule plateforme offrant une analytique des menaces prenant en charge ICS et le Machine Learning. Defender pour IoT offre les fonctionnalités suivantes :

- Insights immédiats sur ICS dans le paysage de l’appareil avec un large éventail de détails sur les attributs.
- Connaissance intégrée prenant en charge ICS sur les protocoles, les appareils, les applications OT et leur comportement.
- Insights immédiats sur les vulnérabilités ainsi que les menaces connues et de type Zero-day.
- Technologie de modélisation des menaces ICS automatisée pour prédire les chemins les plus probables des attaques ICS ciblées via une analytique propriétaire.

L’intégration de Defender pour IoT avec la plateforme Forescout offre un nouveau niveau de visibilité, de surveillance et de contrôle centralisés pour le paysage IoT et OT.

Ces plateformes reliées par un pont activent la visibilité et la gestion automatisées des appareils pour les appareils ICS qui n’étaient pas accessibles précédemment et les flux de travail en silo.

L’intégration offre aux analystes SOC une visibilité à plusieurs niveaux des protocoles OT déployés dans des environnements industriels. Des détails sont disponibles pour des éléments tels que les microprogrammes, les types d’appareils, les systèmes d’exploitation et les scores d’analyse des risques basés sur des technologies Azure Defender pour IoT propriétaires.

> [!Note]
> Les références à CyberX concernent Azure Defender pour IoT.
## <a name="devices"></a>Appareils

### <a name="device-visibility-and-management"></a>Visibilité et gestion des appareils

L’inventaire de l’appareil est enrichi avec des attributs critiques détectés par la plateforme Defender pour IoT. Cela offre les avantages suivants :

- Vous bénéficiez d’une visibilité complète et continue du paysage des appareils OT à partir d’un volet unique.
- Vous êtes renseigné en temps réel sur les risques OT.

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="Inventaire des appareils":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="Détails sur l'appareil":::

### <a name="device-control"></a>Contrôle de l’appareil

L’intégration de Forescout vous aide à réduire le temps nécessaire aux organisations dotées d’une infrastructure industrielle et critique pour détecter, examiner et traiter les cybermenaces.

- Utilisez le renseignement sur les appareils OT d’Azure Defender pour IoT afin de clore le cycle de sécurité en déclenchant des actions de stratégie Forescout. Par exemple, vous pouvez envoyer automatiquement un e-mail d’alerte aux administrateurs SOC lorsque des protocoles spécifiques sont détectés ou lorsque des détails du microprogramme changent.

- Mettez en corrélation les informations relatives à Defender pour IoT avec d’autres modules *Forescout eyeExtend* qui supervisent la surveillance, la gestion des incidents et le contrôle des appareils.

## <a name="system-requirements"></a>Configuration requise

- Azure Defender pour IoT versions 2.4 ou ultérieures
- Forescout versions 8.0 ou ultérieures
- Une licence pour le module *Forescout eyeExtend* pour la plateforme Azure Defender pour IoT.

### <a name="getting-more-forescout-information"></a>Obtention d’informations supplémentaires sur Forescout

Pour plus d’informations sur la plateforme Forescout, consultez le [Centre de ressources Forescout](https://www.forescout.com/company/resources/#resource_filter_group).

## <a name="system-setup"></a>Configuration du système

Cet article explique comment configurer la communication entre la plateforme Defender pour IoT et la plateforme Forescout.

### <a name="set-up-the-defender-for-iot-platform"></a>Configurer la plateforme Defender pour IoT

Pour garantir la communication entre Defender pour IoT et Forescout, générez un jeton d’accès dans Defender pour IoT.

Les jetons d’accès permettent à des systèmes externes d’accéder aux données découvertes par Defender pour IoT et d’effectuer des actions avec ces données à l’aide de l’API REST externe sur des connexions SSL. Vous pouvez générer des jetons d’accès afin d’accéder à l’API REST Azure Defender pour IoT.

Pour générer un jeton :

1. Connectez-vous au capteur Defender pour IoT que Forescout interrogera.

1. Dans la section **Général**, Sélectionnez **Paramètres système**, puis **Jetons d’accès**. La boîte de dialogue **Jetons d’accès** s’ouvre.
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="Jetons d’accès":::
1. Dans la boîte de dialogue **Jetons d’accès**, sélectionnez **Générer un nouveau jeton**.
1. Dans la boîte de dialogue **Nouveau jeton d’accès**, entrez une description pour le jeton.
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="Nouveau jeton d’accès":::
1. Sélectionnez **Suivant**. Le jeton s’affiche dans la boîte de dialogue. :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="Affichez le jeton":::
   > [!NOTE]
   > *Enregistrez le jeton dans un emplacement sûr. Vous en aurez besoin lors de la configuration de la plateforme Forescout*.
1. Sélectionnez **Terminer**.

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Achevez d’ajouter le jeton":::

### <a name="set-up-the-forescout-platform"></a>Configurer la plateforme Forescout

Vous pouvez configurer la plateforme Forescout pour qu’elle communique avec un capteur Defender pour IoT.

Pour configurer :

1. Installez *le module Forescout eyeExtend pour CyberX* sur la plateforme Forescout.

1. Connectez-vous à la console CounterACT, puis, dans le menu **Outils**, sélectionnez **Options**. La boîte de dialogue **Options** s'ouvre.

1. Accédez au dossier **modules** et sélectionnez-le.

1. Dans le volet **Modules**, sélectionnez **Plateforme CyberX**. Le volet de la plateforme Defender pour IoT s’ouvre.

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="Paramètres du module Azure Defender pour IoT":::

   Entrez les informations suivantes :

   - **Adresse du serveur** : entrez l’adresse IP du capteur Defender pour IoT que l’appliance Forescout interrogera.
   - **Jeton d’accès** : entrez le jeton d’accès généré pour le capteur Defender pour IoT qui se connectera à l’appliance Forescout. Pour générer un jeton, consultez [Configurer la plateforme Defender pour IoT](#set-up-the-defender-for-iot-platform).

1. Sélectionnez **Appliquer**.

Si vous souhaitez que la plateforme Forescout communique avec un autre capteur :

1. Créez un jeton d’accès dans le capteur Defender pour IoT approprié.

1. Dans la boîte de dialogue **Modules Forescout** > **Plateforme CyberX** :

   - Supprimez les informations affichées.
   
   - Entrez la nouvelle adresse IP du capteur et les nouvelles informations du jeton d’accès.

### <a name="verify-communication"></a>Vérifier la communication

Après avoir configuré Defender pour IoT et Forescout, ouvrez la boîte de dialogue Jetons d’accès du capteur dans Defender pour IoT.

Si **N/A** est affiché dans le champ **Utilisé** pour ce jeton, cela signifie que la connexion entre le capteur et l’appliance Forescout ne fonctionne pas.

**Utilisé** indique l’heure à laquelle le dernier appel externe avec ce jeton a été reçu.

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Vérifie que le jeton a été reçu":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>Afficher les détections de Defender pour IoT dans Forescout

Pour afficher les attributs d’un appareil :

1. Connectez-vous à la plateforme Forescout, puis accédez à l’**inventaire des ressources**.

1. Accédez à la **plateforme CyberX**. Les attributs d’appareil suivants sont affichés pour les appareils que Defender pour IoT a détectés.

   | Élément | Description |
   |--|--|
   | Autorisé par Azure Defender pour IoT | Appareil que Defender pour IoT a détecté sur votre réseau pendant la période d’apprentissage du réseau. |
   | Microprogramme | Détails du microprogramme de l’appareil. Par exemple, détails du modèle et de la version. |
   | Nom | Nom de l’appareil |
   | Système d’exploitation | Système d’exploitation de l’appareil. |
   | Type | Type d’appareil. Par exemple, PLC, Historien ou Station d’ingénierie. |
   | Fournisseur | Fournisseur de l’appareil. Par exemple, Rockwell Automation. |
   | Niveau de risque | Niveau de risque calculé par Defender pour IoT. |
   | Protocoles | Protocoles détectés dans le trafic généré par l’appareil. |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="Affichez les attributs du microprogramme.":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="Affichez les attributs des fournisseurs.":::

### <a name="viewing-more-details"></a>Affichage de détails supplémentaires

Affichez des informations supplémentaires sur les appareils dirigés par Defender pour IoT. Par exemple, informations sur la conformité et la stratégie de Forescout.

Pour ce faire, cliquez avec le bouton droit sur un appareil dans la section **Hôtes d’inventaire des appareils** . La boîte de dialogue Détails de l’hôte s’ouvre, affichant des informations supplémentaires.

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="Détails de l’hôte":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>Créer des stratégies Azure Defender pour IoT dans Forescout

Vous pouvez utiliser des stratégies Forescout pour automatiser le contrôle et la gestion des appareils détectés par Defender pour IoT. Par exemple,

- Envoyez automatiquement un e-mail aux administrateurs SOC quand des versions de microprogramme spécifiques sont détectées.

- Ajoutez des appareils Defender pour IoT spécifiques détectés à un groupe Forescout pour une gestion plus poussée des flux de travail d’incident et de sécurité, par exemple, avec d’autres intégrations SIEM.

Créez une stratégie personnalisée Forescout avec Defender pour IoT à l’aide des propriétés de condition.

Pour accéder aux propriétés de Defender pour IoT :

1. Accédez à l’**Arborescence Propriétés** dans la boîte de dialogue **Conditions de la stratégie**.

1. Développez le dossier **Plateforme CyberX** dans l’**Arborescence Propriétés**. Les propriétés suivantes de Defender pour IoT sont disponibles.

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="Propriétés":::

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [transférer des informations d’alerte](how-to-forward-alert-information-to-partners.md).
