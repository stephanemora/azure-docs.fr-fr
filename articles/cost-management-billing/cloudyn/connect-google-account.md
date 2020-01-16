---
title: Connecter un compte Google Cloud Platform à Cloudyn dans Azure | Microsoft Docs
description: Connectez un compte Google Cloud Platform pour afficher les données de coût et d’utilisation dans les rapports Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 8849811560c7e3d35a40b4725dbbb63c82745123
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75986449"
---
# <a name="connect-a-google-cloud-platform-account"></a>Connecter un compte Google Cloud Platform

Vous pouvez connecter votre compte Google Cloud Platform existant à Cloudyn. Une fois votre compte connecté à Cloudyn, les données de coût et d’utilisation sont disponibles dans les rapports Cloudyn. Cet article vous aide à configurer et à connecter votre compte Google à Cloudyn.


## <a name="collect-project-information"></a>Collecter des informations sur le projet

Vous commencez par collecter les informations sur votre projet.

1. Connectez-vous à la console Google Cloud Platform à l’adresse [https://console.cloud.google.com](https://console.cloud.google.com).
2. Passez en revue les informations du projet que vous souhaitez intégrer à Cloudyn, puis notez le **Nom du projet** et l’**ID du projet**. Conservez les informations à portée de main pour les étapes ultérieures.  
    ![Nom du projet et ID du projet indiqué dans la console Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. Si la facturation n’est pas activée et liée à votre projet, créez un compte de facturation. Pour plus d’informations, voir [Créer un compte de facturation](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Activer l’exportation de la facturation du compartiment de stockage

Cloudyn récupère vos données de facturation Google à partir d’un compartiment de stockage. Conservez les informations **Nom du compartiment** et **Préfixe du rapport** à portée de main en vue d’un usage ultérieur au moment de l’inscription auprès de Cloudyn.

L’utilisation de Google Cloud Storage pour stocker les rapports d’utilisation occasionne un minimum de frais. Pour plus d’informations, voir [Tarifs de Cloud Storage](https://cloud.google.com/storage/pricing).

1. Si vous n’avez pas activé l’exportation de la facturation vers un fichier, suivez les instructions de la page [Activer l’exportation de la facturation vers un fichier](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Vous pouvez utiliser le format d’exportation de facturation JSON ou CSV.
2. Autrement, dans la console Google Cloud Platform, accédez à **Facturation** > **Exportation de la facturation**. Notez le **Nom du compartiment** et le **Préfixe du rapport** de votre facturation.  
    ![Informations sur l’exportation de la facturation indiquées sur la page d’exportation de la facturation](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Activez les API Google Cloud Platform

Pour collecter les informations sur les actifs et l’utilisation, Cloudyn a besoin que les API Google Cloud Platform suivantes soient activée :

- API BigQuery
- Google Cloud SQL
- API Google Cloud Datastore
- Google Cloud Storage
- API JSON Google Cloud Storage
- API Google Compute Engine

### <a name="enable-or-verify-apis"></a>API Activer ou Vérifier

1. Dans la console Google Cloud Platform, sélectionnez le projet que vous souhaitez inscrire auprès de Cloudyn.
2. Accédez à **API et Services** > **Bibliothèque**.
3. Utilisez une recherche pour trouver chaque API.
4. Pour chaque API, vérifiez que l’option **API activée** est sélectionnée. Autrement, cliquez sur **ACTIVER**.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Ajouter un compte Google Cloud à Cloudyn

1. Ouvrez le portail Cloudyn à partir du portail Azure ou accédez à [https://azure.cloudyn.com](https://azure.cloudyn.com/) et connectez-vous.
2. Cliquez sur **Paramètres** (symbole de roue dentée), puis sélectionnez **Comptes cloud**.
3. Dans **Gestion de comptes**, sélectionnez l’onglet **Comptes Google**, puis cliquez sur **Ajouter un nouveau +** .
4. Dans **Nom du compte Google**, entrez l’adresse de messagerie du compte de facturation, puis cliquez sur **Suivant**.
5. Dans la boîte de dialogue d’authentification de Google, sélectionnez ou entrez un compte Google, puis choisissez d’**AUTORISER** cloudyn.com à accéder à votre compte.
6. Ajoutez les informations de projet que vous avez notées précédemment. Il s’agit de l’**ID du projet**, du **Nom du projet**, du **Nom du compartiment de facturation**, et du préfixe de rapport du **Fichier de facturation**. Cliquez ensuite sur **Enregistrer**.  
    ![Ajouter un projet Google à un compte Cloudyn](./media/connect-google-account/add-project.png)

Votre compte Google apparaît dans la liste des comptes, et devrait indiquer **Authentifié**. Sous celui-ci devraient apparaître le nom et l’ID de votre projet Google, ainsi qu’un symbole de coche verte. L’état du compte devrait être **Terminé**.

Après quelques heures, les rapports Cloudyn affichent les informations de coût et d’utilisation de Google.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur Cloudyn, suivez le tutoriel [Vérifier l’utilisation et les coûts](tutorial-review-usage.md) sur Cloudyn.
