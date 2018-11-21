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
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: fab94088d1d54012a955b0663b078d03b13d6299
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624910"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Trouver les rapports d’activité sur le Portail Azure

Dans cet article, vous allez apprendre à rechercher les rapports d’activité utilisateur Azure Active Directory (Azure AD) sur le portail Azure.

## <a name="audit-logs-report"></a>Rapport de journaux d’audit

Le rapport de journaux d’audit regroupe plusieurs rapports en lien avec les activités des applications sur une vue unique pour la création de rapports contextuels. Pour accéder au rapport de journaux d’audit :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Sélectionnez votre annuaire en haut à droite de l'écran, puis sélectionnez le panneau **Azure Active Directory** à partir du volet de navigation de gauche.
3. Sélectionnez **Journaux d’audit** dans la section **Activité** du panneau Azure Active Directory. 

    ![Journaux d’audit](./media/howto-find-activity-reports/482.png "Journaux d’Audit")

Le rapport de journaux d’audit regroupe les rapports suivants :

* Rapport d’audit
* Activité de réinitialisation de mot de passe
* Activité de l’enregistrement de la réinitialisation de mot de passe
* Activité de groupes en libre-service
* Changements de nom de groupe d’Office 365
* Activité d’approvisionnement de compte
* État de substitution de mot de passe
* Erreurs de configuration de compte

### <a name="filtering-on-audit-logs"></a>Filtrage sur les journaux d’audit

Vous pouvez appliquer le filtrage avancé au rapport d’audit pour accéder à une catégorie spécifique de données d’audit, en spécifiant celle-ci dans le filtre **Catégorie d’activité**. Par exemple, pour afficher toutes les activités en lien avec la réinitialisation de mot de passe en libre-service, sélectionnez la catégorie **Gestion de mot de passe en libre-service**. 

    ![Category options on the Filter Audit Logs page](./media/howto-find-activity-reports/06.png "Category options on the Filter Audit Logs page")

Les catégories d’activités sont les suivantes :

- Annuaire principal
- Gestion des mots de passe en libre-service
- Gestion des groupes en libre-service
- Approvisionnement des comptes


## <a name="sign-ins-report"></a>Rapport de connexions 

La vue **Connexions** inclut toutes les connexions utilisateur ainsi que le rapport **Utilisation des applications**. Vous pouvez également afficher des informations sur l’utilisation des applications dans la section **Gérer** de la vue d’ensemble **Applications d’entreprise**.

    ![Enterprise applications](./media/howto-find-activity-reports/484.png "Enterprise applications")

Pour accéder au rapport de connexions :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Sélectionnez votre annuaire en haut à droite de l'écran, puis sélectionnez le panneau **Azure Active Directory** à partir du volet de navigation de gauche.
3. Sélectionnez **Connexions** dans la section **Activité** du panneau Azure Active Directory. 

    ![Vue Connexions](./media/howto-find-activity-reports/483.png "Vue Connexions")


### <a name="filtering-on-application-name"></a>Filtrage sur le nom de l’application

Vous pouvez utiliser le rapport de connexions pour afficher des détails sur l’utilisation d'une application, en filtrant sur le nom d’utilisateur ou sur le nom de l’application.

![Page Filtrer les événements de connexion](./media/howto-find-activity-reports/07.png "Page Filtrer les événements de connexion")

## <a name="security-reports"></a>Rapports de sécurité

### <a name="anomalous-activity-reports"></a>Rapports d’activités anormales

Les rapports d'activité anormaux fournissent des informations sur les événements à risque qu'Azure AD peut détecter et signaler.

Le tableau suivant répertorie les rapports de sécurité sur les activités anormales d’Azure AD et les types d’événements à risque correspondants dans le portail Azure. Pour plus d’informations, consultez [Événements à risque dans Azure Active Directory](concept-risk-events.md).  


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


### <a name="detected-risk-events"></a>Événements à risque détectés

Sur le **portail Azure**, vous pouvez accéder aux rapports relatifs aux événements à risque détectés dans la section **Sécurité** du panneau [Azure Active Directory](https://portal.azure.com). Les événements à risque détectés sont suivis dans les rapports suivants :   

- [Utilisateurs à risque](concept-user-at-risk.md)
- [Connexions risquées](concept-risky-sign-ins.md)

    ![Rapports de sécurité](./media/howto-find-activity-reports/04.png "Rapports de sécurité")

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble des journaux d’audit](concept-audit-logs.md)
* [Vue d’ensemble des connexions](concept-sign-ins.md)
* [Vue d’ensemble des événements à risque](concept-risk-events.md)
