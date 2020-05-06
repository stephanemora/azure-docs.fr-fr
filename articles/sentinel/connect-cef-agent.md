---
title: Déployer le redirecteur de journal pour connecter des données CEF à Azure Sentinel | Microsoft Docs
description: Découvrez comment déployer l’agent pour connecter des données CEF à Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: 5a8b97e5bef57b29f388c86628f0af5d05e1724a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731655"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Étape 1 : Déployer le redirecteur de journal


Au cours de cette étape, vous allez désigner et configurer la machine Linux qui transférera les journaux de votre solution de sécurité vers votre espace de travail Azure Sentinel. Cet ordinateur peut être une machine physique ou virtuelle dans votre environnement local, une machine virtuelle Azure ou une machine virtuelle dans un autre cloud. À l’aide du lien fourni, vous allez exécuter un script sur l’ordinateur désigné qui effectue les tâches suivantes :
- Installe l’agent Log Analytics pour Linux (également appelé agent OMS) et le configure aux fins suivantes :
    - l’écoute des messages CEF à partir du démon Syslog Linux intégré sur le port TCP 25226
    - l’envoi sécurisé des messages via TLS à votre espace de travail Azure Sentinel, où ils sont analysés et enrichis

- Configure le démon Syslog Linux intégré (rsyslog.d/syslog-ng) aux fins suivantes :
    - l’écoute des messages Syslog de vos solutions de sécurité sur le port TCP 514
    - le transfert des seuls messages qu’il identifie comme CEF à l’agent Log Analytics sur localhost à l’aide du port TCP 25226
 
## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations élevées (sudo) sur votre machine Linux désignée.
- Python doit être installé sur la machine Linux.<br>Utilisez la commande `python -version` pour vérifier.
- La machine Linux ne doit être connectée à aucun espace de travail Azure avant l’installation de l’agent Log Analytics.

## <a name="run-the-deployment-script"></a>Exécuter le script de déploiement
 
1. Dans le menu de navigation d’Azure Sentinel, cliquez sur **Connecteurs de données**. Dans la liste des connecteurs, cliquez sur la mosaïque **Common Event Format (CEF)** , puis sur le bouton **Ouvrir la page du connecteur** dans le coin inférieur droit. 

1. Sous **1.2 Installer le collecteur CEF sur la machine Linux**, copiez le lien fourni sous **Exécuter le script suivant pour installer et appliquer le collecteur CEF** ou à partir du texte ci-dessous :

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Pendant l’exécution du script, vérifiez que vous ne recevez pas de messages d’erreur ou d’avertissement.

Passez à [l’ÉTAPE 2 : Configurez votre solution de sécurité pour transférer des messages CEF](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Explication du script de déploiement

Voici une description commande par commande des actions du script de déploiement.

Choisissez un démon Syslog pour afficher la description appropriée.

# <a name="rsyslog-daemon"></a>[démon rsyslog](#tab/rsyslog)

1. **Téléchargement et installation de l’agent Log Analytics :**

    - Télécharge le script d’installation pour l’agent Linux Log Analytics (OMS)<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Installe l’agent Log Analytics<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Configuration du démon Syslog :**

    1. Ouvre le port 514 pour la communication TCP à l’aide du fichier config Syslog `/etc/rsyslog.conf`.

    1. Configure le démon pour transférer les messages CEF à l’agent Log Analytics sur le port TCP 25226, en insérant un fichier config spécial `security-config-omsagent.conf` dans le répertoire Syslog `/etc/rsyslog.d/` du démon.

        Contenu du fichier `security-config-omsagent.conf` :

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. **Redémarrage du démon Syslog** :

    `service rsyslog restart`

1. **Paramétrage de la configuration de l’agent Log Analytics pour écouter sur le port 25226 et transférer les messages CEF vers Azure Sentinel** :

    1. Télécharge la configuration à partir du référentiel GitHub de l’agent Log Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Redémarre l’agent Log Analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[démon syslog-ng](#tab/syslogng)

1. **Téléchargement et installation de l’agent Log Analytics :**

    - Télécharge le script d’installation pour l’agent Linux Log Analytics (OMS)<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Installe l’agent Log Analytics<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Configuration du démon Syslog :**

    1. Ouvre le port 514 pour la communication TCP à l’aide du fichier config Syslog `/etc/syslog-ng/syslog-ng.conf`.

    1. Configure le démon pour transférer les messages CEF à l’agent Log Analytics sur le port TCP 25226, en insérant un fichier config spécial `security-config-omsagent.conf` dans le répertoire Syslog `/etc/syslog-ng/conf.d/` du démon.

        Contenu du fichier `security-config-omsagent.conf` :

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **Redémarrage du démon Syslog** :

    `service syslog-ng restart`

1. **Paramétrage de la configuration de l’agent Log Analytics pour écouter sur le port 25226 et transférer les messages CEF vers Azure Sentinel** :

    1. Télécharge la configuration à partir du référentiel GitHub de l’agent Log Analytics<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Redémarre l’agent Log Analytics<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à déployer l’agent Log Analytics pour connecter des appliances CEF à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).

