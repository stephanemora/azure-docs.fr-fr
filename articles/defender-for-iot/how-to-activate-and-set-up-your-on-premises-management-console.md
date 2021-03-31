---
title: Activer et configurer votre console de gestion locale
description: L’activation de la console de gestion garantit que les capteurs sont inscrits auprès d’Azure et envoient des informations à la console de gestion locale, et que la console de gestion locale effectue des tâches de gestion sur les capteurs connectés.
ms.date: 3/18/2021
ms.topic: how-to
ms.openlocfilehash: 89ce6da3521248ff7373e23ae8831106cbee74de
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784626"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>Activer et configurer votre console de gestion locale 

L’activation et la configuration de la console de gestion locale garantissent que :

- Les périphériques réseau que vous surveillez par le bais de capteurs connectés sont inscrits auprès d’un compte Azure.

- Les capteurs envoient des informations à la console de gestion locale.

- La console de gestion locale effectue des tâches de gestion sur les capteurs connectés.

- Vous avez installé un certificat SSL.

## <a name="sign-in-for-the-first-time"></a>Se connecter pour la première fois

Pour vous connecter à la console de gestion :

1. Accédez à l’adresse IP que vous avez reçue pour la console de gestion locale lors de l’installation du système.
 
1. Entrez le nom d’utilisateur et le mot de passe que vous avez reçus pour la console de gestion locale lors de l’installation du système. 


