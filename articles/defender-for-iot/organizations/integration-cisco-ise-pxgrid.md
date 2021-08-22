---
title: À propos de l’intégration de Cisco ISE pxGrid
description: En associant les capacités de Defender pour IoT à Cisco ISE pxGrid, les équipes de sécurité bénéficient d’une visibilité sans précédent sur les appareils de l’écosystème de l’entreprise.
ms.date: 12/28/2020
ms.topic: how-to
ms.openlocfilehash: 00151f2e407c65d024f3bd59bdaa85a08ae677f4
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015279"
---
# <a name="about-the-cisco-ise-pxgrid-integration"></a>À propos de l’intégration de Cisco ISE pxGrid

Defender pour IoT offre la seule plateforme de cybersécurité ICS et IoT, créée par des experts de l’équipe bleue disposant d’une solide expérience dans la défense de l’infrastructure nationale critique, et la seule plateforme offrant une analytique des menaces prenant en charge ICS et le Machine Learning. Defender pour IoT offre les fonctionnalités suivantes :

- des informations immédiates sur les appareils ICS, les vulnérabilités et les menaces connues et de type zero-day.

- Connaissance intégrée prenant en charge ICS sur les protocoles, les appareils, les applications OT et leur comportement.

- Technologie de modélisation des menaces ICS automatisée pour prédire les chemins les plus probables des attaques ICS ciblées via une analytique propriétaire.

## <a name="powerful-device-visibility-and-control"></a>Une visibilité et un contrôle puissants des appareils

L’intégration de Defender pour IoT avec Cisco ISE pxGrid offre un nouveau niveau de visibilité, de surveillance et de contrôle centralisés pour le paysage OT.

Ces plateformes associées permettent une visibilité et une protection automatisées des appareils pour les appareils ICS et IIoT qui n’étaient pas accessibles précédemment.

### <a name="ics-and-iiot-device-visibility-comprehensive-and-deep"></a>Visibilité des appareils ICS et IIoT : complète et approfondie

Les technologies brevetées de Defender pour IoT garantissent une détection complète et approfondie des appareils ICS et IIoT la gestion de l’inventaire des données d’entreprise.

Les données relatives aux types d’appareils, fabricants, ports ouverts, numéros de série, révisions de microprogramme, adresses IP et adresse MAC, entre autres, sont mises à jour en temps réel. Defender pour IoT peut encore améliorer la visibilité, la découverte et l’analyse à partir de cette ligne de base en s’intégrant aux sources de données critiques de l’entreprise. Par exemple, les CMDB, le DNS, les pare-feu et les API web.

En outre, la plateforme Defender pour IoT combine une surveillance passive et des techniques de sondage sélectif facultatives pour fournir l’inventaire le plus précis et le plus détaillé des appareils dans les organisations industrielles et d’infrastructures critiques.

### <a name="bridged-capabilities"></a>Capacités de liaison

En associant ces capacités à Cisco ISE pxGrid, les équipes de sécurité bénéficient d’une visibilité sans précédent sur les appareils de l’écosystème de l’entreprise.

L’intégration transparente et robuste avec Cisco ISE pxGrid garantit qu’aucun appareil OT ne reste inconnu et qu’aucune information sur les appareils n’est manquée.

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoint-categories.png" alt-text="Exemple d’identificateur OUI des catégories de points de terminaison.":::

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoints.png" alt-text="Exemples de points de terminaison dans le système":::  

:::image type="content" source="media/integration-cisco-isepxgrid-integration/attributes.png" alt-text="Capture d’écran des attributs situés dans le système.":::  

### <a name="use-case-coverage-ise-policies-based-on-defender-for-iot-attributes"></a>Couverture des cas d’usage : Stratégies ISE basées sur les attributs de Defender pour IoT

Utilisez de puissantes stratégies ISE basées sur le Deep Learning de Defender pour IoT pour gérer les exigences de cas d’usage ICS et IoT.

L’utilisation de stratégies vous permet de fermer le cycle de sécurité et de mettre votre réseau en conformité en temps réel.

Par exemple, les clients peuvent utiliser les attributs ICS et IoT de Defender pour IoT pour créer des stratégies qui gèrent les cas d’usage suivants, notamment :

- Créer une stratégie d’autorisation pour permettre à des appareils connus et autorisés d’entrer dans une zone sensible en fonction des attributs autorisés, par exemple, une version de microprogramme spécifique pour les PLC Rockwell Automation.

- Avertir les équipes de sécurité lorsqu’un appareil ICS est détecté dans une zone non OT.

- Corriger les machines dont les fournisseurs sont obsolètes ou non conformes.

- Mettre en quarantaine et bloquer les appareils en fonction des besoins.

- Générer des rapports sur les PLC ou les RTU exécutant des microprogrammes présentant des vulnérabilités connues (CVE).

### <a name="about-this-article"></a>À propos de cet article

Cet article explique comment configurer pxGrid et la plateforme Defender pour IoT pour garantir que Defender pour IoT injecte des attributs OT dans Cisco ISE.

### <a name="getting-more-information"></a>Obtenir des informations complémentaires

Pour plus d’informations sur les conditions requises pour l’intégration de Cisco ISE pxGrid, consultez <https://www.cisco.com/c/en/us/products/security/pxgrid.html>

## <a name="integration-system-requirements"></a>Configuration requise pour l’intégration

Cet article décrit la configuration requise pour l’intégration :

