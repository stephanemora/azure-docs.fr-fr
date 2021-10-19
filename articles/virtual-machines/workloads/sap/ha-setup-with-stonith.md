---
title: Configuration de la haute disponibilité avec STONITH pour SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Apprenez à établir une haute disponibilité pour SAP HANA sur Azure (grandes instances) dans SUSE en utilisant l’appareil STONITH.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 9/01/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5866fbb227477b0079f5f2ac314357ca8e67a364
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710833"
---
# <a name="high-availability-setup-in-suse-using-the-stonith-device"></a>Configuration de la haute disponibilité dans SUSE avec l’appareil STONITH

Cet article décrit les étapes de configuration de la haute disponibilité dans de grandes instances HANA sur le système d’exploitation SUSE avec l’appareil STONITH.

> [!NOTE]
> Ce guide provient de tests réussis de la configuration dans l’environnement des grandes instances Microsoft HANA. L’équipe de management des services Microsoft pour les grandes instances HANA ne prend pas en charge le système d’exploitation. Pour résoudre les problèmes ou clarifier la couche du système d’exploitation, contactez SUSE. 
>
> L’équipe de management des services Microsoft configure et prend entièrement en charge l’appareil STONITH. Elle peut vous aider à résoudre les problèmes liés aux appareils STONITH.

## <a name="prerequisites"></a>Prérequis

Pour configurer la haute disponibilité à l’aide du clustering SUSE, vous devez :

- Provisionner les grandes instances HANA.
- Installer et enregistrer le système d’exploitation avec les derniers correctifs.
- Connecter les serveurs de grande instance HANA au serveur SMT pour recevoir des correctifs et des packages.
- Configurer le protocole NTP (Network Time Protocol).
- Lire et comprendre la dernière version de la documentation SUSE sur la configuration de la haute disponibilité.

## <a name="setup-details"></a>Détails de la configuration

Ce guide utilise la configuration suivante :

- Système d’exploitation : SLES 12 SP1 pour SAP
- HANA - Grandes instances : 2xS192 (quatre sockets, 2 To)
- Version HANA : HANA 2.0 SP1
- Noms de serveurs : sapprdhdb95 (node1) et sapprdhdb96 (node2)
- Appareil STONITH : basé sur iSCSI
- Protocole NTP configuré sur l’un des nœuds de grande instance HANA

Quand vous configurez des grandes instances HANA avec la réplication système HANA, vous pouvez demander à l’équipe de management des services Microsoft de configurer l’appareil STONITH. Faites cela au moment du provisionnement. 

Si vous êtes déjà client avec de grandes instances HANA provisionnées, vous pouvez toujours faire configurer l’appareil STONITH. Fournissez les informations suivantes à l’équipe de gestion des services Microsoft dans le formulaire de demande de service (SRF). Vous pouvez obtenir le formulaire SRF en passant par le responsable technique de compte ou votre contact Microsoft pour l’intégration d’une grande instance HANA.

- Nom du serveur et adresse IP du serveur (par exemple, myhanaserver1 et 10.35.0.1)
- Emplacement (par exemple, USA Est)
- Nom du client (par exemple, Microsoft)
- Identificateur du système HANA (SID) (par exemple, H11)

Une fois l’appareil STONITH configuré, l’équipe de management des services Microsoft vous fournit le nom de l’appareil de bloc STONITH (SBD) et l’adresse IP du stockage iSCSI. Vous pouvez utiliser ces informations pour configurer l’installation STONITH. 

Suivez les étapes décrites dans les sections suivantes pour configurer la haute disponibilité à l’aide de STONITH.

## <a name="identify-the-sbd-device"></a>Identifier l’appareil SBD

> [!NOTE]
> Cette section s’applique uniquement à des clients existants. Si vous êtes un nouveau client, vous pouvez ignorer cette section, car l’équipe de gestion des services Microsoft vous fournira le nom de l’appareil SBD.

1. Modifiez */etc/iscsi/initiatorname.isci* en : 

    ``` 
    iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
    ```
    
    L’équipe de gestion des services Microsoft fournit cette chaîne. Modifier le fichier sur *les deux* nœuds. Toutefois, le numéro de nœud est différent sur chaque nœud.
    
    ![Capture d’écran représentant un fichier initiatorname avec les valeurs InitiatorName d’un nœud.](media/HowToHLI/HASetupWithStonith/initiatorname.png)

