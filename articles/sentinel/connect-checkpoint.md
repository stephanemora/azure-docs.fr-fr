---
title: Connecter des données Check Point à Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment connecter des données Check Point à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 3229233d-400d-4971-8d76-eaa0d6591d75
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: eaad8a6ae3d2dbaacf01e256bb6aa5691b05af49
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620590"
---
# <a name="connect-your-check-point-appliance"></a>Connecter votre appliance Check Point

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez connecter Azure Sentinel à n’importe quelle appliance Check Point en enregistrant les fichiers journaux au format CEF Syslog. Avec l’intégration à Azure Sentinel, vous pouvez facilement exécuter des fonctions d’analytique et des requêtes sur les données des fichiers journaux à partir de Check Point. Pour plus d’informations sur la façon dont Azure Sentinel ingère les données au format CEF, voir [Connecter des appliances CEF](connect-common-event-format.md).

> [!NOTE]
> Les données seront stockées à l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="step-1-connect-your-check-point-appliance-using-an-agent"></a>Étape 1 : Connecter votre appliance Check Point à l’aide d’un agent

Pour connecter votre appliance Check Point à Azure Sentinel, vous devez déployer un agent sur un système dédié (machine virtuelle ou ordinateur local) afin de prendre en charge les communications entre l’appliance et Azure Sentinel. Vous pouvez déployer l’agent manuellement ou automatiquement. Le déploiement automatique n’est disponible que si votre machine dédiée est une nouvelle machine virtuelle que vous créez dans Azure. 

Vous pouvez également déployer l’agent manuellement sur une machine virtuelle Azure existante, sur une machine virtuelle dans un autre cloud, ou sur un ordinateur local.

Pour accéder à un diagramme réseau des deux options, voir [Connecter des sources de données](connect-data-sources.md).

### <a name="deploy-the-agent-in-azure"></a>Déployer l’agent dans Azure

1. Dans le portail Azure Sentinel, cliquez sur **Data Connectors** (Connecteurs de données) et sélectionnez le type de votre appliance. 