Conditions relatives à Defender pour IoT

- Defender pour IoT version 2.5

Conditions relatives à Cisco

- pxGrid version 2.0

- Cisco ISE version 2.4

Configuration requise pour le réseau

- Vérifiez que l’appliance Defender pour IoT a accès à l’interface de gestion Cisco ISE.

- Vérifiez que vous disposez d’un accès à l’interface de ligne de commande pour toutes les appliances Defender pour IoT de votre entreprise.

> [!NOTE]
> Seuls les appareils ayant des adresses MAC sont synchronisés à Cisco ISE pxGrid.

## <a name="cisco-ise-pxgrid-setup"></a>Installation de Cisco ISE pxGrid

Cet article explique comment :

- Configurer la communication avec pxGrid

- S’abonner à la rubrique d’un appareil de point de terminaison

- Générer des certificats

- Définir les paramètres de pxGrid

## <a name="set-up-communication-with-pxgrid"></a>Configurer la communication avec pxGrid

Cet article explique comment configurer la communication avec pxGrid.

Pour configurer la communication :

1. Connectez-vous à Cisco ISE.

1. Sélectionnez **Administration**>**Système** et **Déploiement**.

1. Sélectionnez le nœud requis. Dans l’onglet Paramètres généraux, cochez la case **pxGrid**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/pxgrid.png" alt-text="Vérifiez que la case pxGrid est cochée.":::

1. Sélectionnez l’onglet **Configuration du profilage**.

1. Cochez la case **pxGrid**. Ajoutez une description.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/profile-configuration.png" alt-text="Capture d’écran de l’ajout d’une description":::

## <a name="subscribe-to-the-endpoint-device-topic"></a>S’abonner à la rubrique d’un appareil de point de terminaison

Vérifiez que le nœud ISE pxGrid s’est abonné à la rubrique de l’appareil de point de terminaison. Accédez à **Administration**>**Services pxGrid**>**Clients web**. À partir de là, vous pouvez vérifier qu’ISE s’est abonné à la rubrique de l’appareil de point de terminaison.

## <a name="generate-certificates"></a>Générer des certificats

Cet article explique comment générer des certificats.

Pour générer :

1. Sélectionnez **Administration** > **Services pxGrid**, puis sélectionnez **Certificats**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/certificates.png" alt-text="Sélectionnez l’onglet Certificats afin de générer un certificat.":::

1. Dans le champ **Je souhaite**, sélectionnez **Générer un certificat unique (sans demande de signature de certificat)** .

1. Renseignez les champs restants et sélectionnez **Créer**.

1. Créez la clé du certificat client et le certificat de serveur, puis convertissez-les en magasin de clés Java. Vous devez les copier dans chaque capteur Defender pour IoT de votre réseau.

## <a name="define-pxgrid-settings"></a>Définir les paramètres de pxGrid

Pour définir les paramètres :

1. Sélectionnez **Administration** > **Services pxGrid**, puis sélectionnez **Paramètres**.

1. Activez les options **Approuver automatiquement les nouveaux comptes basés sur des certificats** et **Autoriser la création de comptes basés sur un mot de passe**.

  :::image type="content" source="media/integration-cisco-isepxgrid-integration/allow-these.png" alt-text="Vérifiez que les deux cases sont cochées.":::

## <a name="set-up-the-defender-for-iot-appliances"></a>Configurer les appliances Defender pour IoT

Cet article explique comment configurer les appliances Defender pour IoT pour qu’elles communiquent avec pxGrid. La configuration doit être effectuée sur toutes les appliances Defender pour IoT qui injecteront des données dans Cisco ISE.

Pour configurer des appliances :

1. Connectez-vous à l’interface de ligne de commande du capteur.

1. Copiez `trust.jks`, qui a été créé précédemment sur le capteur. Collez-le dans `/var/cyberx/properties/`.

1. Modifiez `/var/cyberx/properties/pxgrid.properties`:

    1. Ajoutez une clé et une approbation, puis stockez les noms de fichiers et les mots de passe.

    2. Ajoutez le nom d’hôte de l’instance pxGrid.

    3. `Enabled=true`

1. Redémarrez l’appliance.

1. Répétez ces étapes pour chaque appliance de votre entreprise qui injecte des données.

## <a name="view-and-manage-detections-in-cisco-ise"></a>Afficher et gérer les détections dans Cisco ISE

1. Les détections de point de terminaison s’affichent dans l’onglet **Visibilité** > **Points de terminaison** du contexte ISE.

1. Sélectionnez **Stratégie** > **Profilage** > **Ajouter** > **Règles** >  **+ Condition** > **Créer une condition**.

1. Sélectionnez **Attribut** et utilisez les dictionnaires d’appareils IoT pour créer une règle de profilage basée sur les attributs injectés. Les attributs suivants sont injectés.

    - Type d’appareil

    - Révision du matériel

    - Adresse IP

    - Adresse MAC

    - Nom

    - Product ID

    - Protocol

    - Numéro de série

    - Révision du logiciel

    - Fournisseur

Seuls les appareils ayant des adresses MAC sont synchronisés.

## <a name="troubleshooting-and-logs"></a>Résolution des problèmes et journaux

Les journaux sont accessibles sous les chemins suivants :

- `/var/cyberx/logs/pxgrid.log`

- `/var/cyberx/logs/core.log`

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [transférer des informations d’alerte](how-to-forward-alert-information-to-partners.md).
