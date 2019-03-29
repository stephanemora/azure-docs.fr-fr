---
title: Collecter des données de Palo Alto Networks Sentinel version préliminaire d’Azure | Microsoft Docs
description: Découvrez comment collecter des données de Palo Alto Networks dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: bcab43869b488eed1cc6693a2970b6ea4bb2e7c1
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576341"
---
# <a name="connect-your-palo-alto-networks-appliance"></a>Connecter votre application Palo Alto Networks

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez vous connecter Azure Sentinel vers n’importe quel appareil Palo Alto Networks en enregistrant les fichiers journaux sous Syslog CEF. L’intégration avec Azure Sentinel permet d’exécuter facilement analytique et des requêtes sur les données du fichier journal de Palo Alto Networks. Pour plus d’informations sur la façon dont Azure Sentinel ingère les données de format CEF, consultez [appliances de connecter le format CEF](connect-common-event-format.md).

> [!NOTE]
> Données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="step-1-connect-your-palo-alto-networks-appliance-using-an-agent"></a>Étape 1 : Connecter votre application Palo Alto Networks à l’aide d’un agent

Pour connecter votre application Palo Alto Networks Sentinel Azure, vous devez déployer un agent sur un ordinateur dédié (machine virtuelle ou en local) pour prendre en charge la communication entre l’appliance et Sentinel Azure. Vous pouvez déployer l’agent manuellement ou automatiquement. Le déploiement automatique n’est disponible que si votre machine dédiée est une nouvelle machine virtuelle que vous créez dans Azure. 

Vous pouvez également déployer l’agent manuellement sur une machine virtuelle Azure existante, sur une machine virtuelle dans un autre cloud, ou sur un ordinateur local.

