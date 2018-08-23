---
title: FAQ sur les rapports Azure Active Directory | Microsoft Docs
description: FAQ sur les rapports Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 05/10/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: f1683321e23eff82e73dc9bb44941fc390633b8c
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42145990"
---
# <a name="azure-active-directory-reporting-faq"></a>FAQ sur les rapports Azure Active Directory

Cet article répond aux questions fréquemment posées sur les rapports Azure Active Directory (Azure AD). Pour plus d’informations, consultez la page [Génération de rapports Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Prise en main 

**Q : J’utilise les API de point de terminaison https://graph.windows.net/&lt;nom-locataire&gt;/reports/ pour tirer (pull) par programmation les rapports d’audit Azure AD et les rapports d’utilisation des applications intégrées dans nos systèmes de création de rapports. Que dois-je utiliser à présent ?**

**R :** Consultez la [documentation de référence sur les API](https://developer.microsoft.com/graph/) pour savoir comment utiliser les nouvelles API dans le but d’accéder aux [rapports d’activité](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal). Ce point de terminaison comporte deux rapports (un d’audit et un autre sur les connexions) qui fournissent toutes les données dont vous disposiez dans l’ancien point de terminaison d’API. Ce nouveau point de terminaison comporte également un rapport sur les connexions, relatif à la licence Azure AD Premium, que vous pouvez utiliser pour obtenir des informations sur l’utilisation des applications, l’utilisation des appareils et les connexions utilisateur.

--- 

**Q : J’utilise les API de point de terminaison https://graph.windows.net/&lt;nom-locataire&gt;/reports/ pour tirer (pull) par programmation les rapports de sécurité Azure AD (concernant certains types de détections, telles que les fuites d’informations d’identification ou les connexions à partir d’adresses IP anonymes) dans notre système de création de rapports. Que dois-je utiliser à présent ?**

**R :** Vous pouvez utiliser [l’API Identity Protection relative aux événements à risque](../identity-protection/graph-get-started.md) pour accéder aux détections de sécurité via Microsoft Graph. Ce nouveau format offre davantage de flexibilité dans la façon dont vous pouvez interroger les données, c’est-à-dire avec un filtrage avancé, une sélection des champs, etc. De plus, il rassemble les événements à risque sous un même type pour faciliter l’intégration aux serveurs SIEM et autres outils de collecte de données. Étant donné que les données ont différents formats, vous ne pouvez pas remplacer vos anciennes requêtes par une nouvelle requête. Toutefois, [la nouvelle API utilise Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), qui est désormais la plateforme standard de Microsoft pour les API telles qu’Office 365 et Azure AD. Le travail nécessaire peut donc développer vos investissements MS Graph actuels ou vous aider à démarrer la transition vers cette nouvelle plateforme standard.

--- 

**Q : Comment obtenir une licence Premium ?**

**R :** Consultez [Prise en main d’Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) pour connaître la réponse à cette question.

---

**Q : Au bout de combien de temps peut-on consulter les données d’activité après avoir obtenu une licence Premium ?**

**R :** Si vous avez déjà des données d’activité sous une licence gratuite, vous pouvez voir les mêmes données. Si vous n’avez pas de données, cela prendra un ou deux jours.

---

**Q : Peut-on voir les données du mois dernier après avoir obtenu une licence Azure AD Premium ?**

**R :** Si vous êtes récemment passé à une version Premium (y compris une version d’évaluation), vous pouvez voir les données jusqu’à 7 jours dans un premier temps. Au fur et à mesure de l’accumulation des données, vous verrez jusqu’à 30 jours.

---

**Q : Faut-il être administrateur général pour voir les activités de connexion sur le portail Azure ou pour obtenir des données par le biais de l’API ?**

**R :** Non. Vous devez être **Lecteur Sécurité**, **Administrateur de sécurité** ou **Administrateur général** pour afficher les données des rapports dans le portail Azure ou par le biais de l’API.

---


## <a name="activity-logs"></a>Journaux d’activité


**Q : Quelle est la rétention des données des journaux d’activité (audit et connexions) sur le Portail Azure ?** 

**R :** Consultez [Pendant combien de temps les données collectées sont stockées ?](reference-reports-data-retention.md#q-for-how-long-is-the-collected-data-stored) pour la réponse à cette question.

--- 

**Q : Au bout de combien de temps peut-on voir les données d’activité une fois la tâche terminée ?**

**R :** Les journaux d’activité d’audit ont une latence comprise entre 15 minutes et 1 heure. Pour certains enregistrements, la mise à jour des journaux d’activité de connexion peut prendre de 15 minutes à 2 heures.

---


**Q : Peut-on obtenir des informations du journal d’activité d’Office 365 avec le Portail Azure ?**

**R :** Bien que les journaux d’activité d’Office 365 et d’Azure AD partagent une grande partie des ressources de l’annuaire, vous devez accéder au Centre d’administration Office 365 pour obtenir une vue complète des journaux d’activité d’Office 365.

---


**Q : Quelles API faut-il utiliser pour obtenir des informations sur les journaux d’activité d’Office 365 ?**

**R :** Utilisez les API Gestion d’Office 365 pour accéder aux [journaux d’activité d’Office 365 avec une API](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview).

---

**Q : Combien d’enregistrements peut-on télécharger à partir du Portail Azure ?**

**R :** Vous pouvez télécharger jusqu’à 5 000 enregistrements à partir du portail Azure. Les enregistrements sont triés par *date* et, par défaut, vous obtenez les 5 000 enregistrements les plus récents.

---

## <a name="risky-sign-ins"></a>Connexions risquées

**Q : ldentity Protection m’informe d’un événement à risque, mais je ne vois pas à quelle connexion il correspond parmi toutes les connexions répertoriées. Est-ce normal ?**

**R :** Oui, Identity Protection évalue les risques pour tous les flux d’authentification, qu’ils soient interactifs ou non. Toutefois, seules les connexions interactives sont répertoriées.

---

**Q : Comment puis-je télécharger le rapport « Utilisateurs avec indicateur de risque » dans le portail Azure ?**

**R :** L’option permettant de télécharger le rapport *Utilisateurs avec indicateur de risque* sera bientôt disponible.

---

**Q : Comment puis-je savoir pourquoi une connexion ou un utilisateur ont été signalés comme étant « à risque » dans le portail Azure ?**

**R :** Les utilisateurs de l’édition Premium peuvent en savoir plus sur les événements à risque sous-jacents en cliquant sur l’utilisateur répertorié sous « Utilisateurs avec indicateur de risque » ou en cliquant sur « Connexions à risque ». Les utilisateurs de l’édition gratuite et de l’édition De base peuvent voir les utilisateurs et les connexions à risque, mais pas les événements à risque sous-jacents.

---

**Q : Comment les adresses IP sont-elles calculées dans le rapport des connexions et des connexions à risque ?**

**R :** les adresses IP sont émises de manière à ce qu’il n’existe aucune connexion définitive entre une adresse IP et l’endroit où se trouve physiquement l’ordinateur avec cette adresse. Ceci est compliqué par des facteurs tels que les fournisseurs mobiles et les VPN qui émettent des adresses IP à partir de pools centraux souvent très éloignés de l’endroit où l’appareil client est réellement utilisé. Étant donné ce qui précède, la conversion de l’adresse IP en un emplacement physique constitue la meilleure solution pour les suivis, les données de registre, les recherches inversées et d’autres informations. 

---

**Q : Que signifie l’événement à risque « Connexion avec un risque supplémentaire détectée » ?**

**R :** Pour vous donner une idée de toutes les connexions à risque dans votre environnement, « Connexion avec un risque supplémentaire détectée » fonctionne comme un conteneur de connexions pour les détections propres aux abonnés d’Azure Active Directory Identity Protection.

---

**Q : Que signifie l’événement à risque « Connexion avec un risque supplémentaire détectée » ?**

**R :** Pour vous donner une idée de toutes les connexions à risque dans votre environnement, « Connexion avec un risque supplémentaire détectée » fonctionne comme un conteneur de connexions pour les détections propres aux abonnés d’Azure Active Directory Identity Protection.

---

## <a name="conditional-access"></a>Accès conditionnel

**Q : Quelles sont les nouveautés apportées par cette fonctionnalité ?**

**R :** Les clients peuvent maintenant résoudre les problèmes de stratégies d’accès conditionnel grâce à tous les rapports de connexion. Les clients peuvent examiner l’état de l’accès conditionnel et consulter en détail les stratégies applicables à la connexion, ainsi que les résultats de chaque stratégie.

**Q : Comment faire pour démarrer ?**

**R :** Pour commencer :
    * Accédez au rapport de connexion dans le [portail Azure](https://portal.azure.com). 
    * Cliquez sur la connexion que vous souhaitez dépanner.
    * Accédez à l’onglet **Accès conditionnel**. Cet onglet permet de consulter toutes les stratégies ayant eu une incidence sur des connexions, ainsi que le résultat de chaque stratégie. 
    
**Q : Quelles sont toutes les valeurs pouvant être prises par l’état de l’accès conditionnel ?**

**R :** L’état de l’accès conditionnel peut prendre les valeurs suivantes :
    * **Non applicable** : Aucune stratégie d’accès conditionnel ne s’appliquait à l’utilisateur et à l’application. 
    * **Réussite** : Une stratégie d’accès conditionnel s’appliquait à l’utilisateur et à l’application, et les stratégies d’accès conditionnel ont été respectées. 
    * **Échec** : Une stratégie d’accès conditionnel s’appliquait à l’utilisateur et à l’application, et les stratégies d’accès conditionnel n’ont pas été respectées. 
    
**Q : Quelles sont toutes les valeurs pouvant être prises par les résultats de la stratégie d’accès conditionnel ?**

**R :** Une stratégie d’accès conditionnel peut aboutir aux résultats suivants :
    * **Réussite** : La stratégie a été respectée.
    * **Échec** : La stratégie n’a pas été respectée.
    * **Non applicable** : Ce résultat peut découler du non-respect des conditions de la stratégie.
    * **Inactive** : La stratégie se trouve dans un état désactivé. 
    
**Q : Le nom de la stratégie dans le rapport de connexion ne correspond pas tout le temps au nom de la stratégie d’accès conditionnel. Pourquoi ?**

**R :** Dans tout le rapport de connexion, le nom de la stratégie correspond au nom qu’elle avait au moment de la connexion. Il peut différer si vous l’avez mis à jour ultérieurement, c’est-à-dire après la connexion.