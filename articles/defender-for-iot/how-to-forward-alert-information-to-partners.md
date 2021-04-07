---
title: Transférer les informations d’alerte
description: Vous pouvez envoyer des informations d’alerte à des systèmes partenaires en utilisant des règles de transfert.
ms.date: 12/02/2020
ms.topic: how-to
ms.openlocfilehash: bc405f7d4837bf81d9cfcd859d562b7152cfc54b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778795"
---
# <a name="forward-alert-information"></a>Transférer les informations d’alerte

Vous pouvez envoyer des informations d’alerte à des partenaires qui s’intègrent à Azure Defender pour IoT, à des serveurs Syslog, à des adresses e-mail et bien plus encore. L’utilisation de règles de transfert vous permet de transmettre rapidement des informations d’alerte aux parties prenantes de la sécurité.  

Syslog et d’autres actions de transfert par défaut sont fournis avec votre système. D’autres actions de transfert peuvent être disponibles lorsque vous intégrez des fournisseurs partenaires, par exemple Microsoft Azure Sentinel, ServiceNow ou Splunk.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-information-screen.png" alt-text="Informations d’alerte.":::

Les administrateurs Defender pour IoT sont autorisés à utiliser des règles de transfert.

## <a name="about-forwarded-alert-information"></a>À propos des informations d’alerte transférées

Les alertes fournissent des informations sur un large éventail d’événements opérationnels et de sécurité. Par exemple :

  - Date et heure de l’alerte

  - Moteur ayant détecté l’événement

  - Titre de l’alerte et message descriptif

  - Gravité de l’alerte

  - Nom et adresse IP de la source et de la destination

  - Trafic suspect détecté

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Analyse des adresses détectée.":::

Les informations pertinentes sont envoyées aux systèmes partenaires lors de la création des règles de transfert.

## <a name="create-forwarding-rules"></a>Créer des règles de transfert

Pour créer une nouvelle règle de transfert :

1. Sélectionnez **Transfert** dans le menu latéral.

   ::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule-screen.png" alt-text="Icône Créer une règle de transfert.":::

2. Sélectionnez **Créer une règle de transfert**.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/create-a-forwardong-rule.png" alt-text="Créez une nouvelle règle de transfert.":::

3. Entrez le nom de la règle de transfert.

### <a name="forwarding-rule-criteria"></a>Critères des règles de transfert 

Définissez les critères de déclenchement d’une règle de transfert. L’utilisation de critères de règle de transfert permet d’identifier et de gérer le volume d’informations envoyé du capteur aux systèmes externes. Les options suivantes sont disponibles :

**Protocoles** : Déclenche uniquement la règle de transfert si le trafic détecté s’exécutait sur des protocoles spécifiques. Sélectionnez les protocoles requis dans la liste déroulante ou sélectionnez-les tous.

**Moteurs** : Sélectionnez les moteurs requis ou choisissez-les tous. Les alertes des moteurs sélectionnés seront envoyées.

**Niveaux de gravité** : Il s’agit de l’incident minimal à transférer, en termes de niveau de gravité. Par exemple, si vous sélectionnez **Mineur**, les alertes mineures et toute alerte supérieure à ce niveau de gravité seront transférées. Les niveaux sont prédéfinis.

### <a name="forwarding-rule-actions"></a>Actions des règles de transfert

Les actions de règle de transfert demandent au capteur de transférer les informations d’alerte aux fournisseurs ou serveurs partenaires. Vous pouvez créer plusieurs actions pour chaque règle de transfert.

En plus des actions de transfert fournies avec votre système, d’autres actions peuvent être disponibles lorsque vous intégrez des fournisseurs partenaires. 

#### <a name="email-address-action"></a>Action d’adresse e-mail

Envoyez un e-mail qui contient les informations d’alerte. Vous pouvez entrer une adresse e-mail par règle.

Pour définir l’e-mail pour la règle de transfert :

1. Entrez une seule adresse e-mail. Si plusieurs e-mails doivent être envoyés, créez une autre action.

2. Entrez le fuseau horaire de l’horodatage pour la détection d’alerte sur les SIEM.

3. Sélectionnez **Envoyer**.

#### <a name="syslog-server-actions"></a>Actions du serveur Syslog

Les formats suivants sont pris en charge :

- SMS

- Messages CEF

