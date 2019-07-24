---
title: Connecter des données CEF à Azure Sentinel en préversion | Microsoft Docs
description: Découvrez comment connecter des données CEF à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cbf5003b-76cf-446f-adb6-6d816beca70f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: rkarlin
ms.openlocfilehash: 8e711c0586ce63d4293e2fb0914bbe884b55971f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389966"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Connectez votre solution externe en utilisant le format CEF

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez connecter Azure Sentinel à une solution externe qui vous permet d’enregistrer les fichiers journaux dans Syslog. Si votre appliance vous permet d'enregistrer les journaux au format Syslog Common Event Format (CEF), l'intégration avec Azure Sentinel vous permet d'exécuter facilement des analyses et des requêtes dans les données.

> [!NOTE] 
> Les données sont stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="how-it-works"></a>Fonctionnement

La connexion entre Azure Sentinel et votre appareil CEF se fait en trois étapes :

1. Sur l'appliance, vous devez définir ces valeurs pour que l'appliance envoie les journaux nécessaires dans le format nécessaire à l'agent Azure Sentinel Syslog. Vous pouvez modifier ces paramètres dans votre appliance, à condition de les modifier également dans le démon Syslog sur l'agent Azure Sentinel.
    - Protocol = UDP
    - Port = 514
    - Facility = Local-4
    - Format = CEF
2. L'agent Syslog collecte les données et les envoie en toute sécurité à Log Analytics, où elles sont analysées et enrichies.
3. L'agent stocke les données dans un espace de travail Log Analytics afin qu'elles puissent être interrogées au besoin, à l'aide d'analyses, de règles de corrélation et de tableaux de bord.

> [!NOTE]
> L'agent peut collecter les journaux à partir de plusieurs sources, mais doit être installé sur une machine proxy dédiée.

## <a name="step-1-connect-to-your-cef-appliance-via-dedicated-azure-vm"></a>Étape 1 : Connectez-vous à votre appliance CEF via une machine virtuelle Azure dédiée

Vous devez déployer un agent sur une machine Linux dédiée (machine virtuelle ou ordinateur local) afin de prendre en charge les communications entre l’appliance et Azure Sentinel. Vous pouvez déployer l’agent manuellement ou automatiquement. Le déploiement automatique est basé sur les modèles Resource Manager et ne peut être utilisé que si votre machine Linux dédiée est une nouvelle machine virtuelle que vous créez dans Azure.

 ![CEF dans Azure](./media/connect-cef/cef-syslog-azure.png)

Vous pouvez également déployer l’agent manuellement sur une machine virtuelle Azure existante, sur une machine virtuelle dans un autre cloud, ou sur un ordinateur local. 

 ![CEF local](./media/connect-cef/cef-syslog-onprem.png)

### <a name="deploy-the-agent-in-azure"></a>Déployer l’agent dans Azure


1. Dans le portail Azure Sentinel, cliquez sur **Data Connectors** (Connecteurs de données) et sélectionnez le type de votre appliance. 

