---
title: Utilisation du pack de contenu Power BI Azure Active Directory | Microsoft Docs
description: Découvrir comment utiliser le pack de contenu Power BI Azure Active Directory
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d810b8be496bbfd1c5bd88d8221e77dd5b76c889
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622962"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Utilisation du pack de contenu Power BI Azure Active Directory

|  |
|--|
|Actuellement, le pack de contenu Azure AD Power BI utilise les API Azure AD Graph pour récupérer des données à partir de votre locataire Azure AD. Par conséquent, vous pouvez voir certaines différences entre les données disponibles dans le pack de contenu et les données récupérées à l’aide des [API Microsoft Graph pour les rapports](concept-reporting-api.md). |
|  |

Le pack de contenu Power BI pour Azure Active Directory (Azure AD) contient des rapports prédéfinis conçus pour vous aider à comprendre comment vos utilisateurs adoptent et utilisent les fonctionnalités Azure AD. Cela vous permet d’obtenir des insights sur toutes les activités au sein de votre répertoire, à l’aide de l’expérience de visualisation riche dans Power BI. Vous pouvez aussi créer votre propre tableau de bord et le partager avec d’autres personnes de votre organisation. 

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une licence Azure AD premium (P1/P2) pour utiliser le pack de contenu. 

## <a name="install-the-content-pack"></a>Installer le pack de contenu

Consultez le [démarrage rapide](quickstart-install-power-bi-content-pack.md) pour installer le pack de contenu Azure AD Power BI.

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>Rapports inclus dans cette version du pack de contenu de journaux Azure AD

Les rapports suivants sont inclus dans le pack de contenu Power BI Azure AD. Les rapports contiennent les données des **30 derniers jours**.

**Rapport sur l’utilisation des applications et les tendances** : ce rapport vous donne des informations sur les applications utilisées dans votre organisation. Vous pouvez obtenir la liste des applications les plus populaires, ou comprendre comment une application récemment déployée dans votre organisation est utilisée. Cela vous permet de suivre et d’améliorer l’utilisation au fil du temps.

**Connexions par emplacement et utilisateurs** : ce rapport fournit des données sur toutes les connexions effectuées à l’aide d’Azure Identity. Ce rapport permet d’examiner de manière plus approfondie les connexions individuelles et de répondre aux questions telles que :

- À partir de quel endroit l’utilisateur s’est-il connecté ?
- Quel utilisateur se connecte le plus et d’où se connecte-t’il ? 
- La connexion a-t-elle réussie ?  
 
Vous pouvez également filtrer les résultats en sélectionnant une date ou un emplacement spécifique.

**Utilisateurs uniques par application** : ce rapport fournit une vue de l’ensemble des utilisateurs uniques utilisant une application donnée. Il comprend uniquement les utilisateurs qui se sont connectés « *avec succès* » à une application.

**Connexions d’appareil** : ce rapport vous permet de comprendre les différents profils d’appareils utilisés au sein de votre organisation et de déterminer les stratégies d’appareils en fonction de ce qui est utilisé. Il fournit des données autour du type de systèmes d’exploitation et de navigateurs utilisés pour la connexion aux applications, ainsi que des informations détaillées sur les utilisateurs, notamment :

- User Name
- Adresse IP
- Lieu 
- État de la connexion 

**Synthèse SSPR** : ce rapport vous aide à comprendre l’utilisation de l’outil SSPR au sein de votre organisation. Vous pouvez voir le nombre de tentatives de réinitialisation de mot de passe via l’outil SSPR et du nombre de celles ayant abouti. Vous pouvez aussi approfondir les échecs de réinitialisation de mot de passe pour comprendre les raisons pour lesquelles certains échecs se sont produits. 

## <a name="customize-azure-ad-activity-content-pack"></a>Personnalisation du pack de contenu d’activité Azure AD

**Modifier la visualisation** : vous pouvez modifier une visualisation des rapports en cliquant sur **Modifier le rapport** et sélectionner la visualisation que vous souhaitez.
 
![Pack de contenu Power BI Azure Active Directory](./media/howto-power-bi-content-pack/09.png) 
 
![Pack de contenu Power BI Azure Active Directory](./media/howto-power-bi-content-pack/10.png) 

**Inclure des champs supplémentaires** : vous pouvez ajouter un champ au rapport ou le supprimer en sélectionnant l’élément visuel auquel vous souhaitez ajouter/supprimer le champ. Par exemple, vous pouvez ajouter le champ « État de la connexion » à la vue de table, comme indiqué ci-dessous. 
 
![Pack de contenu Power BI Azure Active Directory](./media/howto-power-bi-content-pack/11.png) 

**Épingler des visualisations au tableau de bord** : vous pouvez personnaliser votre tableau de bord et inclure vos propres visualisations au rapport et l’épingler sur le tableau de bord. 

![Pack de contenu Power BI Azure Active Directory](./media/howto-power-bi-content-pack/13.png) 
 
**Partager le tableau de bord** : vous pouvez également partager le tableau de bord avec les utilisateurs au sein de votre organisation. Une fois le rapport partagé, les utilisateurs peuvent voir les champs que vous avez sélectionnés dans le rapport.
 
![Pack de contenu Power BI Azure Active Directory](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>Planification d’une actualisation quotidienne de votre rapport Power BI

Pour planifier une actualisation quotidienne de votre rapport Power BI, accédez à **Jeux de données** > **Paramètres** > **Planifier l’actualisation** et définissez-la comme indiqué ci-dessous.
 
![Pack de contenu Power BI Azure Active Directory](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>Mise à jour vers une version plus récente du pack de contenu

Si vous souhaitez mettre à jour votre pack de contenu vers une version plus récente :

- Téléchargez le nouveau pack de contenu et configurez-le conformément aux instructions figurant dans cet article.

- Une fois configuré, accédez à **Source de données** > **Paramètres** > **Informations d’identification de la source de données** et entrez à nouveau vos informations d’identification.

    ![Pack de contenu Power BI Azure Active Directory](./media/howto-power-bi-content-pack/16.png) 

Après avoir vérifié que la nouvelle version du pack de contenu fonctionne comme prévu, vous pouvez supprimer l’ancienne version, si nécessaire en supprimant les rapports et les jeux de données sous-jacents associés à ce pack de contenu.

## <a name="still-having-issues"></a>Vous rencontrez toujours des problèmes ? 

Consultez notre [guide de résolution des problèmes](troubleshoot-content-pack.md). Pour obtenir une aide générale sur Power BI, consultez ces [articles d’aide](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
 
 
## <a name="next-steps"></a>Étapes suivantes

* [Installer un pack de contenu Power BI](quickstart-install-power-bi-content-pack.md).
* [Résoudre les problèmes liés aux erreurs de pack de contenu](troubleshoot-content-pack.md).
* [Que sont les rapports Azure AD ?](overview-reports.md).
