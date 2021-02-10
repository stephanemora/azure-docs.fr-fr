---
title: Utiliser les alertes dans la console de gestion locale
description: Utilisez la console de gestion locale pour obtenir une vue au niveau de l’entreprise des menaces récentes sur votre réseau et mieux comprendre comment les utilisateurs de capteurs les gèrent.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: f52e308199ddb8bc21809009ad71918d077ac5b2
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835738"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>Utiliser les alertes dans la console de gestion locale 

Vous pouvez effectuer les opérations suivantes à partir de la fenêtre **Alertes** dans la console de gestion :

- Utiliser des filtres d’alerte

- Utiliser des compteurs d’alerte

- Afficher les informations sur l’alerte

- Gérer les événements d’alerte

- Créer des règles d’exclusion d’alerte

- Déclencher des règles d’exclusion d’alerte à partir de systèmes externes

- Accélérer le flux de travail des incidents avec les groupes d’alertes

## <a name="view-alerts-in-the-on-premises-management-console"></a>Afficher les alertes dans la console de gestion locale

La console de gestion locale agrège les alertes de tous les capteurs connectés. Cela fournit une vue au niveau de l’entreprise des menaces récentes sur votre réseau et vous aide à mieux comprendre comment les utilisateurs de capteurs les traitent.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="Capture d’écran de la fenêtre Alertes.":::

### <a name="work-with-alert-filters"></a>Utiliser des filtres d’alerte

La fenêtre **Alertes** affiche les alertes générées par les capteurs connectés à votre console de gestion locale. Vous pouvez afficher toutes les alertes pour les capteurs connectés ou présenter les alertes envoyées à partir d’un capteur spécifique :

- Site

- Zone

- Appareil

- Capteur

Sélectionnez **Effacer les filtres** pour afficher toutes les alertes.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="Désactivez vos filtres en sélectionnant le bouton Effacer les filtres.":::

### <a name="work-with-alert-counters"></a>Utiliser des compteurs d’alerte

Les compteurs d’alerte fournissent une répartition des alertes par gravité et état reconnu.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="Le compteur d’alertes affiche le nombre d’alertes que vous avez.":::

Les niveaux de gravité suivants apparaissent dans le compteur d’alertes :

- **Critique** : Indique une attaque malveillante qui doit être gérée immédiatement.

- **Majeure** : Indique une menace de sécurité qu’il est important de traiter.

- **Mineure** : Indique un écart par rapport au comportement de ligne de base qui peut contenir une menace de sécurité.

- **Avertissement** : Indique un écart par rapport au comportement de ligne de base sans menace de sécurité.

Les niveaux de gravité sont prédéfinis.

Vous pouvez ajuster le compteur pour fournir des nombres en fonction des alertes avec et sans accusé de réception. Les alertes sans accusé de réception ont été déclenchées par Defender pour les capteurs IoT, mais elles n’ont pas encore été révisées par les opérateurs au niveau du capteur.

Lorsque l’option **Afficher les alertes acquittées** est sélectionnée, toutes les alertes reconnues s’affichent dans la fenêtre **Alertes**.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="Affichez vos alertes acquittées.":::

### <a name="view-alert-information"></a>Afficher les informations sur l’alerte

L’alerte présente les informations suivantes :

- Résumé de l’événement d’alerte.

- Gravité de l'alerte.

- Un lien vers l’alerte dans le capteur qui l’a détectée.

- Un UUID d’alerte. L’UUID est constitué de l’ID d’alerte qui est associé à l’événement d’alerte détecté sur le capteur, séparé par un trait d’union et suivi d’un numéro d’ID système unique.

**UUID d’alerte de la console de gestion locale**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="Un appareil est connecté, mais n’est pas autorisé.":::

**ID d’alerte de capteur**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="ID d’alerte de capteur.":::

L’utilisation des UUID garantit que chaque alerte affichée dans la console de gestion locale peut faire l’objet d’une recherche et est identifiable par un numéro unique. Cela est nécessaire, car les alertes générées à partir de plusieurs capteurs peuvent produire le même ID d’alerte.

