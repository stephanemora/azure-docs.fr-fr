---
title: Détection avancée des attaques multiphases dans Azure Sentinel
description: Utilisez la technologie Fusion d’Azure Sentinel pour réduire l’épuisement des alertes et créer des incidents actionnables basés sur la détection avancée des attaques multiphases.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 87ca322cbdfdd8a53a3ecefcb120a961ea1bb936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587921"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Détection avancée des attaques multiphases dans Azure Sentinel


> [!IMPORTANT]
> Certaines fonctionnalités de fusion dans Azure Sentinel sont actuellement en version préliminaire publique.
> Ces fonctionnalités étant fournies sans contrat de niveau de service, elles sont déconseillées pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



À l’aide de la technologie Fusion basée sur le machine learning, Azure Sentinel peut détecter automatiquement les attaques multiphases en combinant les comportements anormaux et les activités suspectes observés à différentes phases de la chaîne de destruction. Azure Sentinel génère alors des incidents qui, sans cela, seraient très difficiles à identifier. Ces incidents couvrent au moins deux alertes ou activités. Le système est conçu pour générer des incidents haute fidélité, à faible volume, présentant un niveau de gravité élevé.

Personnalisée pour votre environnement, cette détection réduit les taux de faux positifs et peut également détecter les attaques avec des informations limitées ou manquantes.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Configuration de la détection avancée des attaques multiphases

Cette détection est activée par défaut dans Azure Sentinel. Suivez les instructions ci-après pour vérifier l’état de la détection ou pour la désactiver, par exemple si vous utilisez une autre solution pour créer des incidents en fonction de plusieurs alertes :

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com).

2. Accédez à **Azure Sentinel** > **Configuration** > **Analytique**.

3. Sélectionnez **Règles actives**, puis localisez **Détection avancée des attaques multiphases** dans la colonne **Nom**. Consultez la colonne **État** pour vérifier si cette détection est activée ou désactivée.

4. Pour changer l’état, sélectionnez cette entrée, puis, dans le panneau **Détection avancée des attaques multiphases**, sélectionnez **Modifier**.

5. Le changement d’état est sélectionné automatiquement dans le panneau de l’**Assistant de création de règles**. Sélectionnez **Suivant : Réviser**, puis sélectionnez **Enregistrer**. 

Les modèles de règles ne s’appliquent pas à la détection avancée des attaques multiphases.

> [!NOTE]
> Azure Sentinel utilise actuellement 30 jours de données historiques pour former les systèmes de Machine Learning. Ces données sont toujours chiffrées à l’aide des clés de Microsoft à mesure qu’elles passent par le pipeline de Machine Learning. Cependant, les données d’apprentissage ne sont pas chiffrées à l’aide de [clés gérées par le client (CMK)](customer-managed-keys.md) si vous avez activé CMK dans votre espace de travail Azure Sentinel. Pour refuser la fusion, accédez à **Azure Sentinel** \> **configuration** \> **Analytics \> Règles actives \> Détection avancée des attaques en plusieurs étapes**, puis, dans la colonne **État**, sélectionnez **Désactiver**.

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Fusion avec Palo Alto Networks et Microsoft Defender ATP

Ces scénarios combinent deux des journaux fondamentaux qu’utilisent les analystes de sécurité : des journaux de pare-feu de Palo Alto Networks et des journaux de détection de points de terminaison de Microsoft Defender ATP. Dans tous les scénarios répertoriés ci-dessous, une activité suspecte est détectée au point de terminaison qui implique une adresse IP externe, puis est suivie d’un trafic anormal de l’adresse IP externe vers le pare-feu. Dans les journaux Palo Alto, Azure Sentinel se concentre sur les [journaux des menaces](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), et le trafic est considéré comme suspect lorsque des menaces sont autorisées (données, fichiers, saturations, paquets, analyses, logiciels espions, URL, virus, vulnérabilités, virus d’incendie, incendies suspects).

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Demande réseau au service d’anonymisation TOR, suivie d’un trafic anormal signalé par le pare-feu de Palo Alto Networks.

