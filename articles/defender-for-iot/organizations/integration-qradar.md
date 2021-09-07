---
title: Intégration de Qradar
description: Configurez l’intégration de la solution Defender pour IoT avec QRadar.
ms.date: 1/4/2021
ms.topic: article
ms.openlocfilehash: 26808482ad9fabd528d2ec7e0d846c301c2c2ebf
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341414"
---
# <a name="about-the-qradar-integration"></a>À propos de l’intégration de QRadar

Defender pour IoT offre la seule plateforme de cybersécurité ICS et IoT, créée par des experts de l’équipe bleue disposant d’une solide expérience dans la défense de l’infrastructure nationale critique, et la seule plateforme offrant une analytique des menaces prenant en charge ICS et le Machine Learning.

Defender pour IoT a intégré sa plateforme de surveillance des menaces ICS continue avec IBM QRadar. 

Voici quelques-uns des avantages de cette intégration :

- La possibilité de transférer Azure Defender pour les alertes IoT à IBM QRadar pour une informatique unifiée, ainsi que la surveillance de la sécurité et la gouvernance.

- La possibilité d’obtenir une vue d’ensemble sur les environnements informatiques et les environnements OT. Cela vous permet de détecter et de répondre aux attaques à plusieurs qui franchissent souvent les limites informatiques et OT.

- Intégration aux flux de travail SOC existants.

## <a name="configuring-syslog-listener-for-qradar"></a>Configuration de l’écouteur Syslog pour QRadar

Pour configurer l’écouteur Syslog afin qu’il fonctionne avec QRadar :

1. Connectez-vous à QRadar.