> [!NOTE]
> Par défaut, les UUID sont affichés dans les systèmes partenaires suivants lors de la définition des règles de transfert : ArcSight, serveurs syslog, QRadar, Sentinel et NetWitness. Aucune configuration n’est requise.

Pour afficher les informations sur l’alerte :

- Dans la liste d’alertes, sélectionnez une alerte.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="Capture d’écran des informations d’alerte.":::

Pour afficher l’alerte dans le capteur :

- Sélectionnez **OUVRIR LE CAPTEUR** de capteur à partir de l’alerte.

Pour afficher les appareils dans une carte de zone :

- Pour afficher la carte de l’appareil avec une focalisation sur l’appareil en état d’alerte et tous les appareils qui y sont connectés, sélectionnez **AFFICHER LES APPAREILS**.

## <a name="manage-alert-events"></a>Gérer les événements d’alerte

Vous pouvez gérer les événements d’alerte détectés par les capteurs organisationnels comme suit :

- Découvrir ou accuser réception des événements d’alerte. Sélectionnez **Découvrir et accuser réception** pour découvrir tous les événements d’alerte qui peuvent être autorisés et pour accuser réception de tous les événements d’alerte qui ne sont pas acquittés actuellement.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="Sélectionnez En savoir Découvrir et accuser réception pour tout découvrir.":::

- Coupez ou réactivez les événements d’alerte.

## <a name="create-alert-exclusion-rules"></a>Créer des règles d’exclusion d’alerte

Demandez à Defender pour IoT d’ignorer les déclencheurs d’alerte en fonction des éléments suivants :

- Fuseaux horaires et périodes

- Adresse de l’appareil (IP, MAC, sous-réseau)

- Noms des alertes

- Un capteur spécifique

Créez des règles d’exclusion d’alerte lorsque vous souhaitez que Defender pour IoT ignore l’activité qui déclenchera une alerte.

Par exemple, si vous savez que tous les appareils OT surveillés par un capteur spécifique vont subir des procédures de maintenance pendant deux jours, vous pouvez définir une règle d’exclusion qui indique à Defender pour IoT de supprimer les alertes détectées par ce capteur au cours de la période prédéfinie.

### <a name="alert-exclusion-logic"></a>Logique d’exclusion d’alerte

La logique de la règle d’alerte est basée sur l’opérateur `AND`. Cela signifie qu’une alerte est déclenchée uniquement lorsque toutes les conditions de règle sont remplies.

Si une condition de règle n’est pas définie, la condition inclut toutes les options. Par exemple, si vous n’incluez pas le nom d’un capteur dans la règle, elle sera appliquée à tous les capteurs.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="Capture d’écran de la vue Créer une règle d’exclusion.":::

Les résumés des règles s’affichent dans la fenêtre **Règle d’exclusion**.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="Capture d’écran de la vue Résumé de la règle d’exclusion.":::

Outre l’utilisation des règles d’exclusion, vous pouvez supprimer les alertes en les désactivant.

### <a name="create-exclusion-rules"></a>Créer des règles d’exclusion

Pour créer des règles d’exclusion :

1. Dans le volet gauche de la console de gestion locale, sélectionnez **Exclusion d’alerte**. Définissez une nouvelle règle d’exclusion en sélectionnant l’icône **Ajouter** :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: dans le coin supérieur droit de la fenêtre qui s’ouvre. La boîte de dialogue **Créer une règle d’exclusion** s’ouvre.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="Créez une exclusion d’alerte en renseignant les informations ici.":::

2. Entrez un nom de règle dans le champ **Nom**. Le nom ne peut pas contenir de guillemets (`"`).