Dans ce scénario, Azure Sentinel détecte d’abord une alerte indiquant que Microsoft Defender - Protection avancée contre les menaces a détecté une demande réseau adressée à un service d’anonymisation TOR, qui génère une activité anormale. Celle-ci a été initiée sous le compte {account name} avec l’ID d’identificateur de sécurité {sid} à {time}. L’adresse IP sortante de la connexion était {IndividualIp}.
Ensuite, une activité inhabituelle a été détectée par le pare-feu de Palo Alto Networks à {TimeGenerated}. Cela indique qu’un trafic malveillant est entré dans votre réseau. L’adresse IP de destination pour le trafic réseau est {DestinationIP}.

Ce scénario est actuellement en préversion publique.


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell a établi une connexion réseau suspecte, suivie d’un trafic anormal signalé par le pare-feu de Palo Alto Networks.

Dans ce scénario, Azure Sentinel détecte d’abord une alerte indiquant que Microsoft Defender - Protection avancée contre les menaces a détecté que PowerShell a établi une connexion réseau suspecte entraînant une activité anormale détectée par un pare-feu de Palo Alto Networks. Celle-ci a été initiée par le compte {account name} avec l’ID d’identificateur de sécurité {sid} à {time}. L’adresse IP sortante de la connexion était {IndividualIp}. Ensuite, une activité inhabituelle a été détectée par le pare-feu de Palo Alto Networks à {TimeGenerated}. Cela indique qu’un trafic malveillant est entré dans votre réseau. L’adresse IP de destination pour le trafic réseau est {DestinationIP}.

Ce scénario est actuellement en préversion publique.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Connexion sortante à l’adresse IP avec un historique des tentatives d’accès non autorisées suivie d’un trafic anormal signalé par le pare-feu Palo Alto Networks

Dans ce scénario, Azure Sentinel détecte une alerte indiquant que Microsoft Defender - Protection avancée contre les menaces a détecté une connexion sortante vers une adresse IP avec un historique de tentatives d’accès non autorisées entraînant une activité anormale détectée le pare-feu de Palo Alto Networks. Celle-ci a été initiée par le compte {account name} avec l’ID d’identificateur de sécurité {sid} à {time}. L’adresse IP sortante de la connexion était {IndividualIp}. Après cela, une activité inhabituelle a été détectée par le pare-feu de Palo Alto Networks à {TimeGenerated}. Cela indique qu’un trafic malveillant est entré dans votre réseau. L’adresse IP de destination pour le trafic réseau est {DestinationIP}.

Ce scénario est actuellement en préversion publique.



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Fusion avec Identity Protection et Microsoft Cloud App Security

Dans le cadre de la détection avancée des attaques multiphases, Azure Sentinel prend en charge les scénarios suivants, combinant les événements d’anomalie provenant d’Azure Active Directory Identity Protection et de Microsoft Cloud App Security :

