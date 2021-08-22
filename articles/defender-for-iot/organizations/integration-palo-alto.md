---
title: Intégration de Palo Alto
description: Defender pour IoT a intégré sa plateforme de surveillance continue des menaces ICS aux pare-feu de nouvelle génération de Palo Alto pour permettre le blocage des menaces critiques plus rapidement et plus efficacement.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 49c00f9ad7cc1980a8690e35ed19351e20417c6a
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015213"
---
# <a name="about-the-palo-alto-integration"></a>À propos de l’intégration de Palo Alto

Defender pour IoT a intégré sa plateforme de surveillance continue des menaces ICS aux pare-feu de nouvelle génération de Palo Alto pour permettre le blocage des menaces critiques plus rapidement et plus efficacement.

Les types d’intégration suivants sont disponibles :

- Option de blocage automatique : intégration directe de Defender pour IoT avec Palo Alto

- Envoi de recommandations de blocage au système de gestion centralisée : intégration de Defender pour IoT avec Panorama

## <a name="configure-immediate-blocking-by-specified-palo-alto-firewall"></a>Configurer un blocage immédiat par un pare-feu Palo Alto spécifique

Dans les cas critiques, tels que les alertes relatives aux programmes malveillants, vous pouvez activer le blocage automatique. Pour ce faire, configurez une règle de transfert dans Defender pour IoT qui envoie directement une commande de blocage à un pare-feu Palo Alto spécifique.

Lorsque Defender pour IoT identifie une menace critique, la solution envoie une alerte qui comprend une option de blocage de la source infectée. La sélection de l’option **Bloquer la source** dans les détails de l’alerte active la règle de transfert, qui envoie la commande de blocage au pare-feu Palo Alto spécifié.

Pour configurer :

1. Dans le volet gauche, sélectionnez **Transfert**.

   :::image type="content" source="media/integration-paloalto/forwarding.png" alt-text="Écran de l’alerte de transfert.":::

1. Sélectionnez **Créer une règle de transfert**.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Créer une règle de transfert":::

1. Pour configurer la règle de transfert du pare-feu de nouvelle génération (NGFW) de Palo Alto :  
 
   - Définissez les paramètres de la règle standard et, dans la liste déroulante **Actions**, sélectionnez **Envoyer au NGFW de Palo Alto**.
   
   :::image type="content" source="media/integration-paloalto/edit.png" alt-text="Modifier votre règle de transfert.":::

1. Dans le volet Actions, définissez les paramètres suivants :

   - **Hôte** : Entrez l’adresse IP du serveur du NGFW.
   - **Port** : Entrez le port du serveur du NGFW.
   - **Nom d’utilisateur** : Entrez le nom d’utilisateur du serveur du NGFW.
   - **Mot de passe** : Entrez le mot de passe du serveur du NGFW.
   - **Configurer** : Configurez les options suivantes pour autoriser le blocage des sources suspectes par le pare-feu de Palo Alto :
     - **Bloquer les codes de fonction non conformes** : Violations de protocole, notamment une valeur de champ non conforme qui ne respecte pas la spécification du protocole ICS (possible code malveillant exploitant une faille de sécurité).
     - **Bloquer les mises à jour non autorisées des microprogrammes/programmes du PLC** : Modifications non autorisées du PLC.
     - **Bloquer l’arrêt non autorisé du PLC** : Arrêt du PLC (temps d’arrêt).
     - **Bloquer les alertes relatives aux programmes malveillants** : Blocage des tentatives de programmes malveillants industriels (TRITON, NotPetya, etc.). Vous pouvez sélectionner l’option de **blocage automatique**. Dans ce cas, le blocage est exécuté automatiquement et immédiatement.
     - **Bloquer l’analyse non autorisée** : Analyse non autorisée (reconnaissance potentielle).
     
1. Sélectionnez **Envoyer**.

Pour bloquer la source suspecte : 

1. Dans le volet **Alertes**, sélectionnez l’alerte relative à l’intégration à Palo Alto. La boîte de dialogue **Détails de l’alerte** s’affiche.
   
   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Détails de l'alerte":::

1. Pour bloquer automatiquement la source suspecte, sélectionnez **Bloquer la source**. La boîte de dialogue **Veuillez confirmer**, s’affiche.

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Confirmez le blocage sur l’écran Veuillez confirmer.":::

1. Dans la boîte de dialogue **Veuillez confirmer**, sélectionnez **OK**. La source suspecte est bloquée par le pare-feu de Palo Alto.

## <a name="sending-blocking-policies-to-palo-alto-panorama"></a>Envoi de stratégies de blocage à Palo Alto Panorama

