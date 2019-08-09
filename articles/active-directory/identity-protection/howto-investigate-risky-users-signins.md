---
title: Examen des connexions et des utilisateurs à risque dans Azure Active Directory Identity Protection (version actualisée) | Microsoft Docs
description: Découvrez comment examiner des connexions et des utilisateurs à risque dans Azure Active Directory Identity Protection (version actualisée).
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.author: joflore
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.date: 01/25/2019
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a90195a2d0899b0a157cc67badd2f9873164987
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108964"
---
# <a name="how-to-investigate-risky-users-and-sign-ins"></a>Procédure : Examiner les utilisateurs et les connexions à risque 


Les rapports Connexions à risque et Utilisateurs à risque vous permettent d’examiner les risques pesant sur votre environnement et d’en tirer des insights. La possibilité de filtrer et trier les connexions et utilisateurs à risque vous permet de mieux comprendre les intrusions potentielles dans votre organisation. 


## <a name="risky-users-report"></a>Rapport Utilisateurs à risque

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

- Événements à risque non liés à une connexion

- Historique des risques



De plus, vous pouvez :

![Rapport Utilisateurs à risque](./media/howto-investigate-risky-users-signins/08.png)

- afficher le raccourci de toutes les connexions pour visualiser le rapport des connexions de l’utilisateur concerné ;

- afficher toutes les connexions à risque pour visualiser toutes les connexions de l’utilisateur concerné qui ont été marquées comme à risque ;

- réinitialiser le mot de passe d’un utilisateur si vous pensez que l’identité de ce dernier a été compromise ;

- ignorer le risque de l’utilisateur si vous pensez que les événements à risque actifs d’un utilisateur sont des faux positifs. Pour plus d’informations, consultez l’article lié à [l’amélioration de la précision de la détection](howto-improve-detection-accuracy.md).



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

- Combien de connexions présentant des événements à risque liés à une adresse IP anonyme ont-elles réussi la semaine dernière ?

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

- confirmer la sécurité.

Pour plus d’informations, consultez l’article lié à [l’amélioration de la précision de la détection](howto-improve-detection-accuracy.md).




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



## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une vue d’ensemble du service Azure AD Identity Protection, consultez l’article de [présentation d’Azure AD Identity Protection](overview-v2.md).
