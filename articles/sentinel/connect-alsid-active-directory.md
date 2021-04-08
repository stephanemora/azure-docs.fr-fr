---
title: Connecter votre Alsid pour Active Directory à Azure Sentinel | Microsoft Docs
description: Découvrez comment utiliser le connecteur Alsid pour Active Directory afin d’extraire des journaux Alsid dans Azure Sentinel. Affichez les données Alsid dans des classeurs, créez des alertes et améliorez l’investigation.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 654ecb65068e4321b85594d96e8ca7a7f73cde7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99566562"
---
# <a name="connect-your-alsid-for-active-directory-ad-to-azure-sentinel"></a>Connecter votre Alsid pour Active Directory (AD) à Azure Sentinel

> [!IMPORTANT]
> Le connecteur Alsid pour Active Directory est actuellement en **préversion**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

Cet article explique comment connecter votre solution Alsid pour AD à Azure Sentinel. Le connecteur de données Alsid pour Active Directory vous permet de connecter facilement vos journaux Alsid pour AD à Azure Sentinel de façon à pouvoir consulter les données dans des classeurs, les interroger pour créer des alertes personnalisées et les incorporer pour améliorer l’investigation. L’intégration entre Alsid pour AD et Azure Sentinel utilise un serveur Syslog avec l’agent Log Analytics installé. Elle utilise également un analyseur de journal personnalisé basé sur une fonction Kusto.

> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer de l’autorisation d’écriture sur l’espace de travail Azure Sentinel.

- Votre solution Alsid pour AD doit être configurée pour exporter des journaux via Syslog.

## <a name="send-alsid-for-ad-logs-to-azure-sentinel-via-the-syslog-agent"></a>Envoyer des journaux Alsid pour AD à Azure Sentinel via l’agent Syslog

Configurez Alsid pour AD de façon à transférer les messages Syslog à votre espace de travail Azure Sentinel via l’agent Syslog.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**.

1. Dans la galerie **Connecteurs de données**, sélectionnez le connecteur **Alsid pour Active Directory (préversion)** , puis sélectionnez **Ouvrir la page du connecteur**.

1. Suivez les instructions de la page du connecteur **Alsid pour Active Directory** :

    1. Configurer un serveur Syslog

        1. Si vous n’avez pas de serveur Syslog Linux pour Alsid pour AD auquel envoyer les journaux, créez-en un. Azure Sentinel prend en charge les démons **rsyslog** et **syslog-ng**. 

        1. Configurez votre serveur Syslog de façon à ce qu’il génère des journaux Alsid pour AD dans un fichier séparé.

    1. Configurer Alsid pour AD afin d’envoyer les journaux à votre serveur Syslog

        1. Sur votre portail **Alsid pour AD**, accédez à *Système*, *Configuration*, puis *Syslog*. À partir de là, vous pouvez créer une alerte Syslog destinée à votre serveur Syslog. Pour le serveur distant, utilisez l’adresse IP de la machine Linux sur laquelle vous avez installé l’agent Linux.

        1. Vérifiez que les journaux sont correctement collectés sur votre serveur dans un fichier distinct (pour ce faire, vous pouvez utiliser le bouton **Tester la configuration** dans la configuration d’alerte *Syslog* dans Alsid pour AD).

    1. Installer et intégrer l’agent Log Analytics pour Linux

        - Choisissez une machine virtuelle Linux Azure ou une machine Linux non Azure (physique ou virtuelle). Suivez les liens et instructions affichés sur l'écran. Pour plus de détails, consultez [Configurer votre machine ou appliance Linux](connect-syslog.md#configure-your-linux-machine-or-appliance).

    1. Configurer les journaux que l’agent Log Analytics doit collecter

        - Sélectionnez les installations et les gravités dans la configuration des paramètres avancés de l’espace de travail.

            1. Cliquez sur le lien **Ouvrir la configuration des paramètres avancés de votre espace de travail >** sur la page du connecteur.

            1. Dans l’écran **Paramètres avancés** , sélectionnez **Données**, puis **Journaux personnalisés**.

            1. Activez la case à cocher **Appliquer la configuration ci-dessous à mes machines Linux**, puis cliquez sur **Ajouter**.

            1. Cliquez sur **Sélectionner un fichier** afin de charger un exemple de fichier Syslog Alsid pour AD à partir de la machine Linux exécutant le serveur Syslog, puis cliquez sur **Suivant**.

            1. Vérifiez que l’option **Définir un délimiteur d’enregistrement** est définie sur **Nouvelle ligne**, puis cliquez sur **Suivant**.

            1. Sélectionnez **Linux**, puis entrez le chemin d’accès du fichier Syslog, cliquez sur **+** , puis sur **Suivant**.

            1. Dans le champ Nom, tapez *AlsidForADLog* devant le suffixe _CL, puis cliquez sur **Terminé**.
    
Jusqu’à 20 minutes peuvent s’écouler avant que vos journaux commencent à apparaître dans Log Analytics.

## <a name="find-your-data"></a>Recherche de données

Une fois la connexion établie, les données s’affichent dans **Journaux**, sous **Journaux personnalisés**, dans la table *AlsidForADLog_CL*.

Le fait que ce connecteur de données fonctionne comme prévu dépend d’un analyseur basé sur une fonction Kusto. Pour configurer la fonction Kusto **afad_parser** à utiliser dans les requêtes et les classeurs, procédez comme suit.

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Journaux**.