Defender pour IoT et Palo Alto Networks disposent d’une intégration standard qui crée automatiquement de nouvelles stratégies dans Panorama, le système de gestion des réseaux de Palo Alto Networks. Cette intégration nécessite une confirmation de l’administrateur Panorama et n’autorise pas le blocage automatique.

L’intégration est destinée aux incidents suivants :

- **Modifications non autorisées du PLC :** Mise à jour de la logique d’échelle ou du microprogramme d’un appareil. Cela peut représenter une activité légitime ou être une tentative visant à compromettre l’appareil. La compromission peut se produire par l’insertion d’un code malveillant, tel qu’un cheval de Troie d’accès à distance (RAT) ou de paramètres qui obligent le processus physique, tel qu’une turbine tournante, à fonctionner de manière non sécurisée.

- **Violation de protocole :** Structure de paquet ou valeur de champ qui ne respecte pas la spécification du protocole. Il peut s’agir d’une application mal configurée ou d’une tentative malveillante visant à compromettre l’appareil. Par exemple, en provoquant une condition de dépassement de la mémoire tampon dans l’appareil cible.

- **Arrêt du PLC :** Commande qui entraîne l’arrêt de l’appareil, ce qui risque de compromettre le processus physique contrôlé par le PLC.

- **Programme malveillant industriel détecté dans le réseau ICS :** Programme malveillant qui manipule les appareils ICS en utilisant leurs protocoles natifs, tels que TRITON et Industroyer. Defender pour IoT détecte également les programmes malveillants qui se sont déplacés latéralement dans l’environnement ICS et SCADA, tels que Conficker, WannaCry et NotPetya.

- **Programmes malveillants d’analyse :** Outils de reconnaissance qui collectent des données sur la configuration du système lors d’une phase de pré-attaque. Par exemple, le cheval de Troie Havex analyse les réseaux industriels à la recherche d’appareils utilisant OPC, un protocole standard utilisé par les systèmes SCADA sous Windows pour communiquer avec les appareils ICS.

## <a name="the-process"></a>Processus

Quand Defender pour IoT détecte un cas d’usage préconfiguré, le bouton **Bloquer la source** est ajouté à l’alerte. Ensuite, lorsque l’utilisateur **CyberX** sélectionne le bouton **Bloquer la source**, Defender pour IoT crée des stratégies sur Panorama en envoyant la règle de transfert prédéfinie.

La stratégie est appliquée uniquement lorsque l’administrateur Panorama l’envoie (push) au NGFW approprié dans le réseau.

Dans les réseaux informatiques, il peut y avoir des adresses IP dynamiques. Par conséquent, pour ces sous-réseaux, la stratégie doit être basée sur le nom de domaine complet (nom DNS), et non sur l’adresse IP. Defender pour IoT effectue une recherche inversée et fait correspondre des appareils ayant une adresse IP dynamique à leur nom de domaine complet (nom DNS) chaque nombre d’heures configuré.

En outre, Defender pour IoT envoie un e-mail à l’utilisateur Panorama approprié pour l’informer qu’une nouvelle stratégie créée par Defender pour IoT est en attente d’approbation. La figure ci-dessous présente l’architecture d’intégration de Defender pour IoT avec Panorama.

:::image type="content" source="media/integration-paloalto/structure.png" alt-text="Architecture d’intégration Panorama avec CyberX":::

## <a name="create-panorama-blocking-policies-in-defender-for-iot-configuration"></a>Créer des stratégies de blocage Panorama dans la configuration de Defender pour IoT

### <a name="to-configure-dns-lookup"></a>Pour configurer la recherche DNS

1. Dans le volet gauche, sélectionnez **Paramètres système**.

1. Dans le volet **Paramètres système**, sélectionnez **Paramètres DNS** :::image type="icon" source="media/integration-paloalto/settings.png":::.

   :::image type="content" source="media/integration-paloalto/configuration.png" alt-text="Configurer les paramètres DNS.":::

1. Dans la boîte de dialogue **Modifier les paramètres DNS**, définissez les paramètres suivants :

   - **État** : État du programme de résolution DNS.

   - **Adresse du serveur DNS** : Entrez l’adresse IP ou le nom de domaine complet du serveur DNS du réseau.
   - **Port du serveur DNS** : Entrez le port utilisé pour interroger le serveur DNS.
   - **Sous-réseaux** : Définissez la plage de sous-réseau d’adresses IP dynamiques. Plage dans laquelle Defender pour IoT inverse la recherche de l’adresse IP sur le serveur DNS pour qu’elle corresponde à son nom de domaine complet actuel.
   - **Planifier la recherche inversée** : Définissez les options de planification comme suit :
     - À des heures spécifiques : Spécifiez le moment où la recherche inversée doit être effectuée quotidiennement.
     - À intervalles fixes (en heures) : Définissez la fréquence d’exécution de la recherche inversée.
   - **Nombre d’étiquettes** : Demandez à Defender pour IoT de résoudre automatiquement les adresses IP réseau en noms de domaine complets d’appareil. <br />Pour configurer la résolution du nom de domaine complet DNS, ajoutez le nombre d’étiquettes de domaine à afficher. Jusqu’à 30 caractères s’affichent de gauche à droite.
