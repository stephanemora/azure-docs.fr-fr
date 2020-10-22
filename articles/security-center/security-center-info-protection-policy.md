---
title: Personnaliser la stratégie de protection des informations SQL - Azure Security Center
description: Découvrez comment personnaliser les stratégies de protection des informations dans Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: 6991c222590b52ca4dadb2b9f5a9661bf731c4c4
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340833"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Personnaliser la stratégie de protection des informations SQL dans Azure Security Center (préversion)
 
Vous pouvez définir et personnaliser une stratégie de protection des informations SQL pour tout votre locataire Azure dans Azure Security Center.

La protection des informations est une fonctionnalité de sécurité avancée pour la découverte, la classification, l’étiquetage et la création de rapports de données sensibles dans vos ressources de données Azure. La découverte et la classification de vos données les plus sensibles (professionnelles, financières, soins de santé, données personnelles, etc.) peuvent jouer un rôle essentiel dans la protection des informations de l’organisation. Elles peuvent servir d’infrastructure pour :
- Aider à répondre aux standards de confidentialité des données et aux exigences de conformité réglementaires
- Des scénarios de sécurité comme la supervision (audit) et la génération d’alertes en cas d’accès anormaux aux données sensibles
- Contrôler l’accès et renforcer la sécurité des bases des magasins de données contenant des données sensibles
 
La [protection des informations SQL](../azure-sql/database/data-discovery-and-classification-overview.md) implémente ce paradigme pour vos magasins de données SQL, actuellement pris en charge pour Azure SQL Database. La protection des informations SQL détecte et classe automatiquement les données potentiellement sensibles, fournit un mécanisme permettant d’étiqueter de manière permanente les données sensibles avec des attributs de classification, et elle fournit un tableau de bord détaillé montrant l’état de classification de la base de données. En outre, il calcule la sensibilité du jeu de résultats des requêtes SQL, pour permettre un audit explicite des requêtes extrayant des données sensibles, et protéger les données. Pour plus d’informations sur la protection des informations SQL, consultez [Découverte et classification des données d’Azure SQL Database](../azure-sql/database/data-discovery-and-classification-overview.md).
 
Le mécanisme de classification est basé sur deux constructions principales qui composent la taxonomie de classification : les **étiquettes** et les **types d’informations**.
- **Étiquettes** : principaux attributs de classification, utilisés pour définir le niveau de confidentialité des données stockées dans la colonne. 
- **Types d’informations** : spécifie une granularité supplémentaire concernant le type des données stockées dans la colonne.
 
La protection des informations est fournie avec un ensemble intégré d’étiquettes et de types d’informations utilisés par défaut. Pour personnaliser ces étiquettes et ces types, vous pouvez personnaliser la stratégie de protection des informations dans Security Center.
 
## <a name="customize-the-information-protection-policy"></a>Personnaliser la politique de protection des informations
Pour personnaliser la stratégie de protection des informations pour votre locataire Azure, vous devez disposer [des privilèges d’administrateur sur le groupe d’administration racine du locataire](security-center-management-groups.md). 
 
1. Dans le menu principal de Security Center, sous **RESSOURCES SÉCURITÉ HYGIÈNE**, accédez à **Données et stockage**, puis cliquez sur le bouton **Protection des informations SQL**.

   ![Configurer la politique de protection des informations](./media/security-center-info-protection-policy/security-policy.png) 
 
2. Sur la page **Protection des informations SQL**, vous pouvez afficher votre jeu d’étiquettes actuel. Il s’agit des principaux attributs de classification utilisés pour classer le niveau de sensibilité de vos données. À ce stade, vous pouvez configurer les **étiquettes de protection des informations** et les **types d’informations** du locataire. 
 
### <a name="customizing-labels"></a>Personnalisation des étiquettes
 