Si vous avez oublié votre mot de passe, sélectionnez l’option **Récupérer le mot de passe**, et consultez [Récupération du mot de passe](how-to-manage-the-on-premises-management-console.md#password-recovery) pour obtenir des instructions sur la récupération de votre mot de passe.

## <a name="get-and-upload-an-activation-file"></a>Obtenir et charger un fichier d’activation

Une fois que vous vous êtes connecté pour la première fois, vous devez activer la console de gestion locale en obtenant et en chargeant un fichier d’activation. 

Pour obtenir un fichier d’activation :

1. Accédez à la page **Tarification** du portail Azure Defender pour IoT. 
1. Sélectionnez l’abonnement auquel associer la console de gestion locale.
1. Sélectionnez l’onglet **Télécharger le fichier d’activation de la console de gestion**. Le fichier d’activation est téléchargé.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Télécharger le fichier d’activation.":::

Pour charger un fichier d’activation :

1. Accédez à la page **Paramètres système** de la console de gestion locale.
1. Sélectionner l’icône **Activation** :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/activation-icon.png" border="false":::.
1. Sélectionnez **Choisir un fichier**, puis sélectionnez le fichier que vous avez téléchargé.

Après l’activation initiale, le nombre d’appareils surveillés pourrait dépasser le nombre d’appareils validés définis lors de l’intégration. Cela se produit si vous connectez d’autres capteurs à la console de gestion. S’il y a un écart entre le nombre d’appareils surveillés et le nombre d’appareils validés, un avertissement s’affiche sur la console de gestion. Dans ce cas, chargez un nouveau fichier d’activation.

## <a name="set-up-a-certificate"></a>Configuration d'un certificat

Après l’installation de la console de gestion, un certificat auto-signé local est généré et utilisé pour accéder à la console. Quand un administrateur se connecte à la console de gestion pour la première fois, il est invité à intégrer un certificat SSL/TLS. 

Deux niveaux de sécurité sont disponibles :

- répondre aux exigences de chiffrement et de certificat spécifiques requises par votre organisation en téléchargeant le certificat signé par une autorité de certification ;
- Autorisez la validation entre la console de gestion et les capteurs connectés. La validation est évaluée par rapport à une liste de révocation de certificats et à la date d’expiration du certificat. *En cas d’échec de la validation, la communication entre la console de gestion et le capteur est interrompue et une erreur de validation apparaît sur la console.* Cette option est activée par défaut après l’installation.  

La console prend en charge les types de certificats suivants :

- Infrastructure à clé privée et d’entreprise (PKI privée)

- Infrastructure à clé publique (PKI publique)

- Généré localement sur l’appliance (auto-signé localement) 

  > [!IMPORTANT]
  > Nous vous recommandons de ne pas utiliser de certificat auto-signé. Le certificat n’est pas sécurisé et doit être utilisé uniquement pour les environnements de test. Le propriétaire du certificat ne peut pas être validé et la sécurité de votre système ne peut pas être assurée. N’utilisez jamais cette option pour les réseaux de production.

Pour charger un certificat :

1. Quand vous y êtes invité après vous être connecté, définissez un nom de certificat.
1. Chargez les fichiers de clé et CRT.
1. Entrez une phrase secrète et chargez un fichier PEM le cas échéant.

Vous devrez peut-être actualiser votre écran après avoir chargé le certificat signé par une autorité de certification.

Pour désactiver la validation entre la console de gestion et les capteurs connectés :

1. Sélectionnez **Suivant**.
1. Désactivez le bouton bascule **Activer la validation à l’échelle du système**.

Pour plus d’informations sur le chargement d’un nouveau certificat, les fichiers de certificat pris en charge et les éléments connexes, consultez [Gérer la console de gestion locale](how-to-manage-the-on-premises-management-console.md).

## <a name="connect-sensors-to-the-on-premises-management-console"></a>Connecter des capteurs à la console de gestion locale

Vous devez vous assurer que les capteurs envoient des informations à la console de gestion locale et que la console de gestion locale peut effectuer des sauvegardes, gérer des alertes et effectuer d’autres activités sur les capteurs. Pour ce faire, utilisez les procédures suivantes pour vérifier que vous établissez une connexion initiale entre les capteurs et la console de gestion locale.

Deux options sont disponibles pour connecter des capteurs Azure Defender pour IoT à la console de gestion locale :

- Se connecter à partir de la console du capteur

- Se connecter à l’aide du tunneling

Après vous être connecté, vous devez configurer un site avec ces capteurs.

### <a name="connect-sensors-from-the-sensor-console"></a>Connecter des capteurs à partir de la console du capteur

Pour connecter des capteurs spécifiques à la console de gestion locale à partir de la console du capteur :

1. Dans le volet gauche de la console du capteur, sélectionnez **Paramètres système**.

2. Sélectionnez **Connexion au gestionnaire**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-not-connected.png" alt-text="Capture d’écran de la fenêtre d’état d’une console de gestion locale, indiquant Non connecté.":::

3. Dans la zone de texte **Adresse**, entrez l’adresse IP de la console de gestion locale à laquelle vous souhaitez vous connecter.

4. Sélectionnez **Se connecter**. L’état change :

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-connected.png" alt-text="Capture d’écran de la fenêtre d’état d’une console de gestion locale, indiquant Connecté.":::

### <a name="connect-sensors-by-using-tunneling"></a>Connecter des capteurs à l’aide du tunneling

Activez une connexion sécurisée par tunneling entre les capteurs de l’organisation et la console de gestion locale. Cette configuration permet de contourner l’interaction avec le pare-feu de l’organisation et, par conséquent, de réduire la surface d’attaque.

L’utilisation du tunneling vous permet de connecter la console de gestion locale à partir de son adresse IP et d’un seul port (c’est-à-dire, 9000) à n’importe quel capteur.

Pour configurer le tunneling à partir de la console de gestion locale :

- Connectez-vous à la console de gestion locale et exécutez les commandes suivantes :

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

Pour configurer le tunneling sur le capteur :

1. Ouvrez manuellement le port TCP 9000 sur le capteur (network.properties). Si le port n’est pas ouvert, le capteur rejette la connexion depuis la console de gestion locale.

2. Connectez-vous à chaque capteur et exécutez les commandes suivantes :

   ```bash
   sudo cyberx-xsense-management-connect -ip <centralmanagerIPAddress>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,102,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>Configurer un site

La carte d’entreprise par défaut fournit une vue globale de vos appareils selon plusieurs niveaux d’emplacements géographiques.

La visualisation de vos appareils peut être nécessaire lorsque la structure organisationnelle et les autorisations utilisateur sont complexes. Dans ces cas, la configuration du site peut être déterminée par une structure organisationnelle globale, en plus de la structure standard du site ou de la zone.

Pour prendre en charge cet environnement, vous devez créer une topologie d’entreprise globale basée sur les unités commerciales, les régions, les sites et les zones de votre organisation. Vous devez également définir des autorisations d’accès utilisateur autour de ces entités à l’aide de groupes d’accès.

Les groupes d’accès permettent de mieux contrôler l’emplacement où les utilisateurs gèrent et analysent des appareils dans la plateforme Defender pour IoT.

### <a name="how-it-works"></a>Fonctionnement

Pour chaque site, vous pouvez définir une unité commerciale et une région. Vous pouvez ensuite ajouter des zones, qui sont des entités logiques dans votre réseau. 

Pour chaque zone, vous devez attribuer au moins un capteur. Le modèle à cinq niveaux offre la flexibilité et la granularité requises pour fournir un système de protection qui reflète la structure de votre organisation.

Vous pouvez modifier vos sites directement à partir de n’importe quelle vue de la carte. Lorsque vous ouvrez un site à partir d’une vue de la carte, le nombre d’alertes ouvertes s’affiche à côté de chaque zone.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="Capture d’écran d’une carte de la console de gestion locale avec la superposition des données de Berlin.":::

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="Diagramme montrant les capteurs et la relation régionale.":::

Pour configurer un site :

1. Ajoutez de nouvelles unités commerciales pour refléter la structure logique de votre organisation.

2. Ajoutez de nouvelles régions pour refléter les régions de votre organisation.

3. Ajoutez un site.

4. Ajoutez des zones à un site.

5. Connectez les capteurs.

6. Attribuez le capteur à des zones de site.

Pour ajouter des unités commerciales :

1. Dans la vue Entreprise, sélectionnez **Tous les sites** > **Gérer les unités commerciales**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-business-unit-screen.png" alt-text="Capture d’écran montrant la vue Gérer les unités commerciales.":::

2. Entrez le nom de la nouvelle unité commerciale et sélectionnez **AJOUTER**.

Pour ajouter une nouvelle région :

1. Dans la vue Entreprise, sélectionnez **Toutes les régions** > **Gérer les régions**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-regions-screen.png" alt-text="Capture d’écran montrant la vue Gérer les régions.":::

2. Entrez le nom de la nouvelle région et sélectionnez **AJOUTER**.

Pour ajouter un nouveau site :

1. Dans la vue Entreprise, sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: dans la barre supérieure. Votre curseur apparaît sous la forme d’un signe plus ( **+** ).

2. Placez le **+** à l’emplacement du nouveau site et sélectionnez-le. La boîte de dialogue **Créer un site** s’ouvre.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="Capture d’écran de la vue Créer un site.":::

3. Définissez le nom et l’adresse physique du nouveau site, puis sélectionnez **ENREGISTRER**. Le nouveau site s’affiche sur la carte des sites.

Pour supprimer un site :

1. Dans la fenêtre **Gestion des sites**, sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: dans la barre qui contient le nom du site, puis sélectionnez **Supprimer le site**. La boîte de confirmation s’affiche et vous permet de confirmer que vous souhaitez supprimer le site.

2. Dans la zone de confirmation, sélectionnez **OUI**. La boîte de confirmation se ferme, et la fenêtre **Gestion des sites** s’affiche sans le site que vous venez de supprimer.

## <a name="create-enterprise-zones"></a>Créer des zones d’entreprise

Les zones sont des entités logiques qui vous permettent de diviser les appareils d’un site en groupes selon différentes caractéristiques. Par exemple, vous pouvez créer des groupes pour les lignes de production, les sous-stations, les zones de site ou les types d’appareils. Vous pouvez définir des zones en fonction des caractéristiques qui conviennent à votre organisation.

Vous configurez des zones dans le cadre du processus de configuration du site.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="Capture d’écran de la vue Zones dans Gestion des sites.":::

Le tableau suivant décrit les paramètres de la fenêtre **Gestion des sites**.

| Paramètre | Description |
|--|--|
| Nom | Nom du capteur. Vous ne pouvez modifier ce nom qu’à partir du capteur. Pour plus d’informations, consultez le guide de l’utilisateur Defender pour IoT. |
| IP | Adresse IP du capteur. |
| Version | Version du capteur. |
| Connectivité | État de connectivité du capteur. L’état peut être **Connecté** ou **Déconnecté**. |
| Dernière mise à niveau | Date de la dernière mise à niveau. |
| Progression de la mise à niveau | La barre de progression indique l’état du processus de mise à niveau, comme suit :<br />- Chargement du package<br />- Préparation de l’installation<br />- Arrêt des processus<br />- Sauvegarde de données<br />- Prise d’un instantané<br />- Mise à jour de la configuration<br />- Mise à jour des dépendances<br />- Mise à jour des bibliothèques<br />- Mise à jour corrective des bases de données<br />- Démarrage des processus<br />- Validation de la validité du système<br />- Validation réussie<br />- Réussite<br />- Échec<br />- Mise à niveau démarrée<br />- Démarrage de l’installationogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >Pour plus d’informations sur la mise à niveau, adressez-vous à [Support Microsoft](https://support.microsoft.com/) pour obtenir de l’aide. |
| Appareils | Nombre de d’appareils OT que le capteur surveille. |
| Alertes | Nombre d’alertes sur le capteur. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | Permet d’attribuer un capteur à des zones. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| Permet de supprimer un capteur déconnecté du site. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | Indique le nombre de capteurs actuellement connectés à la zone. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | Indique le nombre de ressources OT actuellement connectées à la zone. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | Indique le nombre d’alertes envoyées par les capteurs qui sont attribués à la zone. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | Libère des capteurs des zones. |

Pour ajouter une zone à un site :

1. Dans la fenêtre **Gestion des sites**, sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: dans la barre qui contient le nom du site, puis sélectionnez **Ajouter une zone**. La boîte de dialogue **Créer une zone** s’affiche.

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="Capture d’écran de la vue Créer une zone.":::

2. Entrez le nom de la zone.

3. Entrez une description de la nouvelle zone qui indique clairement les caractéristiques que vous avez utilisées pour diviser le site en zones.

4. Sélectionnez **SAVE** (Enregistrer). La nouvelle zone s’affiche dans la fenêtre **Gestion des sites** sous le site auquel cette zone appartient.

Pour modifier une zone :

1. Dans la fenêtre **Gestion des sites**, sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: dans la barre qui contient le nom de la zone, puis sélectionnez **Modifier la zone**. La boîte de dialogue **Modifier la zone** s’affiche.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="Capture d’écran qui affiche la boîte de dialogue Modifier la zone.":::

2. Modifiez les paramètres de la zone et sélectionnez **ENREGISTRER**.

Pour supprimer une zone :

1. Dans la fenêtre **Gestion des sites**, sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: dans la barre qui contient le nom de la zone, puis sélectionnez **Supprimer la zone**.

2. Dans la zone de confirmation, sélectionnez **OUI**.

Pour filtrer selon l’état de la connectivité :

- Dans l’angle supérieur gauche, sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: à côté de **Connectivité**, puis sélectionnez l’une des options suivantes :

  - **Tout** : Présente tous les capteurs qui communiquent avec cette console de gestion locale.

  - **Connecté** : Présente uniquement les capteurs connectés.

  - **Déconnecté** : Présente uniquement les capteurs déconnectés.

Pour filtrer selon l’état de la mise à niveau :

- Dans l’angle supérieur gauche, sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: à côté d’**État de la mise à niveau**, puis sélectionnez l’une des options suivantes :

  - **Tout** : Présente tous les capteurs qui communiquent avec cette console de gestion locale.

  - **Valide** : Présente les capteurs dont l’état de mise à niveau est valide.

  - **En cours** : Présente les capteurs en cours de mise à niveau.

  - **Échec** : Présente les capteurs dont le processus de mise à niveau a échoué.

## <a name="assign-sensors-to-zones"></a>Attribuer des capteurs à des zones

Pour chaque zone, vous devez attribuer des capteurs qui analysent le trafic local et génèrent des alertes. Vous pouvez attribuer uniquement les capteurs qui sont connectés à la console de gestion locale.

Pour attribuer un capteur :

1. Sélectionnez **Gestion des sites**. Les capteurs non attribués s’affichent dans l’angle supérieur gauche de la boîte de dialogue.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="Capture d’écran de la vue Capteurs non attribués.":::

2. Vérifiez que l’état de **Connectivité** est Connecté. Si ce n’est pas le cas, consultez [Connecter des capteurs à la console de gestion locale](#connect-sensors-to-the-on-premises-management-console) pour plus d’informations sur la connexion. 

3. Sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: pour le capteur que vous souhaitez attribuer.

4. Dans la boîte de dialogue **Attribuer un capteur**, sélectionnez l’unité commerciale, la région, le site et la zone à attribuer.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="Capture d’écran de la vue Attribuer un capteur.":::

5. Sélectionnez **ATTRIBUER**.

Pour annuler l’attribution d’un capteur et le supprimer :

1. Déconnectez le capteur de la console de gestion locale. Pour plus d’informations, consultez [Connecter des capteurs à la console de gestion locale](#connect-sensors-to-the-on-premises-management-console).

2. Dans la fenêtre **Gestion des sites**, sélectionnez le capteur et sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false":::. Le capteur apparaît dans la liste des capteurs non attribués après quelques instants.

3. Pour supprimer le capteur non attribué du site, sélectionnez le capteur dans la liste des capteurs non attribués, puis sélectionnez :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::.

## <a name="see-also"></a>Voir aussi

[Résoudre les problèmes du capteur et de la console de gestion locale](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