1. Dans le volet gauche, sélectionnez **Administration** > **Sources de données**.

   [:::image type="content" source="media/integration-qradar/log.png" alt-text="Sélectionnez les sources de journaux dans les options disponibles.":::](media/integration-qradar/log.png#lightbox)

1. Dans la fenêtre Sources de données, sélectionnez **Sources de journaux**.

   [:::image type="content" source="media/integration-qradar/modal.png" alt-text="Une fois que vous avez sélectionné Syslog, la fenêtre modale s’ouvre.":::](media/integration-qradar/modal.png#lightbox)

1. Dans la fenêtre **Modal**, sélectionnez **Ajouter**.

   :::image type="content" source="media/integration-qradar/source.png" alt-text="Ajoutez une source de journal en renseignant les champs appropriés.":::

1. Dans la boîte de dialogue **Ajouter une source de journal**, définissez les paramètres suivants :

   - **Nom de la source de journal** : `<XSense Name>`
   
   - **Description de la source de journal** : `<XSense Name>`
   
   - **Type de source de journal** : `Universal LEEF`

   - **Configuration du protocole** : `Syslog`
   
   - **Identificateur de la source de journal** : `<XSenseName>`
   
   > [!NOTE]
   > Le nom de l’identificateur de la source de journal ne doit pas contenir d’espace blanc. Il est recommandé de remplacer chaque caractère d’espace blanc par un trait de soulignement.

1. Sélectionnez **Enregistrer**.

1. Sélectionnez **Déployer les modifications**.

:::image type="content" source="media/integration-qradar/deploy.png" alt-text="Capture d’écran de l’affichage Déployer les modifications":::

## <a name="deploying-defender-for-iot-platform-qid"></a>Déploiement de Defender pour la plateforme IoT QID

QID est un identificateur d’événement dans QRadar. Tous les rapports de la plateforme Defender pour IoT sont marqués sous le même événement (alerte XSense).

**Pour déployer XSENSE QID** :

1. Connectez-vous à la console QRadar.

1. Créez un fichier appelé `xsense_qids`.

1. Dans le fichier, utilisez la commande suivante : `,XSense Alert,XSense Alert Report From <XSense Name>,5,7001`.

1. Execute: `sudo /opt/qradar/bin/qidmap_cli.sh -i -f <path>/xsense_qids`. Le message indiquant que le QID a été déployé avec succès s’affiche.

## <a name="setting-up-qradar-forwarding-rules"></a>Configuration des règles de transfert QRadar

Dans l’appliance Defender for IoT, configurez une règle de transfert Qradar. Mappez la règle dans la console de gestion locale.

**Pour définir des notifications QRadar dans l’appliance Defender for IoT** :

1. Sélectionnez **Transfert** dans le menu latéral.

   :::image type="content" source="media/integration-qradar/create.png" alt-text="Créer une règle de transfert.":::

1. Définissez l’Action sur **QRadar**.

1. Configurez l’adresse IP QRadar et le fuseau horaire.

1. Sélectionnez **Envoyer**.

**Pour mapper les notifications à QRadar dans le gestionnaire central** :

1. Sélectionnez **Transfert** dans le menu latéral.

1. Dans l’interface graphique utilisateur de Qradar, sous QRadar, sélectionnez **Activité de journal**.

1. Sélectionnez **Ajouter un filtre** et définissez les paramètres suivants :
   - Paramètre : `Log Sources [Indexed]`
   - Opérateur : `Equals`
   - Groupe de sources du journal : `Other`
   - Source du journal : `<Xsense Name>`

1. Double-cliquez sur un rapport inconnu à partir de XSense.

1. Sélectionnez **Événement de mappage**.

1. Dans la page événement de source de journal modal, effectuez les sélections suivantes :
   - Catégorie de niveau élevé - Activité suspecte + Catégorie de bas niveau - Événement suspect inconnu + Journal
   - Type de source - quelconque

1. Sélectionnez **Recherche**.

1. Dans les résultats, choisissez la ligne dans laquelle le nom XSense s’affiche, puis sélectionnez **OK**.

Tous les rapports XSense sont désormais marqués en tant qu’alertes XSense.

## <a name="adding-custom-fields-to-alerts"></a>Ajout de champs personnalisés aux alertes

**Pour ajouter des champs personnalisés aux alertes** :

1. Sélectionnez **Extraire la propriété**.

1. Sélectionnez **Basé sur Regex**.

1. Configurez les champs suivants :
   - Nouvelle propriété : _faites un choix dans liste ci-dessous_
      - Description de l’alerte Xsense
      - ID de l’alerte Xsense
      - Score de l’alerte Xsense
      - Titre de l’alerte Xsense
      - Nom de la destination Xsense
      - Redirection directe de Xsense
      - Adresse IP de l’expéditeur Xsense
      - Nom de l’expéditeur Xsense
      - Moteur d’alerte Xsense
      - Nom de l’appareil source Xsense
   - Cochez **Optimiser l’analyse**
   - Type de champ : `AlphaNumeric`
   - Cochez **Activé**
   - Type de source de journal : `Universal LEAF`
   - Source du journal : `<Xsense Name>`
   - Nom de l’événement (doit être déjà défini sur Alerte XSense)
   - Groupe de capture : 1
   - Regex :
      - RegEx description de l’alerte Xsense : `msg=(.*)(?=\t)`
      - RegEx ID de l’alerte Xsense :`alertId=(.*)(?=\t)`
      - RegEx score de l’alerte Xsense : `Detected score=(.*)(?=\t)`
      - RegEx titre de l’alerte Xsense : `title=(.*)(?=\t)`
      - RegEx nom de la destination Xsense :`dstName=(.*)(?=\t)`
      - RegEx redirection directe de Xsense : `rta=(.*)(?=\t)`
      - RegEx adresse IP de l’expéditeur Xsense : `reporter=(.*)(?=\t)`
      - RegEx nom de l’expéditeur Xsense : `senderName=(.*)(?=\t)`
      - RegEx moteur d’alerte Xsense : `engine =(.*)(?=\t)`
      - RegEx nom de l’appareil source Xsense : `src`

## <a name="defining-defender-for-iot-appliance-name"></a>Définition de Defender pour le nom de l’appliance IoT

Vous pouvez changer le nom de la plateforme à tout moment.

Lors de la création de sites et de l’affectation d’appliances à des zones dans la console de gestion locale, vous devez attribuer un nom significatif à chaque appliance. Par exemple, « Motos PL unité 2 » signifie que cette appliance protège l’unité #2 de la ligne de production motos. 

Il est important de choisir un nom explicite pour votre appliance, car le nom de l’appliance est transmis aux journaux. Lorsque vous examinez les journaux, chaque alerte est associée à un capteur. Vous pouvez identifier le capteur qui est lié à chaque alerte en fonction de son nom.

**Pour changer le nom de l’appliance** :

1. Dans le menu latéral, sélectionnez le nom de l’appliance actuelle. La boîte de dialogue **Modifier la configuration de la console de gestion** apparaît.

   :::image type="content" source="media/integration-qradar/edit-management-console.png" alt-text="Changez le nom de votre console.":::

1. Entrez un nom dans le champ Nom, puis sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [transférer des informations d’alerte](how-to-forward-alert-information-to-partners.md).