1. Copiez la requête suivante et collez-la dans la fenêtre de requête.
    ```kusto
    let CodenameTable=datatable(Codename: string, Explanation: string) [
    "test-checker-codename", "This is a test checker",
    "", "Not an alert",
    "C-ADM-ACC-USAGE", "Recent use of the default administrator account",
    "C-UNCONST-DELEG", "Dangerous delegation",
    "C-PASSWORD-DONT-EXPIRE", "Accounts with never expiring passwords",
    "C-USERS-CAN-JOIN-COMPUTERS", "Users allowed to join computers to the domain",
    "C-CLEARTEXT-PASSWORD", "Potential clear-text password",
    "C-PROTECTED-USERS-GROUP-UNUSED", "Protected Users group not used",
    "C-PASSWORD-POLICY", "Weak password policies are applied on users",
    "C-GPO-HARDENING", "Domain without computer-hardening GPOs",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-AAD-CONNECT", "Verify permissions related to AAD Connect accounts",
    "C-AAD-SSO-PASSWORD", "Verify AAD SSO account password last change",
    "C-GPO-SD-CONSISTENCY", "Verify sensitive GPO objects and files permissions",
    "C-DSHEURISTICS", "Domain using a dangerous backward-compatibility configuration",
    "C-DOMAIN-FUNCTIONAL-LEVEL", "Domains have an outdated functional level",
    "C-DISABLED-ACCOUNTS-PRIV-GROUPS", "Disabled accounts in privileged groups",
    "C-DCSHADOW", "Rogue domain controllers",
    "C-DC-ACCESS-CONSISTENCY", "Domain controllers managed by illegitimate users",
    "C-DANGEROUS-TRUST-RELATIONSHIP", "Dangerous trust relationship",
    "C-DANGEROUS-SENSITIVE-PRIVILEGES", "Dangerous sensitive privileges",
    "C-DANG-PRIMGROUPID", "User Primary Group ID",
    "C-BAD-PASSWORD-COUNT", "Brute-force attack detection",
    "C-ADMINCOUNT-ACCOUNT-PROPS", "AdminCount attribute set on standard users",
    "C-ACCOUNTS-DANG-SID-HISTORY", "Accounts having a dangerous SID History attribute",
    "C-ABNORMAL-ENTRIES-IN-SCHEMA", "Dangerous rights in AD's schema",
    "C-GPOLICY-DISABLED-UNLINKED", "Unlinked, disabled or orphan GPO",
    "C-KERBEROS-CONFIG-ACCOUNT", "Kerberos configuration on user account",
    "C-KRBTGT-PASSWORD", "KDC password last change",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-NATIVE-ADM-GROUP-MEMBERS", "Native administrative group members",
    "C-NETLOGON-SECURITY", "Unsecured configuration of Netlogon protocol",
    "C-OBSOLETE-SYSTEMS", "Computers running an obsolete OS",
    "C-PASSWORD-NOT-REQUIRED", "Account that might have an empty password",
    "C-PKI-WEAK-CRYPTO", "Use of weak cryptography algorithms into Active Directory PKI",
    "C-PRE-WIN2000-ACCESS-MEMBERS", "Accounts using a pre-Windows 2000 compatible access control",
    "C-PRIV-ACCOUNTS-SPN", "Privileged accounts running Kerberos services",
    "C-REVER-PWD-GPO", "Reversible passwords in GPO",
    "C-ROOTOBJECTS-SD-CONSISTENCY", "Root objects permissions allowing DCSync-like attacks",
    "C-SDPROP-CONSISTENCY", "Ensure SDProp consistency",
    "C-SENSITIVE-CERTIFICATES-ON-USER", "Ensure SDProp consistency",
    "C-SLEEPING-ACCOUNTS", "Sleeping accounts",
    "C-USER-PASSWORD", "User account using old password",
    "C-USERS-REVER-PWDS", "Reversible passwords"
    ];
    let Common = AlsidForADLog_CL
    | parse RawData with
                         Time:datetime  " "
                         Host:string  " "
                         Product:string "["
                         PID:int "]: \""
                         MessageType:int "\" \""
                         AlertID:int "\" \""
                         Forest:string "\" \""
                         Domain:string "\" "
                         DistinctPart:string;
    let Deviances = Common
    | where MessageType == 0 | parse DistinctPart with "\""
                         Codename:string "\" \""
                         Severity:string "\" \""
                         ADObject:string "\" \""
                         DevianceID:string "\" \""
                         ProfileID:string "\" \""
                         ReasonCodename:string "\" \""
                         EventID:string "\""
                         Attributes:string "\r\n";
    let Changes = Common
    | where MessageType == 1
    | parse kind=regex DistinctPart with "\""
                         ADObject:string "\" \""
                         EventID:string "\" \""
                         EventType:string "\" "
                         Attributes:string "\r?\n";
    union Changes, Deviances
    | project-away DistinctPart, Product, _ResourceId, _SubscriptionId
    | lookup kind=leftouter CodenameTable on Codename;
    ```

1. Cliquez sur la liste déroulante **Enregistrer**, puis sur **Enregistrer**. Dans le panneau **Enregistrer** :

    1. Sous **Nom**, entrez **afad_parser**.

    1. Sous **Enregistrer sous**, choisissez **Fonction**.

    1. Sous **Alias de fonction**, entrez **afad_parser**.

    1. Sous **Catégorie**, entrez **Fonctions**.

    1. Cliquez sur **Enregistrer**.

    L’activation des applications de fonction prend généralement entre 10 et 15 minutes.

Vous êtes maintenant prêt à interroger les données Alsid pour AD en entrant `afad_parser` dans la ligne supérieure de la fenêtre de requête.

Pour obtenir plus d’exemples de requêtes, consultez l’onglet **Étapes suivantes** dans la page du connecteur.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment connecter Alsid pour AD à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilisez des classeurs](tutorial-monitor-your-data.md) pour superviser vos données.
