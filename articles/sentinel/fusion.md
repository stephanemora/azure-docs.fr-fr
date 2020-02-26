---
title: Détection avancée des attaques multiphases dans Azure Sentinel
description: Utilisez la technologie Fusion d’Azure Sentinel pour réduire l’épuisement des alertes et créer des incidents actionnables basés sur la détection avancée des attaques multiphases.
services: sentinel
documentationcenter: na
author: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2020
ms.author: rkarlin
ms.openlocfilehash: ada2ad67bc3634d8e6a31d3c8a69fc0c8b08a93a
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2020
ms.locfileid: "77369695"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Détection avancée des attaques multiphases dans Azure Sentinel

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

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Fusion avec Palo Alto Networks et Microsoft Defender ATP

- Demande réseau au service d’anonymisation TOR, suivie d’un trafic anormal signalé par le pare-feu Palo Alto Networks

- PowerShell a établi une connexion réseau suspecte suivie d’un trafic anormal signalé par le pare-feu Palo Alto Networks

- Connexion sortante à l’adresse IP avec un historique des tentatives d’accès non autorisées suivie d’un trafic anormal signalé par le pare-feu Palo Alto Networks



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

