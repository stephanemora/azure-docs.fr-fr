---
title: Rapports d’activité de connexion dans le portail Azure Active Directory | Microsoft Docs
description: Présentation des rapports d’activité de connexion dans le portail Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b69dca5abddd56b29abf3e482e51b3d2a41612e7
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864470"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Rapports d’activité de connexion dans le portail Azure Active Directory

L’architecture de création de rapports dans Azure Active Directory (Azure AD) comprend les composants suivants :

- **Activité** 
    - **Connexions** – Il s’agit d’informations sur l’utilisation des applications managées et les activités de connexion des utilisateurs.
    - **Journaux d’audit** - [Journaux d’audit](concept-audit-logs.md) – Fournit des informations sur les activités du système liées aux utilisateurs et à la gestion des groupes, les applications gérées et les activités de répertoire.
- **Sécurité** 
    - **Connexions risquées** : une [connexion risquée](concept-risky-sign-ins.md) est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur.
    - **Utilisateurs avec indicateur de risque** : un [utilisateur à risque](concept-user-at-risk.md) correspond à un indicateur de compte d’utilisateur susceptible d’être compromis.

Cette rubrique présente une vue d’ensemble du rapport de connexions.

## <a name="prerequisites"></a>Conditions préalables

### <a name="who-can-access-the-data"></a>Qui peut accéder aux données ?
* Les utilisateurs ayant le rôle Administrateur de sécurité, Lecteur Sécurité et Lecteur de rapports
* Les administrateurs généraux
* De plus, tous les utilisateurs (non administrateurs) peuvent accéder à leurs propres connexions 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>De quelle licence Azure AD avez-vous besoin pour accéder à l’activité de connexion ?
* Votre client doit avoir une licence Azure AD Premium associée pour afficher tous les rapports d’activités de connexion. Consultez [Bien démarrer avec Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) pour mettre à niveau votre édition d’Azure Active Directory. Notez que si vous n’aviez aucune donnée d’activité avant la mise à niveau, l’affichage des données dans les rapports prendra quelques jours une fois la mise à niveau vers une licence premium effectuée.

## <a name="sign-ins-report"></a>Rapport de connexions

Le rapport de connexions des utilisateurs permet de répondre aux questions suivantes :

* Quel est le modèle de connexion d’un utilisateur ?
* Combien d’utilisateurs se sont connectés au cours d’une semaine ?
* Quel est l’état de ces connexions ?

