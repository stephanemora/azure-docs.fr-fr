---
title: Connecter des données Fortinet à Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment connecter des données Fortinet à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: 339b8c1b59720989016f68fdb94fae30c26b42f0
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679280"
---
# <a name="connect-your-fortinet-appliance"></a>Connecter votre appliance Fortinet

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service. Nous ne la recommandons pas pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’utilisation supplémentaires des Préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez connecter Azure Sentinel à n’importe quelle appliance Fortinet en enregistrant les fichiers journaux au format CEF (Common Event Format) Syslog. Avec l’intégration à Azure Sentinel, vous pouvez facilement exécuter une analytique et des requêtes sur les données des fichiers journaux à partir de Fortinet. Pour plus d’informations sur la façon dont Azure Sentinel ingère les données au format CEF, consultez [Connecter des appliances CEF](connect-common-event-format.md).

> [!NOTE]
> Les données sont stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>Étape 1 : Connecter votre appliance Fortinet à l’aide d’un agent

Pour connecter votre appliance Fortinet à Azure Sentinel, déployez un agent sur une machine virtuelle ou un ordinateur local dédié afin de prendre en charge la communication entre l’appliance et Azure Sentinel. 

Vous pouvez également déployer l’agent manuellement sur une machine virtuelle Azure existante, sur une machine virtuelle dans un autre cloud, ou sur un ordinateur local.

> [!NOTE]
> Veillez à configurer la sécurité de la machine en fonction de la stratégie de sécurité de votre organisation. Par exemple, vous pouvez configurer votre réseau de sorte qu’il s’accorde à la stratégie de sécurité de votre réseau d’entreprise, et modifier les ports et les protocoles dans le démon pour les adapter à vos besoins. 