Pour afficher un diagramme de réseau des deux options, consultez [connecter des sources de données](connect-data-sources.md#agent-options).


### <a name="deploy-the-agent-in-azure"></a>Déployer l’agent dans Azure

1. Dans le portail Azure Sentinel, cliquez sur **collecte des données** et sélectionnez le type de votre appliance. 

1. Sous **configuration de l’agent Linux Syslog**:
   - Choisissez **déploiement automatique** si vous souhaitez créer un nouvel ordinateur qui est préinstallé avec l’agent Sentinel Azure et inclut tous les besoins de configuration, comme décrit ci-dessus. Sélectionnez **déploiement automatique** et cliquez sur **déploiement d’agent automatique**. Vous accédez à la page d’achat pour une machine virtuelle dédiée qui est automatiquement connecté à votre espace de travail, est. La machine virtuelle est un **standard D2s v3 (2 processeurs virtuels, 8 Go de mémoire)** et a une adresse IP publique.
      1. Dans le **déploiement personnalisé** page, fournir vos informations et choisissez un nom d’utilisateur et un mot de passe et si vous acceptez les termes et conditions, achetez la machine virtuelle.
      1. Configurez votre appliance pour envoyer des journaux en utilisant les paramètres répertoriés dans la page de connexion. Pour le connecteur générique Common Event Format, utilisez ces paramètres :
         - Protocol = UDP
         - Port = 514
         - Fonctionnalité = 4-Local
         - Format = CEF
   - Choisissez **déploiement manuel** si vous souhaitez utiliser une machine virtuelle existante en tant que l’ordinateur Linux dédié sur lequel l’agent Sentinel Azure doit être installé. 
      1. Sous **télécharger et installer l’agent Syslog**, sélectionnez **machine virtuelle Linux Azure**. 
      1. Dans le **machines virtuelles** écran qui s’ouvre, sélectionnez l’ordinateur que vous souhaitez utiliser, cliquez sur **Connect**.
      1. Dans l’écran de connecteur, sous **Syslog vers l’avant et configurer**, définissez si votre démon Syslog est **rsyslog.d** ou **syslog-ng**. 
      1. Copiez ces commandes et les exécuter sur votre appliance :
          - Si vous avez sélectionné rsyslog.d :
              
            1. Indiquer le démon Syslog pour écouter sur la fonctionnalité local_4 et envoyer les messages Syslog à l’agent Sentinel Azure à l’aide du port 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. Téléchargez et installez le [security_events le fichier de configuration](https://aka.ms/asi-syslog-config-file-linux) qui configure l’agent Syslog pour écouter sur le port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Où {0} doit être remplacé par votre GUID de l’espace de travail.
            
            1. Redémarrer le démon syslog `sudo service rsyslog restart`
             
          - Si vous avez sélectionné syslog-ng :

              1. Indiquer le démon Syslog pour écouter sur la fonctionnalité local_4 et envoyer les messages Syslog à l’agent Sentinel Azure à l’aide du port 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Téléchargez et installez le [security_events le fichier de configuration](https://aka.ms/asi-syslog-config-file-linux) qui configure l’agent Syslog pour écouter sur le port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Où {0} doit être remplacé par votre GUID de l’espace de travail.

              3. Redémarrer le démon syslog `sudo service syslog-ng restart`
      2. Redémarrez l’agent Syslog à l’aide de cette commande : `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Confirmer qu’il n’existe aucune erreur dans le journal de l’agent en exécutant cette commande : `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Déployer l’agent sur un serveur Linux locaux

Si vous n’utilisez pas Azure, déployer manuellement l’agent Sentinel Azure s’exécute sur un serveur Linux dédié.


1. Dans le portail Azure Sentinel, cliquez sur **collecte des données** et sélectionnez le type de votre appliance.
1. Pour créer une VM Linux dédié, sous **configuration de l’agent Linux Syslog** choisissez **déploiement manuel**.
   1. Sous **télécharger et installer l’agent Syslog**, sélectionnez **machine Non-Azure Linux**. 
   1. Dans le **agent Direct** écran qui s’affiche, sélectionnez **Agent pour Linux** pour télécharger l’agent ou exécutez cette commande pour le télécharger sur votre ordinateur Linux :   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. Dans l’écran de connecteur, sous **Syslog vers l’avant et configurer**, définissez si votre démon Syslog est **rsyslog.d** ou **syslog-ng**. 
      1. Copiez ces commandes et les exécuter sur votre appliance :
         - Si vous avez sélectionné rsyslog :
           1. Indiquer le démon Syslog pour écouter sur la fonctionnalité local_4 et envoyer les messages Syslog à l’agent Sentinel Azure à l’aide du port 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. Téléchargez et installez le [security_events le fichier de configuration](https://aka.ms/asi-syslog-config-file-linux) qui configure l’agent Syslog pour écouter sur le port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Où {0} doit être remplacé par votre GUID de l’espace de travail.
           3. Redémarrer le démon syslog `sudo service rsyslog restart`
         - Si vous avez sélectionné syslog-ng :
            1. Indiquer le démon Syslog pour écouter sur la fonctionnalité local_4 et envoyer les messages Syslog à l’agent Sentinel Azure à l’aide du port 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. Téléchargez et installez le [security_events le fichier de configuration](https://aka.ms/asi-syslog-config-file-linux) qui configure l’agent Syslog pour écouter sur le port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Où {0} doit être remplacé par votre GUID de l’espace de travail.
            3. Redémarrer le démon syslog `sudo service syslog-ng restart`
      1. Redémarrez l’agent Syslog à l’aide de cette commande : `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Confirmer qu’il n’existe aucune erreur dans le journal de l’agent en exécutant cette commande : `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Étape 2 : Transférer les journaux de Palo Alto Networks à l’agent Syslog

Configurer Palo Alto Networks pour transférer les messages Syslog au format CEF à votre espace de travail Azure par le biais de l’agent Syslog :
1.  Accédez à [Guides de Configuration de Format d’événement commun (CEF)](https://docs.paloaltonetworks.com/resources/cef) et téléchargez le fichier pdf pour votre type d’appliance. Suivez les instructions dans le guide de configuration de votre appliance Palo Alto Networks à collecter les événements du format CEF. 

1.  Accédez à [Syslog configurer surveillance](https://aka.ms/asi-syslog-paloalto-forwarding) et suivez les étapes 2 et 3 pour configurer le transfert d’événements CEF à partir de votre appliance Palo Alto Networks pour Azure Sentinel.

    1. Veillez à définir le **Syslog de format de serveur** à **BSD**.
    1. Veillez à définir le **numéro établissement** sur la même valeur que vous définissez dans l’agent Syslog.

> [!NOTE]
> Les opérations de copier/coller à partir du fichier PDF peuvent modifier le texte et insérer des caractères aléatoires. Pour éviter ce problème, copiez le texte dans un éditeur et supprimer tous les caractères qui risquent du format du journal avant de le coller, comme vous pouvez le voir dans cet exemple.
 
  ![Problème de copie de texte CEF](./media/connect-cef/paloalto-text-prob1.png)

## <a name="step-3-validate-connectivity"></a>Étape 3 : Valider la connectivité

Il peut prendre plus de 20 minutes jusqu'à ce que vos journaux commencent à apparaître dans le journal Analytique. 

1. Assurez-vous que vos journaux sont bien au port de droite de l’agent Syslog. Exécutez cette commande à l’ordinateur d’agent Syslog : `tcpdump -A -ni any  port 514 -vv` Cette commande affiche les fichiers journaux qui diffuse en continu à partir de l’appareil sur l’ordinateur de Syslog. Assurez-vous que les journaux sont reçus à partir de l’appliance source sur le port de droite et la facilité de droite.
2. Vérifiez qu’il existe une communication entre le démon Syslog et l’agent. Exécutez cette commande à l’ordinateur d’agent Syslog : `tcpdump -A -ni any  port 25226 -vv` Cette commande affiche les fichiers journaux qui diffuse en continu à partir de l’appareil sur l’ordinateur de Syslog. Assurez-vous que les journaux sont également reçus sur l’agent.
3. Si les deux de ces commandes fourni les résultats réussis, vérifiez l’Analytique de journal pour voir si vos journaux arrivent. Tous les événements transmis en continu à partir de ces appareils apparaissent sous une forme brute dans Analytique de journal sous `CommonSecurityLog ` type.
1. Pour vérifier s’il existe des erreurs ou si les journaux ne sont pas reçues, Regarder dans `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
4. Assurez-vous que votre taille par défaut des messages Syslog est limitée à 2 048 octets (2 Ko). Si les journaux sont trop longs, mettez à jour le security_events.conf à l’aide de cette commande : `message_length_limit 4096`
6. Pour utiliser le schéma pertinent dans Analytique de journal pour les événements de Palo Alto Networks, recherchez **CommonSecurityLog**.







## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les appareils Palo Alto Networks pour Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).

