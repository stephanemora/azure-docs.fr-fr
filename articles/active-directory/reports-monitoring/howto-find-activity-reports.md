---
title: Rechercher les rapports d’activité utilisateur Azure Active Directory dans le portail Azure | Microsoft Docs
description: Découvrez où se trouvent les rapports d’activité utilisateur Azure Active Directory sur le portail Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: report-monitor
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 182537d6f07b624f2395f591681ed4596579bde0
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2018
ms.locfileid: "42139829"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Trouver les rapports d’activité sur le Portail Azure

Dans cet article, nous expliquons comment trouver les rapports d’activité utilisateur Azure Active Directory sur le portail Azure.

## <a name="activity-and-integrated-app-reports"></a>Rapports d’activité et d’application intégrée

Dans le portail Azure, pour la création de rapports basés sur le contexte, les rapports existants sont fusionnés en une seule vue. Une seule API sous-jacente fournit les données de la vue.

Pour afficher cette vue, dans le panneau **Azure Active Directory**, sous **Activité**, sélectionnez **Journaux d’audit**.

![Journaux d’audit](./media/howto-find-activity-reports/482.png "Journaux d’Audit")

Les rapports suivants sont consolidés dans cette vue :

* Rapport d’audit
* Activité de réinitialisation de mot de passe
* Activité de l’enregistrement de la réinitialisation de mot de passe
* Activité de groupes en libre-service
* Changements de nom de groupe d’Office 365
* Activité d’approvisionnement de compte
* État de substitution de mot de passe
* Erreurs de configuration de compte


Le rapport d’utilisation de l’application a été amélioré et inclus dans la vue **Connexions**. Pour afficher cette vue, sur le panneau **Azure Active Directory**, sous **Activité**, sélectionnez **Connexions**.

![Vue Connexions](./media/howto-find-activity-reports/483.png "Vue Connexions")

La vue **Connexions** inclut toutes les connexions des utilisateurs. Vous pouvez utiliser ces informations pour obtenir des informations sur l’utilisation de l’application. Vous pouvez également afficher des informations sur l’utilisation de l’application dans la vue d’ensemble **Applications d’entreprise** sous la section **Gérer**.

![Applications d’entreprise](./media/howto-find-activity-reports/484.png "Applications d’entreprise")

## <a name="access-a-specific-report"></a>Accéder à un rapport spécifique

Bien que le portail Azure offre une vue unique, vous pouvez également consulter des rapports spécifiques.

### <a name="audit-logs"></a>Journaux d’audit

En réponse aux commentaires des clients, dans le portail Azure, vous pouvez utiliser un filtrage avancé pour accéder aux données de votre choix. Vous pouvez utiliser le filtre *Catégorie de l’activité*, qui répertorie les différents types de journaux d’activité dans Azure AD. Pour restreindre les résultats à ce que vous recherchez, vous pouvez sélectionner une catégorie.

Par exemple, si vous souhaitez simplement obtenir les activités liées aux réinitialisations de mot de passe libre-service, vous pouvez choisir la catégorie **Gestion des mots de passe en libre-service**. Les catégories affichées sont basées sur la ressource que vous utilisez.  

![Options de catégories dans la page Filtrer les journaux d’audit](./media/howto-find-activity-reports/06.png "Options de catégories dans la page Filtrer les journaux d’audit")

Les catégories d’activités sont les suivantes :

- Annuaire principal
- Gestion des mots de passe en libre-service
- Gestion des groupes en libre-service
- Approvisionnement des comptes

### <a name="application-usage"></a>Utilisation des applications

Pour afficher plus d’informations sur l’utilisation des applications (pour toutes les applications ou une seule application), sous **Activité**, sélectionnez **Connexions**. Pour restreindre les résultats, vous pouvez filtrer par nom d’utilisateur ou par nom d’application.

![Page Filtrer les événements de connexion](./media/howto-find-activity-reports/07.png "Page Filtrer les événements de connexion")

### <a name="security-reports"></a>Rapports de sécurité

#### <a name="azure-ad-anomalous-activity-reports"></a>Rapports d’activités anormales Azure AD

Les rapports de sécurité sur les activités anormales d’Azure Active Directory sont consolidés pour vous fournir une seule vue centralisée. Cette vue affiche tous les événements liés à la sécurité qu’Azure AD peut détecter et signaler dans des rapports.

Le tableau suivant répertorie les rapports de sécurité sur les activités anormales d’Azure AD et les types d’événements à risque correspondants dans le portail Azure.

| Rapport d’activités anormales Azure AD |  Type d’événement à risque signalé par Identity Protection|
| :--- | :--- |
| Utilisateurs avec des informations d’identification volées | Informations d’identification divulguées |
| Activité de connexion anormale | Voyage impossible vers des emplacements inhabituels |
| Connexions à partir d’appareils potentiellement infectés | Connexions depuis des appareils infectés|
| Connexions à partir de sources inconnues | Connexions depuis des adresses IP anonymes |
| Connexions depuis des adresses IP avec des activités suspectes | Connexions depuis des adresses IP avec des activités suspectes |
| - | Connexions depuis des emplacements non connus |

Les rapports de sécurité sur les activités anormales d’Azure AD suivants ne sont pas inclus en tant qu’événements à risque dans le portail Azure :

* Connexions après plusieurs échecs
* Connexions depuis plusieurs zones géographiques

Pour plus d’informations, consultez [Événements à risque dans Azure Active Directory](concept-risk-events.md).  


#### <a name="detected-risk-events"></a>Événements à risque détectés

Dans le portail Azure, vous pouvez accéder aux rapports sur les événements à risque détectés dans le panneau **Azure Active Directory** sous **Sécurité**. Les événements à risque détectés sont suivis dans les rapports suivants :   

- les utilisateurs à risque ;
- les connexions risquées.

![Rapports de sécurité](./media/howto-find-activity-reports/04.png "Rapports de sécurité")

Pour plus d’informations sur les rapports de sécurité, consultez :

- [Rapport sur la sécurité des utilisateurs courant un risque dans le portail Azure Active Directory](concept-user-at-risk.md)
- [Rapports sur les connexions risquées dans le portail Azure Active Directory](concept-risky-sign-ins.md)


Pour afficher le rapport **d’utilisation des applications**, dans le panneau **Azure Active Directory**, sous **Gérer**, sélectionnez **Applications d’entreprise**, puis **Connexions**.


![Rapport des connexions aux applications d’entreprise](./media/howto-find-activity-reports/199.png)

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble des rapports, consultez [Création de rapports Azure Active Directory](overview-reports.md).
