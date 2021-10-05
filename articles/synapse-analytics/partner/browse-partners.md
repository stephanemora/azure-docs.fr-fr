---
title: Découvrir des solutions tierces de partenaires Azure Synapse à l’aide de Synapse Studio
description: Découvrez comment détecter de nouvelles solutions tierces qui sont étroitement intégrées à des partenaires Azure Synapse
services: synapse-analytics
manager: santoshb
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/14/2021
author: periclesrocha
ms.author: procha
ms.reviewer: omafnan, wiassaf
ms.custom: seo-lt-2019
ms.openlocfilehash: c765fcce0b95027ffa28915752bd3f370070a894
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123482030"
---
# <a name="discover-partner-solutions-through-synapse-studio"></a>Découvrir des solutions partenaires à l’aide de Synapse Studio

Synapse Studio permet de découvrir des partenaires de solutions qui étendent les fonctionnalités d’Azure Synapse. De connecteurs de données, via des outils de data wrangling, des moteurs d’orchestration et d’autres charges de travail, la page **Parcourir les partenaires** sert de hub pour la découverte d’applications et solutions ISV tierces agréées pour fonctionner avec Azure Synapse Analytics. Synapse Studio simplifie la prise en main de ces partenaires, dans de nombreux cas avec une configuration automatisée de la connexion initiale à la plateforme partenaire.

## <a name="participating-partners"></a>Partenaires participants
Le tableau suivant répertorie les solutions de partenaires actuellement prises en charge. Veillez à le consulter régulièrement, car nous ajoutons sans cesse de nouveaux partenaires à cette liste. 

| Partenaire | Nom de la solution |
| ------- | ------------- |
| ![Incorta](./media/data-integration/incorta-logo.png) | Incorta Intelligent Ingest for Azure Synapse |
| ![Informatica](./media/data-integration/informatica_logo.png) | Informatica Intelligent Data Management Cloud |
| ![Qlik Data Integration (anciennement Attunity)](./media/business-intelligence/qlik_logo.png) | Qlik Data Integration (anciennement Attunity) |

## <a name="requirements"></a>Configuration requise
Lorsque vous avez choisi une application partenaire, Azure Synapse Studio provisionne un environnement de bac à sable (sandbox) que vous pouvez utiliser pour cette version d’évaluation, ce qui vous permet de tester rapidement les solutions de partenaires avant de décider de les utiliser avec vos données de production. Les objets suivants sont créés : 

|  Objet  |    Détails    |
| -------- | ------------- |
| Un [pool SQL dédié](../overview-what-is.md) nommé **Partner_[NomPartenaire]_pool** | Niveau de performance DW100c. |
| Une [connexion SQL](/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) nommée **Partner_[NomPartenaire]_login** | Créée sur votre base de données `master`. Le mot de passe de cette connexion SQL vous est spécifié lors de la création de votre évaluation.|
| Un [utilisateur de base de données](../../azure-sql/database/logins-create-manage.md) | Nouvel utilisateur de base de données, mappé à la nouvelle connexion SQL. Cet utilisateur est ajouté au rôle db_owner pour la base de données qui vient d’être créée. |

Dans tous les cas, **[NomPartenaire]** est le nom de l’ISV tiers qui propose la version d’évaluation. 

### <a name="security"></a>Sécurité 
Une fois les objets requis créés, Synapse Studio envoie des informations sur votre nouvel environnement sandbox à l’application partenaire, vous offrant ainsi une expérience d’évaluation personnalisée. Les informations suivantes sont envoyées à nos partenaires : 
- Prénom
- Nom
- Adresse de messagerie
-  Détails sur l’environnement Synapse requis pour établir une connexion :     
    - Nom DNS de votre espace de travail Synapse (nom du serveur)
    - Nom du pool SQL (base de données)
    - Connexion SQL (nom d’utilisateur uniquement)
    
Nous ne partageons jamais de mots de passe avec l’application partenaire, y compris le mot de passe de la connexion SQL nouvellement créée. Vous devrez saisir votre mot de passe dans l’application partenaire.

### <a name="costs"></a>Coûts
Le pool SQL dédié créé pour votre essai partenaire entraîne des coûts en continu, qui sont basés sur le nombre de blocs DWU et d’heures d’exécution. Veillez à suspendre le pool SQL créé pour cet essai partenaire lorsqu’il n’est pas utilisé, afin d’éviter des frais inutiles. 

## <a name="starting-a-new-partner-trial"></a>Démarrer un nouvel essai partenaire 

1) Dans la page d’accueil de Synapse Studio, sous **Découvrir plus**, sélectionnez **Parcourir les partenaires**.
2) La page Parcourir les partenaires affiche tous les partenaires qui proposent actuellement des versions d’évaluation qui permettent une connectivité directe avec Azure Synapse. Choisissez une solution partenaire.
3) La page des détails sur le partenaire affiche des informations pertinentes sur cette application et des liens pour en savoir plus sur la solution. Lorsque vous êtes prêt à démarrer un essai, sélectionnez **Se connecter au partenaire**.
4) Dans la page **Se connecter à la solution [NomPartenaire]** , notez les conditions requises pour cette connexion au partenaire. Modifiez le nom du pool SQL et les paramètres de connexion SQL si vous le souhaitez (ou acceptez les valeurs par défaut), saisissez le mot de passe de votre nouvelle connexion SQL, puis sélectionnez **Se connecter**.

Les objets requis seront créés pour votre essai partenaire. Vous êtes ensuite redirigé vers une page partenaire pour fournir des informations supplémentaires (si nécessaire) et démarrer votre essai. 

> [!NOTE]
> Microsoft ne contrôle pas l’expérience d’essai partenaire. Les partenaires proposent des essais de produits selon leurs propres termes, et l’expérience, la disponibilité de l’essai et les fonctionnalités peuvent varier en fonction du partenaire. Microsoft n’offre pas de support pour les applications tierces proposées dans Synapse Studio. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur certains de nos autres partenaires, consultez [Partenaires d’intégration des données](data-integration.md), [Partenaires de gestion des données](data-management.md) et [Partenaires de Machine Learning et d’IA](machine-learning-ai.md).