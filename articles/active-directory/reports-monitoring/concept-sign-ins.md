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
ms.date: 10/28/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e7b0c379783af2f9131d487f45c0f4e2009e258
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232140"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Rapports d’activité de connexion dans le portail Azure Active Directory

L’architecture de création de rapports dans Azure Active Directory (Azure AD) comprend les composants suivants :

- **Activité** 
    - **Connexions** – Il s’agit d’informations sur l’utilisation des applications managées et les activités de connexion des utilisateurs.
    - **Journaux d’audit** - [Journaux d’audit](concept-audit-logs.md) : fournit des informations sur les activités du système liées aux utilisateurs et à la gestion des groupes, les applications gérées et les activités de répertoire.
- **Sécurité** 
    - **Connexions risquées** : une [connexion risquée](concept-risky-sign-ins.md) correspond à un indicateur de tentative de connexion d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur.
    - **Utilisateurs avec indicateur de risque** : un [utilisateur à risque](concept-user-at-risk.md) correspond à un indicateur de compte d’utilisateur susceptible d’être compromis.

Cet article présente une vue d’ensemble du rapport de connexions.

## <a name="prerequisites"></a>Prérequis

### <a name="who-can-access-the-data"></a>Qui peut accéder aux données ?

* Les utilisateurs ayant les rôles Administrateur de la sécurité, Lecteur Sécurité, Lecteur général et Lecteur de rapports
* Les administrateurs généraux
* Tous les utilisateurs (non administrateurs) peuvent accéder à leurs propres connexions 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>De quelle licence Azure AD avez-vous besoin pour accéder à l’activité de connexion ?

* Votre client doit avoir une licence Azure AD Premium associée pour afficher tous les rapports d’activités de connexion. Consultez [Bien démarrer avec Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) pour mettre à niveau votre édition d’Azure Active Directory. Quelques jours seront nécessaires pour que les données s’affichent dans les rapports après la mise à niveau vers une licence premium s’il n’y a aucune activité de données avant la mise à niveau.

## <a name="sign-ins-report"></a>Rapport de connexions

Le rapport de connexions des utilisateurs permet de répondre aux questions suivantes :

* Quel est le modèle de connexion d’un utilisateur ?
* Combien d’utilisateurs se sont connectés au cours d’une semaine ?
* Quel est l’état de ces connexions ?

