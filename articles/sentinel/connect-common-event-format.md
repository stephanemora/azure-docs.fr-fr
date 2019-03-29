---
title: Collecter des données CEF Sentinel version préliminaire d’Azure | Microsoft Docs
description: Découvrez comment collecter des données CEF dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: cbf5003b-76cf-446f-adb6-6d816beca70f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/26/2019
ms.author: rkarlin
ms.openlocfilehash: 0d3ecfed766f8a1ba558e0b0cd4fe6a27c33e441
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579629"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Connectez votre solution externe à l’aide du Format d’événement commun

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vous pouvez connecter Azure Sentinel avec une solution externe qui vous permet d’enregistrer les fichiers journaux dans Syslog. Si votre appliance vous permet d’enregistrer les journaux comme Syslog Common Event Format CEF (), l’intégration avec Azure Sentinel vous permet de facilement exécuter analytique et les requêtes sur les données.

> [!NOTE]
> 
> Données sont stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="how-it-works"></a>Fonctionnement

La connexion entre Azure Sentinel et votre appliance CEF a lieu en trois étapes :

1. Sur l’appliance, vous devez définir ces valeurs, afin que l’appliance envoie nécessaires se connecte au format nécessaire à l’agent Azure Sentinel Syslog. Vous pouvez modifier ces paramètres dans votre appliance, tant que vous aussi les modifiez dans le démon Syslog sur l’agent Sentinel Azure.
    - Protocol = UDP
    - Port = 514
    - Fonctionnalité = 4-Local
    - Format = CEF
2. L’agent Syslog collecte les données et l’envoie en toute sécurité à l’Analytique de journal, où il est analysé et enrichie.
3. L’agent stocke les données dans un espace de travail Analytique de journal, donc il peut être interrogé en fonction des besoins, à l’aide d’analytique, les règles de corrélation et les tableaux de bord.


## <a name="step-1-connect-to-your-cef-appliance-via-dedicated-azure-vm"></a>Étape 1 : Se connecter à votre appliance CEF par le biais de machine virtuelle Azure dédiée

Vous devez déployer un agent sur un ordinateur Linux dédié (machine virtuelle ou en local) pour prendre en charge la communication entre l’appliance et Sentinel Azure. Vous pouvez déployer l’agent manuellement ou automatiquement. Déploiement automatique est basé sur des modèles Resource Manager et peut être utilisé uniquement si votre ordinateur Linux dédié est une nouvelle machine virtuelle que vous créez dans Azure.

 ![CEF dans Azure](./media/connect-cef/cef-syslog-azure.png)

Vous pouvez également déployer l’agent manuellement sur une machine virtuelle Azure existante, sur une machine virtuelle dans un autre cloud, ou sur un ordinateur local. 

 ![CEF local](./media/connect-cef/cef-syslog-onprem.png)

### <a name="deploy-the-agent-in-azure"></a>Déployer l’agent dans Azure


1. Dans le portail Azure Sentinel, cliquez sur **collecte des données** et sélectionnez le type de votre appliance. 

1. Sous **configuration de l’agent Linux Syslog**:
   - Choisissez **déploiement automatique** si vous souhaitez créer un nouvel ordinateur qui est préinstallé avec l’agent Sentinel Azure et inclut tous les besoins de configuration, comme décrit ci-dessus. Sélectionnez **déploiement automatique** et cliquez sur **déploiement d’agent automatique**. Vous accédez à la page d’achat pour une VM Linux dédié qui est automatiquement connecté à votre espace de travail, est. La machine virtuelle est un **standard D2s v3 (2 processeurs virtuels, 8 Go de mémoire)** et a une adresse IP publique.
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
  
## <a name="step-2-validate-connectivity"></a>Étape 2 : Valider la connectivité

Il peut prendre plus de 20 minutes jusqu'à ce que vos journaux commencent à apparaître dans le journal Analytique. 

1. Assurez-vous que vos journaux sont bien au port de droite de l’agent Syslog. Exécutez cette commande à l’ordinateur d’agent Syslog : `tcpdump -A -ni any  port 514 -vv` Cette commande affiche les fichiers journaux qui diffuse en continu à partir de l’appareil sur l’ordinateur de Syslog. Assurez-vous que les journaux sont reçus à partir de l’appliance source sur le port de droite et la facilité de droite.
2. Vérifiez qu’il existe une communication entre le démon Syslog et l’agent. Exécutez cette commande à l’ordinateur d’agent Syslog : `tcpdump -A -ni any  port 25226 -vv` Cette commande affiche les fichiers journaux qui diffuse en continu à partir de l’appareil sur l’ordinateur de Syslog. Assurez-vous que les journaux sont également reçus sur l’agent.
3. Si les deux de ces commandes fourni les résultats réussis, vérifiez l’Analytique de journal pour voir si vos journaux arrivent. Tous les événements transmis en continu à partir de ces appareils apparaissent sous une forme brute dans Analytique de journal sous `CommonSecurityLog ` type.
1. Pour vérifier s’il existe des erreurs ou si les journaux ne sont pas reçues, Regarder dans `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
4. Assurez-vous que votre taille par défaut des messages Syslog est limitée à 2 048 octets (2 Ko). Si les journaux sont trop longs, mettez à jour le security_events.conf à l’aide de cette commande : `message_length_limit 4096`
6. Pour utiliser le schéma pertinent dans Analytique de journal pour les événements CEF, recherchez **CommonSecurityLog**.



## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter des appareils de CEF à Sentinel Azure. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).

