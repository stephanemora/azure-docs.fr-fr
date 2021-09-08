---
title: Utilisation des autorisations dans Azure Spring Cloud
description: Cet article explique comment créer des rôles personnalisés qui délèguent des autorisations aux ressources Azure Spring Cloud.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 61c4eb0322de60bb90d49bd71b111fe8b2db07ea
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563435"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Comment utiliser des autorisations dans Azure Spring Cloud
Cet article explique comment créer des rôles personnalisés qui délèguent des autorisations aux ressources Azure Spring Cloud. Les rôles personnalisés étendent les [rôles intégrés Azure](../role-based-access-control/built-in-roles.md) avec diverses autorisations.

Nous allons implémenter les rôles personnalisés suivants.

* **Rôle Développeur** :

    * Déployer
    * Test
    * Redémarrer les applications
    * Appliquer et modifier les configurations d’application dans le référentiel Git
    * Récupérer les flux de journaux

* **Rôle Ingénieur DevOps** :

    * Créer, lire, mettre à jour et tout supprimer dans Azure Spring Cloud
* **Ops – Rôle Ingénierie de fiabilité des sites** :

    * Redémarrer les applications
    * Récupérer les flux de journaux
    * Impossible de modifier les applications ou les configurations

* **Rôles Azure Pipelines/Jenkins/GitHub Actions** :

    * Effectuer des opérations de création, de lecture, de mise à jour et de suppression
    * Utiliser les modèles Terraform ou ARM pour créer et configurer tout ce qui se trouve dans les applications et Azure Spring Cloud au sein de l’instance de service : Azure Pipelines, Jenkins ou GitHub Actions.

## <a name="define-the-developer-role"></a>Définir le rôle Développeur

Le rôle Développeur comprend les autorisations pour redémarrer les applications et afficher leurs flux de journaux. Ce rôle ne peut apporter de modifications ni aux applications ni aux configurations.

#### <a name="portal"></a>[Portail](#tab/Azure-portal)
1. Dans le portail Azure, ouvrez l’abonnement dans lequel vous souhaitez attribuer le rôle personnalisé.
2. Ouvrez **Contrôle d’accès (IAM)** .
3. Sélectionnez **Ajouter**.
4. Sélectionnez **Ajouter un rôle personnalisé**.
5. Sélectionnez **Suivant** :

   ![Capture d’écran représentant l’onglet de Base de la fenêtre Créer un rôle personnalisé.](media/spring-cloud-permissions/create-custom-role.png)

6. Sélectionnez **Ajouter des autorisations** :

   ![Capture d’écran montrant le bouton Ajouter des autorisations.](media/spring-cloud-permissions/add-permissions.png)