3. Dans la section **Par fuseau horaire/période**, entrez une période dans un fuseau horaire spécifique. Utilisez cette fonctionnalité lorsqu’une règle d’exclusion est créée pour une période spécifique dans un fuseau horaire, mais qu’elle doit être implémentée en même temps dans d’autres fuseaux horaires. Par exemple, vous pouvez être amené à appliquer une règle d’exclusion comprise entre 8 h et 10 h dans trois fuseaux horaires différents. Dans ce cas, créez trois règles d’exclusion distinctes qui utilisent la même période et le fuseau horaire approprié.

4. Sélectionnez **AJOUTER**. Pendant la période d’exclusion, aucune alerte n’est créée sur les capteurs connectés.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="Capture d’écran de la vue par période de temps.":::

5. Dans la section **Par adresse d’appareil**, définissez les éléments suivants :

  - Adresse IP, adresse MAC ou adresse de sous-réseau de l’appareil que vous souhaitez exclure.

  - Direction du trafic pour les appareils exclus, source et destination.

6. Sélectionnez **AJOUTER**.

7. Dans la section **Par titre d’alerte**, commencez à saisir le titre de l’alerte. Dans la liste déroulante, sélectionnez le titre ou les titres d’alerte à exclure.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="Capture d’écran de l’affichage par titre d’alerte.":::

8. Sélectionnez **AJOUTER**.

9. Dans la section **Par nom de capteur**, commencez à saisir le nom du capteur. Dans la liste déroulante, sélectionnez le ou les capteurs que vous souhaitez exclure.

10. Sélectionnez **AJOUTER**.

11. Sélectionnez **SAVE** (Enregistrer). La nouvelle règle apparaît dans la liste des règles.

Pour supprimer des alertes, vous pouvez soit les désactiver, soit créer des règles d’exclusion d’alerte. Cette section décrit les cas d’utilisation potentiels pour ces deux fonctionnalités.

- **Règle d’exclusion**. Écrivez une règle d’exclusion dans les cas suivants :

  - Vous savez à l’avance que vous souhaitez exclure l’événement de la base de données. Par exemple, vous savez que le scénario détecté sur un certain capteur déclenchera des alertes non pertinentes. Par exemple, vous effectuerez des tâches de maintenance sur des PLC de l’organisation sur un site spécifique et souhaitez supprimer les alertes relatives aux PLC pour ce site.

  - Vous souhaitez que Defender pour IoT ignore les événements pour une plage de temps spécifique (pour les tâches de maintenance système).

  - Vous souhaitez ignorer les événements d’un sous-réseau spécifique.

  - Vous souhaitez contrôler les événements d’alerte générés à partir de plusieurs capteurs avec une seule règle.

  - Vous ne souhaitez pas suivre l’exclusion d’alerte en tant qu’événement dans le journal des événements.

- **Muet**. Désactivez une alerte lorsque :

  - Les éléments qui doivent être désactivés ne sont pas planifiés. Vous ne savez pas à l’avance quels événements ne seront pas pertinents.

  - Vous souhaitez supprimer l’alerte de la fenêtre **Alertes**, mais vous souhaitez tout de même la suivre dans le journal des événements.

  - Vous souhaitez ignorer les événements sur un canal spécifique.

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>Déclencher des règles d’exclusion d’alerte à partir de systèmes externes

Déclenchez des règles d’exclusion d’alerte à partir de systèmes externes. Par exemple, gérez des règles d’exclusion à partir de systèmes de gestion de tickets d’entreprise ou de systèmes qui gèrent les processus de maintenance réseau.

Définissez les capteurs, les moteurs, l’heure de début et l’heure de fin pour appliquer la règle. Pour plus d’informations, consultez [Capteur d’API Defender pour IoT et API de la console de gestion](references-work-with-defender-for-iot-apis.md).

Les règles que vous créez à l’aide de l’API s’affichent dans la fenêtre **Règle d’exclusion** sous la forme RO.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="Capture d’écran de la vue Modifier la règle d’exclusion.":::

## <a name="see-also"></a>Voir aussi

[Utiliser des alertes sur votre capteur](how-to-work-with-alerts-on-your-sensor.md)
