---
title: Comment investiguer les risques dans Azure Active Directory Identity Protection (version actualisée) | Microsoft Docs
description: Découvrez comment examiner des utilisateurs, des détections et des connexions à risque dans Azure Active Directory Identity Protection (version actualisée).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e9715b63ccfbf1da3bdaedf947ea4cd65109b7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129485"
---
# <a name="how-to-investigate-risk"></a>Procédure : Examiner les risques

En utilisant les connexions à risque, les utilisateurs à risque et les rapports de détections de risques, vous pouvez examiner et mieux comprendre les risques dans votre environnement. La possibilité de filtrer et de trier les connexions, les utilisateurs et les détections à risque vous permet de mieux comprendre les intrusions potentielles dans votre organisation. 

## <a name="risky-users-report"></a>Rapport sur les utilisateurs à risque

Les informations indiquées dans le rapport Utilisateurs à risque vous permettent de trouver des réponses aux questions suivantes :

- Quels utilisateurs présentent un risque élevé ?
- Quels utilisateurs présentent l’état de risque corrigé ?

Votre premier point d’entrée dans ce rapport est la section **Examiner** de la page de sécurité.

![Rapport Utilisateurs à risque](./media/howto-investigate-risky-users-signins/01.png)

Affichage par défaut du rapport Utilisateurs à risque :

- Nom
- État du risque
- Niveau de risque
- Détail du risque
- Dernière mise à jour du risque
- Type
- Statut

![Rapport Utilisateurs à risque](./media/howto-investigate-risky-users-signins/03.png)

Vous pouvez personnaliser le mode Liste en cliquant sur **Colonnes** dans la barre d’outils.

![Rapport Utilisateurs à risque](./media/howto-investigate-risky-users-signins/04.png)

La boîte de dialogue des colonnes vous permet d’afficher des champs supplémentaires ou de supprimer des champs déjà affichés.

En cliquant sur un élément dans le mode Liste, vous pouvez obtenir toutes les informations disponibles le concernant dans un affichage horizontal.

![Rapport Utilisateurs à risque](./media/howto-investigate-risky-users-signins/05.png)

Affichage détaillé :

- Informations de base
- Connexions à risque récentes
- Les détections de risques non liées à une connexion
- Historique des risques

De plus, vous pouvez :

![Rapport Utilisateurs à risque](./media/howto-investigate-risky-users-signins/08.png)

- afficher le raccourci de toutes les connexions pour visualiser le rapport des connexions de l’utilisateur concerné ;
- afficher toutes les connexions à risque pour visualiser toutes les connexions de l’utilisateur concerné qui ont été marquées comme à risque ;
- réinitialiser le mot de passe d’un utilisateur si vous pensez que l’identité de ce dernier a été compromise ;
- ignorer le risque de l’utilisateur si vous pensez que les détections de risques actives d’un utilisateur sont des faux positifs. Pour plus d’informations, consultez l’article [Fournir un feedback sur les détections de risques dans Azure AD Identity Protection](howto-provide-risk-event-feedback.md).

### <a name="filter-risky-users"></a>Filtrage des utilisateurs à risque

Pour limiter les données transmises à un niveau qui vous convient, vous pouvez filtrer les données des utilisateurs à risque à l’aide des champs par défaut suivants :

- Nom
- Nom d’utilisateur
- État du risque
- Niveau de risque
- Type
- Statut

![Rapport Utilisateurs à risque](./media/howto-investigate-risky-users-signins/06.png)

Le filtre **Nom** vous permet de spécifier le nom ou le nom d’utilisateur principal de l’utilisateur qui vous intéresse.

Le filtre **État du risque** vous permet de sélectionner les états suivants :

- À risque
- Corrigé
- Ignoré

Le filtre **Niveau de risque** vous permet de sélectionner les niveaux suivants :

- Élevé
- Moyenne
- Faible

Le filtre **Type** vous permet de sélectionner les types suivants :

- Invité
- Membre

Le filtre **État** vous permet de sélectionner les états suivants :

- Deleted
- Actif

### <a name="download-risky-users-data"></a>Téléchargement des données des utilisateurs à risque

Vous pouvez télécharger les données relatives aux utilisateurs à risque afin de les utiliser en dehors du portail Azure. Quand vous cliquez sur Télécharger, vous générez un fichier CSV contenant les 2 500 enregistrements les plus récents. 