7. Dans la zone de recherche, recherchez **Microsoft.app**. Sélectionnez **Microsoft Azure Spring Cloud** :

   ![Capture d’écran qui montre les résultats de la recherche de Microsoft.app.](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. Sélectionnez les autorisations pour le rôle Développeur.

   Dans **Microsoft.AppPlatform/Spring**, sélectionnez :
   * **Écriture : Créer ou mettre à jour une instance de service Azure Spring Cloud**
   * **Lecture : Récupérer une instance de service Azure Spring Cloud**
   * **Autre : Répertorier les clés de test de l’instance de service Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/Spring/apps**, sélectionnez :
   * **Lecture : Lire l’application Microsoft Azure Spring Cloud**
   * **Autre : Récupérer l’URL de chargement des ressources de l’application Microsoft Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/Spring/apps/bindings**, sélectionnez :
   * **Lecture : Lire la liaison de l’application Microsoft Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/Spring/apps/deployments**, sélectionnez :
   * **Écriture : Écrire le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Lecture : Lire le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Autre : Démarrer le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Autre : Arrêter le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Autre : Redémarrer le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Autre : Récupérer l’URL du fichier journal du déploiement de l’application Microsoft Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/Spring/apps/domains**, sélectionnez :
   * **Lecture : Lire le domaine personnalisé de l’application Microsoft Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/Spring/certificates**, sélectionnez :
   * **Lecture : Lire le certificat Microsoft Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/locations/operationResults/Spring**, sélectionnez :
   * **Lecture : Lire le résultat de l’opération**

   Dans **Microsoft.AppPlatform/locations/operationStatus/operationId**, sélectionnez :
   * **Lecture : Lire l’état de l’opération**

    [ ![Capture d’écran montrant les sélections pour les autorisations Developler.](media/spring-cloud-permissions/developer-permissions-box.png) ](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. Sélectionnez **Ajouter**.

10. Passez en revue les autorisations.

11. Sélectionnez **Examiner et créer**.

#### <a name="json"></a>[JSON](#tab/JSON)
1. Dans le portail Azure, ouvrez l’abonnement dans lequel vous souhaitez attribuer le rôle personnalisé.
2. Ouvrez **Contrôle d’accès (IAM)** .
3. Sélectionnez **Ajouter**.
4. Sélectionnez **Ajouter un rôle personnalisé**.
5. Sélectionnez **Suivant**.

6. Sélectionnez l’onglet **JSON**.

7. Sélectionnez **Modifier**, puis supprimer le texte par défaut :

   ![Capture d’écran montrant le texte JSON par défaut.](media/spring-cloud-permissions/create-custom-role-edit-json.png)

8. Collez le code JSON suivant pour définir le rôle Développeur :

   ```json
   {
     "properties": {
       "roleName": "Developer",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/domains/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/Spring/certificates/read",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

   ![Capture d’écran montrant le code JSON pour le rôle Développeur.](media/spring-cloud-permissions/create-custom-role-json.png)

9. Sélectionnez **Enregistrer**.

10. Passez en revue les autorisations.

11. Sélectionnez **Examiner et créer**.

---

## <a name="define-the-devops-engineer-role"></a>Définir le rôle Ingénieur DevOps

Cette procédure définit un rôle disposant des autorisations pour déployer, tester et redémarrer les applications Azure Spring Cloud.

#### <a name="portal"></a>[Portail](#tab/Azure-portal)
1. Répétez les étapes 1 à 4 de la procédure pour ajouter le rôle Développeur.

2. Sélectionnez les autorisations pour le rôle Ingénieur DevOps :

   Dans **Microsoft.AppPlatform/Spring**, sélectionnez :
   * **Écriture : Créer ou mettre à jour une instance de service Azure Spring Cloud**
   * **Supprimer : Supprimer une instance de service Azure Spring Cloud**
   * **Lecture : Récupérer une instance de service Azure Spring Cloud**
   * **Autre : Activer un point de terminaison de test de l’instance de service Azure Spring Cloud**
   * **Autre : Désactiver un point de terminaison de test de l’instance de service Azure Spring Cloud**
   * **Autre : Répertorier les clés de test de l’instance de service Azure Spring Cloud**
   * **Autre : Régénérer une clé de test de l’instance de service Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/Spring/apps**, sélectionnez :
   * **Écriture : Écrire l’application Microsoft Azure Spring Cloud**
   * **Supprimer : Supprimer l’application Microsoft Azure Spring Cloud**
   * **Lecture : Lire l’application Microsoft Azure Spring Cloud**
   * **Autre : Récupérer l’URL de chargement des ressources de l’application Microsoft Azure Spring Cloud**
   * **Autre : Valider le domaine personnalisé de l’application Microsoft Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/Spring/apps/bindings**, sélectionnez :
   * **Écriture : Écrire la liaison de l’application Microsoft Azure Spring Cloud**
   * **Supprimer : Supprimer la liaison de l’application Microsoft Azure Spring Cloud**
   * **Lecture : Lire la liaison de l’application Microsoft Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/Spring/apps/deployments**, sélectionnez :
   * **Écriture : Écrire le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Supprimer : Supprimer le déploiement de l’application Azure Spring Cloud**
   * **Lecture : Lire le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Autre : Démarrer le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Autre : Arrêter le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Autre : Redémarrer le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Autre : Récupérer l’URL du fichier journal du déploiement de l’application Microsoft Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/Spring/apps/deployments/skus**, sélectionnez :
   * **Lecture : Répertorier les SKU disponibles pour le déploiement d’application**

   Dans **Microsoft.AppPlatform/locations**, sélectionnez :
   * **Autre : Vérifier la disponibilité du nom**

   Dans **Microsoft.AppPlatform/locations/operationResults/Spring**, sélectionnez :
   * **Lecture : Lire le résultat de l’opération**

   Dans **Microsoft.AppPlatform/locations/operationStatus/operationId**, sélectionnez :
   * **Lecture : Lire l’état de l’opération**

   Dans **Microsoft.AppPlatform/skus**, sélectionnez :
   * **Lecture : Répertorier les SKU disponibles**

   [ ![Capture d’écran montrant les sélections pour les autorisations DevOps .](media/spring-cloud-permissions/dev-ops-permissions.png) ](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. Sélectionnez **Ajouter**.

4. Passez en revue les autorisations.

5. Sélectionnez **Examiner et créer**.

#### <a name="json"></a>[JSON](#tab/JSON)

1. Répétez les étapes 1 à 4 de la procédure pour ajouter le rôle Développeur.
2. Sélectionnez **Suivant**.

3. Sélectionnez l’onglet **JSON**.

4. Sélectionnez **Modifier**, puis supprimer le texte par défaut :

   ![Capture d’écran montrant le texte JSON par défaut.](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. Collez le code JSON suivant pour définir le rôle Ingénieur DevOps :

   ```json
   {
     "properties": {
       "roleName": "DevOps engineer",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/delete",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/enableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/disableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/regenerateTestKey/action",
             "Microsoft.AppPlatform/Spring/apps/write",
             "Microsoft.AppPlatform/Spring/apps/delete",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/validateDomain/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/write",
             "Microsoft.AppPlatform/Spring/apps/bindings/delete",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/delete",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/skus/read",
             "Microsoft.AppPlatform/locations/checkNameAvailability/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read",
             "Microsoft.AppPlatform/skus/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. Passez en revue les autorisations.

7. Sélectionnez **Examiner et créer**.

---

## <a name="define-the-ops---site-reliability-engineering-role"></a>Définir le rôle Ops – Rôle Ingénierie de fiabilité des sites

Cette procédure définit un rôle disposant des autorisations pour déployer, tester et redémarrer les applications Azure Spring Cloud.

#### <a name="portal"></a>[Portail](#tab/Azure-portal)
1. Répétez les étapes 1 à 4 de la procédure pour ajouter le rôle Développeur.
2. Sélectionnez les autorisations pour le rôle Ops – Ingénierie de fiabilité des sites :

   Dans **Microsoft.AppPlatform/Spring**, sélectionnez :
   * **Lecture : Récupérer une instance de service Azure Spring Cloud**
   * **Autre : Répertorier les clés de test de l’instance de service Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/Spring/apps**, sélectionnez :
   * **Lecture : Lire l’application Microsoft Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/apps/deployments**, sélectionnez :
   * **Lecture : Lire le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Autre : Démarrer le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Autre : Arrêter le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Autre : Redémarrer le déploiement de l’application Microsoft Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/locations/operationResults/Spring**, sélectionnez :
   * **Lecture : Lire le résultat de l’opération**

   Dans **Microsoft.AppPlatform/locations/operationStatus/operationId**, sélectionnez :
   * **Lecture : Lire l’état de l’opération**

   [ ![Capture d’écran montrant les sélections pour les autorisations d’ingénierie de fiabilité de site OPS.](media/spring-cloud-permissions/ops-sre-permissions.png)](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. Sélectionnez **Ajouter**.

4. Passez en revue les autorisations.

5. Sélectionnez **Examiner et créer**.

#### <a name="json"></a>[JSON](#tab/JSON)
1. Répétez les étapes 1 à 4 de la procédure pour ajouter le rôle Développeur.
2. Sélectionnez **Suivant**.

3. Sélectionnez l’onglet **JSON**.

4. Sélectionnez **Modifier**, puis supprimer le texte par défaut :

   ![Capture d’écran montrant le texte JSON par défaut.](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. Collez le code JSON suivant pour définir le rôle d’ingénierie de fiabilité de site OPS :

   ```json
   {
     "properties": {
       "roleName": "Ops - Site Reliability Engineering",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. Passez en revue les autorisations.

7. Sélectionnez **Examiner et créer**.

---

## <a name="define-the-azure-pipelines--jenkins--github-actions-role"></a>Définir le rôle Azure Pipelines/Jenkins/GitHub Actions

Ce rôle peut créer et configurer tout ce qui se trouve dans les applications et Azure Spring Cloud avec une instance de service. Ce rôle est destiné à libérer ou à déployer du code.

#### <a name="portal"></a>[Portail](#tab/Azure-portal)

1. Répétez les étapes 1 à 4 de la procédure pour ajouter le rôle Développeur.
2. Ouvrez les options **Autorisations**.

3. Sélectionnez les autorisations pour le rôle Azure Pipelines/Jenkins/GitHub Actions :

   Dans **Microsoft.AppPlatform/Spring**, sélectionnez :
   * **Écriture : Créer ou mettre à jour une instance de service Azure Spring Cloud**
   * **Supprimer : Supprimer une instance de service Azure Spring Cloud**
   * **Lecture : Récupérer une instance de service Azure Spring Cloud**
   * **Autre : Activer un point de terminaison de test de l’instance de service Azure Spring Cloud**
   * **Autre : Désactiver un point de terminaison de test de l’instance de service Azure Spring Cloud**
   * **Autre : Répertorier les clés de test de l’instance de service Azure Spring Cloud**
   * **Autre : Régénérer une clé de test de l’instance de service Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/Spring/apps**, sélectionnez :
   * **Écriture : Écrire l’application Microsoft Azure Spring Cloud**
   * **Supprimer : Supprimer l’application Microsoft Azure Spring Cloud**
   * **Lecture : Lire l’application Microsoft Azure Spring Cloud**
   * **Autre : Récupérer l’URL de chargement des ressources de l’application Microsoft Azure Spring Cloud**
   * **Autre : Valider le domaine personnalisé de l’application Microsoft Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/Spring/apps/bindings**, sélectionnez :
   * **Écriture : Écrire la liaison de l’application Microsoft Azure Spring Cloud**
   * **Supprimer : Supprimer la liaison de l’application Microsoft Azure Spring Cloud**
   * **Lecture : Lire la liaison de l’application Microsoft Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/Spring/apps/deployments**, sélectionnez :
   * **Écriture : Écrire le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Supprimer : Supprimer le déploiement de l’application Azure Spring Cloud**
   * **Lecture : Lire le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Autre : Démarrer le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Autre : Arrêter le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Autre : Redémarrer le déploiement de l’application Microsoft Azure Spring Cloud**
   * **Autre : Récupérer l’URL du fichier journal du déploiement de l’application Microsoft Azure Spring Cloud**

   Dans **Microsoft.AppPlatform/Spring/apps/deployments/skus**, sélectionnez :
   * **Lecture : Répertorier les SKU disponibles pour le déploiement d’application**

   Dans **Microsoft.AppPlatform/locations**, sélectionnez :
   * **Autre : Vérifier la disponibilité du nom**

   Dans **Microsoft.AppPlatform/locations/operationResults/Spring**, sélectionnez :
   * **Lecture : Lire le résultat de l’opération**

   Dans **Microsoft.AppPlatform/locations/operationStatus/operationId**, sélectionnez :
   * **Lecture : Lire l’état de l’opération**

   Dans **Microsoft.AppPlatform/skus**, sélectionnez :
   * **Lecture : Répertorier les SKU disponibles**

   [ ![Capture d’écran montrant les sélections pour les autorisations Azure Pipelines/Jenkins/GitHub Actions.](media/spring-cloud-permissions/pipelines-permissions-box.png) ](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)

4. Sélectionnez **Ajouter**.

5. Passez en revue les autorisations.

6. Sélectionnez **Examiner et créer**.

#### <a name="json"></a>[JSON](#tab/JSON)

1. Répétez les étapes 1 à 4 de la procédure pour ajouter le rôle Développeur.
2. Sélectionnez **Suivant**.

3. Sélectionnez l’onglet **JSON**.

4. Sélectionnez **Modifier**, puis supprimer le texte par défaut :

   ![Capture d’écran montrant le texte JSON par défaut.](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. Collez le code JSON suivant pour définir le rôle Azure Pipelines / Jenkins / GitHub Actions :

   ```json
   {
     "properties": {
       "roleName": "Azure Pipelines/Provisioning",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/delete",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/enableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/disableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/regenerateTestKey/action",
             "Microsoft.AppPlatform/Spring/apps/write",
             "Microsoft.AppPlatform/Spring/apps/delete",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/validateDomain/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/write",
             "Microsoft.AppPlatform/Spring/apps/bindings/delete",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/delete",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/skus/read",
             "Microsoft.AppPlatform/locations/checkNameAvailability/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. Sélectionnez **Ajouter**.

7. Passez en revue les autorisations.

---

## <a name="see-also"></a>Voir aussi

* [Créer ou mettre à jour des rôles Azure personnalisés en utilisant le portail Azure](../role-based-access-control/custom-roles-portal.md)

Pour plus d’informations sur les trois méthodes qui permettent de définir une autorisation personnalisée, consultez :
* [Cloner un rôle](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [Commencer à partir de zéro](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [Démarrer à partir d’un JSON](../role-based-access-control/custom-roles-portal.md#start-from-json)
