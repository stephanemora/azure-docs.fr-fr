---
title: Déployer un service web
titleSuffix: ML Studio (classic) - Azure
description: Comment convertir une expérience d’entraînement en expérience prédictive, la préparer au déploiement, puis la déployer en tant que service web Azure Machine Learning Studio (classique).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 482dc0ab549ec6b9f05bc9f74a3d31ac13646ce5
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169208"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Déployer un service web Azure Machine Learning Studio (classique)

Azure Machine Learning Studio (classique) vous permet de générer et de tester une solution d’analytique prédictive. Vous pouvez ensuite déployer la solution comme un service web.

Les services web Machine Learning Studio (classique) fournissent une interface entre une application et un modèle de scoring du workflow Machine Learning Studio (classique). Une application externe peut communiquer en temps réel avec un modèle de scoring du workflow Machine Learning Studio (classique). Un appel à un service web Machine Learning Studio (classique) retourne les résultats d’une prédiction à une application externe. Pour générer cet appel, vous transmettez une clé API créée au moment du déploiement du service web. Un service web Machine Learning Studio (classique) s’appuie sur l’architecture REST, souvent choisie pour les projets de programmation web.

Azure Machine Learning Studio (classique) propose deux types de services web :

* Service de requête-réponse (RRS, Request-Response Service) : Un service hautement scalable à faible latence qui effectue la notation d’un seul enregistrement de données.
* Service d'exécution de lots (BES, Batch Execution Service) : Un service asynchrone qui effectue la notation d’un lot d’enregistrements de données.

La saisie pour BES ressemble à la saisie de données utilisée par RRS. La principale différence réside dans le fait que BES lit un bloc d’enregistrements à partir de différentes sources, par exemple le stockage Blob Azure, le stockage Table Azure, Azure SQL Database, HDInsight (requêtes Hive) et les sources HTTP.

D'un point de vue très général, vous déployez votre modèle en trois étapes :

