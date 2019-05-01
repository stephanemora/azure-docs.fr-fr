---
title: Installer et configurer l’intégration de rapport de coût de AWS et d’utilisation avec Azure Cost Management
description: Cet article vous indique si le paramétrage et la configuration de l’intégration de rapport de coût de AWS et d’utilisation avec Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 688bcc02b14d101008afc76662fd6548446cb329
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870281"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Installer et configurer l’intégration de rapport de coût de AWS et d’utilisation

Avec l’intégration de rapport de coût de Amazon Web Services et d’utilisation, vous pouvez surveiller et contrôler vos dépenses AWS dans Azure Cost Management. L’intégration permet à un emplacement unique dans le portail Azure où vous pouvez surveiller et le contrôle de dépense pour Azure et AWS. Cet article explique comment configurer l’intégration et la configurer de sorte que vous utilisez les fonctionnalités de gestion des coûts pour analyser les coûts et passez en revue les budgets.

Gestion des coûts lit le rapport de coût de AWS et d’utilisation stocké dans un compartiment S3 à l’aide de vos informations d’identification d’accès AWS pour obtenir les définitions de rapport et télécharger des fichiers GZIP CSV de rapport.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Créer un rapport de coût et d’utilisation dans AWS

À l’aide d’un rapport de coût et d’utilisation est AWS méthode recommandée pour collecter et traiter les coûts AWS. Pour plus d’informations, consultez le [AWS coût et le rapport d’utilisation](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) article de documentation.

Utilisez le **rapports** page de la console de facturation et gestion des coûts dans AWS pour créer un rapport de coût et d’utilisation avec les étapes suivantes.

1. Connectez-vous à la Console de gestion AWS et ouvrir la console de gestion des coûts et de facturation à https://console.aws.amazon.com/billing.
2. Dans le volet de navigation, cliquez sur **rapports**.
3. Cliquez sur **créer rapport**.
4. Pour **nom_élément_rapport**, tapez un nom pour votre rapport.
5. Pour **unité de temps**, choisissez **horaire**.
6. Pour **Include**, ajoutez les ID de chaque ressource dans le rapport et sélectionnez **ID de ressource**.
7. Pour **activer la prise en charge de**, aucune sélection n’est requise.
8. Pour **paramètres d’actualisation des données**, sélectionnez **actualiser automatiquement votre coût &amp; rapport d’utilisation lorsque les frais sont détectés pour les mois précédents offrent clôturés**.
9. Cliquez sur **Suivant**.
10. Pour **compartiment Amazon S3**, tapez le nom du compartiment Amazon S3 dans lequel vous souhaitez remis à des rapports, cliquez sur **Vérifiez**. Le compartiment doit avoir les autorisations appropriées pour être valide. Pour plus d’informations sur l’ajout des autorisations dans le compartiment, consultez [compartiment de paramètre et les autorisations d’accès objet](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/set-permissions.html).
11. Pour **préfixe du chemin d’accès au rapport**, tapez le préfixe de chemin d’accès au rapport que vous souhaitez être ajouté au nom de votre rapport.
12. Pour **Compression**, sélectionnez **GZIP**.
13. Cliquez sur **Suivant**.
14. Une fois que vous avez passé en revue les paramètres pour votre rapport, cliquez sur **revue et terminer**.
    Remarque la **nom_élément_rapport**. Vous allez l’utiliser dans les étapes ultérieures.

Il peut prendre jusqu'à 24 heures avant que AWS démarrer la remise des rapports à votre compartiment Amazon S3. Après le démarrage de la remise, AWS met à jour les fichiers de rapport AWS coût et d’utilisation au moins une fois par jour.

## <a name="create-a-role-and-policy-in-aws"></a>Créer un rôle et une stratégie dans AWS

Gestion des coûts Azure accède au compartiment S3 où le rapport de coût et d’utilisation se trouve plusieurs fois par jour. Cost Management a besoin d’accéder aux informations d’identification dont vous recherchez de nouvelles données. Vous créez un rôle et une stratégie dans AWS pour autoriser l’accès à Cost Management.

Pour activer l’accès en fonction du rôle à un compte AWS dans Azure Cost Management, le rôle est créé dans la console AWS. Vous devez avoir le _Role ARN_ et _ID externe_ à partir de la console AWS. Plus tard, vous les utiliser dans la création d’une page du connecteur AWS dans Azure Cost Management.

1. Connectez-vous à votre console AWS et sélectionnez **Services**.
2. Dans la liste des services, sélectionnez **IAM**.
3. Sélectionnez **rôles** puis cliquez sur **Create Role**.
4. Dans la page suivante, sélectionnez **autre compte AWS**.
5. Dans **ID de compte** , entrez _432263259397_.
6. Dans **Options**, sélectionnez **nécessite un ID externe (meilleure pratique lorsqu’une tierce partie suppose que ce rôle)**.
7. Dans **ID externe**, entrez l’ID externe. L’ID externe est un code secret partagé entre le rôle AWS et Azure Cost Management. Le même ID externe est également utilisé dans la page Nouveau connecteur dans Cost Management. Par exemple, un ID externe ressemble à _Companyname1234567890123_.
    Ne modifiez pas la sélection pour **MFA nécessitent**. Il doit rester désactivée.