1. Vous pouvez modifier ou supprimer une étiquette existante ou bien en ajouter une nouvelle. Pour modifier une étiquette existante, sélectionnez-la puis cliquez sur **Configurer**, en haut ou dans le menu contextuel à droite. Pour ajouter une nouvelle étiquette, cliquez sur **Créer une étiquette** dans la barre de menus supérieure, ou en bas de la table des étiquettes.
2. Depuis l’écran **Configurer l’étiquette de sensibilité**, vous pouvez créer ou modifier le nom d’une étiquette et sa description. Vous pouvez également définir si l’étiquette est active ou désactivée en passant la commande **Activé** sur ON ou sur OFF. Enfin, vous pouvez ajouter ou supprimer des types d’informations associés à l’étiquette. Toutes les données découvertes correspondant à ce type d’information incluent automatiquement l’étiquette de sensibilité associée dans les recommandations de classification.
3. Cliquez sur **OK**.
 
   ![Configurer l’étiquette de sensibilité](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Les étiquettes sont répertoriées dans un ordre de sensibilité croissante. Pour modifier le classement entre des étiquettes, faites-les glisser pour les réorganiser dans la table, ou bien utilisez les boutons **Monter** et **Descendre** pour modifier l’ordre. 
 
    ![Liste d’étiquettes](./media/security-center-info-protection-policy/move-up.png)
 
5. Veillez à cliquer sur **Enregistrer** en haut de l’écran lorsque vous avez terminé.
 
 
## <a name="adding-and-customizing-information-types"></a>Ajout et personnalisation des types d’informations
 
1. Vous pouvez gérer et personnaliser des types d’informations en cliquant sur **Gérer les types d’informations**.
2. Pour ajouter un nouveau **type d’informations**, sélectionnez **Créer un type d’informations** dans le menu supérieur. Vous pouvez définir un nom, une description et des chaînes de motif de recherche pour le **type d’informations**. Les chaînes de motif de recherche peuvent éventuellement utiliser des mots clés avec des caractères génériques (en utilisant le caractère « % »), que le moteur de découverte automatique utilise pour identifier les données sensibles dans vos bases de données, à partir des métadonnées de la colonne.
 
    ![Créer un type d'informations](./media/security-center-info-protection-policy/info-types.png)
 
3. Vous pouvez également configurer les **types d’informations** intégrés en ajoutant des chaînes de motif de recherche supplémentaires, en désactivant certaines des chaînes existantes, ou en modifiant la description. Vous ne pouvez pas supprimer les **types d’informations** intégrés ou modifier leurs noms. 
4. Les **types d’informations** sont classés par un ordre croissant du classement de découverte, ce qui signifie que les types en haut de la liste tentent de correspondre en premier lieu. Pour modifier le classement entre des types d’informations, faites-les glisser à l’emplacement correct dans la table, ou bien utilisez les boutons **Monter** et **Descendre** pour modifier l’ordre. 
5. Cliquez sur **OK** lorsque vous avez terminé.
6. Une fois que vous avez terminé la gestion de vos types d’informations, veillez à associer les types pertinents avec les étiquettes appropriées, en cliquant sur **Configurer** pour une étiquette particulière et en ajoutant ou en supprimant les types d’informations qu’il faut.
7. Veillez à cliquer sur **Enregistrer** dans le panneau principal **Étiquettes** pour appliquer toutes vos modifications.
 
Une fois que votre stratégie de protection des informations est entièrement définie et enregistrée, il s’applique à la classification des données sur toutes les bases de données Azure SQL dans votre locataire.

## <a name="manage-sql-information-protection-using-azure-powershell"></a>Gérer la protection des informations SQL à l’aide d’Azure PowerShell

- [Get-AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy) : récupère la stratégie de protection des informations SQL du locataire effective.
- [Set-AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy) : définit la stratégie de protection des informations SQL du locataire effective.
 
## <a name="next-steps"></a>Étapes suivantes
 
Dans cet article, vous avez appris à définir une stratégie de protection des informations SQL dans Azure Security Center. Pour en savoir plus sur l’utilisation de la protection des informations SQL pour classifier et protéger des données sensibles dans vos bases de données SQL, consultez [Découverte et classification des données de base de données Azure SQL](../azure-sql/database/data-discovery-and-classification-overview.md). 

Pour plus d’informations sur les stratégies de sécurité et la sécurité des données dans Azure Security Center, consultez les articles suivants :
 
- [Définition de stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure
- [Sécurité des données Azure Security Center](security-center-data-security.md) : découvrez comment Security Center gère et protège les données