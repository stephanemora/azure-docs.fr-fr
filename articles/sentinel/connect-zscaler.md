---
title: Connecter des données Zscaler à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données Zscaler à Azure Sentinel.
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
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 45351cc29b2b7028863aff06ab5a511674604d6f
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048954"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Connecter Zscaler Internet Access à Azure Sentinel

> [!IMPORTANT]
> Le connecteur de données Zscaler dans Azure Sentinel est actuellement en préversion publique.
> Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article explique comment connecter votre appliance Zscaler Internet Access à Azure Sentinel. Le connecteur de données Zscaler vous permet de connecter facilement vos journaux Zscaler Internet Access (ZIA) à Azure Sentinel, de consulter des tableaux de bord, de créer des alertes personnalisées et d’améliorer l’investigation. En utilisant Zscaler sur Azure Sentinel, vous obtenez davantage d’insights sur l’usage d’Internet dans votre organisation, ce qui vous aide à améliorer la sécurité des opérations. 


## <a name="how-it-works"></a>Fonctionnement

Vous devez déployer un agent sur une machine Linux dédiée (machine virtuelle ou locale) qui prend en charge les communications entre Zscaler Internet Access et Azure Sentinel. Le diagramme suivant décrit la configuration dans le cas d’une machine virtuelle Linux dans Azure.

 ![CEF dans Azure](./media/connect-cef/cef-syslog-azure.png)

Cette configuration conviendra également si vous utilisez une machine virtuelle dans un autre cloud ou sur un ordinateur local. 

 ![CEF local](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Considérations relatives à la sécurité

Veillez à configurer la sécurité de la machine en fonction de la stratégie de sécurité de votre organisation. Par exemple, vous pouvez configurer votre réseau de sorte qu’il s’accorde à la stratégie de sécurité de votre réseau d’entreprise, et modifier les ports et les protocoles dans le démon pour les adapter à vos besoins. Vous pouvez utiliser les instructions suivantes pour améliorer la configuration de la sécurité de votre machine :  [Sécurisation de la machine virtuelle dans Azure](../virtual-machines/linux/security-policy.md), [Bonnes pratiques pour la sécurité réseau](../security/fundamentals/network-best-practices.md).

Pour utiliser la communication TLS entre la solution de sécurité et la machine Syslog, vous devez configurer le démon Syslog (rsyslog or syslog-ng) pour communiquer avec le TLS : [Chiffrement du trafic Syslog avec TLS –rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [Chiffrement des messages du journal avec TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Prérequis
Assurez-vous que la machine Linux que vous utilisez en tant que proxy exécute l’un des systèmes d’exploitation suivants :

- 64 bits
  - CentOS 6 et 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 et 7
  - Red Hat Enterprise Linux Server 6 et 7
  - Debian GNU/Linux 8 et 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS et 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32 bits
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 et 9
   - Ubuntu Linux 14.04 LTS et 16.04 LTS
 
 - Versions du démon
   - Syslog-ng : 2.1 - 3.22.1
   - Rsyslog : v8
  
 - RFC Syslog pris en charge
   - RFC Syslog 3164
   - RFC Syslog 5424
 
Assurez-vous que votre machine répond également aux exigences suivantes : 
- Autorisations
    - Vous devez disposer d’autorisations élevées (sudo) sur votre machine. 
- Configuration logicielle requise
    - Vérifiez que Python est en cours d’exécution sur votre machine
## <a name="step-1-deploy-the-agent"></a>ÉTAPE 1 : Déployer l’agent

Au cours de cette étape, vous devez sélectionner la machine Linux qui fera office de proxy entre Azure Sentinel et votre solution de sécurité. Vous devez exécuter un script sur la machine proxy qui :
- Installe l’agent Log Analytics et le configure en fonction des besoins pour écouter les messages Syslog sur le port 514 via TCP et envoyer les messages CEF à votre espace de travail Azure Sentinel.
- Configure le démon Syslog pour transférer les messages CEF à l’agent Log Analytics via le port 25226.
- Configure l’agent Syslog pour collecter les données et les envoyer de manière sécurisée à Log Analytics, où elles sont analysées et enrichies.
 
 
1. Dans le portail Azure Sentinel, cliquez sur **Data Connectors** (Connecteurs de données), puis sélectionnez **Zscaler** et **Open Connector Page** (Ouvrir la page du connecteur). 

1. Sous **Installer et configurer l’agent Syslog**, sélectionnez le type de votre machine (Azure, autre cloud ou locale). 
   > [!NOTE]
   > Étant donné que le script de l’étape suivante installe l’agent Log Analytics et connecte la machine à votre espace de travail Azure Sentinel, vérifiez que cette machine n’est pas connectée à un autre espace de travail.
1. Vous devez disposer d’autorisations élevées (sudo) sur votre machine. Vérifiez que vous disposez de Python sur votre machine à l’aide de la commande suivante : `python –version`

1. Exécutez le script suivant sur votre machine proxy.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Pendant l’exécution du script, vérifiez que vous ne recevez pas de messages d’erreur ou d’avertissement.


## <a name="step-2-configure-your-zscaler-to-send-cef-messages"></a>ÉTAPE 2 : Configurer votre appliance Zscaler pour envoyer des messages CEF

1. Sur l’appliance Zscaler, vous devez définir ces valeurs pour que l’appliance envoie les journaux nécessaires dans le format approprié à l’agent Azure Sentinel Syslog, en fonction de l’agent Log Analytics. Vous pouvez modifier ces paramètres dans votre appliance, à condition de les modifier également dans le démon Syslog sur l'agent Azure Sentinel.
    - Protocole = TCP
    - Port = 514
    - Format = CEF
    - Adresse IP : assurez-vous d’envoyer les messages CEF à l’adresse IP de la machine virtuelle dédiée à cet effet.
 Pour plus d’informations, consultez le [Guide de déploiement Zscaler et Azure Sentinel](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Cette solution prend en charge Syslog RFC 3164 ou RFC 5424.


1. Pour utiliser le schéma pertinent dans Log Analytics pour les événements CEF, recherchez `CommonSecurityLog`.

## <a name="step-3-validate-connectivity"></a>ÉTAPE 3 : Valider la connectivité

1. Ouvrez Log Analytics pour vous assurer que les journaux sont reçus à l’aide du schéma CommonSecurityLog.<br> Plus de 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics. 

1. Avant d’exécuter le script, nous vous recommandons d’envoyer des messages à partir de votre solution de sécurité pour vous assurer qu’ils sont transmis à la machine proxy Syslog que vous avez configurée. 
1. Vous devez disposer d’autorisations élevées (sudo) sur votre machine. Vérifiez que vous disposez de Python sur votre machine à l’aide de la commande suivante : `python –version`
1. Exécutez le script suivant pour vérifier la connectivité entre l’agent, Azure Sentinel et votre solution de sécurité. Celui-ci vérifie que le transfert de démon est correctement configuré, écoute les ports appropriés et s’assure que rien ne bloque la communication entre le démon et l’agent Log Analytics. Le script envoie également des messages fictifs « TestCommonEventFormat » pour vérifier la connectivité de bout en bout. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Zscaler Internet Access à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).

