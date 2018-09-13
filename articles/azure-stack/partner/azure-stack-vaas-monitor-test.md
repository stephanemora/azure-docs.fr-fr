---
title: Surveiller un test avec le service Validation en tant que service pour Azure Stack | Microsoft Docs
description: Surveiller un test avec le service Validation en tant que service pour Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 2dc4d3f2855864ff80648b5b9635ff28c0dacbb7
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163324"
---
# <a name="monitor-a-test-with-azure-stack-validation-as-a-service"></a>Surveiller un test avec le service Validation en tant que service pour Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Vous pouvez surveiller l’exécution en cours ou achevée de suites de tests en consultant la page **Opérations**. Cette page indique en détail l’état du test et de ses opérations.

## <a name="monitor-a-test"></a>Analyser un test

1. Sélectionnez une solution.

2. Sélectionnez **Gérer** sur l’une des vignettes de flux de travail.

3. Cliquez sur un flux de travail pour ouvrir la page de résumé des tests correspondante.

4. Développez le menu contextuel **[...]** de l’une des instances de suite de tests.

5. Sélectionnez **Afficher les opérations**.

![Alt text](media\image4.png)

Vous pouvez télécharger les journaux relatifs aux tests dont l’exécution est terminée à partir de la page de résumé des tests en cliquant sur **Télécharger les journaux** dans le menu contextuel **[...]** d’un test. Les partenaires Azure Stack peuvent utiliser ces journaux pour déboguer les problèmes des tests ayant échoué.

## <a name="open-the-test-pass-summary"></a>Ouvrir le résumé de passe de test

1. Ouvrez le portail. 
2. Sélectionnez le nom d’une solution existante contenant des tests précédemment exécutés ou planifiés.

    ![Gestion des passes de test](media/managetestpasses.png)

3. Sélectionnez **Gérer** dans le panneau **Test Passes** (Passes de test).
4. Sélectionnez la passe de test pour ouvrir le résumé correspondant. Vous pouvez consulter le nom du test, les dates de création et d’exécution du test, la durée du test, ainsi que le résultat (réussite ou échec).
5. Sélectionnez [ **. . .** ].

### <a name="test-pass-summary"></a>Résumé de passe de test

| Colonne | Description |
| --- | --- |
| Nom du test | Nom du test. Référence le numéro de validation. |
| Date de création | Date de création de la passe de test. |
| Démarré | Date d’exécution de la passe de test. |
| Duration | Durée de l’exécution de la passe de test. |
| Statut | Résultat (réussite ou échec) de la passe de test. |
| Nom de l’agent | Nom de domaine complet de l’agent. |
| Nombre total d’opérations | Nombre total de tentatives d’opérations dans la passe de test. |
| Passed operations (Opérations réussies) | Nombre d’opérations réussies dans la passe de test. |
|  Failed Operations (Opérations ayant échoué) | Nombre d’opérations ayant échoué. |

### <a name="group-columns-in-the-test-pass-summary"></a>Grouper des colonnes du résumé de passe de test

Vous pouvez sélectionner et faire glisser une colonne dans l’en-tête pour créer un groupe sur la valeur de colonne.

## <a name="reschedule-a-test"></a>Replanifier un test

1. [Ouvrez le résumé de passe de test](#open-the-test-pass-summary).
2. Sélectionnez **Reschedule** (Replanifier) pour replanifier la passe de test.
3. Entrez le mot de passe d’administrateur du cloud pour votre instance Azure Stack.
4. Entrez la chaîne de connexion du stockage des diagnostics que vous avez définie lorsque vous avez configuré votre compte.
5. Replanifiez le test.

## <a name="cancel-a-test"></a>Annuler un test

1. [Ouvrez le résumé de passe de test](#open-the-test-pass-summary).
2. Sélectionnez **Annuler**.

## <a name="get-test-information"></a>Obtenir les informations d’un test

1. [Ouvrez le résumé de passe de test](#open-the-test-pass-summary).
2. Sélectionnez **View information** (Afficher les informations).

**Informations de test**

| NOM | Description |
| -- | -- |
| Nom du test | Nom du test, par exemple Validation de mise à jour du fabricant OEM sur Azure Stack 1806 RC. |
| Test version (Version du test) | Version du test, par exemple 5.1.4.0. |
| Publisher | Éditeur du test, tel que Microsoft. |
| Catégorie | Catégorie du test, par exemple **Fonctionnel** ou **Fiabilité**. |
| Target services (Services cibles) | Services testés, tels que Machines virtuelles. |
| Description | Description du test. |
| Estimated duration (minutes) (Durée estimée [en minutes]) | Durée en minutes de l’exécution du test. |
| Liens | Lien d’accès à la fonctionnalité GitHub de suivi des problèmes. |

## <a name="get-test-parameters"></a>Obtenir les paramètres de test

1. [Ouvrez le résumé de passe de test](#open-the-test-pass-summary).
2. Sélectionnez **View parameters** (Afficher les paramètres).

**Paramètres**

| NOM | Description |
| -- | -- |
| Nom du test | Nom du test, par exemple oemupdate1806test. |
| Test version (Version du test) | Version du test, par exemple 5.1.4.0. |
| Test instance ID (ID d’instance de test) | Identificateur global unique de l’instance spécifique du test, par exemple 20b20645-b400-4f0d-bf6f-1264d866ada9. |
| cloudAdminUser | Nom du compte utilisé en tant qu’administrateur du cloud, par exemple **cloudadmin**. |
| DiagnosticsContainerName | ID du conteneur de diagnostics, par exemple 04dd3815-5f35-4158-92ea-698027693080. |

## <a name="get-test-operations"></a>Obtenir les opérations de test

1. [Ouvrez le résumé de passe de test](#open-the-test-pass-summary).
2. Sélectionnez **Afficher les opérations**. Le volet de résumé des opérations s’affiche.

## <a name="get-test-logs"></a>Obtenir les journaux de test

1. [Ouvrez le résumé de passe de test](#open-the-test-pass-summary).
2. Sélectionnez **Télécharger les journaux**.  
    Un fichier zip nommé ReleaseAAAA-MM-JJ.zip contenant les journaux se télécharge.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur la [Validation en tant que service Azure Stack](https://docs.microsoft.com/azure/azure-stack/partner).