1. Sélectionnez **SAVE** (Enregistrer).
1. Pour vous assurer que vos paramètres DNS sont corrects, sélectionnez **Test de recherche**. Le test garantit que l’adresse IP et le port du serveur DNS sont correctement définis.

### <a name="to-configure-a-forwarding-rule-to-blocks-suspected-traffic-with-the-palo-alto-firewall"></a>Pour configurer une règle de transfert afin de bloquer le trafic suspect avec le pare-feu de Palo Alto

1. Dans le volet gauche, sélectionnez **Transfert**. Le volet Transfert s’affiche.

   :::image type="content" source="media/integration-paloalto/forward.png" alt-text="L’écran de transfert.":::

1. Dans le volet **Transfert**, sélectionnez **Créer une règle de transfert**.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Créer une règle de transfert":::

1. Pour configurer la règle de transfert de Palo Alto Panorama :

   Définissez les paramètres de la règle standard et, dans la liste déroulante **Actions**, sélectionnez **Envoyer à Palo Alto Panorama**. Le volet Détails de l’action s’affiche.

   :::image type="content" source="media/integration-paloalto/details.png" alt-text="Action select":::

1. Dans le volet Actions, définissez les paramètres suivants :

   - **Hôte** : Entrez l’adresse IP du serveur Panorama.

   - **Port** : Entrez le port du serveur Panorama.
   - **Nom d’utilisateur** : Entrez le nom d’utilisateur du serveur Panorama.
   - **Mot de passe** : Entrez le mot de passe du serveur Panorama.
   - **Adresse du rapport** : Définissez la manière dont le blocage est exécuté, comme suit :
   
     - **Par adresse IP** : Crée toujours des stratégies de blocage sur Panorama en fonction de l’adresse IP.
     
     - **Par nom de domaine complet ou adresse IP** : Crée des stratégies de blocage sur Panorama en fonction du nom de domaine complet, s’il en existe un ; sinon, en fonction de l’adresse IP.
     
   - **E-mail** : Définissez l’adresse e-mail de notification de stratégie.
     > [!NOTE]
     > Vérifiez que vous avez configuré un serveur de messagerie dans Defender pour IoT. Si aucune adresse e-mail n’est entrée, Defender pour IoT n’envoie pas d’e-mail de notification.
   - **Exécuter une recherche DNS lors de la détection d’une alerte (case à cocher)**  : Lorsque l’option « Par nom de domaine complet ou adresse IP » est définie dans l’adresse du rapport. Cette case à cocher est activée par défaut. Si seule l’adresse IP est définie, cette option est désactivée.
   - **Configurer** : Configurez les options suivantes pour autoriser le blocage des sources suspectes par Palo Alto Panorama :
   
     - **Bloquer les codes de fonction non conformes** : Violations de protocole, notamment une valeur de champ non conforme qui ne respecte pas la spécification du protocole ICS (possible code malveillant exploitant une faille de sécurité).
     
     - **Bloquer les mises à jour non autorisées des microprogrammes/programmes du PLC** : Modifications non autorisées du PLC.
     
     - **Bloquer l’arrêt non autorisé du PLC** : Arrêt du PLC (temps d’arrêt).
     
     - **Bloquer les alertes relatives aux programmes malveillants** : Blocage des tentatives de programmes malveillants industriels (TRITON, NotPetya, etc.). Vous pouvez sélectionner l’option de **blocage automatique**. Dans ce cas, le blocage est exécuté automatiquement et immédiatement.
     
     - **Bloquer l’analyse non autorisée** : Analyse non autorisée (reconnaissance potentielle).
     
1. Sélectionnez **Envoyer**.

### <a name="to-block-the-suspicious-source"></a>Pour bloquer la source suspecte

1. Dans le volet **Alertes**, sélectionnez l’alerte relative à l’intégration à Palo Alto. La boîte de dialogue **Détails de l’alerte** s’affiche.

   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Détails de l'alerte":::        

1. Pour bloquer automatiquement la source suspecte, sélectionnez **Bloquer la source**.

1. Dans la boîte de dialogue **Veuillez confirmer**, sélectionnez **OK**.

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Confirmer":::

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [transférer des informations d’alerte](how-to-forward-alert-information-to-partners.md).