- Messages LEEF

- Messages d’objet

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-actions-rule.png" alt-text="Créer des actions de règle de transfert.":::

Entrez les paramètres suivants :

- Nom d’hôte et port Syslog.

- Protocole TCP et UDP.

- Fuseau horaire de l’horodatage pour la détection d’alerte sur les SIEM.

- Fichier de clé et fichier de certificat de chiffrement TLS pour les serveurs CEF (facultatif).
    
:::image type="content" source="media/how-to-work-with-alerts-sensor/configure-encryption.png" alt-text="Configurez votre chiffrement pour votre règle de transfert.":::

| Champs de sortie des SMS à Syslog | Description |
|--|--|
| Date et heure | Date et heure auxquelles le serveur Syslog a reçu les informations. |
| Priority | User.Alert |
| HostName | Adresse IP du capteur |
| Protocol | TCP ou UDP |
| Message | Capteur : Nom du capteur.<br /> Alerte : Titre de l’alerte.<br /> Tapez : Type de l’alerte. Peut être **Violation de protocole**, **Violation de stratégie**, **Programme malveillant**, **Anomalie** ou **Opérationnelle**.<br /> Gravité : Gravité de l’alerte. Peut être **Avertissement**, **Mineur**, **Majeur** ou **Critique**.<br /> Source : Nom de l’appareil source.<br /> IP source : Adresse IP de l’appareil source.<br /> Destination : Nom de l’appareil de destination.<br /> Adresse IP de destination : Adresse IP de l’appareil de destination.<br /> Message : Message de l’alerte.<br /> Groupe d’alertes : Groupe d’alertes associé à l’alerte. |


| Sortie de l’objet Syslog | Description |
|--|--|
| Date et heure |   Date et heure auxquelles le serveur Syslog a reçu les informations. |  
| Priority |    User.Alert | 
| HostName |    IP du capteur | 
| Message | Nom du capteur : Nom de l’appliance. <br /> Heure de l’alerte : Heure à laquelle l’alerte a été détectée : Peut varier de l’heure du serveur Syslog et dépend de la configuration du fuseau horaire de la règle de transfert. <br /> Titre de l’alerte : Titre de l’alerte. <br /> Message d’alerte : Message de l’alerte. <br /> Gravité d’alerte : Gravité de l’alerte : **Avertissement**, **Mineur**, **Majeur** ou **Critique**. <br /> Type d’alerte : **Violation de protocole**, **Violation de stratégie**, **Programme malveillant**, **Anomalie** ou **Opérationnelle**. <br /> Protocole : Protocole de l’alerte.  <br /> **Source_MAC** : Adresse IP, nom, fournisseur ou système d’exploitation de l’appareil source. <br /> Destination_MAC : Adresse IP, nom, fournisseur ou système d’exploitation de l’appareil de destination. Si des données sont manquantes, la valeur sera **s.o.** . <br /> alert_group : Groupe d’alertes associé à l’alerte. |


| Format de sortie CEF Syslog | Description |
|--|--|
| Date et heure | Date et heure auxquelles le serveur Syslog a reçu les informations. |
| Priority | User.Alert | 
| HostName | Adresse IP du capteur |
| Message | CEF:0 <br />Azure Defender pour IoT <br />Nom du capteur : Nom de l’appliance de détection. <br />Version du capteur <br />Titre de l’alerte : Titre de l’alerte. <br />msg : Message de l’alerte. <br />protocole : Protocole de l’alerte. <br />gravité :  **Avertissement**, **Mineur**, **Majeur** ou **Critique**. <br />type :  **Violation de protocole**, **Violation de stratégie**, **Programme malveillant**, **Anomalie** ou **Opérationnelle**. <br /> start : Heure à laquelle l’alerte a été détectée. <br />Peut varier de l’heure du serveur Syslog et dépend de la configuration du fuseau horaire de la règle de transfert. <br />src_ip : Adresse IP de l’appareil source.  <br />dst_ip : Adresse IP de l’appareil de destination.<br />cat : Groupe d’alertes associé à l’alerte.  |