8. Cliquez sur **Suivant : Autorisations**.
9. Cliquez sur **Create policy** (Créer une stratégie). Un nouvel onglet de navigateur s’ouvre dans laquelle vous créez une nouvelle stratégie.
10. Cliquez sur **choisir un service**.
11. Type **coût et le rapport d’utilisation**.
12. Sélectionnez **niveau d’accès**, **en lecture** > **DescribeReportDefinitions**. Cela permet la que Gestion des coûts lire que CUR il signale sont définis et déterminer s’ils correspondent à la condition préalable définition de rapport.
13. Cliquez sur **ajouter des autorisations supplémentaires**.
14. Cliquez sur **choisir un service**.
15. Type _S3_.
16. Sélectionnez **niveau d’accès**, **liste** > **ListBucket**. Cette action Obtient la liste des objets dans le compartiment S3.
17. Sélectionnez **niveau d’accès**, **en lecture** > **GetObject**. Cette action permet la facturation de téléchargement de fichiers.
18. Sélectionnez **ressources**.
19. Sélectionnez **compartiment – ARN ajouter**.
20. Dans **nom du compartiment**, entrez le compartiment utilisé pour stocker les fichiers CUR.
21. Sélectionnez **objet – ARN ajouter**.
22. Dans **nom du compartiment**, entrez le compartiment utilisé pour stocker les fichiers CUR.
23. Dans **nom de l’objet**, sélectionnez **n’importe quel**.
24. Cliquez sur **ajouter des autorisations supplémentaires**.
25. Cliquez sur **choisir un service**.
26. Type _coût Service Explorer_.
27. Sélectionnez **actions de tous les services de l’Explorateur de coût (ce :\*)**. Cette action valide le fait que la collection est correcte.
28. Cliquez sur **ajouter des autorisations supplémentaires**.
29. Type **organisations**.
30. Sélectionnez **niveau d’accès, liste** > **ListAccounts**. Cette action Obtient les noms des comptes.
31. Dans **vérifier la stratégie**, entrez un nom pour la nouvelle stratégie. Vérification pour vous assurer que vous avez entré les informations correctes, puis cliquez sur **créer une stratégie**.
32. Revenez à l’onglet précédent et actualiser votre page de navigateur web. Dans la barre de recherche, recherchez votre nouvelle stratégie.
33. Sélectionnez **suivant : révision**.
34. Entrez un nom pour le nouveau rôle. Vérification pour vous assurer que vous avez entré les informations correctes, puis cliquez sur **Create Role**.
    Remarque la **Role ARN** et **ID externe** utilisé dans les étapes précédentes lorsque vous avez créé le rôle. Vous allez les utiliser ultérieurement lorsque vous configurez le connecteur Azure Cost Management.

La stratégie de JSON doit ressembler à l’exemple suivant. Remplacez _bucketname_ par le nom de votre compartiment S3.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
            "ce:*",
            "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-aws-connector-in-azure"></a>Configurer un nouveau connecteur AWS dans Azure

Utilisez les informations suivantes pour créer un nouveau connecteur AWS et démarrer la surveillance de vos coûts AWS.

