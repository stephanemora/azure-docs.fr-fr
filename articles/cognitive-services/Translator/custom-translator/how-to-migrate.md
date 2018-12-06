---
title: Migrer un espace de travail et des projets Microsoft Translator Hub - Custom Translator
titleSuffix: Azure Cognitive Services
description: Migrez votre espace de travail et vos projets Hub vers Custom Translator.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 611cd0878e88d2e1c0a988f73b57e391c5a8551d
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975905"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrer un espace de travail et des projets Microsoft Hub vers Custom Translator

Vous pouvez migrer votre espace de travail et vos projets [Microsoft Translator Hub](https://hub.microsofttranslator.com/) vers Custom Translator. La migration démarre à partir du hub.


Les éléments suivants sont migrés au cours du processus :

1.  Les définitions de projet(s).

2.  La définition de la formation sera utilisée pour créer une nouvelle définition de modèle sur Custom Translator.

3.  Les fichiers parallèles et unilingues utilisés dans les formations seront tous migrés en tant que nouveaux documents dans Custom Translator.

4.  Le test système automatiquement généré et les données de paramétrage seront exportés et créés en tant que nouveaux documents dans Custom Translator.

Pour toutes les formations déployées, Custom Translator formera le modèle sans frais. Vous avez la possibilité de les déployer manuellement.

>[!Note]
>Pour une formation réussie, Custom Translator nécessite au moins 10 000 phrases extraites. Si le nombre de phrases extraites est inférieur au [minimum suggéré](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences), Custom Translator ne peut pas effectuer l'apprentissage.

Toutes les formations réussies et qui ne sont pas déployées seront migrées en tant que brouillon vers Custom Translator.

## <a name="find-custom-translator-workspace-id"></a>Rechercher l’ID de l’espace de travail Custom Translator

Pour migrer un espace de travail [Microsoft Translator Hub](https://hub.microsofttranslator.com/), vous avez besoin de l’ID de l’espace de travail de destination dans Custom Translator. L’espace de travail de destination dans Custom Translator représente l’endroit vers lequel tous vos espaces de travail et projets du hub doivent être migrés.

Vous trouverez votre ID d’espace de travail de destination dans la page des paramètres de Custom Translator : 

1.  Accédez à la page « Settings » (Paramètres) du portail Custom Translator.

2.  Vous trouverez l’ID de l’espace de travail dans la section sur les informations de base.

    ![Comment trouver l’ID de l’espace de travail de destination](media/how-to/how-to-find-destination-ws-id.png)

3. Conservez votre ID d’espace de travail de destination pour y faire référence au cours du processus de migration.

## <a name="migrate-workspace"></a>Migrer un espace de travail

Lorsque vous migrez l’intégralité de votre espace de travail Hub vers Custom Translator, vos projets, documents et formations sont également migrés vers Custom Translator. Avant la migration, vous devez choisir si vous souhaitez migrer uniquement des formations déployées ou toutes vos formations réussies.

Pour migrer un espace de travail :

1.  Connectez-vous à Microsoft Translator Hub.

2.  Accédez à la page des paramètres (Settings).

3.  Dans la page « Settings » (Paramètres), cliquez sur « Migrate Workspace data to Custom Translator » (Migrer les données de l’espace de travail vers Custom Translator).

    ![Migrer à partir du hub](media/how-to/how-to-migrate-workspace-from-hub.png)

4.  Dans la page suivante, sélectionnez une de ces deux options :

    a.  Deployed Trainings only (Formations déployées uniquement) : cette option migre uniquement vos systèmes déployés et les documents associés.

    b.  All Successful Trainings (Toutes les formations réussies) : cette option migrera toutes vos formations réussies et les documents associés.

    c.  Entrez votre ID d’espace de travail de destination dans Custom Translator.

    ![Migrer à partir du hub](media/how-to/how-to-migrate-from-hub-screen.png)

5.  Cliquez sur Envoyer une demande.

## <a name="migrate-project"></a>Migrer un projet

Si vous souhaitez migrer vos projets de manière sélective, Microsoft Translator Hub vous offre cette possibilité.

Pour migrer un projet :

1.  Connectez-vous à Microsoft Translator Hub.

2.  Accédez à la page des projets (Projects).

3.  Cliquez sur le lien « Migrate » (Migrer) du projet correspondant.

    ![Migrer à partir du hub](media/how-to/how-to-migrate-from-hub.png)

4.  Dans la page suivante, sélectionnez une de ces deux options :

    a.  Deployed Trainings only (Formations déployées uniquement) : cette option migre uniquement vos systèmes déployés et les documents associés. 

    b.  All Successful Trainings (Toutes les formations réussies) : cette option migrera toutes vos formations réussies et les documents associés.

    c.  Entrez votre ID d’espace de travail de destination dans Custom Translator.

    ![Migrer à partir du hub](media/how-to/how-to-migrate-from-hub-screen.png)

5.  Cliquez sur « Envoyer une demande ».

## <a name="migration-history"></a>Historique des migrations

Lorsque vous avez demandé la migration d’un espace de travail/projet à partir du Hub, vous trouverez l’historique des migrations dans la page des paramètres de Custom Translator. 

Pour afficher l’historique des migrations, procédez comme suit :

1.  Accédez à la page « Settings » (Paramètres) du portail Custom Translator.

2.  Dans la section de l’historique des migrations de la page des paramètres, cliquez sur Migration History (Historique des migrations).

    ![Historique des migrations](media/how-to/how-to-migration-history.png)

La page de l’historique des migrations s’affiche après les informations suivantes pour récapituler chaque migration que vous avez demandée.

1.  Migrated By (Migré par) : nom et adresse e-mail de l’utilisateur qui a soumis cette demande de migration

2.  Migrated On (Migré le) : date et heure de la migration

3.  Projects (Projets) : nombre de projets demandés pour la migration par rapport au nombre de projets effectivement migrés.

4.  Trainings (Formations) : nombre de formations demandées pour la migration par rapport au nombre de formations effectivement migrées.

5.  Documents : nombre de documents demandés pour la migration par rapport au nombre de documents effectivement migrés.

    ![Détails de l’historique des migrations](media/how-to/how-to-migration-history-details.png)

Si vous souhaitez obtenir un rapport plus détaillé sur la migration de vos projets, formations et documents, vous pouvez exporter ces informations au format CSV.

>[!Note]
>La migration est uniquement prise en charge pour les paires de langues pour lesquelles des langues NMT existent. Consultez la liste des [langues NMT actuellement prises en charge](https://www.microsoft.com/translator/business/languages/). Pour les paires de langues qui ne disposent pas de langues NMT, les données seront déplacées du Hub vers Custom Translator, mais les formations ne peuvent pas être effectuées pour ces paires de langues.

## <a name="custom-translator-versus-hub"></a>Custom Translator ou hub

Ce tableau compare les fonctionnalités de Microsoft Translator Hub et celles de Custom Translator.

|   | Hub | Custom Translator |
|:-----|:----:|:----:|
|État de la fonctionnalité de personnalisation   | Disponibilité générale  | VERSION PRÉLIMINAIRE |
| Version de l'API de texte  | V2    | V3  |
| Personnalisation TA statistique | Oui   | Non  |
| Personnalisation TA neuronale | Non     | Oui |
| Nouvelle personnalisation unifiée des services vocaux | Non     | Oui |
| Sans trace | Oui | OUI |

## <a name="next-steps"></a>Étapes suivantes

- [Effectuer l’apprentissage d’un modèle](how-to-train-model.md).
- Commencez à utiliser votre modèle Custom Translator déployé via [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