1. Sous **Linux Syslog agent configuration** (Configuration de l’agent Linux Syslog) :
   - Choisissez **Automatic Deployment** (Déploiement automatique) si vous souhaitez créer un ordinateur qui est préinstallé avec l’agent Azure Sentinel et inclut toute la configuration nécessaire, comme décrit ci-dessus. Sélectionnez **Automatic Deployment** (Déploiement automatique) et **Automatic Agent Deployment** (Déploiement automatique d’agent). Vous accédez alors à la page d’achat d’une machine virtuelle Linux dédiée, qui est connectée automatiquement à votre espace de travail. La machine virtuelle est une machine **standard D2s v3 (deux processeurs virtuels, 8 Go de mémoire)** et elle a une adresse IP publique.
      1. Dans la page **Custom deployment** (Déploiement personnalisé), spécifiez vos informations, choisissez un nom d’utilisateur et un mot de passe et, si vous acceptez les termes et conditions, achetez la machine virtuelle.
      1. Configurez votre appliance pour envoyer des journaux en utilisant les paramètres listés sur la page de connexion. Pour le connecteur générique Common Event Format, utilisez ces paramètres :
         - Protocol = UDP
         - Port = 514
         - Facility = Local-4
         - Format = CEF
   - Choisissez **Manual deployment** (Déploiement manuel) si vous souhaitez utiliser une machine virtuelle existante comme ordinateur Linux dédié sur lequel l’agent Azure Sentinel doit être installé. 
      1. Sous **Download and install the Syslog agent** (Télécharger et installer l’agent Syslog), sélectionnez **Azure Linux virtual machine** (Machine virtuelle Linux Azure). 
      1. Sur l’écran **Virtual machines** (Machines virtuelles) qui s’affiche, sélectionnez l’ordinateur que vous souhaitez utiliser, puis cliquez sur **Connect** (Se connecter).
      1. Sur l’écran de connecteur, sous **Configure and forward Syslog** (Configurer et transférer Syslog), définissez si votre démon Syslog est **rsyslog.d** ou **syslog-ng**. 
      1. Copiez ces commandes et exécutez-les sur votre appliance :
          - Si vous avez sélectionné rsyslog.d :
              
            1. Demandez au démon Syslog d’écouter sur la fonctionnalité local_4 et d’envoyer les messages Syslog à l’agent Azure Sentinel en utilisant le port 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. Téléchargez et installez le [fichier de configuration security_events](https://aka.ms/asi-syslog-config-file-linux) qui configure l’agent Syslog pour écouter sur le port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`, où {0} doit être remplacé par le GUID de votre espace de travail.
            
            1. Redémarrez le démon Syslog `sudo service rsyslog restart`<br> Pour plus d’informations, voir la [documentation de rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
           
          - Si vous avez sélectionné syslog-ng :

              1. Demandez au démon Syslog d’écouter sur la fonctionnalité local_4 et d’envoyer les messages Syslog à l’agent Azure Sentinel en utilisant le port 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Téléchargez et installez le [fichier de configuration security_events](https://aka.ms/asi-syslog-config-file-linux) qui configure l’agent Syslog pour écouter sur le port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`, où {0} doit être remplacé par le GUID de votre espace de travail.

              3. Redémarrez le démon Syslog `sudo service syslog-ng restart` <br>Pour plus d’informations, voir la [documentation de syslog-ng](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2)
      2. Redémarrez l’agent Syslog à l’aide de cette commande : `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Confirmez qu’il n’existe aucune erreur dans le journal de l’agent en exécutant cette commande : `tail /var/opt/microsoft/omsagent/log/omsagent.log`

 Pour utiliser le schéma pertinent dans Log Analytics pour les événements CEF, recherchez `CommonSecurityLog`.


### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Déployer l’agent sur un serveur Linux local

Si vous n’utilisez pas Azure, déployez manuellement l’agent Azure Sentinel pour l’exécuter sur un serveur Linux dédié.


1. Dans le portail Azure Sentinel, cliquez sur **Data Connectors** (Connecteurs de données) et sélectionnez le type de votre appliance.
1. Pour créer une machine virtuelle Linux dédiée, sous **Linux Syslog agent configuration** (Configuration de l’agent Linux Syslog), choisissez **Manual deployment** (Déploiement manuel).
   1. Sous **Download and install the Syslog agent** (Télécharger et installer l’agent Syslog), sélectionnez **Non-Azure Linux machine** (Machine virtuelle non-Azure). 
   1. Sur l’écran **Direct Agent** qui s’affiche, sélectionnez **Agent for Linux** (Agent pour Linux) pour télécharger l’agent, ou exécutez cette commande pour le télécharger sur votre ordinateur Linux :   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. Sur l’écran de connecteur, sous **Configure and forward Syslog** (Configurer et transférer Syslog), définissez si votre démon Syslog est **rsyslog.d** ou **syslog-ng**. 
      1. Copiez ces commandes et exécutez-les sur votre appliance :
         - Si vous avez sélectionné rsyslog :
           1. Demandez au démon Syslog d’écouter sur la fonctionnalité local_4 et d’envoyer les messages Syslog à l’agent Azure Sentinel en utilisant le port 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. Téléchargez et installez le [fichier de configuration security_events](https://aka.ms/asi-syslog-config-file-linux) qui configure l’agent Syslog pour écouter sur le port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`, où {0} doit être remplacé par le GUID de votre espace de travail.
           3. Redémarrez le démon Syslog `sudo service rsyslog restart`
         - Si vous avez sélectionné syslog-ng :
            1. Demandez au démon Syslog d’écouter sur la fonctionnalité local_4 et d’envoyer les messages Syslog à l’agent Azure Sentinel en utilisant le port 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. Téléchargez et installez le [fichier de configuration security_events](https://aka.ms/asi-syslog-config-file-linux) qui configure l’agent Syslog pour écouter sur le port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`, où {0} doit être remplacé par le GUID de votre espace de travail.
            3. Redémarrez le démon Syslog `sudo service syslog-ng restart`
      1. Redémarrez l’agent Syslog à l’aide de cette commande : `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Confirmez qu’il n’existe aucune erreur dans le journal de l’agent en exécutant cette commande : `tail /var/opt/microsoft/omsagent/log/omsagent.log`
  
 Pour utiliser le schéma pertinent dans Log Analytics pour les événements CEF, recherchez `CommonSecurityLog`.

## <a name="step-2-forward-common-event-format-cef-logs-to-syslog-agent"></a>Étape 2 : Transférer les journaux d’activité au format CEF vers l'agent Syslog

Définissez votre solution de sécurité pour envoyer des messages Syslog au format CEF à votre agent Syslog. Assurez-vous d'utiliser les mêmes paramètres que ceux qui apparaissent dans la configuration de votre agent. Ils correspondent généralement à ceux-ci :

- Port 514
- Facility local4

## <a name="step-3-validate-connectivity"></a>Étape 3 : Valider la connectivité

L’affichage de vos journaux dans Log Analytics peut prendre plus de 20 minutes. 

1. Vérifiez que vous utilisez la fonctionnalité correcte. Elle doit être identique dans votre appliance et dans Azure Sentinel. Vous pouvez vérifier quel fichier de fonctionnalité vous utilisez dans Azure Sentinel et le modifier dans le fichier `security-config-omsagent.conf`. 

2. Vérifiez que vos journaux utilisent le port correct sur l’agent Syslog. Exécutez cette commande sur l’ordinateur de l’agent Syslog : `tcpdump -A -ni any  port 514 -vv` Cette commande affiche les journaux qui sont diffusés de l’appareil vers l’ordinateur Syslog. Vérifiez que les journaux sont reçus en provenance de l’appliance source sur le port et la fonctionnalité adéquats.

3. Vérifiez que les journaux que vous envoyez sont conformes à la [RFC 5424](https://tools.ietf.org/html/rfc542).

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

8. Vérifiez que la taille par défaut des messages Syslog est limitée à 2 048 octets (2 Ko). Si les journaux sont trop longs, mettez à jour le fichier security_events.conf à l’aide de cette commande : `message_length_limit 4096`


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les appliances CEF à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).