![Rapport sur les utilisateurs à risque](./media/howto-investigate-risky-users-signins/07.png)

Vous pouvez personnaliser le mode Liste en cliquant sur Colonnes dans la barre d’outils.
 
Cela vous permet d’afficher des champs supplémentaires, ou de supprimer des champs qui sont déjà affichés.
 
Pour en savoir plus sur un utilisateur à risque, cliquez sur le tiroir Détails pour le développer.

## <a name="risky-sign-ins-report"></a>Rapports sur les connexions risquées

Les informations indiquées dans le rapport Connexions à risque vous permettent de trouver des réponses aux questions suivantes :

- Combien de connexions présentant des détections de risques liées à une adresse IP anonyme ont-elles réussi la semaine dernière ?
- Quels sont les utilisateurs pour lesquels une compromission a été confirmée le mois dernier ?
- Quels sont les utilisateurs ayant eu des connexions au portail Office 365 à risque ?

Votre premier point d’entrée dans ce rapport est la section **Examiner** de la page de sécurité.

![Rapports sur les connexions risquées](./media/howto-investigate-risky-users-signins/02.png)

Affichage par défaut du rapport Connexions à risque :

- Date
- Utilisateur
- Application
- État de la connexion
- État du risque
- Niveau de risque (agrégé)
- Niveau de risque (temps réel)
- Accès conditionnel
- MFA obligatoire  

![Rapports sur les connexions risquées](./media/howto-investigate-risky-users-signins/09.png)

Vous pouvez personnaliser le mode Liste en cliquant sur **Colonnes** dans la barre d’outils.

![Rapport Utilisateurs à risque](./media/howto-investigate-risky-users-signins/11.png)

La boîte de dialogue des colonnes vous permet d’afficher des champs supplémentaires ou de supprimer des champs déjà affichés.

En cliquant sur un élément dans le mode Liste, vous pouvez obtenir toutes les informations disponibles le concernant dans un affichage horizontal.

![Rapport Utilisateurs à risque](./media/howto-investigate-risky-users-signins/12.png)

Affichage détaillé :

- Informations de base
- Informations sur l’appareil
- Informations sur le risque
- Informations d’authentification multifacteur (MFA)
- Accès conditionnel

De plus, vous pouvez :

![Rapport Utilisateurs à risque](./media/howto-investigate-risky-users-signins/13.png)

- confirmer la compromission ; 
- Confirmer comme étant sécurisée

Pour plus d’informations, consultez l’article [Fournir un feedback sur les détections de risques dans Azure AD Identity Protection](howto-provide-risk-event-feedback.md).

### <a name="filter-risky-sign-ins"></a>Filtrage des connexions à risque

Pour limiter les données transmises à un niveau qui vous convient, vous pouvez filtrer les données des utilisateurs à risque à l’aide des champs par défaut suivants :

- Utilisateur
- Application
- État de la connexion
- État du risque
- Niveau de risque (agrégé)
- Niveau de risque (temps réel)
- Accès conditionnel
- Date
- Type de niveau de risque

![Rapports sur les connexions risquées](./media/howto-investigate-risky-users-signins/14.png)

Le filtre **Nom** vous permet de spécifier le nom ou le nom d’utilisateur principal de l’utilisateur qui vous intéresse.

Le filtre **Application** vous permet de spécifier l’application cloud à laquelle l’utilisateur a essayé d’accéder.

Le filtre **État de la connexion** vous permet de sélectionner :

- Tous
- Succès
- Échec

Le filtre **État du risque** vous permet de sélectionner les états suivants :

- À risque
- État compromis confirmé
- Sécurité confirmée
- Ignoré
- Corrigé

Le filtre **Niveau de risque (agrégé)** vous permet de sélectionner les niveaux suivants :

- Élevé
- Moyenne
- Faible

Le filtre **Niveau de risque (temps réel)** vous permet de sélectionner les niveaux suivants :

- Élevé
- Moyenne
- Faible

Le filtre **Accès conditionnel** vous permet de sélectionner les éléments suivants :

- Tous
- Non applicable
- Succès
- Échec

Le filtre **Date** vous permet de définir un intervalle de temps pour les données renvoyées.
Les valeurs possibles sont les suivantes :