* **[Créez une expérience d'apprentissage]**  : dans Studio (classique), vous pouvez entraîner et tester un modèle d’analytique prédictive à l’aide de données d’entraînement que vous fournissez, en utilisant un grand ensemble d’algorithmes Machine Learning intégrés.
* **[Convertissez-la en une expérience prédictive]** : lorsque votre modèle a été formé avec des données existantes et que vous êtes prêt à l’utiliser pour la notation de nouvelles données, vous préparez et simplifiez votre expérience prédictive.
* **Déployez** en tant que **[Nouveau service web]** ou **[service web classique]** : lorsque vous déployez votre expérience prédictive en tant que service web Azure, les utilisateurs peuvent envoyer des données à votre modèle et recevoir les prédictions de ce dernier.

## <a name="create-a-training-experiment"></a>Créez une expérience d'apprentissage

Pour entraîner un modèle d’analytique prédictive, vous utilisez Azure Machine Learning Studio (classique) afin de créer une expérience d’entraînement dans laquelle vous incluez plusieurs modules destinés à charger des données d’entraînement, préparer les données nécessaires, appliquer des algorithmes de machine learning et évaluer les résultats. Vous pouvez effectuer une itération sur une expérience et essayer des algorithmes d'apprentissage automatique différents pour comparer et évaluer les résultats.

Le processus de création et gestion d’expériences d’apprentissage est traité de manière plus approfondie dans d’autres sections. Pour plus d’informations, voir les articles suivants :

* [Créer une expérience simple dans Azure Machine Learning Studio (classique)](create-experiment.md)
* [Développer une solution prédictive avec Azure Machine Learning Studio (classique)](tutorial-part1-credit-risk.md)
* [Importer vos données d’entraînement dans Azure Machine Learning Studio (classique)](import-data.md)
* [Gérer des itérations d’expériences dans Azure Machine Learning Studio (classique)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertir l'expérience de formation en expérience prédictive

Après avoir formé votre modèle, vous êtes prêt à convertir votre expérience d’apprentissage en expérience prédictive pour noter des nouvelles données.

En la convertissant en expérience prédictive, vous préparez votre modèle formé à être déployé en tant que service web de notation. Les utilisateurs du service web peuvent envoyer des données d’entrée à votre modèle, qui leur renvoie les résultats de sa prédiction. Lorsque vous convertissez l’expérience en expérience prédictive, tenez compte du mode d’utilisation de votre modèle par les autres utilisateurs.

Pour convertir votre expérience de formation en expérience prédictive, cliquez sur **Exécuter** en bas du canevas de l’expérience, cliquez sur **Configurer le service web**, puis sélectionnez **Service web prédictif**.

![Convertir une expérience de notation](./media/publish-a-machine-learning-web-service/figure-1.png)

Pour plus d’informations sur la façon d’effectuer cette conversion, consultez [Guide pratique pour préparer un modèle pour le déploiement dans Azure Machine Learning Studio (classique)](convert-training-experiment-to-scoring-experiment.md).

Les étapes suivantes décrivent le déploiement d’une expérience prédictive comme nouveau service web. Vous pouvez également déployer l’expérience comme service web classique.

## <a name="deploy-it-as-a-new-web-service"></a>Déployer en tant que nouveau service web

À présent que l’expérience prédictive a été correctement préparée, vous pouvez la déployer comme nouveau (basé sur Resource Manager) service web Azure. En accédant au service web, les utilisateurs peuvent envoyer des données à votre modèle, qui renvoie alors ses prédictions.

Pour déployer votre expérience prédictive, cliquez sur **Exécuter** en bas de la zone de dessin de l’expérience. Une fois l’expérience terminée, cliquez sur **Déployer le service web**, puis sélectionnez **Déployer le service web [Nouveau]** .  La page de déploiement du portail de service web Machine Learning Studio (classique) s’ouvre.

> [!NOTE] 
> Pour déployer un nouveau service web, vous devez disposer d’autorisations suffisantes dans l’abonnement dans lequel déployer le service web. Pour en savoir plus, consultez la rubrique [Gérer un service web à l’aide du portail des services web Azure Machine Learning](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Page d’expérience de déploiement du portail du service web

Sur la page de l’expérience de déploiement, entrez le nom du service web.
Sélectionnez un plan de tarification. Si vous disposez d’un plan de tarification existant, vous pouvez le sélectionner. Sinon, vous devez créer un nouveau plan de tarification pour le service.

1. Dans la liste déroulante **Plan tarifaire**, sélectionnez un plan existant ou choisissez l’option **Select new plan** (Sélectionner un nouveau plan).
2. Dans **Plan Name**, (Nom du plan), tapez un nom qui identifiera le plan sur votre facture.
3. Sélectionnez un des **niveaux de plans mensuels**. Les niveaux de plan s’appliquent par défaut aux plans de votre région par défaut et votre service web est déployé dans cette région.

Cliquez sur **Déployer** pour ouvrir la page **Démarrage rapide** de votre service web.

La page Démarrage rapide du service web fournit des conseils et vous donne accès aux tâches couramment effectuées après la création d’un service web. À partir de là, vous pouvez facilement accéder à la fois à la page Test et à la page Utiliser.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Test de votre nouveau service web

Pour tester votre nouveau service web, cliquez sur **Tester le service web** dans la section des tâches courantes. Dans la page de test, vous pouvez tester votre service web comme un service de requête-réponse (RRS) ou comme un service d’exécution de lots (BES).

La page de test RRS affiche les entrées, les sorties et tous les paramètres globaux que vous avez définis pour l’expérience. Pour tester le service web, vous pouvez entrer manuellement les valeurs appropriées pour les entrées ou fournir un fichier au format CSV contenant les valeurs de test.

Pour tester le service en tant que Service de requête-réponse (RRS), en mode d’affichage liste, entrez les valeurs appropriées pour les entrées et cliquez sur **Test Request-Response**(Tester la requête-réponse). Vos résultats de prédiction s’affichent dans la colonne de sortie à gauche de la page.

![Entrer les valeurs appropriées pour tester votre service web](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Pour tester votre service d’exécution de lots (BES), cliquez sur **Lot**. Sur la page de test BES, cliquez sur Parcourir en dessous de votre entrée et sélectionnez un fichier CSV contenant les exemples de valeurs appropriés. Si vous n’avez pas de fichier CSV et que vous avez créé votre expérience prédictive à l’aide de Machine Learning Studio (classique), vous pouvez télécharger le jeu de données à utiliser pour votre expérience prédictive.

Pour télécharger le jeu de données, ouvrez Machine Learning Studio (classique). Ouvrez votre expérience prédictive et cliquez avec le bouton droit sur l’entrée correspondant à votre expérience. Dans le menu contextuel, sélectionnez **Jeu de données**, puis **Télécharger**.

![Télécharger votre jeu de données à partir du canevas Studio (classique)](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Cliquez sur **Test**. L’état de votre travail de traitement par lots s’affiche à droite sous **Test Batch Jobs**(Travaux de test de traitement par lots).

![Tester votre travail d'exécution de lots avec le portail de services web](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Dans la page **CONFIGURATION**, vous pouvez modifier la description et le titre, mettre à jour la clé du compte de stockage et activer les exemples de données pour votre service web.

![Configurer votre service web](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Accès à votre nouveau service web

Une fois que vous avez déployé votre service web à partir de Machine Learning Studio (classique), vous pouvez envoyer des données au service et recevoir des réponses programmatiquement.

La page **Utiliser** fournit toutes les informations dont vous avez besoin pour accéder à votre service web. Elle contient notamment la clé API pour activer l’accès autorisé au service.

Pour plus d’informations sur l’accès à un service web Machine Learning Studio (classique), consultez [Guide pratique pour utiliser un service web Azure Machine Learning Studio (classique)](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Gestion de votre nouveau service web

Vous pouvez gérer vos nouveaux services web par le biais du portail des services web Machine Learning Studio (classique). Dans la [page principale du portail](https://services.azureml.net/), cliquez sur **Services web**. Sur la page de services web, vous pouvez supprimer ou copier un service. Pour surveiller un service spécifique, cliquez sur le service, puis sur **Tableau de bord**. Pour surveiller les tâches de traitement par lots associées au service web, cliquez sur **Batch Request Log**(Journal de requête de traitement par lots).

### <a id="multi-region"></a> Déployer votre nouveau service web dans plusieurs régions

Vous pouvez facilement déployer un nouveau service web dans plusieurs régions, sans avoir besoin de plusieurs abonnements ni espaces de travail.

La tarification est spécifique à chaque région et vous devez donc définir un profil de facturation pour chaque région dans laquelle vous allez déployer le service web.

#### <a name="create-a-plan-in-another-region"></a>Créer un plan dans une autre région

1. Connectez-vous aux [services web Microsoft Azure Machine Learning](https://services.azureml.net/).
2. Cliquez sur l’option de menu **Abonnements** .
3. Sur la page de présentation des abonnements, cliquez sur **nouveau**.
4. Dans la liste déroulante **Abonnement** , sélectionnez l’abonnement dans lequel résidera le nouveau plan.
5. Dans la liste déroulante **Région** , sélectionnez une région pour le nouveau plan. Les options du plan de la région sélectionnée apparaissent dans la section **Plan Options** (Option du plan) de la page.
6. Dans la liste déroulante **Groupe de ressources** , sélectionnez un groupe de ressources pour le plan. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../../azure-resource-manager/management/overview.md).
7. Dans **Plan Name** (Nom du plan), tapez le nom du plan.
8. Sous **Plan Options**(Options du plan), cliquez sur le niveau de facturation du nouveau plan.
9. Cliquez sur **Créer**.

#### <a name="deploy-the-web-service-to-another-region"></a>Déployer le service web dans une autre région

1. Sur la page des services web Microsoft Azure Machine Learning, cliquez sur l’option de menu **Services web**.
2. Sélectionnez le service web que vous déployez dans une nouvelle région.
3. Cliquez sur **Copier**.
4. Dans **Nom du service web**, tapez le nouveau nom du service web.
5. Dans **Description du service web**, tapez une description du service web.
6. Dans la liste déroulante **Abonnement** , sélectionnez l’abonnement dans lequel résidera le nouveau service web.
7. Dans la liste déroulante **Groupe de ressources** , sélectionnez un groupe de ressources pour le service web. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../../azure-resource-manager/management/overview.md).
8. Dans la liste déroulante **Région** , sélectionnez la région dans laquelle vous voulez déployer le service web.
9. Dans la liste déroulante **Compte de stockage** , sélectionnez un compte de stockage où stocker le service web.
10. Dans la liste déroulante **Plan de tarification** , sélectionnez un plan dans la région que vous avez sélectionnée à l’étape 8.
11. Cliquez sur **Copier**.

## <a name="deploy-it-as-a-classic-web-service"></a>Déployer en tant que service web classique

Maintenant que l’expérience prédictive est correctement préparée, vous pouvez la publier en tant que service web Azure classique. En accédant au service web, les utilisateurs peuvent envoyer des données à votre modèle, qui renvoie alors ses prédictions.

Pour déployer votre expérience prédictive, cliquez sur **Exécuter** en bas du canevas de l’expérience, puis cliquez sur **Déployer le service web**. Le service web est configuré et vous êtes placé dans le tableau de bord du service web.

![Déployer votre service web à partir de Studio (classique)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Test de votre service web classique

Vous pouvez tester le service web dans le portail des services web Machine Learning Studio (classique) ou dans Machine Learning Studio (classique).

Pour tester le service web de requête-réponse, cliquez sur le bouton **Test** dans le tableau de bord du service web. Une boîte de dialogue s'affiche pour vous demander les données d'entrée du service. Il s'agit des colonnes attendues par l'expérience de notation. Entrez un jeu de données, puis cliquez sur **OK**. Les résultats générés par le service web sont affichés au bas du tableau de bord.

Vous pouvez cliquer sur le lien d’aperçu **Test** pour tester votre service dans le portail des services web d’Azure Machine Learning Studio (classique), comme indiqué précédemment dans la section relative au nouveau service web.

Pour tester le service d’exécution de lots, cliquez sur le lien d’aperçu **Test**. Sur la page de test BES, cliquez sur Parcourir en dessous de votre entrée et sélectionnez un fichier CSV contenant les exemples de valeurs appropriés. Si vous n’avez pas de fichier CSV et que vous avez créé votre expérience prédictive à l’aide de Machine Learning Studio (classique), vous pouvez télécharger le jeu de données à utiliser pour votre expérience prédictive.

![Test du service web](./media/publish-a-machine-learning-web-service/figure-3.png)

Dans la page **CONFIGURATION**, vous pouvez modifier le nom d’affichage du service et lui donner une description. Le nom et la description s’affichent dans le [portail Azure](https://portal.azure.com/) où vous gérez vos services web.

Vous pouvez fournir une description de vos données d’entrée, de vos données de sortie et des paramètres de service web en saisissant une chaîne pour chaque colonne sous **SCHÉMA D’ENTRÉE**, **SCHÉMA DE SORTIE** et **PARAMÈTRE DU SERVICE WEB**. Ces descriptions sont utilisées dans la documentation relative à l'exemple de code fournie pour le service web.

Vous pouvez activer la journalisation pour diagnostiquer toute défaillance que vous observez lors de l’accès à votre service web. Pour plus d’informations, consultez [Activer la journalisation pour les services web Machine Learning Studio (classique)](web-services-logging.md).

![Activer la journalisation dans le portail des services](./media/publish-a-machine-learning-web-service/figure-4.png)

Vous pouvez également configurer les points de terminaison pour le service web dans le portail des services web Azure Machine Learning en procédant de façon similaire à la procédure décrite précédemment dans la section relative au nouveau service web. Les options sont différentes. Vous pouvez ajouter ou modifier la description du service, activer la journalisation et activer des exemples de données à des fins de test.

### <a name="access-your-classic-web-service"></a>Accès à votre service web classique

Une fois que vous avez déployé votre service web à partir d’Azure Machine Learning Studio (classique), vous pouvez envoyer des données au service et recevoir des réponses programmatiquement.

Le tableau de bord fournit toutes les informations dont vous avez besoin pour accéder à votre service web. Par exemple, la clé API est fournie afin de permettre un accès autorisé au service et des pages d'aide API sont fournies pour vous aider à commencer à écrire votre code.

Pour plus d’informations sur l’accès à un service web Machine Learning Studio (classique), consultez [Guide pratique pour utiliser un service web Azure Machine Learning Studio (classique)](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>Gestion de votre service web classique

Vous pouvez effectuer diverses actions pour analyser un service web. Vous pouvez le mettre à jour et le supprimer. Vous pouvez également ajouter des points de terminaison à un service web classique en plus du point de terminaison par défaut créé lors de son déploiement.

Pour plus d’informations, consultez [Gérer un espace de travail Azure Machine Learning Studio (classique)](manage-workspace.md) et [Gérer un service web à l’aide du portail des services web Azure Machine Learning Studio (classique)](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Mise à jour du service web
Vous pouvez apporter des modifications à votre service web, par exemple mettre à jour le modèle avec des données d'apprentissage supplémentaires, et le déployer à nouveau, en remplaçant le service web d'origine.

Pour mettre à jour le service web, ouvrez l'expérience prédictive d'origine utilisée pour déployer le service web et effectuez une copie modifiable en cliquant sur **ENREGISTRER SOUS**. Effectuez vos modifications, puis cliquez sur **Déployer le service web**.

Étant donné que vous avez déjà déployé cette expérience, une invite vous demande si vous souhaitez remplacer (service web classique) ou mettre à jour (nouveau service web) le service existant. Si vous cliquez sur **OUI** ou **Mettre à jour**, le service web existant est arrêté et la nouvelle expérience prédictive est déployée à la place.

> [!NOTE]
> Si vous avez apporté des modifications de configuration dans le service web d'origine, par exemple, si vous saisissez un nouveau nom d'affichage ou une description, vous devrez saisir ces valeurs à nouveau.

Pour mettre à jour votre service web, vous pouvez reformer le modèle à l’aide d’un programme. Pour plus d’informations, consultez [Réentraîner les modèles Machine Learning Studio (classique) programmatiquement](/azure/machine-learning/studio/retrain-machine-learning-model).

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir davantage de détails techniques sur le fonctionnement du déploiement, consultez [Guide pratique pour faire évoluer un modèle Machine Learning Studio (classique) d’une simple expérience en service web opérationnel](model-progression-experiment-to-web-service.md).

* Pour plus d’informations sur la façon de préparer votre modèle à des fins de déploiement, consultez [Guide pratique pour préparer votre modèle au déploiement dans Azure Machine Learning Studio (classique)](convert-training-experiment-to-scoring-experiment.md).

* Il existe plusieurs moyens d’utiliser l’API REST et d’accéder au service web. Consultez [Guide pratique pour utiliser un service web Azure Machine Learning Studio (classique)](consume-web-services.md).

<!-- internal links -->
[Créez une expérience d'apprentissage]: #create-a-training-experiment
[Convertissez-la en une expérience prédictive]: #convert-the-training-experiment-to-a-predictive-experiment
[Nouveau service web]: #deploy-it-as-a-new-web-service
[Service web classique]: #deploy-it-as-a-classic-web-service
[nouveau]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