| Format de sortie LEEF Syslog | Description |
|--|--|
| Date et heure |   Date et heure auxquelles le serveur Syslog a reçu les informations. |  
| Priority |    User.Alert | 
| HostName |    IP du capteur |
| Message | Nom du capteur : Nom de l’appliance Azure Defender pour IoT. <br />LEEF:1.0 <br />Azure Defender pour IoT <br />Capteur  <br />Version du capteur <br />Alerte Azure Defender pour IoT <br />titre : Titre de l’alerte. <br />msg : Message de l’alerte. <br />protocole : Protocole de l’alerte.<br />gravité :  **Avertissement**, **Mineur**, **Majeur** ou **Critique**. <br />type : Type de l’alerte : **Violation de protocole**, **Violation de stratégie**, **Programme malveillant**, **Anomalie** ou **Opérationnelle**. <br />start : Heure de l’alerte. Notez qu’elle peut être différente de l’heure du serveur Syslog. (Cela dépend de la configuration du fuseau horaire.) <br />src_ip : Adresse IP de l’appareil source.<br />dst_ip : Adresse IP de l’appareil de destination. <br />cat : Groupe d’alertes associé à l’alerte. |

Après avoir entré toutes les informations, sélectionnez **Envoyer**.

#### <a name="netwitness-action"></a>Action NetWitness

Envoyez des informations d’alerte à un serveur NetWitness.

Pour définir les paramètres de transfert de NetWitness :

1. Entrez les informations **Nom d’hôte** et **Port** de NetWitness.

2. Entrez le fuseau horaire de l’horodatage pour la détection d’alerte sur les SIEM.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/add-timezone.png" alt-text="Ajoutez un fuseau horaire à votre règle de transfert.":::

3. Sélectionnez **Envoyer**.

#### <a name="integrated-vendor-actions"></a>Actions des fournisseurs intégrés

Vous avez peut-être intégré un fournisseur de sécurité, de gestion de périphériques ou d’autres secteurs d’activité à votre système. Ces intégrations vous permettent d’effectuer les actions suivantes :

  - Envoyer des informations d’alerte.

  - Envoyer des informations de l’inventaire des appareils.

  - Communiquer avec les pare-feu côté fournisseur.

Les intégrations permettent de faire le pont entre des solutions de sécurité auparavant cloisonnées, d’améliorer la visibilité des appareils et d’accélérer la réponse à l’échelle du système afin d’atténuer plus rapidement les risques.

Utilisez la section Actions pour entrer les informations d’identification et d’autres informations nécessaires pour communiquer avec les fournisseurs intégrés.

Pour plus d’informations sur la configuration des règles de transfert pour les intégrations, reportez-vous aux articles pertinents sur l’intégration des partenaires.

### <a name="test-forwarding-rules"></a>Tester les règles de transfert

Testez la connexion entre le capteur et le serveur partenaire défini dans vos règles de transfert :

1. Sélectionnez la règle dans la boîte de dialogue **Règle de transfert**.

2. Cochez la case **Plus**.

3. Sélectionnez **Envoyer un message de test**.

4. Accédez au système de votre partenaire pour vérifier que les informations envoyées par le capteur ont été reçues.

### <a name="edit-and-delete-forwarding-rules"></a>Modifier et supprimer des règles de transfert 

Pour modifier une règle de transfert :

- Dans l’écran **Règle de transfert**, sélectionnez **Modifier** sous le menu déroulant **Plus**. Apportez les modifications souhaitées, puis sélectionnez **Envoyer**.

Pour supprimer une règle de transfert :

- Dans l’écran **Règle de transfert**, sélectionnez **Supprimer** sous le menu déroulant **Plus**. Dans la boîte de dialogue **Avertissement**, sélectionnez **OK**.

### <a name="forwarding-rules-and-alert-exclusion-rules"></a>Règles de transfert et règles d’exclusion d’alerte

L’administrateur peut avoir défini des règles d’exclusion d’alerte. Ces règles aident les administrateurs à obtenir un contrôle plus granulaire sur le déclenchement des alertes en demandant au capteur d’ignorer les événements d’alerte en fonction de différents paramètres. Ces paramètres peuvent inclure des adresses d’appareil, des noms d’alerte ou des capteurs spécifiques.

Cela signifie que les règles de transfert que vous définissez peuvent être ignorées en fonction des règles d’exclusion créées par votre administrateur. Les règles d’exclusion sont définies dans la console de gestion locale.

## <a name="see-also"></a>Voir aussi

[Accélérer les workflows d’alerte](how-to-accelerate-alert-incident-response.md)
