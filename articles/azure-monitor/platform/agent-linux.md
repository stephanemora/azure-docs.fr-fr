---
title: Connecter des ordinateurs Linux à Azure Monitor | Microsoft Docs
description: Cet article décrit la connexion d’ordinateurs Linux hébergés dans d’autres clouds ou localement à Azure Monitor avec l’agent Log Analytics pour Linux.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: 40c279a4beee9fbebe2de7f272fe51d9039f071c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668704"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Connecter des ordinateurs Linux à Azure Monitor

Pour surveiller et gérer des machines virtuelles ou des ordinateurs physiques dans votre centre de données local ou dans un environnement cloud avec Azure Monitor, vous devez déployer l’agent Log Analytics et le configurer pour qu’il rende compte à un espace de travail Log Analytics. L’agent prend également en charge le rôle Runbook Worker hybride pour Azure Automation.

L'agent Log Analytics pour Linux peut être installé en utilisant l'une des méthodes suivantes. Des détails sur l’utilisation de chaque méthode sont fournis plus loin dans l’article.

* [Téléchargez et installez manuellement](#install-the-agent-manually) l’agent. Ceci est nécessaire lorsque l’ordinateur Linux n'a pas accès à Internet et communiquera avec Azure Monitor ou Azure Automation via la [passerelle Log Analytics](gateway.md). 
* [Installez l’agent pour Linux à l’aide d’un script wrapper](#install-the-agent-using-wrapper-script) hébergé sur GitHub. Il s’agit de la méthode recommandée pour installer et mettre à niveau l'agent lorsque l'ordinateur dispose d’une connexion Internet, directement ou via un serveur proxy.

Pour comprendre la configuration prise en charge, consultez la page [Prise en charge des systèmes d’exploitation Linux](log-analytics-agent.md#supported-linux-operating-systems) et [Configuration du pare-feu réseau](log-analytics-agent.md#network-firewall-requirements).

>[!NOTE]
>L’agent Log Analytics pour Linux ne peut pas être configuré pour envoyer des rapports à plus d’un espace de travail Log Analytics. Il ne peut être configuré que pour envoyer des rapports à un groupe de gestion System Center Operations Manager et à l'espace de travail Log Analytics, simultanément ou individuellement.

## <a name="agent-install-package"></a>Package d’installation de l’agent

L'agent Log Analytics pour Linux comprend plusieurs packages. Le fichier de la version contient les packages suivants, disponibles en exécutant l’application shell avec le paramètre `--extract` :

**Package** | **Version** | **Description**
----------- | ----------- | --------------
omsagent | 1.12.15 | Agent Log Analytics pour Linux
omsconfig | 1.1.1 | Agent de configuration pour l'agent Log Analytics
omi | 1.6.3 | Open Management Infrastructure (OMI) -- Serveur CIM léger. *Notez qu'OMI a besoin d'un accès root pour exécuter une tâche cron nécessaire au fonctionnement du service*
scx | 1.6.3 | Fournisseurs CIM OMI pour les mesures de performances des systèmes d’exploitation
apache-cimprov | 1.0.1 | Fournisseur de surveillance des performances d’Apache HTTP Server pour OMI. Installé uniquement si Apache HTTP Server est détecté.
mysql-cimprov | 1.0.1 | Fournisseur de surveillance des performances de MySQL Server pour OMI. Installé uniquement si MySQL/MariaDB Server est détecté.
docker-cimprov | 1.0.0 | Fournisseur de Docker pour OMI. Installé uniquement si Docker est détecté.

### <a name="agent-installation-details"></a>Détails de l’installation de l’agent

Après avoir installé les packages de l’agent Log Analytics pour Linux, les modifications de configuration système suivantes sont appliquées. Ces artefacts sont supprimés lorsque le package omsagent est désinstallé.

* Un utilisateur sans privilège nommé `omsagent` est créé. Le démon est exécuté avec ces informations d’identification. 
* Un fichier sudoers *include* est créé dans `/etc/sudoers.d/omsagent`. Cela autorise `omsagent` à redémarrer les démons syslog et omsagent. Si les directives *include* sudo ne sont pas prises en charge dans la version installée de sudo, ces entrées sont inscrites dans `/etc/sudoers`.
* La configuration de syslog est modifiée pour transférer un sous-ensemble d’événements à l’agent. Pour plus d'informations, voir [Configurer la collecte de données Syslog](data-sources-syslog.md).

Sur un ordinateur Linux supervisé, l'agent apparaît comme `omsagent`. `omsconfig` est l’agent de configuration de l’agent Log Analytics pour Linux qui cherche la nouvelle configuration côté portail toutes les 5 minutes. La nouvelle configuration mise à jour est appliquée aux fichiers de configuration de l’agent, situés dans `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

## <a name="obtain-workspace-id-and-key"></a>Obtenir l’ID et la clé d’espace de travail

Avant d’installer l’agent Log Analytics pour Linux, vous devez disposer de l’ID et de la clé d’espace de travail pour votre espace de travail Log Analytics. Ces informations sont nécessaires pour configurer correctement l’agent et faire en sorte qu’il puisse communiquer avec Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. En haut à gauche dans le portail Azure, sélectionnez **Tous les services**. Dans la zone de recherche, entrez **Log Analytics**. Lorsque vous écrivez, au fur et à mesure de votre saisie, la liste est filtrée. Sélectionnez **Espaces de travail Log Analytics**.

2. Dans votre liste d’espaces de travail Log Analytics, sélectionnez l’espace de travail créé précédemment. (Vous l’avez peut-être nommé **DefaultLAWorkspace**.)

3. Sélectionnez **Paramètres avancés** :

    ![Menu Paramètres avancés pour Log Analytics dans le portail Azure](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Sélectionnez **Sources connectées**, puis **Serveurs Linux**.

5. Des valeurs figurent à droite d’**ID de l’espace de travail** et de **Clé primaire**. Copiez-collez ces deux valeurs dans votre éditeur favori.

## <a name="install-the-agent-manually"></a>Installation de l’agent manuellement

L’agent Log Analytics pour Linux est fourni dans un groupe de scripts shell à extraction automatique et pouvant être installé. Ce groupe contient des packages Debian et RPM pour chacun des composants de l’agent et peut être installé directement ou être extrait pour récupérer les packages individuels. Un groupe est fourni pour les architectures x64 et un autre l’est pour les architectures x86. 

> [!NOTE]
> Pour les machines virtuelles Azure, nous vous recommandons d'installer l'agent sur ces machines en utilisant l'[extension de machine virtuelle Azure Log Analytics](../../virtual-machines/extensions/oms-linux.md) pour Linux. 

1. [Téléchargez](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) et transférez le bundle approprié (x64 ou x86) sur votre machine virtuelle ou ordinateur physique Linux à l’aide de scp/sftp.

2. Installez le groupe à l’aide de l’argument `--install`. Pour intégrer un espace de travail Log Analytics pendant l'installation, spécifiez les paramètres `-w <WorkspaceID>` et `-s <workspaceKey>` copiés précédemment.

    >[!NOTE]
    >Vous devez utiliser l'argument `--upgrade` si des packages dépendants tels que omi, scx, omsconfig ou leurs anciennes versions sont installés, comme ce serait le cas si l'agent System Center Operations Manager pour Linux est déjà installé. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Pour configurer l'agent Linux afin de l’installer et de le connecter à un espace de travail Log Analytics via une passerelle Log Analytics, exécutez la commande suivante en spécifiant le proxy, l'ID de l'espace de travail et les paramètres clés de l'espace de travail. Cette configuration peut être spécifiée sur la ligne de commande en incluant `-p [protocol://][user:password@]proxyhost[:port]`. La propriété *proxyhost* accepte un nom de domaine complet ou l’adresse IP du serveur de passerelle Log Analytics.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Si une authentification est requise, vous devez spécifier le nom d'utilisateur et le mot de passe. Par exemple : 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Pour configurer l’ordinateur Linux en vue d’une connexion à un espace de travail Log Analytics dans le cloud Azure Government, exécutez la commande suivante en fournissant l’ID d’espace de travail et la clé primaire copiés précédemment.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Si vous souhaitez installer les packages d'agents et les configurer pour qu’ils génèrent ultérieurement des rapports sur un espace de travail Log Analytics spécifique, exécutez la commande suivante :

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Si vous souhaitez extraire les packages de l’agent du bundle sans installer l’agent, exécutez la commande suivante :

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>Installer l’agent en utilisant le script du wrapper

Les étapes suivantes configurent l'agent pour Log Analytics dans Azure et dans Azure Government Cloud en utilisant le script du wrapper pour les ordinateurs Linux qui peuvent communiquer directement ou via un serveur proxy pour télécharger puis installer l'agent hébergé sur GitHub.  

Si votre ordinateur Linux a besoin d’un serveur proxy pour communiquer avec Log Analytics, cette configuration peut être spécifiée sur la ligne de commande en incluant `-p [protocol://][user:password@]proxyhost[:port]`. La propriété *protocol* accepte `http` ou `https`, et la propriété *proxyhost* accepte un nom de domaine complet ou l’adresse IP du serveur proxy. 

Par exemple : `https://proxy01.contoso.com:30443`

Si l'authentification est requise dans l'un ou l'autre cas, vous devez spécifier le nom d'utilisateur et le mot de passe. Par exemple : `https://user01:password@proxy01.contoso.com:30443`

1. Pour configurer l’ordinateur Linux en vue d’une connexion à un espace de travail Log Analytics, exécutez la commande suivante en fournissant l’ID d’espace de travail et la clé primaire. La commande suivante télécharge l’agent, valide sa somme de contrôle et l’installe.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    La commande suivante contient le paramètre `-p` proxy et un exemple de syntaxe quand l’authentification est requise par votre serveur proxy :

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Pour configurer l’ordinateur Linux en vue d’une connexion à un espace de travail Log Analytics dans le cloud Azure Government, exécutez la commande suivante en fournissant l’ID d’espace de travail et la clé primaire copiés précédemment. La commande suivante télécharge l’agent, valide sa somme de contrôle et l’installe. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    La commande suivante contient le paramètre `-p` proxy et un exemple de syntaxe quand l’authentification est requise par votre serveur proxy :

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Redémarrez l’agent en exécutant la commande suivante : 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>Mettre à niveau à partir d’une version antérieure

La mise à niveau à partir d'une version antérieure, à compter de la version 1.0.0-47, est prise en charge dans chaque version. Effectuez l’installation avec le paramètre `--upgrade` pour mettre à niveau tous les composants de l’agent vers la dernière version.

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Gestion et maintenance de l’agent Log Analytics pour Windows et Linux](agent-manage.md) pour en savoir plus sur la manière de reconfigurer, mettre à niveau ou supprimer l’agent de la machine virtuelle.

- Consultez [Résolution des problèmes de l’agent Linux](agent-linux-troubleshoot.md) si vous rencontrez des problèmes lors de l’installation ou de gestion de l’agent.