2. Modifiez */etc/iscsi/iscsid.conf* en définissant `node.session.timeo.replacement_timeout=5` et `node.startup = automatic`. Modifier le fichier sur *les deux* nœuds.

3. Exécutez la commande de détection suivante sur les *deux* nœuds.

    ```
    iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
    ```
    
    Les résultats affichent quatre sessions.
    
    ![Capture d’écran représentant une fenêtre de console avec les résultats de la commande de détection.](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

4. Exécutez la commande suivante sur les *deux* nœuds pour vous connecter à l’appareil iSCSI. 

    ```
    iscsiadm -m node -l
    ```
    
    Les résultats affichent quatre sessions.
    
    ![Capture d’écran représentant une fenêtre de console avec les résultats de la commande de nœud.](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

5. Utilisez la commande suivante pour exécutez le script de nouvelle analyse *rescan-scsi-bus.sh*. Ce script affiche les nouveaux disques créés pour vous.  Exécutez-la sur *les deux* nœuds.

    ```
    rescan-scsi-bus.sh
    ```
    
    Les résultats doivent afficher un numéro d’unité logique supérieur à zéro (par exemple : 1, 2 et ainsi de suite).
     
    ![Capture d’écran représentant une fenêtre de console avec les résultats du script.](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

6. Pour obtenir le nom de l’appareil, exécutez la commande suivante sur les *deux* nœuds. 

    ```
      fdisk –l
    ```
    
    Dans les résultats, choisissez l’appareil avec la taille 178 Mio.
    
    ![Capture d’écran représentant une fenêtre de console avec les résultats de la commande f disk.](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="initialize-the-sbd-device"></a>Initialiser l’appareil SBD

1. Utilisez la commande suivante pour initialiser l’appareil SBD sur les *deux* nœuds.

    ```
    sbd -d <SBD Device Name> create
    ```
    ![Capture d’écran représentant une fenêtre de console avec le résultat de la commande s b d create.](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2. Utilisez la commande suivante sur les *deux* nœuds pour vérifier ce qui a été écrit sur l’appareil.

    ```
    sbd -d <SBD Device Name> dump
    ```

## <a name="configure-the-suse-ha-cluster"></a>Configurer le cluster à haute disponibilité SUSE

1. Utilisez la commande suivante pour vérifier si les patterns ha_sles et SAPHanaSR-doc sont installés sur les *deux* nœuds. S’ils ne sont pas installés, installez-les.

    ```
    zypper in -t pattern ha_sles
    zypper in SAPHanaSR SAPHanaSR-doc
    ```
    ![Capture d’écran représentant une fenêtre de console avec le résultat de la commande pattern.](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
    
    ![Capture d’écran représentant une fenêtre de console avec le résultat de la commande SAPHanaSR-doc.](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)
    
2. Configurez le cluster à l’aide de la commande `ha-cluster-init` ou de l’Assistant yast2. Dans cet exemple, nous utilisons l’Assistant yast2. Effectuez cette étape uniquement sur le *nœud principal*.

    1. Accédez à **yast2** > **Haute disponibilité** > **Cluster**.
    
        ![Capture d’écran représentant le Centre de contrôle YaST dans lequel Haute disponibilité et Cluster sont sélectionnés.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)        
                
    1. Dans la boîte de dialogue qui s’affiche à propos de l’installation du package hawk, sélectionnez **Annuler**, car le package halk2 est déjà installé.
    
        ![Capture d’écran représentant une boîte de dialogue avec les options Installer et Annuler.](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)
                    
    1. Dans la boîte de dialogue qui s’affiche pour continuer, sélectionnez **Continuer**.
    
        ![Capture d’écran représentant un message sur la poursuite de l’installation sans installer les packages requis.](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)        
        
    1. La valeur attendue est le nombre de nœuds déployés (dans ce cas : 2). Sélectionnez **Suivant**.  

     
        
    1. Ajoutez des noms de nœuds, puis sélectionnez **Ajouter les fichiers suggérés**.

        ![Capture d’écran représentant la fenêtre Configurer le cluster ainsi que les listes Hôte de synchronisation et Fichier de synchronisation.](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)
        
    1. Sélectionnez **Activer csync2**.
    
    1. Sélectionnez **Générer des clés prépartagées**. 
    
    1. Dans le message contextuel qui s’affiche, sélectionnez **OK**.
    
        ![Capture d’écran représentant un message qui indique que votre clé a été générée.](media/HowToHLI/HASetupWithStonith/yast-key-file.png)
        
    1. L’authentification est effectuée à l’aide des adresses IP et des clés prépartagées dans Csync2. Le fichier de clé est généré avec `csync2 -k /etc/csync2/key_hagroup`. 
    
        Copiez manuellement le fichier *key_hagroup* sur tous les membres du cluster après sa création. Veillez à copier le fichier à partir de node1 vers node2. Sélectionnez ensuite **Suivant**.
        
        ![Capture d’écran représentant la boîte de dialogue Configurer le cluster avec les options nécessaires pour copier la clé vers tous les membres du cluster.](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)
        
    1. Dans l’option par défaut, le **démarrage** était **désactivé**. **Activez-le** pour que le service pacemaker se lance au démarrage. Vous pouvez effectuer votre choix en fonction de vos exigences d’installation.

        ![Capture d’écran représentant la fenêtre Service de cluster avec le démarrage activé.](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)
    
    1. Sélectionnez **Suivant** pour terminer la configuration du cluster.

## <a name="set-up-the-softdog-watchdog"></a>Configurer l’agent de surveillance softdog

1. Ajoutez la ligne suivante à */etc/init.d/boot.local* sur *les deux* nœuds.
    
    ```
    modprobe softdog
    ```
    ![Capture d’écran représentant un fichier de démarrage dans lequel la ligne softdog a été ajoutée.](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)
    
2. Utilisez la commande suivante pour mettre à jour le fichier */etc/sysconfig/sbd* sur *les deux* nœuds.
    
    ```
    SBD_DEVICE="<SBD Device Name>"
    ```
    ![Capture d’écran représentant le fichier s b d dans lequel la valeur S B D_DEVICE a été ajoutée.](media/HowToHLI/HASetupWithStonith/sbd-device.png)
    
3. Chargez le module du noyau sur *les deux* nœuds en exécutant la commande suivante.
    
    ```
    modprobe softdog
    ```
    ![Capture d’écran représentant une fenêtre de console partielle avec la commande modprobe softdog.](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4. Utilisez la commande suivante pour vous assurer que softdog est exécuté sur *les deux* nœuds.
    
    ```
    lsmod | grep dog
    ```
    ![Capture d’écran représentant une fenêtre de console partielle avec le résultat de l’exécution de la commande l s mod.](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)
    
5. Utilisez la commande suivante pour démarrer l’appareil SBD sur *les deux* nœuds.

    ```
    /usr/share/sbd/sbd.sh start
    ```
    ![Capture d’écran représentant une fenêtre de console partielle avec la commande start.](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)
    
6. Utilisez la commande suivante pour tester le démon SBD sur *les deux* nœuds. 
    
    ```
    sbd -d <SBD Device Name> list
    ```
    Les résultats affichent deux entrées après la configuration sur les deux nœuds.    
    
    ![Capture d’écran représentant fenêtre de console partielle dans laquelle deux entrées sont affichées.](media/HowToHLI/HASetupWithStonith/sbd-list.png)
    
7. Envoyez le message de test suivant à l’*un* de vos nœuds.

    ```
    sbd  -d <SBD Device Name> message <node2> <message>
    ```
    
8. Sur le *second* nœud (node2), utilisez la commande suivante pour vérifier l’état du message.
    
    ```
    sbd  -d <SBD Device Name> list
    ```
    ![Capture d’écran représentant une fenêtre de console partielle dans laquelle l’un des membres affiche une valeur de test pour l’autre membre.](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)
    
9. Pour adopter la configuration SBD, mettez à jour le fichier */etc/sysconfig/sbd* comme suit sur *les deux* nœuds.

    ```
    SBD_DEVICE=" <SBD Device Name>" 
    SBD_WATCHDOG="yes" 
    SBD_PACEMAKER="yes" 
    SBD_STARTMODE="clean" 
    SBD_OPTS=""
    ```
10. Utilisez la commande suivante pour démarrer le service pacemaker sur le *nœud principal* (node1).

    ```
    systemctl start pacemaker
    ```
    ![Capture d’écran représentant une fenêtre de console qui affiche l’état après le démarrage du service pacemaker.](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)
    
    Si le service pacemaker échoue, consultez la section [Scénario 5 : échec du service Pacemaker](#scenario-5-pacemaker-service-fails) plus loin dans cet article.

## <a name="join-the-node-to-the-cluster"></a>Joindre le nœud au cluster

Exécutez la commande suivante sur *node2* pour lui permettre de rejoindre le cluster.

```
ha-cluster-join
```

Si vous recevez une erreur lors de la jonction au cluster, consultez la section [Scénario 6 : Node2 ne peut pas rejoindre le cluster](#scenario-6-node2-cant-join-the-cluster) plus loin dans cet article.

## <a name="validate-the-cluster"></a>Valider le cluster

1. Utilisez la commande suivante pour vérifier et éventuellement démarrer le cluster pour la première fois sur *les deux* nœuds.
    
     ```
    systemctl status pacemaker
    systemctl start pacemaker
    ```
    ![Capture d’écran représentant une fenêtre de console avec l’état du service pacemaker.](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
        
2. Exécutez la commande suivante pour vérifier que *les deux* nœuds sont en ligne. Vous pouvez l’exécuter sur *l’un ou l’autre des nœuds* du cluster.
    
    ```
    crm_mon
    ```
    ![Capture d’écran représentant une fenêtre de console avec les résultats de la commande c r m_mon.](media/HowToHLI/HASetupWithStonith/crm-mon.png)
    
    Vous pouvez également vous connecter à hawk pour vérifier l’état du cluster : `https://\<node IP>:7630`. L’utilisateur par défaut est **hacluster** et le mot de passe est **linux**. Si nécessaire, vous pouvez changer le mot de passe avec la commande `passwd`.
    
## <a name="configure-cluster-properties-and-resources"></a>Configurer les propriétés et ressources du cluster

Cette section décrit les étapes permettant de configurer les ressources du cluster.
Dans cet exemple, vous configurez les ressources suivantes. Vous pouvez configurer le reste (si nécessaire) en faisant référence au guide de la haute disponibilité SUSE.

- Bootstrap du cluster
- Appareil STONITH
- Adresse IP virtuelle

Effectuez la configuration sur le *nœud principal* uniquement.

1. Créez le fichier de démarrage du cluster et configurez-le en ajoutant le texte suivant.
    
    ```
    sapprdhdb95:~ # vi crm-bs.txt
    # enter the following to crm-bs.txt
    property $id="cib-bootstrap-options" \
    no-quorum-policy="ignore" \
    stonith-enabled="true" \
    stonith-action="reboot" \
    stonith-timeout="150s"
    rsc_defaults $id="rsc-options" \
    resource-stickiness="1000" \
    migration-threshold="5000"
    op_defaults $id="op-options" \
    timeout="600"
    ```

2. Utilisez la commande suivante pour ajouter la configuration au cluster.

    ```
    crm configure load update crm-bs.txt
    ```
    ![Capture d’écran représentant une fenêtre de console partielle dans laquelle la commande c r m est exécutée.](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)
    
3. Configurez l’appareil STONITH en ajoutant la ressource, en créant le fichier et en ajoutant du texte comme suit.

    ```
    # vi crm-sbd.txt
    # enter the following to crm-sbd.txt
    primitive stonith-sbd stonith:external/sbd \
    params pcmk_delay_max="15"
    ```
    Utilisez la commande suivante pour ajouter la configuration au cluster.
        
    ```
    crm configure load update crm-sbd.txt
    ```
        
4. Ajoutez l’adresse IP virtuelle de la ressource en créant le fichier et en ajoutant le texte suivant.

    ```
    # vi crm-vip.txt
    primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
    operations $id="rsc_ip_HA1_HDB10-operations" \
    op monitor interval="10s" timeout="20s" \
    params ip="10.35.0.197"
    ```
    
    Utilisez la commande suivante pour ajouter la configuration au cluster.
    
    ```
    crm configure load update crm-vip.txt
    ```
        
5. Utilisez la commande `crm_mon` pour valider les ressources. 

    Les résultats montrent les deux ressources.

    ![Capture d’écran représentant une fenêtre de console avec deux ressources.](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

    Vous pouvez aussi vérifier l’état à l’adresse *https://\<node IP address>:7630/cib/live/state*.
    
    ![Capture d’écran affichant l’état des deux ressources.](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)
    
## <a name="test-the-failover-process"></a>Tester le processus de basculement

1. Pour tester le processus de basculement, utilisez la commande suivante pour arrêter le service pacemaker sur node1.

    ```
    Service pacemaker stop
    ```
    
    Les ressources basculent vers node2.

2. Arrêtez le service pacemaker sur node2 et les ressources basculent sur node1.

    Voici l’état avant le basculement :  
    ![Capture d’écran affichant l’état des deux ressources avant le basculement.](media/HowToHLI/HASetupWithStonith/Before-failover.png)  
    
    Voici l’état après le basculement :  
    ![Capture d’écran affichant l’état des deux ressources après le basculement.](media/HowToHLI/HASetupWithStonith/after-failover.png)
    
    ![Capture d’écran représentant une fenêtre de console qui affiche l’état des ressources après le basculement.](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  
    

## <a name="troubleshooting"></a>Résolution des problèmes

Cette section décrit les scénarios d’échec que vous pouvez rencontrer pendant la configuration.

### <a name="scenario-1-cluster-node-not-online"></a>Scénario 1 : le nœud de cluster n’est pas en ligne

Si l’un des nœuds n’apparaît pas en ligne dans le Gestionnaire de cluster, vous pouvez essayer cette procédure pour le mettre en ligne.

1. Utilisez la commande suivante pour démarrer le service iSCSI.

    ```
    service iscsid start
    ```
    
2. Utilisez la commande suivante pour vous connecter à ce nœud iSCSI.

    ```
    iscsiadm -m node -l
    ```
    
    Le résultat attendu ressemble à ce qui suit :

    ```
    sapprdhdb45:~ # iscsiadm -m node -l
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
    ```
### <a name="scenario-2-yast2-doesnt-show-graphical-view"></a>Scénario 2 : Yast2 ne montre pas la vue graphique

L’écran graphique de yast2 est utilisé pour configurer le cluster à haute disponibilité dans cet article. Si yast2 ne s’ouvre pas avec la fenêtre graphique indiquée et lance une erreur Qt, effectuez les étapes suivantes pour installer les packages requis. Si yast2 s’ouvre avec la fenêtre graphique, vous pouvez ignorer ces étapes.

Voici un exemple de l’erreur Qt :

![Capture d’écran représentant une fenêtre de console partielle qui affiche un message d’erreur.](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

Voici un exemple de la sortie attendue :

![Capture d’écran représentant le Centre de contrôle YaST dans lequel Haute disponibilité et Cluster sont en surbrillance.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

1. Assurez-vous d’être connecté en tant qu’utilisateur « racine » et d’avoir configuré SMT pour télécharger et installer les packages.

2. Accédez à **yast** > **Logiciels** > **Gestion des logiciels** > **Dépendances**, puis sélectionnez **Installer les packages recommandés**. 

    >[!NOTE]
    >Effectuez les étapes sur *les deux* nœuds, afin de pouvoir accéder à la vue graphique yast2 à partir des deux nœuds.
    
    La capture d’écran suivante représente l’écran attendu.
    
    ![Capture d’écran représentant une fenêtre de console qui affiche le Centre de contrôle YaST.](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)
    
3. Sous **Dépendances**, sélectionnez **Installer les packages recommandés**.

    ![Capture d’écran représentant une fenêtre de console dans laquelle l’option Installer les packages recommandés est sélectionnée.](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

4. Passez en revue les modifications et sélectionnez **OK**.

    ![Capture d’écran représentant une fenêtre de console avec une liste des packages sélectionnés pour l’installation.](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

    L’installation du package se poursuit.

    ![Capture d’écran représentant une fenêtre de console qui affiche la progression de l’installation.](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

5. Sélectionnez **Suivant**.    

6. Quand l’écran **Installation réussie** apparaît, sélectionnez **Terminer**.

    ![Capture d’écran représentant une fenêtre de console qui affiche un message de réussite.](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

7. Utilisez les commandes suivantes pour installer les packages libqt4 et libyui-qt.
    
    ```
    zypper -n install libqt4
    ```
    ![Capture d’écran représentant une fenêtre de console lors de l’installation du premier package.](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
    
    ```
    zypper -n install libyui-qt
    ```
    ![Capture d’écran représentant une fenêtre de console lors de l’installation du deuxième package.](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
    
    ![Capture d’écran représentant une fenêtre de console lors de l’installation du deuxième package, suite.](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)
    
    Yast2 peut maintenant ouvrir la vue graphique.

    ![Capture d’écran représentant le Centre de contrôle YaST dans lequel Logiciel et Mise à jour en ligne sont sélectionnés.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-doesnt-show-the-high-availability-option"></a>Scénario 3 : Yast2 ne montre pas l’option de haute disponibilité

Pour que l’option de haute disponibilité soit visible dans le Centre de contrôle yast2, vous devez installer les autres packages.

1. Accédez à **Yast2** > **Logiciels** > **Gestion des logiciels**. Puis sélectionnez **Logiciels** > **Mise à jour en ligne**.  

    ![Capture d’écran représentant le Centre de contrôle YaST dans lequel Logiciel et Mise à jour en ligne sont sélectionnés.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

2. Sélectionnez des patterns pour les éléments suivants. Puis sélectionnez **Accepter**.

    - Base de serveur SAP HANA
    - Compilateur et outils C/C++
    - Haute disponibilité
    - Base de serveur d’applications SAP

    ![Capture d’écran représentant la sélection du premier pattern dans l’élément pour compilateur et outils.](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)

    ![Capture d’écran représentant la sélection du deuxième pattern dans l’élément pour compilateur et outils.](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

4. Dans la liste des packages qui ont été modifiés pour résoudre les dépendances, sélectionnez **Continuer**.

    ![Capture d’écran représentant la boîte de dialogue Packages modifiés qui affiche les packages modifiés pour résoudre les dépendances.](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

4. Dans la page d’état **Exécution de l’installation**, sélectionnez **Suivant**.

    ![Capture d’écran représentant la page d’état Exécution de l’installation.](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

5. Une fois l’installation terminée, un rapport d’installation s’affiche. Sélectionnez **Terminer**.

    ![Capture d’écran représentant le rapport d’installation.](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scénario 4 : échec de l’installation de HANA avec une erreur des assemblys gcc

En cas d’échec de l’installation de HANA, vous pouvez recevoir l’erreur suivante.

![Capture d’écran représentant un message d’erreur qui indique que le système d’exploitation n’est pas prêt pour les assemblys g c c 5.](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Pour résoudre le problème, installez les bibliothèques libgcc_sl et libstdc++6 comme indiqué dans la capture d’écran suivante.

![Capture d’écran représentant une fenêtre de console lors de l’installation des bibliothèques requises.](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scénario 5 : échec du service Pacemaker

Les informations suivantes s’affichent si le service pacemaker ne peut pas démarrer.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Pour le corriger, supprimez la ligne suivante du fichier */usr/lib/systemd/system/fstrim.timer* :

```
Persistent=true
```
    
![Capture d’écran représentant le fichier f s trim avec la valeur Persistent=true à supprimer.](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node2-cant-join-the-cluster"></a>Scénario 6 : Node2 ne peut pas rejoindre le cluster

L’erreur suivante s’affiche s’il y a un problème avec la jonction de node2 au cluster existant par le biais de la commande *ha-cluster-join*.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![Capture d’écran représentant une fenêtre de console avec un message d’erreur indiquant que les clés S S H ne peuvent pas être récupérées à partir d’une adresse IP spécifique.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Pour corriger :

1. Exécutez les commandes suivantes sur *les deux* nœuds.

    ```
    ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
    cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
    ```

    ![Capture d’écran représentant une fenêtre de console partielle dans laquelle la commande est exécutée sur le premier nœud.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

    ![Capture d’écran représentant une fenêtre de console partielle dans laquelle la commande est exécutée sur le deuxième nœud.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

2. Vérifiez que node2 est ajouté au cluster.

    ![Capture d’écran représentant une fenêtre de console avec une commande join réussie.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez plus d’informations sur la configuration de la haute disponibilité SUSE dans les articles suivants : 

- [SAP HANA SR Performance Optimized Scenario](https://www.suse.com/support/kb/doc/?id=000019450) (site web SUSE)
- [Fencing and STONITH](https://documentation.suse.com/sle-ha/15-SP1/html/SLE-HA-all/cha-ha-fencing.html) (site web SUSE)
- [Be Prepared for Using Pacemaker Cluster for SAP HANA – Part 1: Basics](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/) (blog SAP)
- [Be Prepared for Using Pacemaker Cluster for SAP HANA – Part 2: Failure of Both Nodes](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/) (blog SAP)

Découvrez comment effectuer une sauvegarde et une restauration au niveau des fichiers pour un système d’exploitation :

> [!div class="nextstepaction"]
> [Sauvegarde et restauration du système d’exploitation](large-instance-os-backup.md)
