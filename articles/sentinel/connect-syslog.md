---
title: Connecter des données Syslog à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données Syslog à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: rkarlin
ms.openlocfilehash: d5f3d24d10262f28023523668c22f4571799cff9
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610469"
---
# <a name="connect-your-external-solution-using-syslog"></a>Connectez votre solution externe à l’aide de Syslog

Vous pouvez connecter n’importe quelle appliance locale prenant en charge Syslog à Azure Sentinel. C’est possible via un agent basé sur une machine Linux entre l’appliance et Azure Sentinel. Si votre machine Linux est dans Azure, vous pouvez diffuser les journaux de votre appliance ou application vers un espace de travail dédié que vous créez dans Azure, avant de le connecter. Si votre machine Azure n’est pas dans Azure, vous pouvez diffuser les journaux de votre appliance vers une machine virtuelle locale dédiée sur laquelle vous installez l’Agent pour Linux. 

> [!NOTE]
> Si votre appliance prend en charge Syslog CEF, la connexion est plus complète et vous devez choisir cette option et suivre les instructions présentes dans [Connexion des données à partir de CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Fonctionnement

Syslog est un protocole de journalisation d’événements commun à Linux. Les applications envoient les messages qui peuvent être stockés sur l’ordinateur local ou remis à un collecteur Syslog. Lorsque l’agent Log Analytics pour Linux est installé, il configure le démon Syslog local pour qu’il transfère des messages à l’agent. L’agent envoie ensuite le message à Azure Monitor, où un enregistrement correspondant est créé.

Pour en savoir plus, voir [Sources de données Syslog dans Azure Monitor](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> - L’agent peut collecter les journaux à partir de plusieurs sources, mais doit être installé sur une machine proxy dédiée.
> - Si vous souhaitez prendre en charge des connecteurs à la fois pour CEF et Syslog sur la même machine virtuelle, effectuez les étapes suivantes pour éviter de dupliquer des données :
>    1. Suivez les instructions pour [connecter votre CEF](connect-common-event-format.md).
>    2. Pour connecter les données Syslog, accédez à **Paramètres** > **Paramètres de l’espace de travail** > **Paramètres avancés** > **Données** > **Syslog** et définissez les fonctionnalités et leurs priorités afin qu’elles ne soient pas les mêmes que celles que vous avez utilisées dans votre configuration CEF. <br></br>Si vous sélectionnez **Appliquer la configuration ci-dessous à mes machines**, ces paramètres sont appliqués à toutes les machines virtuelles connectées à cet espace de travail.


## <a name="connect-your-syslog-appliance"></a>Connecter votre appliance Syslog

1. Dans Azure Sentinel, sélectionnez **Connecteurs de données**, puis le connecteur **Syslog**.

2. Dans le panneau **Syslog**, sélectionnez **Ouvrir la page du connecteur**.

3. Installez l’agent Linux :
    
    - Si votre machine virtuelle Linux se trouve dans Azure, sélectionnez **Download and install agent on Azure Linux virtual machine** (Télécharger et installer l’agent sur la machine virtuelle Linux Azure). Dans le panneau **Machines virtuelles**, sélectionnez les machines virtuelles sur lesquelles installer l’agent, puis cliquez sur **Connecter**.
    - Si votre machine Linux ne se trouve pas dans Azure, sélectionnez **Download and install agent on Linux non-Azure machine** (Télécharger et installer l’agent sur la machine virtuelle Linux non-Azure). Dans le panneau **Direct agent** (Agent direct), copiez la commande **Download and onboard agent for Linux** (Télécharger et intégrer l’agent pour Linux), puis exécutez-la sur votre ordinateur. 
    
   > [!NOTE]
   > Veillez à configurer les paramètres de sécurité pour ces ordinateurs conformément à la stratégie de sécurité de votre organisation. Par exemple, vous pouvez configurer les paramètres réseau de sorte qu’ils soient conformes à la stratégie de sécurité réseau de votre organisation, puis modifier les ports et les protocoles dans le démon pour les adapter aux exigences de sécurité.

4. Sélectionnez **Open your workspace advanced settings configuration** (Ouvrir la configuration des paramètres avancés de votre espace de travail).

5. Dans le panneau **Paramètres avancés**, sélectionnez **Données** > **Syslog**. Ajoutez ensuite les installations du connecteur à collecter.
    
    Ajoutez les installations que votre appliance Syslog inclut dans ses en-têtes de journal. Vous pouvez voir cette configuration dans votre appliance Syslog dans **Syslog-d** dans le dossier `/etc/rsyslog.d/security-config-omsagent.conf` et dans **r-Syslog** de `/etc/syslog-ng/security-config-omsagent.conf`.
    
    Si vous souhaitez utiliser la détection de connexion SSH anormale avec les données que vous collectez, ajoutez **auth** et **authpriv**. Pour plus de détails, voir la [section suivante](#configure-the-syslog-connector-for-anomalous-ssh-login-detection).

6. Après avoir ajouté toutes les installations à surveiller et ajusté les options de gravité pour chacune d’elles, activez la case à cocher **Appliquer la configuration ci-dessous à mes machines**.

7. Sélectionnez **Enregistrer**. 

8. Sur votre appliance Syslog, assurez-vous que vous envoyez les installations que vous avez spécifiées.

9. Pour utiliser le schéma pertinent dans Azure Monitor pour les journaux Syslog, recherchez **Syslog**.

10. Vous pouvez utiliser la fonction Kusto décrite dans [Utilisation de fonctions dans les requêtes de journal Azure Monitor](../azure-monitor/log-query/functions.md) pour analyser vos messages Syslog. Vous pouvez ensuite les enregistrer sous la forme d’une nouvelle fonction de Log Analytics à utiliser comme nouveau type de données.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Configurer le connecteur Syslog pour la détection de connexion SSH anormale

> [!IMPORTANT]
> La détection de connexion SSH anormale est actuellement en préversion publique.
> Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel peut appliquer du Machine Learning aux données Syslog pour identifier une activité de connexion SSH (Secure Shell) anormale. Il s’agit entre autres des scénarios suivants :

- Voyage impossible : lorsque deux événements de connexion réussis se produisent à partir de deux emplacements où il est impossible de se trouver dans l’intervalle de temps des deux événements de connexion.
- Emplacement inattendu : l’emplacement à partir duquel un événement de connexion réussi s’est produit est suspect. Par exemple, l’emplacement n’a pas été observé récemment.
 
Cette détection nécessite une configuration spécifique du connecteur de données Syslog : 

1. Pour l’étape 5 de la procédure précédente, assurez- vous que **auth** et **authpriv** sont sélectionnés en tant qu’installations à surveiller. Conservez les paramètres par défaut pour les options de gravité, afin qu’elles soient toutes sélectionnées. Par exemple :
    
    > [!div class="mx-imgBorder"]
    > ![Installations requises pour la détection de connexion SSH anormale](./media/connect-syslog/facilities-ssh-detection.png)

2. Laissez suffisamment de temps pour la collecte des informations Syslog. Ensuite, accédez à **Azure Sentinel - Logs**, puis copiez et collez la requête suivante :
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    Modifiez l’**Intervalle de temps** si nécessaire, puis sélectionnez **Exécuter**.
    
    Si le résultat obtenu est égal à zéro, vérifiez la configuration du connecteur et que les ordinateurs analysés ont une activité de connexion réussie pour la période que vous avez spécifiée pour votre requête.
    
    Si le résultat est supérieur à zéro, vos données Syslog sont adaptées à la détection de connexion SSH anormale. Vous activez cette détection à partir de **Analytique** >  **Modèles de règle** > **Détection de connexion SSH anormale (préversion)** .

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les appliances locales Syslog à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.