Commencez avec le [portail Azure](https://portal.azure.com). Pour accéder au rapport de connexions, sélectionnez **Connexions**, puis rendez-vous sur **Supervision**. Il peut s’écouler jusqu’à deux heures avant que certains enregistrements de connexion s’affichent dans le portail.

![Activité de connexion](./media/concept-sign-ins/reporting-azure-sign-in-screen.png "Activité de connexion")

> [!IMPORTANT]
> Le rapport des connexions montre seulement les connexions **interactives**, c’est-à-dire les connexions où un utilisateur se connecte manuellement avec son nom d’utilisateur et son mot de passe. Les connexions non interactives, comme l’authentification de service à service, n’apparaissent pas dans le rapport des connexions. 

Un journal de connexion inclut un mode Liste par défaut, qui indique :

- La date de connexion
- L’utilisateur associé
- L’application à laquelle l’utilisateur s’est connecté
- L’état de connexion
- L’état de la détection de risque
- L’état de l’exigence de l’authentification multifacteur (MFA)

![Activité de connexion](./media/concept-sign-ins/sign-in-activity.png "Activité de connexion")

Vous pouvez personnaliser le mode Liste en cliquant sur **Colonnes** dans la barre d’outils.

![Activité de connexion](./media/concept-sign-ins/19.png "Activité de connexion")

Affiche des champs supplémentaires ou supprime des champs déjà affichés.

![Activité de connexion](./media/concept-sign-ins/02.png "Activité de connexion")

Sélectionnez un élément dans la vue sous forme de liste pour obtenir des informations plus détaillées.

![Activité de connexion](./media/concept-sign-ins/basic-sign-in.png "Activité de connexion")

> [!NOTE]
> Les clients peuvent maintenant résoudre les problèmes de stratégies d’accès conditionnel grâce à tous les rapports de connexion. Les clients peuvent examiner l’état de l’accès conditionnel et consulter en détail les stratégies applicables à la connexion et les résultats de chaque stratégie en cliquant sur l’onglet **Accès conditionnel** pour obtenir un rapport de connexion.
> Pour en savoir plus, consultez le [Forum aux questions sur les informations de l’accès conditionnel dans toutes les connexions](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrer les activités de connexion

Commencez par réduire les données signalées jusqu’au niveau qui vous convient. Ensuite, filtrez les données de connexions en utilisant le champ de date comme filtre par défaut. Azure AD vous offre une large gamme de filtres supplémentaires que vous pouvez définir.

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

- Un mois
- 7 jours
- 24 heures
- Intervalle de temps personnalisé

Lorsque vous sélectionnez une plage personnalisée, vous pouvez configurer une heure de début et une heure de fin.

Si vous ajoutez des champs à votre affichage de connexions, ils sont automatiquement ajoutés à la liste de filtres. Par exemple, en ajoutant un champ **Application cliente** à votre liste, vous obtenez également une autre option de filtre qui vous permet de définir les filtres suivants :  
![Activité de connexion](./media/concept-sign-ins/12.png "Activité de connexion")

- **Browser**  
    Ce filtre affiche tous les événements où des tentatives de connexion ont été effectuées à l’aide des flux de navigateur.
- **Exchange ActiveSync (pris en charge)**  
    Ce filtre affiche toutes les tentatives de connexion où le protocole Exchange ActiveSync (EAS) a été tenté depuis des plateformes prises en charge comme iOS, Android et Windows Phone.
- **Exchange ActiveSync (non pris en charge)**  
    Ce filtre affiche toutes les tentatives de connexion où le protocole EAS a été tenté depuis des plateformes non prises en charge comme les distributions Linux.
- **Clients Mobile Apps et Desktop** : ce filtre affiche toutes les tentatives de connexion qui n’utilisaient pas de flux de navigateur. Par exemple, des applications mobiles provenant de n’importe quelle plateforme et utilisant n’importe quel protocole ou n’importe quelle application cliente Desktop comme Office sur Windows ou MacOS.
  
- **Autres clients**
    - **IMAP**  
        Un client de messagerie hérité qui utilise IMAP pour récupérer le courrier électronique.
    - **MAPI**  
        Office 2013, où la bibliothèque ADAL est activée et utilise MAPI.
    - **Anciens clients Office**  
        Office 2013 dans sa configuration par défaut où la bibliothèque ADAL n’est pas activée et utilise MAPI, ou Office 2016, où la bibliothèque ADAL a été désactivée.
    - **POP**  
        Un client de messagerie hérité qui utilise POP3 pour récupérer le courrier électronique.
    - **SMTP**  
        Un client de messagerie hérité qui utilise SMTP pour envoyer le courrier électronique.

## <a name="download-sign-in-activities"></a>Télécharger les activités de connexion

Cliquez sur l’option **Télécharger** pour créer un fichier CSV ou JSON des 250 000 enregistrements les plus récents. Commencez par [télécharger les données de connexions](quickstart-download-sign-in-report.md) si vous souhaitez les utiliser en dehors du portail Azure.  

![Télécharger](./media/concept-sign-ins/71.png "Téléchargement")

> [!IMPORTANT]
> Le nombre d’enregistrements que vous pouvez télécharger est limité par les [stratégies de rétention de rapport Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Raccourcis vers les données de connexions

Azure AD et le portail Azure vous offrent d’autres points d’entrée pour accéder aux données de connexions :

- Vue d’ensemble de la protection de la sécurité des identités
- Utilisateurs
- Groupes
- Applications d’entreprise

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Données de connexion des utilisateurs dans la protection de la sécurité des identités

Le graphique des connexions des utilisateurs figurant sur la page de présentation **Protection de la sécurité des identités** affiche les agrégations hebdomadaires des connexions. La valeur par défaut de cette période est de 30 jours.

![Activité de connexion](./media/concept-sign-ins/06.png "Activité de connexion")

Lorsque vous cliquez sur un jour dans le graphique des connexions, vous obtenez une vue d’ensemble des activités de connexion correspondantes.

Chaque ligne de la liste des activités de connexion affiche :

* Qui s’est connecté ?
* Quelle application a été la cible de la connexion ?
* Quel est l’état de la connexion ?
* Quel est l’état MFA de la connexion ?

En cliquant sur un élément, vous obtenez plus d’informations sur l’opération de connexion :

- ID d'utilisateur
- Utilisateur
- Nom d’utilisateur
- ID de l'application
- Application
- Client
- Location
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
* Quelles sont les trois principales applications dans votre organisation ?
* Comment fonctionne mon application la plus récente ?

Le point d’entrée de ces données correspond aux trois principales applications de votre organisation. Les données sont contenues dans le rapport sur les 30 derniers jours dans la section **Vue d’ensemble** sous **Applications d’entreprise**.

![Activité de connexion](./media/concept-sign-ins/10.png "Activité de connexion")

Les graphiques d’utilisation des applications affiche les agrégations hebdomadaires des connexions pour vos trois principales applications au cours d’une période donnée. La valeur par défaut de cette période est de 30 jours.

![Activité de connexion](./media/concept-sign-ins/graph-chart.png "Activité de connexion")

Si vous le souhaitez, vous pouvez définir la focalisation sur une application spécifique.

![Reporting](./media/concept-sign-ins/single-app-usage-graph.png "Reporting")

Lorsque vous cliquez sur un jour dans le graphique d’utilisation des applications, vous obtenez une liste détaillée des activités de connexion.

L’option **Connexions** vous fournit une vue d’ensemble complète de tous les événements de connexion à vos applications.

## <a name="office-365-activity-logs"></a>Journaux d’activité Office 365

Vous pouvez consulter les journaux d’activité Office 365 dans le [centre d’administration Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). N’oubliez pas que les journaux d’activité d’Office 365 et d’Azure AD partagent un nombre important de ressources de répertoire. Seul le centre d’administration Microsoft 365 fournit une vue complète des journaux d’activité d’Office 365. 

Vous pouvez également accéder par programme aux journaux d’activité d’Office 365 en utilisant les [API de gestion Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Étapes suivantes

* [Codes d’erreur du rapport d’activité de connexion](reference-sign-ins-error-codes.md)
* [Stratégies de conservation des données Azure AD](reference-reports-data-retention.md)
* [Latences de rapport Azure AD](reference-reports-latencies.md)

