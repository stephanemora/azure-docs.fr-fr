---
title: Chiffrement du service Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Chiffrement des données au repos du service Metrics Advisor.
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: mbullwin
ms.openlocfilehash: 03f6a30bbe857b59c3fa8a6fa668bdac7cb681b1
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341355"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>Chiffrement des données au repos du service Metrics Advisor

Le service Metrics Advisor chiffre automatiquement vos données quand celles-ci sont conservées dans le cloud. Le chiffrement du service Metrics Advisor protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité.

[!INCLUDE [cognitive-services-about-encryption](../../cognitive-services/includes/cognitive-services-about-encryption.md)]

Metrics Advisor prend en charge CMK et le chiffrement double à l’aide de BYOS (apportez votre propre stockage). 

## <a name="steps-to-create-a-metrics-advisor-with-byos"></a>Étapes de création d’un service Metrics Advisor avec BYOS

### <a name="step1-create-an-azure-database-for-postgresql-and-set-admin"></a>Étape 1. Créer une base de données Azure pour PostgreSQL et définir l’administrateur

- Créer une base de données Azure pour PostgreSQL

    Connectez-vous au portail Azure et créez une ressource de la base de données Azure pour PostgreSQL. Voici quelques points à noter :

    1. Sélectionnez l’option de déploiement **Serveur unique**. 
    2. Lorsque vous choisissez Source de données, spécifiez **Aucune**.
    3. Pour Emplacement, assurez-vous de procéder à la création au **même emplacement** que la ressource Metrics Advisor.
    4. Le paramètre Version doit être défini sur **11**. 
    5. « Calcul + Stockage » doit choisir une référence (SKU) « Mémoire optimisée » avec au moins **32 vCores**.
    
    ![Créer une base de données Azure pour PostgreSQL](media/cmk-create.png)

- Définir l’administrateur Active Directory pour la ressource PG nouvellement créée

    Après avoir créé votre base de données Azure pour PostgreSQL. Accédez à la page des ressources de la ressource Azure PG nouvellement créée. Sélectionnez l’onglet Administrateur Active Directory et définissez-vous en tant qu’administrateur.


### <a name="step2-create-a-metrics-advisor-resource-and-enable-managed-identity"></a>Étape 2. Créer une ressource Metrics Advisor et activer l’identité gérée

- Créer une ressource Metrics Advisor sur le Portail Azure

    Accédez de nouveau au portail Azure et recherchez « Metrics Advisor ». Lorsque vous créez une ressource Metrics Advisor, n’oubliez pas ce qui suit :

    1. Choisissez la **région** que vous avez créée pour la base de données Azure pour PostgreSQL. 
    2. Définissez la case « Apporter votre propre stockage » sur **Oui**, puis sélectionnez la base de données Azure pour PostgreSQL que vous venez de créer dans la liste déroulante.

- Activer l’identité gérée pour Metrics Advisor

    Après avoir créé la ressource Metrics Advisor, sélectionnez Identité et définissez Statut sur **Activé** pour activer l’identité gérée.

- Obtenir l’ID d’application de l’identité gérée

    Accédez à Azure Active Directory, puis sélectionnez Applications d’entreprise. Redéfinissez le Type d’application sur **Identité gérée**, copiez le nom de ressource de Metrics Advisor et procédez à la recherche. Ensuite, vous pouvez afficher l'ID d’application à partir du résultat de la requête et le copier.

### <a name="step3-grant-metrics-advisor-access-permission-to-your-azure-database-for-postgresql"></a>Étape 3. Accorder à Metrics Advisor une autorisation d’accès à votre base de données Azure pour PostgreSQL

- Accorder le rôle **Propriétaire** à l’identité gérée dans votre base de données Azure pour PostgreSQL

- Définir les règles de pare-feu

    1. Définissez l’option Autoriser l’accès aux services Azure sur Oui. 
    2. Ajoutez votre adresse clientIP pour vous connecter à la base de données Azure pour PostgreSQL.

- Procurez-vous le jeton d’accès de votre compte avec le type de ressource https://ossrdbms-aad.database.windows.net. Le jeton d’accès est le mot de passe dont vous avez besoin pour vous connecter à la base de données Azure pour PostgreSQL via votre compte. Voici un exemple utilisant le client `az`.

   ```
   az login
   az account get-access-token --resource https://ossrdbms-aad.database.windows.net
   ```

- Après avoir obtenu le jeton, utilisez-le pour vous connecter à votre base de données Azure pour PostgreSQL. Remplacez le nom de serveur par celui figurant dans la vue d’ensemble de votre base de données Azure pour PostgreSQL.

   ```
   export PGPASSWORD=<access-token>
   psql -h <servername> -U <adminaccount@servername> -d postgres
   ```

- Après l’ouverture de session, exécutez les commandes suivantes pour accorder à Metrics Advisor l’autorisation d’accès à la base de données Azure pour PostgreSQL. Remplacez l’ID d’application par l’ID que vous avez obtenu à l’étape 2.

   ```
   SET aad_validate_oids_in_tenant = off;
   CREATE ROLE metricsadvisor WITH LOGIN PASSWORD '<appid>' IN ROLE azure_ad_user;
   ALTER ROLE metricsadvisor CREATEDB;
   GRANT azure_pg_admin TO metricsadvisor;
   ```

En effectuant toutes les étapes ci-dessus, vous avez créé une ressource Metrics Advisor avec CMK pris en charge. Attendez quelques minutes que votre ressource Metrics Advisor soit accessible.

## <a name="next-steps"></a>Étapes suivantes

* [Formulaire de demande de clé gérée par le client du service Metrics Advisor](https://aka.ms/cogsvc-cmk)
* [En savoir plus sur Azure Key Vault](../../key-vault/general/overview.md)