1. Connectez-vous au portail Azure à https://portal.azure.com.
2. Accédez à **coût Management + facturation** > **gestion des coûts**.
3. Sous **paramètres**, cliquez sur **Cloud connecteurs (version préliminaire)**.  
    ![Exemple illustrant les connecteurs de Cloud (paramètre de version préliminaire)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Cliquez sur **+ ajouter** en haut de la page pour créer un nouveau connecteur.
5. Sur la page Créer une AWS connecteur, tapez un **surnom** pour nommer votre connecteur.  
    ![Exemple de la page Créer une AWS connecteur](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Si vous le souhaitez, sélectionnez le groupe d’administration par défaut. Il stocke toutes les découvertes de comptes liés. Vous pouvez configurer ultérieurement.
7. Sous le **facturation** section, sélectionnez **débitons automatiquement le 1 % en disponibilité générale** si vous souhaitez garantir un fonctionnement continu lorsque la version d’évaluation arrive à expiration. Si vous sélectionnez l’option automatique, vous devez sélectionner une facturation **abonnement**.
8. Entrez le **ARN de rôle**. C’est la valeur que vous avez utilisé lorsque vous configurez le rôle dans AWS.
9. Entrez le **ID externe**. C’est la valeur que vous avez utilisé lorsque vous configurez le rôle dans AWS.
10. Entrez le **Nom_élément_rapport** que vous avez créé dans AWS.
11. Cliquez sur **suivant** puis cliquez sur **créer**.

Il peut prendre plusieurs heures pour les nouvelles étendues AWS, le compte consolidé AWS AWS les comptes liés et leurs données de coût apparaisse.

Après avoir créé le connecteur, nous vous recommandons d’affecter le contrôle d’accès au connecteur. Les utilisateurs reçoivent des autorisations sur les étendues nouvellement découvertes : AWS consolidés compte et AWS comptes liés. L’utilisateur qui crée le connecteur est le propriétaire du connecteur, consolidée compte et toutes les liaisons de comptes.

Attribution d’autorisations de connecteur aux utilisateurs une fois la détection se produit n’affecte des autorisations pour les étendues AWS existantes. Au lieu de cela, les autorisations sont affectées uniquement les nouveaux comptes liés.

## <a name="additional-steps"></a>Étapes supplémentaires

- [Configurer des groupes d’administration](../governance/management-groups/index.md#initial-setup-of-management-groups), si vous n’avez pas déjà.
- Vérifiez que les nouvelles étendues sont ajoutés à votre sélecteur d’étendue. N’oubliez pas de cliquer sur **Actualiser** pour afficher les données les plus récentes.
- Dans la page du connecteur Cloud, sélectionnez votre connecteur, puis cliquez sur **accédez au compte de facturation** pour affecter le compte lié à des groupes d’administration.

## <a name="manage-cloud-connectors"></a>Gérer les connecteurs de cloud

Lorsque vous sélectionnez un connecteur dans la page de connecteurs Cloud, vous pouvez :

- Cliquez sur **accédez au compte de facturation** pour afficher les informations de compte consolidé de AWS.
- Cliquez sur **contrôle d’accès** pour gérer l’attribution de rôle pour le connecteur.
- Cliquez sur **modifier** pour mettre à jour le connecteur. Vous ne pouvez pas modifier le numéro de compte de AWS, tel qu’il apparaît dans l’ARN de rôle. Toutefois, vous pouvez créer un nouveau connecteur.
- Cliquez sur **Vérifiez** pour réexécuter le test de vérification pour vous assurer que Cost Management peut collecter des données à l’aide des paramètres du connecteur.

![Exemple montrant la liste des connecteurs AWS créés](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="role-of-azure-management-groups"></a>Rôle de groupes d’administration Azure

Pour créer un emplacement unique pour afficher les informations de fournisseur de cloud de croisée, vous devez placer vos abonnements Azure et les comptes AWS lié dans le même groupe d’administration. Si vous n’avez pas déjà configuré votre environnement Azure avec des groupes d’administration, consultez [configuration des groupes d’administration initiale](../governance/management-groups/index.md#initial-setup-of-management-groups).

Si vous souhaitez séparer les coûts, vous pouvez créer un groupe d’administration qui contient seulement les comptes AWS lié.

## <a name="aws-consolidated-account"></a>Compte AWS consolidé

Le compte AWS consolidé est utilisé pour consolider la facturation et paiement pour plusieurs comptes AWS. Votre compte AWS consolidés joue également un compte AWS lié.

![Exemple de détails d’un compte AWS consolidés](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Dans la page, vous pouvez :

- Cliquez sur **mettre à jour** pour la mise à jour en bloc l’association de comptes AWS lié à un groupe d’administration.
- Cliquez sur **contrôle d’accès** pour définir l’attribution de rôle pour l’étendue.

### <a name="aws-consolidated-account-permissions"></a>Autorisations du compte AWS consolidés

Par défaut, le AWS consolidés compte les autorisations sont définies lors de la création, en fonction des autorisations de connecteur AWS. Le créateur de connecteur est le propriétaire.

Vous gérez le niveau d’accès à l’aide de la page de niveau d’accès du compte AWS consolidés. Toutefois, les autorisations pour le AWS consolidés compte ne sont pas héritées par les comptes AWS liés.

## <a name="aws-linked-account"></a>Compte AWS lié

Le compte AWS lié est où les ressources AWS sont créés et gérés. Un compte lié agit également comme une limite de sécurité.

À partir de cette page, vous pouvez :

- Cliquez sur **mettre à jour** pour mettre à jour l’association de compte AWS lié à un groupe d’administration.
- Cliquez sur **contrôle d’accès** pour définir une attribution de rôle pour l’étendue.

![Exemple de la page compte lié AWS](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="aws-linked-account-permissions"></a>Autorisations du compte AWS lié

Par défaut, les autorisations de compte AWS lié sont définies lors de la création, en fonction des autorisations de connecteur AWS. Le créateur de connecteur est le propriétaire. Vous gérez le niveau d’accès à l’aide de la page de niveau d’accès du compte AWS lié. Autorisations pour le compte AWS consolidés ne sont pas héritées par les comptes AWS liés.

Comptes AWS liés héritent toujours les autorisations du groupe d’administration qu'auquel ils appartiennent.

## <a name="next-steps"></a>Étapes suivantes

- Maintenant que vous avez configuré et configuré l’intégration de rapport de coût de AWS et d’utilisation, passez à [aux coûts AWS de gérer et utilisation](aws-integration-manage.md).
- Si vous n’êtes pas familiarisé avec l’analyse des coûts, consultez [Explorer et analyser les coûts avec analyse des coûts](quick-acm-cost-analysis.md) Guide de démarrage rapide.
- Si vous n’êtes pas familiarisé avec les budgets dans Azure, consultez [créer et gérer des budgets Azure](tutorial-acm-create-budgets.md) didacticiel.