Pour voir un diagramme réseau des deux options, consultez [Connecter des sources de données](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent"></a>Déployer l’agent

1. Dans le portail Azure Sentinel, cliquez sur **Data Connectors** (Connecteurs de données) et sélectionnez **Fortinet** (Fortinet), puis **Open Connector Page** (Ouvrir la page du connecteur). 

1. Sous **Download and install the Syslog agent** (Télécharger et installer l’agent Syslog), sélectionnez le type de votre machine, Azure ou locale. 
1. Sur l’écran **Virtual machines** (Machines virtuelles) qui s’affiche, sélectionnez l’ordinateur que vous souhaitez utiliser, puis cliquez sur **Connect** (Se connecter).
1. Si vous avez choisi **Download and install agent for Azure Linux virtual machines** (Télécharger et installer l’agent pour des machines virtuelles Linux Azure), sélectionnez la machine et cliquez sur **Connect** (Connecter). Si vous avez sélectionné **Download and install agent for non-Azure Linux virtual machines** (Télécharger et installer l’agent pour des machines virtuelles Linux non Azure), sur l’écran **Direct Agent** (Agent direct), exécutez le script sous **Download and onboard agent for Linux** (Télécharger et intégrer l’agent pour Linux).
1. Sur l’écran de connecteur, sous **Configure and forward Syslog** (Configurer et transférer Syslog), définissez si votre démon Syslog est **rsyslog.d** ou **syslog-ng**. 
1. Copiez ces commandes et exécutez-les sur votre appliance :
   - Si vous avez sélectionné rsyslog.d :
            
     1. Demandez au démon Syslog d’écouter sur la fonctionnalité local_4 et d’envoyer les messages Syslog à l’agent Azure Sentinel en utilisant le port 25226. Utilisez cette commande : `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
     1. Téléchargez et installez le [fichier de configuration security_events](https://aka.ms/asi-syslog-config-file-linux) qui configure l’agent Syslog pour écouter sur le port 25226. Utilisez cette commande : `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` où {0} doit être remplacé par le GUID de votre espace de travail.
     1. Redémarrez le démon Syslog à l’aide de cette commande : `sudo service rsyslog restart`
            
   - Si vous avez sélectionné syslog-ng :

      1. Demandez au démon Syslog d’écouter sur la fonctionnalité local_4 et d’envoyer les messages Syslog à l’agent Azure Sentinel en utilisant le port 25226. Utilisez cette commande : `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      1. Téléchargez et installez le [fichier de configuration security_events](https://aka.ms/asi-syslog-config-file-linux) qui configure l’agent Syslog pour écouter sur le port 25226. Utilisez cette commande : `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` où {0} doit être remplacé par le GUID de votre espace de travail.
      1. Redémarrez le démon syslog à l’aide de cette commande : `sudo service syslog-ng restart`
1. Redémarrez l’agent Syslog à l’aide de cette commande : `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Confirmez qu’il n’existe aucune erreur dans le journal de l’agent en exécutant cette commande : `tail /var/opt/microsoft/omsagent/log/omsagent.log`

 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>Étape 2 : Transférer les journaux Fortinet à l’agent Syslog

Configurez Fortinet pour transférer les messages Syslog au format CEF à votre espace de travail Azure par le biais de l’agent Syslog.

1. Ouvrez l’interface CLI sur votre appliance Fortinet et exécutez les commandes suivantes :

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Remplacez l’**adresse IP** du serveur par l’adresse IP de l’agent.
    - Définissez **facility_name** de façon à utiliser la fonctionnalité que vous avez configurée dans l’agent. Par défaut, l’agent définit cette valeur sur local4.
    - Affectez au **port Syslog** la valeur **514** ou la valeur du port défini sur l’agent.
    - Pour activer le format CEF dans les premières versions de FortiOS, vous devrez peut-être exécuter le jeu de commandes **csv disable**.
 
   > [!NOTE] 
   > Pour plus d’informations, accédez à la [bibliothèque de documents Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Sélectionnez votre version et utilisez le manuel d’utilisation (**Handbook**) et la référence des messages de journaux (**Log Message Reference**).

 Pour utiliser le schéma pertinent dans Azure Monitor Log Analytics pour les événements Fortinet, recherchez `CommonSecurityLog`.


## <a name="step-3-validate-connectivity"></a>Étape 3 : Valider la connectivité

Environ 20 minutes peuvent être nécessaires avant que vos journaux commencent à apparaître dans Log Analytics. 

1. Vérifiez que vous utilisez la fonctionnalité correcte. Elle doit être identique dans votre appliance et dans Azure Sentinel. Vous pouvez vérifier quel fichier de fonctionnalité vous utilisez dans Azure Sentinel et le modifier dans le fichier `security-config-omsagent.conf`. 

2. Vérifiez que vos journaux utilisent le port correct sur l’agent Syslog. Exécutez cette commande sur l’ordinateur de l’agent Syslog : `tcpdump -A -ni any  port 514 -vv`. Cette commande affiche les journaux qui sont envoyés de l’appareil vers l’ordinateur Syslog. Vérifiez que les journaux sont reçus en provenance de l’appliance source sur le port correct et la fonctionnalité correcte.

3. Vérifiez que les journaux que vous envoyez sont conformes à la norme [RFC 3164](https://tools.ietf.org/html/rfc3164).

4. Sur l’ordinateur exécutant l’agent Syslog, vérifiez que les ports 514 et 25226 sont ouverts et à l’écoute à l’aide de la commande `netstat -a -n:`. Pour plus d’informations sur l’utilisation de cette commande, consultez [netstat(8) - Linux man page](https://linux.die.net/man/8/netstat). S’ils sont à l’écoute correctement, vous voyez :

   ![Ports Azure Sentinel](./media/connect-cef/ports.png) 

5. Vérifiez que le démon est configuré pour écouter sur le port 514, sur lequel vous envoyez les journaux.
    - Pour rsyslog :<br>Vérifiez que le fichier `/etc/rsyslog.conf` inclut cette configuration :

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Pour plus d’informations, consultez [imudp : UDP Syslog input module](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) et [imtcp: TCP Syslog input module](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module).

   - Pour syslog-ng :<br>Vérifiez que le fichier `/etc/syslog-ng/syslog-ng.conf` inclut cette configuration :

           # source s_network {
            network( transport(UDP) port(514));
             };
     Pour plus d’informations, consultez [imudp : UDP Syslog input module](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html) et [syslog-ng Open source edition 3.16 - Administration guide](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Vérifiez qu’il existe une communication entre le démon Syslog et l’agent. Exécutez cette commande sur l’ordinateur de l’agent Syslog : `tcpdump -A -ni any  port 25226 -vv`. Cette commande affiche les journaux qui sont envoyés de l’appareil vers l’ordinateur Syslog. Vérifiez que les journaux sont également reçus sur l’agent.

6. Si ces deux commandes ont fourni des résultats satisfaisants, vérifiez Log Analytics pour voir si vos journaux arrivent. Tous les événements transmis à partir de ces appliances apparaissent sous forme brute dans Log Analytics sous le type `CommonSecurityLog`.

7. Pour vérifier s’il existe des erreurs ou si les journaux ne sont pas reçus, recherchez dans `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. S’il est indiqué qu’il existe des erreurs d’incompatibilité de format de journal, accédez à `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` et examinez le fichier `security_events.conf`. Vérifiez que vos journaux correspondent au format d’expression régulière que vous voyez dans ce fichier.

8. Vérifiez que la taille par défaut des messages Syslog est limitée à 2 048 octets (2 Ko). Si les journaux sont trop longs, mettez à jour security_events.conf à l’aide de cette commande : `message_length_limit 4096`

10. Si vos journaux Fortinet ne sont pas reçus par l’agent, exécutez cette commande, en fonction du type de démon Syslog utilisé, pour définir la fonctionnalité et configurez les journaux pour rechercher le mot Fortinet dans les journaux :
       - rsyslog.d : `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     Redémarrez le démon Syslog à l’aide de cette commande : `sudo service rsyslog restart`
       - syslog-ng : `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     Redémarrez le démon Syslog à l’aide de cette commande : `sudo service syslog-ng restart`

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à connecter des appliances Fortinet à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).

