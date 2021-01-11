---
title: Accélérer les workflows d’alerte
description: Améliorez les workflows d’alerte et d’incident.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 14d7a0de1cd29b8c07f90c759a4d423d7186fdb9
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835513"
---
# <a name="accelerate-alert-workflows"></a>Accélérer les workflows d’alerte

Cet article explique comment accélérer les workflows d’alerte en utilisant les commentaires d’alerte, les groupes d’alertes et les règles d’alerte personnalisées dans Azure Defender pour IoT.  Ces outils vous aident à :

- Analyser et gérer le volume important d’événements d’alerte détectés sur votre réseau.

- Identifier et traiter une activité réseau spécifique.

## <a name="accelerate-incident-workflows-by-using-alert-comments"></a>Accélérer les workflows d’incident à l’aide de commentaires d’alerte

Utilisez des commentaires d’alerte pour améliorer la communication entre les individus et les équipes lors de l’investigation d’un événement d’alerte.

:::image type="content" source="media/how-to-work-with-alerts-sensor/suspicion-of malicious-activity-screen.png" alt-text="Capture d’écran montrant une activité malveillante.":::

Utilisez les commentaires d’alerte pour améliorer :

- **Étapes de workflow** : Fournir des étapes d’atténuation des alertes.

- **Suivi de workflow** : Informer que les étapes ont été effectuées.

- **Conseils de workflow** : Fournir des recommandations, des insights ou des avertissements concernant l’événement.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-comment-screen.png" alt-text="Capture d’écran montrant des commentaires d’alerte.":::

La liste des options disponibles s’affiche dans chaque alerte. Les utilisateurs peuvent sélectionner un ou plusieurs messages.

Pour ajouter des commentaires d’alerte :

1. Dans le menu latéral, sélectionnez **Paramètres système**.

2. Dans la fenêtre **Paramètre système**, sélectionnez **Commentaires d’alerte**.

3. Dans la zone **Ajouter des commentaires**, entrez le texte du commentaire. Utilisez jusqu’à 50 caractères. Les virgules ne sont pas autorisées.

4. Sélectionnez **Ajouter**.

## <a name="accelerate-incident-workflows-by-using-alert-groups"></a>Accélérer les workflows d’incident à l’aide de groupes d’alertes

Les groupes d’alertes permettent aux équipes SOC d’afficher et de filtrer les alertes dans leurs solutions SIEM, puis de gérer ces alertes en fonction des stratégies de sécurité d’entreprise et des priorités de l’entreprise. Par exemple, les alertes relatives aux nouvelles détections sont organisées dans un groupe de détection. Ce groupe comprend des alertes qui traitent de la détection de nouveaux appareils, de nouveaux VLAN, de nouveaux comptes d’utilisateur, de nouvelles adresses MAC et bien plus encore.

Les groupes d’alertes sont appliqués lorsque vous créez des règles de transfert pour les solutions de partenaires suivantes :

  - Serveurs syslog

  - QRadar

  - ArcSight

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule.png" alt-text="Capture d’écran de la création d’une règle de transfert.":::

Le groupe d’alertes approprié apparaît dans les solutions de sortie des partenaires. 

### <a name="requirements"></a>Spécifications

Le groupe d’alertes s’affiche dans les solutions de partenaires prises en charge avec les préfixes suivants :

  - **cat** pour QRadar, ArcSight, Syslog CEF, Syslog LEEF

  - **Groupe d’alertes** pour les SMS Syslog

  - **alert_group** pour les objets Syslog

Ces champs doivent être configurés dans la solution du partenaire pour afficher le nom du groupe d’alertes. Si aucune alerte n’est associée à un groupe d’alertes, le champ de la solution du partenaire affichera **s.o.** .

### <a name="default-alert-groups"></a>Groupes d’alertes par défaut

Les groupes d’alertes suivants sont définis automatiquement :
|  |  |  |
|--|--|--|
| Comportement de communication anormal | Alertes personnalisées | Accès à distance |
| Comportement de communication HTTP anormal | Découverte | Commandes de redémarrage et d’arrêt |
| Authentification | Modification du microprogramme | Analyser |
| Comportement de communication non autorisé | Commandes non conformes | Trafic de capteur |
| Anomalies de la bande passante | Accès à Internet | Suspicion de programme malveillant |
| Dépassement de capacité de la mémoire tampon | Échecs d’opération | Suspicion d’activité malveillante |
| Échecs de commande | Problèmes de fonctionnement |  |
| Modifications de configuration | Programmation |  |

Les groupes d’alertes sont prédéfinis. Pour plus d’informations sur les alertes associées aux groupes d’alertes et sur la création de groupes d’alertes personnalisés, contactez [Support Microsoft](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="customize-alert-rules"></a>Personnaliser des règles d’alerte

Vous pouvez ajouter des règles d’alerte personnalisées en fonction des informations détectées par les capteurs individuels. Par exemple, définissez une règle qui demande à un capteur de déclencher une alerte basée sur une adresse IP source, une adresse IP de destination ou une commande (dans un protocole). Quand le capteur détecte le trafic défini dans la règle, une alerte ou un événement est généré.

Le message d’alerte indique qu’une règle définie par l’utilisateur a déclenché l’alerte.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Capture d’écran montrant des alertes personnalisées.":::

Pour créer une règle d’alerte personnalisée :

1. Sélectionnez **Alertes personnalisées** dans le menu latéral d’un capteur.
1. Sélectionnez le signe plus ( **+** ) pour créer une règle.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="Capture d’écran montrant une règle définie par l’utilisateur.":::

1. Définissez un nom de règle.
1. Sélectionnez une catégorie ou un protocole dans le volet **Catégories**.
1. Définissez une adresse (IP ou MAC) source et de destination spécifique, ou choisissez n’importe quelle adresse.
1. Ajoutez une condition. La liste des conditions et de leurs propriétés est unique pour chaque catégorie. Vous pouvez sélectionner plusieurs conditions pour chaque alerte.
1. Indiquez si la règle déclenche une **alarme** ou un **événement**.
1. Attribuez un niveau de gravité à l’alerte.
1. Indiquez si l’alerte doit inclure un fichier PCAP.
1. Sélectionnez **Enregistrer**.

La règle est ajoutée à la liste **Règles d’alertes personnalisées**, dans laquelle vous pouvez examiner les paramètres de règle de base, la dernière fois que la règle a été déclenchée, et bien plus encore. Vous pouvez également activer et désactiver la règle dans la liste.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Capture d’écran d’une règle personnalisée ajoutée par l’utilisateur.":::

### <a name="see-also"></a>Voir aussi

[Afficher les informations fournies dans les alertes](how-to-view-information-provided-in-alerts.md)

[Gérer l’événement d’alerte](how-to-manage-the-alert-event.md)