- Dernier mois
- 7 derniers jours
- 24 dernières heures
- Intervalle de temps personnalisé

### <a name="download-risky-sign-ins-data"></a>Téléchargement des données des connexions à risque

Vous pouvez télécharger les données relatives aux connexions à risque afin de les utiliser en dehors du portail Azure. Quand vous cliquez sur Télécharger, vous générez un fichier CSV contenant les 2 500 enregistrements les plus récents. 

![Rapport sur les utilisateurs à risque](./media/howto-investigate-risky-users-signins/15.png)


## <a name="risk-detections-report"></a>Rapport sur les détections de risques

Le rapport sur les détections de risques vous donne un aperçu de chaque détection des risques liés à Identity Protection dans votre locataire.


Le rapport de détection des risques contient une vue par défaut qui indique :

- Date

- Utilisateur

- Adresse IP

- Location

- Type de détection

- État du risque

- Niveau de risque

- ID de la demande
 

Vous pouvez personnaliser le mode Liste en cliquant sur **Colonnes** dans la barre d’outils. La boîte de dialogue des colonnes vous permet d’afficher des champs supplémentaires ou de supprimer des champs déjà affichés.

En cliquant sur un élément dans le mode Liste, vous pouvez obtenir toutes les informations disponibles le concernant dans un affichage horizontal.


La vue Détails affiche des informations supplémentaires sur la détection des risques.

La vue Détails comprend également des liens pour

- Afficher le rapport sur les risques de l’utilisateur
- Afficher les connexions de l’utilisateur
- Afficher les connexions risquées de l’utilisateur
- Afficher la connexion risquée liée
- Afficher les détections de risque de l’utilisateur


### <a name="filter-risk-detections"></a>Filtrer les détections de risques

Pour limiter les données transmises à un niveau qui vous convient, vous pouvez filtrer les données de détection des risques à l’aide des champs par défaut suivants :

- Heure de détection
- Type de détection
- État du risque
- Niveau de risque
- Utilisateur
- Nom d’utilisateur
- Adresse IP
- Location
- Minutage de la détection
- Activité 
- Source
- Type d’émetteur de jeton
- ID de la demande
- ID de corrélation :


Le filtre **Heure de détection** vous permet de définir un intervalle de temps pour les données renvoyées. Ce filtre vous permet de sélectionner :
- 90 derniers jours
- 30 derniers jours
- 7 derniers jours
- 24 dernières heures
- Intervalle de temps personnalisé

Le filtre **Type de détection** vous permet de spécifier le type de détection des risques (tels que les propriétés de connexion inhabituelles).

Le filtre **État du risque** vous permet de sélectionner les états suivants :

- À risque
- État compromis confirmé
- Sécurité confirmée
- Ignoré
- Corrigé

Le filtre **Niveau de risque** vous permet de sélectionner les niveaux suivants :

- Élevé
- Moyenne
- Faible

Le filtre **Utilisateur** vous permet de spécifier le nom ou l’ID d’objet de l’utilisateur qui vous intéresse.

Le filtre **Nom d’utilisateur** vous permet de spécifier le nom d’utilisateur principal de l’utilisateur qui vous intéresse.

Le filtre **Minutage de détection** vous permet de spécifier si la détection était en temps réel ou hors connexion. Remarque : Vous pouvez tester des connexions avec des détections en temps réel à l’aide de la stratégie de risque de connexion. 

Le filtre **Activité** vous permet de spécifier si la détection a été liée à une connexion (par exemple, une adresse IP anonyme) ou à un utilisateur (par exemple, des informations d’identification divulguées).

Le filtre **Source** vous permet de spécifier la source de la détection des risques (par exemple, Azure AD ou Microsoft Cloud App Security). 

Le filtre **Type d’émetteur de jeton** vous permet de spécifier l’endroit où le jeton a été émis (par exemple, Azure AD ou services de fédération Active Directory)


### <a name="download-risk-detections-data"></a>Télécharger les données de détection des risques

Vous pouvez télécharger les données des détections de risques pour les utiliser en dehors du Portail Azure. Quand vous cliquez sur Télécharger, vous générez un fichier CSV contenant les 5 000 enregistrements les plus récents. 

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble du service Azure AD Identity Protection, consultez l’article de [présentation d’Azure AD Identity Protection](overview-v2.md).