Vous pouvez accéder au rapport de connexions en sélectionnant **Connexions** dans la section **Activités** du panneau **Azure Active Directory** du [Portail Azure](https://portal.azure.com). Il peut s’écouler jusqu’à deux heures pour que certains enregistrements de connexion s’affichent dans le portail.

![Activité de connexion](./media/concept-sign-ins/61.png "Activité de connexion")

> [!IMPORTANT]
> Le rapport des connexions montre seulement les connexions **interactives**, c’est-à-dire les connexions où un utilisateur se connecte manuellement avec son nom d’utilisateur et son mot de passe. Les connexions non interactives, comme l’authentification de service à service, n’apparaissent pas dans le rapport des connexions. 

Un journal de connexion inclut un mode Liste par défaut, qui indique :

- La date de connexion
- L’utilisateur associé
- L’application à laquelle l’utilisateur s’est connecté
- L’état de connexion
- L’état de la détection de risque
- L’état de l’exigence de l’authentification multifacteur (MFA)

![Activité de connexion](./media/concept-sign-ins/01.png "Activité de connexion")

Vous pouvez personnaliser le mode Liste en cliquant sur **Colonnes** dans la barre d’outils.

![Activité de connexion](./media/concept-sign-ins/19.png "Activité de connexion")

Cela vous permet d’afficher des champs supplémentaires, ou de supprimer des champs qui sont déjà affichés.

![Activité de connexion](./media/concept-sign-ins/02.png "Activité de connexion")

Sélectionnez un élément dans la vue sous forme de liste pour obtenir des informations plus détaillées.

![Activité de connexion](./media/concept-sign-ins/03.png "Activité de connexion")

> [!NOTE]
> Les clients peuvent maintenant résoudre les problèmes de stratégies d’accès conditionnel grâce à tous les rapports de connexion. Les clients peuvent examiner l’état de l’accès conditionnel et consulter en détail les stratégies applicables à la connexion et les résultats de chaque stratégie en cliquant sur l’onglet **Accès conditionnel** pour obtenir un rapport de connexion.
> Pour en savoir plus, consultez le [Forum aux questions sur les informations de l’accès conditionnel dans toutes les connexions](reports-faq.md#conditional-access).

![Activité de connexion](./media/concept-sign-ins/ConditionalAccess.png "Activité de connexion")


## <a name="filter-sign-in-activities"></a>Filtrer les activités de connexion

Pour limiter les données transmises à un niveau qui vous convient, vous pouvez filtrer les données de connexion à l’aide des champs par défaut suivants :

- Utilisateur
- Application
- État de la connexion
- Accès conditionnel
- Date

![Activité de connexion](./media/concept-sign-ins/04.png "Activité de connexion")

Le filtre **Utilisateur** vous permet de spécifier le nom ou le nom d’utilisateur principal de l’utilisateur qui vous intéresse.

Le filtre **Application** vous permet de spécifier le nom de l’application qui vous intéresse.

Le filtre **État de la connexion** vous permet de sélectionner :

- Tous
- Succès
- Échec

Le filtre **Accès conditionnel** vous permet de sélectionner l’état de stratégie d’autorité de certification pour l’authentification dans :

- Tous
- Non appliqué
- Succès
- Échec

Le filtre **Date** vous permet de définir un intervalle de temps pour les données renvoyées.  
Les valeurs possibles sont les suivantes :

- 1 mois
- 7 jours
- 24 heures
- Intervalle de temps personnalisé

Lorsque vous sélectionnez une plage personnalisée, vous pouvez configurer une heure de début et une heure de fin.

Si vous ajoutez des champs à votre affichage de connexions, ils sont automatiquement ajoutés à la liste de filtres. Par exemple, en ajoutant un champ **Application cliente** à votre liste, vous obtenez également une autre option de filtre qui vous permet de définir les filtres suivants :  
![Activité de connexion](./media/concept-sign-ins/12.png "Activité de connexion")

- **Browser**  
    Ce filtre montre tous les événements où tentatives de connexion ont été effectuées à l’aide de flux de navigateur.
- **Exchange ActiveSync (pris en charge)**  
    Ce filtre affiche toutes les tentatives de connexion où le protocole Exchange ActiveSync (EAS) a été tenté à partir des plateformes prises en charge, comme iOS, Android et Windows Phone.
- **Exchange ActiveSync (non pris en charge)**  
    Ce filtre affiche toutes les tentatives de connexion où le protocole EAS a été tenté à partir des plateformes non prises en charge, par exemple, les distributions Linux.
- **Les applications mobiles et clients de bureau** ce filtre affiche toutes les tentatives de connexion qui ne contenait pas de flux de navigateur. Il peut s’agir des applications mobiles à partir de n’importe quelle plateforme à l’aide de n’importe quel protocole ou d’applications de bureau client telles que Office sur Windows ou MacOS.
  
- **Autres clients**
    - **IMAP**  
        Un client de messagerie hérité à l’aide d’IMAP pour récupérer le courrier électronique.
    - **MAPI**  
        Office 2013, où la bibliothèque ADAL est activée et il est à l’aide de MAPI.
    - **Clients Office plus anciens**  
        Office 2013 dans sa configuration par défaut où la bibliothèque ADAL n’est pas activée et il est à l’aide de MAPI ou Office 2016, où la bibliothèque ADAL a été désactivée.
    - **POP**  
        Un client de messagerie hérité à l’aide de POP3 pour récupérer le courrier électronique.
    - **SMTP**  
        Un client de messagerie hérité à l’aide de SMTP pour envoyer un e-mail.

## <a name="download-sign-in-activities"></a>Télécharger les activités de connexion

Vous pouvez [télécharger les données de connexion](quickstart-download-sign-in-report.md) pour les utiliser en dehors du portail Azure. En cliquant sur **télécharger** vous donne la possibilité de créer un fichier CSV ou JSON des enregistrements de 250 000 plus récents.  

![Télécharger](./media/concept-sign-ins/71.png "Télécharger")

> [!IMPORTANT]
> Le nombre d’enregistrements que vous pouvez télécharger est limité par les [stratégies de rétention de rapport Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Raccourcis vers les données de connexions

En plus d’Azure AD, le portail Azure vous offre d’autres points d’entrée pour accéder aux données de connexion :

- Vue d’ensemble de la protection de la sécurité des identités
- Utilisateurs
- Groupes
- Applications d’entreprise

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Données de connexion des utilisateurs dans la protection de la sécurité des identités

Le graphique des connexions utilisateur dans le **Identity protection de sécurité** page Vue d’ensemble présente les agrégations hebdomadaires des connexions pour tous les utilisateurs dans une période donnée. La valeur par défaut de cette période est de 30 jours.

![Activité de connexion](./media/concept-sign-ins/06.png "Activité de connexion")

Lorsque vous cliquez sur un jour dans le graphique des connexions, vous obtenez une vue d’ensemble des activités de connexion correspondantes.

Chaque ligne de la liste des activités de connexion affiche :

* Qui s’est connecté ?
* Quelle application a été la cible de la connexion ?
* Quel est l’état de la connexion ?
* Quel est l’état MFA de la connexion ?

En cliquant sur un élément, vous obtenez plus d’informations sur l’opération de connexion :

- ID utilisateur
- Utilisateur
- Nom d’utilisateur
- ID de l'application
- Application
- Client
- Lieu
- Adresse IP
- Date
- MFA obligatoire
- État de la connexion

> [!NOTE]
> Les adresses IP sont émises de manière à ce qu’il n’existe aucune connexion définitive entre une adresse IP et l’endroit où se trouve physiquement l’ordinateur avec cette adresse. Le mappage des adresses IP est compliqué par le fait que les fournisseurs mobiles et les VPN qui émettent des adresses IP à partir de pools centraux sont souvent très éloignés de l’endroit où l’appareil client est réellement utilisé. Pour le moment, la conversion de l’adresse IP en un emplacement physique constitue la meilleure solution pour les suivis, les données de registre, les recherches inversées et d’autres informations dans les rapports Azure AD.

Sur la page **Utilisateurs**, vous obtenez une vue d’ensemble complète de toutes les connexions des utilisateurs en cliquant sur **Connexions** dans la section **Activité**.

![Activité de connexion](./media/concept-sign-ins/08.png "Activité de connexion")

## <a name="usage-of-managed-applications"></a>Utilisation des applications gérées

En disposant d’une vue centrée sur les applications de vos données de connexion, vous pouvez répondre aux questions telles que :

* Qui utilise mes applications ?
* Quelles sont les 3 principales applications dans votre organisation ?
* J’ai récemment déployé une application. Comment se comporte-t-elle ?

Les 3 principales applications de votre organisation dans le rapport sur les 30 derniers jours apparaissant dans la section **Vue d’ensemble** sous **Enterprise applications** (Applications d’entreprise) constituent votre point d’entrée.

![Activité de connexion](./media/concept-sign-ins/10.png "Activité de connexion")

L’application utilisation graphique les agrégations hebdomadaires des connexions pour vos 3 principales applications dans une période donnée. La valeur par défaut de cette période est de 30 jours.

![Activité de connexion](./media/concept-sign-ins/47.png "Activité de connexion")

Si vous le souhaitez, vous pouvez définir la focalisation sur une application spécifique.

![Reporting](./media/concept-sign-ins/single_spp_usage_graph.png "Reporting")

Lorsque vous cliquez sur un jour dans le graphique d’utilisation des applications, vous obtenez une liste détaillée des activités de connexion.

L’option **Connexions** vous fournit une vue d’ensemble complète de tous les événements de connexion à vos applications.

![Activité de connexion](./media/concept-sign-ins/11.png "Activité de connexion")

## <a name="office-365-activity-logs"></a>Journaux d’activité Office 365

Vous pouvez afficher les journaux d’activité Office 365 à partir de la [centre d’administration Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Même si Office 365 Azure AD activité et journaux partagent un grand nombre de ressources du répertoire, uniquement le centre d’administration Microsoft 365 fournit une vue complète des journaux d’activité Office 365. 

Vous pouvez également accéder par programme aux journaux d’activité d’Office 365 avec l’[API de gestion Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Étapes suivantes

* [Codes d’erreur du rapport d’activité de connexion](reference-sign-ins-error-codes.md)
* [Stratégies de conservation des données Azure AD](reference-reports-data-retention.md)
* [Latences de rapport Azure AD](reference-reports-latencies.md)