1. Sous **Linux Syslog agent configuration** (Configuration de l’agent Linux Syslog) :
   - Choisissez **Automatic Deployment** (Déploiement automatique) si vous souhaitez créer un ordinateur qui est préinstallé avec l’agent Azure Sentinel et inclut toute la configuration nécessaire, comme décrit ci-dessus. Sélectionnez **Automatic Deployment** (Déploiement automatique) et **Automatic Agent Deployment** (Déploiement automatique d’agent). Vous accédez alors à la page d’achat d’une machine virtuelle dédiée, qui est connectée automatiquement à votre espace de travail. La machine virtuelle est une machine **standard D2s v3 (2 processeurs virtuels, 8 Go de mémoire)** et elle a une adresse IP publique.
     1. Dans la page **Custom deployment** (Déploiement personnalisé), spécifiez vos informations, choisissez un nom d’utilisateur et un mot de passe et, si vous acceptez les termes et conditions, achetez la machine virtuelle.
      
        1. Exécutez ces commandes sur l’ordinateur de l’agent Syslog pour vous assurer que tous les journaux Check Point seront mappés à l’agent Microsoft Azure Sentinel :
           - Si vous utilisez Syslog-ng, exécutez ces commandes (remarque : elles redémarrent l’agent Syslog) :
            
                sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"

             Redémarrez le démon Syslog : `sudo service syslog-ng restart`
           - Si vous utilisez rsyslog, exécutez ces commandes (remarque : elles redémarrent l’agent Syslog) :
                    
                 sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"
             Redémarrez le démon Syslog : `sudo service rsyslog restart`

   - Choisissez **Manual deployment** (Déploiement manuel) si vous souhaitez utiliser une machine virtuelle existante comme ordinateur Linux dédié sur lequel l’agent Azure Sentinel doit être installé. 
      1. Sous **Download and install the Syslog agent** (Télécharger et installer l’agent Syslog), sélectionnez **Azure Linux virtual machine** (Machine virtuelle Linux Azure). 
      1. Sur l’écran **Virtual machines** (Machines virtuelles) qui s’affiche, sélectionnez l’ordinateur que vous souhaitez utiliser, puis cliquez sur **Connect** (Se connecter).
      1. Sur l’écran de connecteur, sous **Configure and forward Syslog** (Configurer et transférer Syslog), définissez si votre démon Syslog est **rsyslog.d** ou **syslog-ng**. 
      1. Copiez ces commandes et exécutez-les sur votre appliance :
          - Si vous avez sélectionné rsyslog.d :
              
            1. Demandez au démon Syslog d’écouter sur la fonctionnalité local_4 et sur « Check Point », puis d’envoyer les messages Syslog à l’agent Azure Sentinel en utilisant le port 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. Téléchargez et installez le [fichier de configuration security_events](https://aka.ms/asi-syslog-config-file-linux) qui configure l’agent Syslog pour écouter sur le port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`, où {0} doit être remplacé par le GUID de votre espace de travail.
           
            1. Redémarrez le démon Syslog `sudo service rsyslog restart`
             
          - Si vous avez sélectionné syslog-ng :

              1. Demandez au démon Syslog d’écouter sur la fonctionnalité local_4 et sur « Check Point », puis d’envoyer les messages Syslog à l’agent Azure Sentinel en utilisant le port 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Téléchargez et installez le [fichier de configuration security_events](https://aka.ms/asi-syslog-config-file-linux) qui configure l’agent Syslog pour écouter sur le port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`, où {0} doit être remplacé par le GUID de votre espace de travail.

              3. Redémarrez le démon Syslog `sudo service syslog-ng restart`
      2. Redémarrez l’agent Syslog à l’aide de cette commande : `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Confirmez qu’il n’existe aucune erreur dans le journal de l’agent en exécutant cette commande : `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Déployer l’agent sur un serveur Linux local

Si vous n’utilisez pas Azure, déployez manuellement l’agent Azure Sentinel pour l’exécuter sur un serveur Linux dédié.

1. Pour créer une machine virtuelle Linux dédiée, sous **Linux Syslog agent configuration** (Configuration de l’agent Linux Syslog), choisissez **Manual deployment** (Déploiement manuel).
   1. Sous **Download and install the Syslog agent** (Télécharger et installer l’agent Syslog), sélectionnez **Non-Azure Linux machine** (Machine virtuelle non-Azure). 
   1. Sur l’écran **Direct Agent** qui s’affiche, sélectionnez **Agent for Linux** (Agent pour Linux) pour télécharger l’agent, ou exécutez cette commande pour le télécharger sur votre ordinateur Linux :   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. Sur l’écran de connecteur, sous **Configure and forward Syslog** (Configurer et transférer Syslog), définissez si votre démon Syslog est **rsyslog.d** ou **syslog-ng**. 
      1. Copiez ces commandes et exécutez-les sur votre appliance :
         - Si vous avez sélectionné **rsyslog** :
           1. Demandez au démon Syslog d’écouter sur la fonctionnalité local_4 et sur « Check Point », puis d’envoyer les messages Syslog à l’agent Azure Sentinel en utilisant le port 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. Téléchargez et installez le [fichier de configuration security_events](https://aka.ms/asi-syslog-config-file-linux) qui configure l’agent Syslog pour écouter sur le port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`, où {0} doit être remplacé par le GUID de votre espace de travail.
           3. Redémarrez le démon Syslog : `sudo service rsyslog restart`
         - Si vous avez sélectionné **syslog-ng** :
            1. Demandez au démon Syslog d’écouter sur la fonctionnalité local_4 et sur « Check Point », puis d’envoyer les messages Syslog à l’agent Azure Sentinel en utilisant le port 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. Téléchargez et installez le [fichier de configuration security_events](https://aka.ms/asi-syslog-config-file-linux) qui configure l’agent Syslog pour écouter sur le port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`, où {0} doit être remplacé par le GUID de votre espace de travail.
            3. Redémarrez le démon Syslog `sudo service syslog-ng restart`
      1. Redémarrez l’agent Syslog à l’aide de cette commande : `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Confirmez qu’il n’existe aucune erreur dans le journal de l’agent en exécutant cette commande : `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-check-point-logs-to-the-syslog-agent"></a>Étape 2 : Transférer les journaux Check Point à l’agent Syslog

Configurez Check Point pour transférer les messages Syslog au format CEF à votre espace de travail Azure par le biais de l’agent Syslog.

1. Accédez à la section [Check Point Log Export](https://aka.ms/asi-syslog-checkpoint-forwarding) (Exportation de journaux Check Point).
2. Faites défiler la page jusqu’au lien **Basic Deployment** (Déploiement de base) et suivez les instructions pour configurer la connexion, en suivant les recommandations suivantes :
   - Définissez le **port Syslog** sur la valeur **514**, ou sur la valeur du port défini sur l’agent.
     - Remplacez le **nom** et **l’adresse IP du serveur cible** dans la CLI par le nom et l’adresse IP de l’agent Syslog.
     - Définissez le format sur **CEF**.
3. Si vous utilisez la version R77.30 ou R80.10, faites défiler la page jusqu’à la section **Installations** et suivez les instructions pour installer un exportateur de journal correspondant à votre version.
 
## <a name="step-3-validate-connectivity"></a>Étape 3 : Valider la connectivité

L’affichage de vos journaux dans Log Analytics peut prendre plus de 20 minutes. 

1. Vérifiez que vous utilisez la fonctionnalité correcte. Elle doit être identique dans votre appliance et dans Azure Sentinel. Vous pouvez vérifier quel fichier de fonctionnalité vous utilisez dans Azure Sentinel et le modifier dans le fichier `security-config-omsagent.conf`. 

2. Vérifiez que vos journaux utilisent le port correct sur l’agent Syslog. Exécutez cette commande sur l’ordinateur de l’agent Syslog : `tcpdump -A -ni any  port 514 -vv` Cette commande affiche les fichiers journaux diffusés en continu à partir de l’appareil sur l’ordinateur Syslog. Assurez-vous que l’appliance source envoie les journaux sur le bon port et la bonne fonctionnalité.

3. Vérifiez que les journaux que vous envoyez sont conformes à la norme [RFC 5424](https://tools.ietf.org/html/rfc542).

4. Sur l’ordinateur exécutant l’agent Syslog, vérifiez que les ports 514, 25226 et 25226 sont ouverts et à l’écoute à l’aide de la commande `netstat -a -n:`. Pour en savoir plus sur l’utilisation de cette commande, voir [netstat(8) - Linux man page](https://linux.die.net/man/8/netstat). S’ils sont à l’écoute correctement, voici ce que vous voyez :

   ![Ports Azure Sentinel](./media/connect-cef/ports.png) 

5. Vérifiez que le démon est configuré pour écouter sur le port 514, sur lequel vous envoyez les journaux.
    - Pour rsyslog :<br>Vérifiez que le fichier `/etc/rsyslog.conf` inclut cette configuration :

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Pour plus d’informations, consultez [imudp : UDP Syslog Input Module](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) et [imtcp : TCP Syslog Input Module](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - Pour syslog-ng :<br>Vérifiez que le fichier `/etc/syslog-ng/syslog-ng.conf` inclut cette configuration :

           # source s_network {
            network( transport(UDP) port(514));
             };
     Pour en savoir plus, voir [imudp: UDP Syslog Input Module](Pour en savoir plus, voir [syslog-ng Open Source Edition 3.16 - Administration Guide](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Vérifiez qu’il existe une communication entre le démon Syslog et l’agent. Exécutez cette commande sur l’ordinateur de l’agent Syslog : `tcpdump -A -ni any  port 25226 -vv` Cette commande affiche les fichiers journaux diffusés en continu à partir de l’appareil sur la machine Syslog. Assurez-vous que l’agent reçoit également les journaux.

6. Si ces deux commandes ont fourni des résultats satisfaisants, vérifiez Log Analytics pour voir si vos journaux arrivent. Tous les événements transmis à partir de ces appliances apparaissent sous forme brute dans Log Analytics sous le type `CommonSecurityLog`.

7. Pour vérifier s’il existe des erreurs ou si les journaux ne sont pas reçus, recherchez dans `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Si des erreurs d’incompatibilité de format de journal sont signalées, accédez à `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` et examinez le fichier `security_events.conf`, en vous assurant que vos journaux présentent le format d’expression régulière que vous voyez dans ce fichier.

8. Vérifiez que la taille par défaut des messages Syslog est limitée à 2 048 octets (2 Ko). Si les journaux sont trop longs, mettez à jour le fichier security_events.conf à l’aide de cette commande : `message_length_limit 4096`

4. Assurez-vous d’exécuter ces commandes :
  
   - Si vous utilisez Syslog-ng, exécutez ces commandes (remarque : elles redémarrent l’agent Syslog) :

         sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"
        Redémarrez le démon Syslog : `sudo service syslog-ng restart`

   - Si vous utilisez rsyslog, exécutez ces commandes (remarque : elles redémarrent l’agent Syslog) : 

         sudo bash -c "printf 'local4.debug @127.0.0.1:25226\n\n:msg, contains, "Check Point" @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"
     Redémarrez le démon Syslog : `sudo service rsyslog restart`




## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter des appliances Check Point à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).