- [Déplacement impossible vers un lieu inhabituel suivi d’une activité Office 365 anormale](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Activité de connexion à partir d’un lieu inconnu suivie d’une activité Office 365 anormale](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Activité de connexion à partir d’un appareil infecté suivie d’une activité Office 365 anormale](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Activité de connexion à partir d’une adresse IP anonyme suivie d’une activité Office 365 anormale](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Activité de connexion d’un utilisateur dont les informations d’identification ont fuité suivie d’une activité Office 365 anormale](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Le [connecteur de données Azure AD Identity Protection](connect-azure-ad-identity-protection.md) et les connecteurs [Cloud App Security](connect-cloud-app-security.md) doivent être configurés.

Les descriptions qui suivent contiennent des variables entre crochets. Azure Sentinel indiquera cependant les valeurs réelles basées sur vos données. Par exemple, Azure Sentinel indiquera le nom d’affichage réel d’un compte et non \<*nom du compte*> et un nombre réel et non \<*nombre*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Déplacement impossible vers un lieu inhabituel suivi d’une activité Office 365 anormale

Il existe sept incidents Azure Sentinel combinant des alertes de déplacement impossible vers un lieu inhabituel (générées par Azure AD Identity Protection) et des alertes d’activité Office 365 anormale (générées par Microsoft Cloud App Security) :

- **Déplacement impossible vers des lieux inhabituels à l’origine d’une exfiltration de boîte aux lettres Office 365**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> impliquant un déplacement impossible vers \<*emplacement*>, un lieu inhabituel, suivi de la définition d’une règle de transfert de boîte de réception suspecte sur la boîte de réception d’un utilisateur.
    
    Ceci peut indiquer que le compte est compromis et que la boîte aux lettres est utilisée pour exfiltrer des informations de votre organisation. L’utilisateur \<*nom du compte*> a créé ou mis à jour une règle de transfert de boîte de réception qui transfère tous les e-mails entrants à l’adresse externe \<*adresse e-mail*>.

- **Déplacement impossible vers des lieux inhabituels à l’origine d’une activité d’administration d’application cloud suspecte**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> impliquant un déplacement impossible vers \<*emplacement*>, un lieu inhabituel.
    
    Ensuite, le compte \<*nom du compte*> a effectué plus de \<*nombre*> activités d’administration au cours d’une même session.

- **Déplacement impossible vers des lieux inhabituels à l’origine d’une suppression de fichiers en masse**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à \<*emplacement*>, un lieu inhabituel. 
    
    Ensuite, le compte \<*nom du compte*> a supprimé \<*nombre*> fichiers distincts au cours d’une même session.

- **Déplacement impossible vers des lieux inhabituels à l’origine d’un téléchargement de fichiers en masse**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> impliquant un déplacement impossible vers \<*emplacement*>, un lieu inhabituel. 
    
    Ensuite, le compte \<*nom du compte*> a téléchargé plus de \<*nombre*> fichiers distincts au cours d’une même session.

- **Déplacement impossible vers des lieux inhabituels à l’origine d’un emprunt d’identité dans Office 365**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> impliquant un déplacement impossible vers \<*emplacement*>, un lieu inhabituel. 
    
    Ensuite, le compte \<*nom du compte*> a effectué un nombre inhabituel (\<*nombre d’activités*>) d’activités d’emprunt d’identité au cours d’une même session.

- **Déplacement impossible vers des lieux inhabituels à l’origine d’un partage de fichiers en masse**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> impliquant un déplacement impossible vers \<*emplacement*>, un lieu inhabituel. 
    
    Ensuite, le compte \<*nom du compte*> a partagé plus de \<*nombre*> fichiers distincts au cours d’une même session.

- **Déplacement impossible vers des lieux inhabituels à l’origine du déploiement d’un ransomware dans une application cloud**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> impliquant un déplacement impossible vers \<*emplacement*>, un lieu inhabituel. 
    
    Ensuite, le compte \<*nom du compte*> a chargé \<*nombre*> fichiers et supprimé \<*nombre*> fichiers au total. 
    
    Ce modèle d’activité indique une attaque par ransomware potentielle.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Activité de connexion à partir d’un lieu inconnu suivie d’une activité Office 365 anormale

Il existe sept incidents Azure Sentinel combinant des alertes d’activité de connexion à partir de lieux inconnus (générées par Azure AD Identity Protection) et des alertes d’activité Office 365 anormale (générées par Microsoft Cloud App Security).

- **Événement de connexion à partir d’un lieu inconnu à l’origine de l’exfiltration d’une boîte aux lettres Exchange Online**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir de \<*emplacement*>, un lieu inconnu, suivi de la définition d’une règle de transfert de boîte de réception suspecte sur la boîte de réception d’un utilisateur.
    
    Ceci peut indiquer que le compte est compromis et que la boîte aux lettres est utilisée pour exfiltrer des informations de votre organisation. L’utilisateur \<*nom du compte*> a créé ou mis à jour une règle de transfert de boîte de réception qui transfère tous les e-mails entrants à l’adresse externe \<*adresse e-mail*>. 

- **Événement de connexion à partir d’un lieu inconnu à l’origine d’une activité d’administration d’application cloud suspecte**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir de \<*emplacement*>, un lieu inconnu. 
    
    Ensuite, le compte \<*nom du compte*> a effectué plus de \<*nombre*> activités d’administration au cours d’une même session.

- **Événement de connexion à partir d’un lieu inconnu à l’origine d’une suppression de fichiers en masse**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir de \<*emplacement*>, un lieu inconnu. 
    
    Ensuite, le compte \<*nom du compte*> a supprimé \<*nombre*> fichiers distincts au cours d’une même session.

- **Événement de connexion à partir d’un lieu inconnu à l’origine d’un téléchargement de fichiers en masse**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir de \<*emplacement*>, un lieu inconnu. 
    
    Ensuite, le compte \<*nom du compte*> a téléchargé plus de \<*nombre*> fichiers distincts au cours d’une même session.

- **Événement de connexion à partir d’un lieu inconnu à l’origine d’un emprunt d’identité dans Office 365**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir de \<*emplacement*>, un lieu inconnu.
    
    Ensuite, le compte \<*nom du compte*> a emprunté l’identité de plus de \<*nombre*> comptes différents au cours d’une même session.

- **Événement de connexion à partir d’un lieu inconnu à l’origine d’un partage de fichiers en masse**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir de \<*emplacement*>, un lieu inconnu. 
    
    Ensuite, le compte \<*nom du compte*> a partagé plus de \<*nombre*> fichiers distincts au cours d’une même session.

- **Événement de connexion à partir d’un lieu inconnu à l’origine du déploiement d’un ransomware dans une application cloud**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir de \<*emplacement*>, un lieu inconnu. 
    
    Ensuite, le compte \<*nom du compte*> a chargé \<*nombre*> fichiers et supprimé \<*nombre*> fichiers au total. 
    
    Ce modèle d’activité indique une attaque par ransomware potentielle.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Activité de connexion à partir d’un appareil infecté suivie d’une activité Office 365 anormale

Il existe sept incidents Azure Sentinel combinant des alertes d’activité de connexion à partir d’appareils infectés (générées par Azure AD Identity Protection) et des alertes d’activité Office 365 anormale (générées par Microsoft Cloud App Security) :

- **Événement de connexion à partir d’un appareil infecté à l’origine de l’exfiltration d’une boîte aux lettres Office 365**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir d’un appareil potentiellement infecté par un programme malveillant, suivi de la définition d’une règle de transfert de boîte de réception suspecte sur la boîte de réception d’un utilisateur.
    
    Ceci peut indiquer que le compte est compromis et que la boîte aux lettres est utilisée pour exfiltrer des informations de votre organisation. L’utilisateur \<*nom du compte*> a créé ou mis à jour une règle de transfert de boîte de réception qui transfère tous les e-mails entrants à l’adresse externe \<*adresse e-mail*>. 

- **Événement de connexion à partir d’un appareil infecté à l’origine d’une activité d’administration d’application cloud suspecte**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir d’un appareil potentiellement infecté par un programme malveillant.
    
    Ensuite, le compte \<*nom du compte*> a effectué plus de \<*nombre*> activités d’administration au cours d’une même session.

- **Événement de connexion à partir d’un appareil infecté à l’origine d’une suppression de fichiers en masse**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir d’un appareil potentiellement infecté par un programme malveillant. 
    
    Ensuite, le compte \<*nom du compte*> a supprimé \<*nombre*> fichiers distincts au cours d’une même session.

- **Événement de connexion à partir d’un appareil infecté à l’origine d’un téléchargement de fichiers en masse**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir d’un appareil potentiellement infecté par un programme malveillant. 
    
    Ensuite, le compte \<*nom du compte*> a téléchargé plus de \<*nombre*> fichiers distincts au cours d’une même session.

- **Événement de connexion à partir d’un appareil infecté à l’origine d’un emprunt d’identité dans Office 365**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir d’un appareil potentiellement infecté par un programme malveillant. 
    
    Ensuite, le compte \<*nom du compte*> a emprunté l’identité de plus de \<*nombre*> comptes différents au cours d’une même session.

- **Événement de connexion à partir d’un appareil infecté à l’origine d’un partage de fichiers en masse**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir d’un appareil potentiellement infecté par un programme malveillant. 
    
    Ensuite, le compte \<*nom du compte*> a partagé plus de \<*nombre*> fichiers distincts au cours d’une même session.

- **Événement de connexion à partir d’un appareil infecté à l’origine du déploiement d’un ransomware dans une application cloud**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir d’un appareil potentiellement infecté par un programme malveillant. 
    
    Ensuite, le compte \<*nom du compte*> a chargé \<*nombre*> fichiers et supprimé \<*nombre*> fichiers au total. 
    
    Ce modèle d’activité indique une attaque par ransomware potentielle.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Activité de connexion à partir d’une adresse IP anonyme suivie d’une activité Office 365 anormale

Il existe sept incidents Azure Sentinel combinant des alertes d’activité de connexion à partir d’une adresse IP anonyme (générées par Azure AD Identity Protection) et des alertes d’activité Office 365 anormale (générées par Microsoft Cloud App Security) :

- **Événement de connexion à partir d’une adresse IP anonyme à l’origine de l’exfiltration d’une boîte aux lettres Office 365**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir de l’adresse IP de proxy anonyme \<*adresse IP*>, suivi de la définition d’une règle de transfert de boîte de réception suspecte sur la boîte de réception d’un utilisateur.
    
    Ceci peut indiquer que le compte est compromis et que la boîte aux lettres est utilisée pour exfiltrer des informations de votre organisation. L’utilisateur \<*nom du compte*> a créé ou mis à jour une règle de transfert de boîte de réception qui transfère tous les e-mails entrants à l’adresse externe \<*adresse e-mail*>. 

- **Événement de connexion à partir d’une adresse IP anonyme à l’origine d’une activité d’administration d’application cloud suspecte**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir de l’adresse IP de proxy anonyme \<*adresse IP*>. 
    
    Ensuite, le compte \<*nom du compte*> a effectué plus de \<*nombre*> activités d’administration au cours d’une même session.

- **Événement de connexion à partir d’une adresse IP anonyme à l’origine d’une suppression de fichiers en masse**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir de l’adresse IP de proxy anonyme \<*adresse IP*>. 
    
    Ensuite, le compte \<*nom du compte*> a supprimé \<*nombre*> fichiers distincts au cours d’une même session.

- **Événement de connexion à partir d’une adresse IP anonyme à l’origine d’un téléchargement de fichiers en masse**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir de l’adresse IP de proxy anonyme \<*adresse IP*>. 
    
    Ensuite, le compte \<*nom du compte*> a téléchargé plus de \<*nombre*> fichiers distincts au cours d’une même session.

- **Événement de connexion à partir d’une adresse IP anonyme à l’origine d’un emprunt d’identité dans Office 365**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir de l’adresse IP de proxy anonyme \<*adresse IP*>. 
    
    Ensuite, le compte \<*nom du compte*> a emprunté l’identité de plus de \<*nombre*> comptes différents au cours d’une même session.

- **Événement de connexion à partir d’une adresse IP anonyme à l’origine d’un partage de fichiers en masse**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir de l’adresse IP de proxy anonyme \<*adresse IP*>. 
    
    Ensuite, le compte \<*nom du compte*> a partagé plus de \<*nombre*> fichiers distincts au cours d’une même session.

- **Événement de connexion à partir d’une adresse IP anonyme à l’origine du déploiement d’un ransomware dans une application cloud**
    
    Cette alerte indique un événement de connexion de \<*nom du compte*> à partir de l’adresse IP de proxy anonyme \<*adresse IP*>. 
    
    Ensuite, le compte \<*nom du compte*> a chargé \<*nombre*> fichiers et supprimé \<*nombre*> fichiers au total. 
    
    Ce modèle d’activité indique une attaque par ransomware potentielle.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Activité de connexion d’un utilisateur dont les informations d’identification ont fuité suivie d’une activité Office 365 anormale

Il existe sept incidents Azure Sentinel combinant des alertes d’activité de connexion d’un utilisateur dont les informations d’identification ont fuité (générées par Azure AD Identity Protection) et des alertes d’activité Office 365 anormale (générées par Microsoft Cloud App Security) :

- **Événement de connexion d’un utilisateur dont les informations d’identification ont fuité à l’origine de l’exfiltration d’une boîte aux lettres Office 365**
    
    Cette alerte indique que des informations d’identification ayant fuité ont été utilisées lors de l’événement de connexion de \<*nom du compte*>, puis qu’une règle de transfert de boîte de réception suspecte a été définie sur la boîte de réception d’un utilisateur. 
    
    Ceci peut indiquer que le compte est compromis et que la boîte aux lettres est utilisée pour exfiltrer des informations de votre organisation. L’utilisateur \<*nom du compte*> a créé ou mis à jour une règle de transfert de boîte de réception qui transfère tous les e-mails entrants à l’adresse externe \<*adresse e-mail*>. 

- **Événement de connexion d’un utilisateur dont les informations d’identification ont fuité à l’origine d’une activité d’administration d’application cloud suspecte**
    
    Cette alerte indique que des informations d’identification ayant fuité ont été utilisées lors de l’événement de connexion de \<*nom du compte*>.
    
    Ensuite, le compte \<*nom du compte*> a effectué plus de \<*nombre*> activités d’administration au cours d’une même session.

- **Événement de connexion d’un utilisateur dont les informations d’identification ont fuité à l’origine d’une suppression de fichiers en masse**
    
    Cette alerte indique que des informations d’identification ayant fuité ont été utilisées lors de l’événement de connexion de \<*nom du compte*>.
    
    Ensuite, le compte \<*nom du compte*> a supprimé \<*nombre*> fichiers distincts au cours d’une même session.

- **Événement de connexion d’un utilisateur dont les informations d’identification ont fuité à l’origine d’un téléchargement de fichiers en masse**
    
    Cette alerte indique que des informations d’identification ayant fuité ont été utilisées lors de l’événement de connexion de \<*nom du compte*>.
    
    Ensuite, le compte \<*nom du compte*> a téléchargé plus de \<*nombre*> fichiers distincts au cours d’une même session.

- **Événement de connexion d’un utilisateur dont les informations d’identification ont fuité à l’origine d’un emprunt d’identité dans Office 365**
    
    Cette alerte indique que des informations d’identification ayant fuité ont été utilisées lors de l’événement de connexion de \<*nom du compte*>. 
    
    Ensuite, le compte \<*nom du compte*> a emprunté l’identité de plus de \<*nombre*> comptes différents au cours d’une même session.

- **Événement de connexion d’un utilisateur dont les informations d’identification ont fuité à l’origine d’un partage de fichiers en masse**
    
    Cette alerte indique que des informations d’identification ayant fuité ont été utilisées lors de l’événement de connexion de \<*nom du compte*>.
    
    Ensuite, le compte \<*nom du compte*> a partagé plus de \<*nombre*> fichiers distincts au cours d’une même session.

- **Événement de connexion d’un utilisateur dont les informations d’identification ont fuité à l’origine du déploiement d’un ransomware dans une application cloud**
    
    Cette alerte indique que des informations d’identification ayant fuité ont été utilisées lors de l’événement de connexion de \<*nom du compte*>. 
    
    Ensuite, le compte \<*nom du compte*> a chargé \<*nombre*> fichiers et supprimé \<*nombre*> fichiers au total. 
    
    Ce modèle d’activité indique une attaque par ransomware potentielle.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous en savez plus sur la détection avancée des attaques multiphases, il peut vous être utile de suivre le guide de démarrage suivant pour savoir comment bénéficier d’une visibilité sur vos données et sur les menaces potentielles : [Bien démarrer avec Azure Sentinel](quickstart-get-visibility.md).

Si vous êtes prêt à examiner les incidents créés pour vous, consultez le tutoriel suivant : [Examiner les incidents avec Azure Sentinel](tutorial-investigate-cases.md).

