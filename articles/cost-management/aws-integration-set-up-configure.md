---
title: Installer et configurer l’intégration de rapport de coût de AWS et d’utilisation avec Azure Cost Management
description: Cet article vous guide lors de la configuration et la configuration de l’intégration de rapport de coût de AWS et d’utilisation avec Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 951178a82e0975f5f2af71bd48cf0f931246ae37
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002121"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Installer et configurer l’intégration de rapport de coût de AWS et d’utilisation

Avec Amazon Web Services (AWS) intégration de rapport (CUR) coût et d’utilisation, vous pouvez surveiller et contrôler vos dépenses AWS dans Azure Cost Management. L’intégration permet à un emplacement unique dans le portail Azure où vous pouvez surveiller et le contrôle de dépense pour Azure et AWS. Cet article explique comment configurer l’intégration et la configurer de sorte que vous utilisez les fonctionnalités de gestion des coûts Azure pour analyser les coûts et passez en revue les budgets.

Processus de gestion des coûts du rapport de coût de AWS et d’utilisation stocké dans un compartiment S3 à l’aide de vos informations d’identification d’accès AWS pour obtenir les définitions de rapport et télécharger des fichiers GZIP CSV de rapport.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Créer un rapport de coût et d’utilisation dans AWS

Un rapport de coût et d’utilisation constitue le moyen recommandé d’AWS pour collecter et traiter les coûts AWS. Pour plus d’informations, consultez le [AWS coût et le rapport d’utilisation](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) documentation.

Utilisez le **coût & rapports d’utilisation** page de la console de facturation et gestion des coûts dans AWS pour créer un rapport de coût et d’utilisation avec les étapes suivantes :

1. Connectez-vous à la Console de gestion AWS et ouvrez le [console facturation et gestion des coûts](https://console.aws.amazon.com/billing).
2. Dans le volet de navigation, sélectionnez **coût & rapports d’utilisation**.
3. Sélectionnez **créer rapport**.
4. Pour **nom_élément_rapport**, entrez un nom pour votre rapport.
5. Pour **détails du rapport supplémentaire** , pour inclure les ID de chaque ressource dans le rapport, puis sélectionnez **incluent un ID de ressource**.
6. Pour **paramètres d’actualisation des données**, indiquez si vous souhaitez le rapport de coût de AWS et d’utilisation pour actualiser si AWS s’applique remboursements, crédits, ou prennent en charge des frais à votre compte après la finalisation de votre facture. Lors de l’actualisation d’un rapport, un nouveau rapport est téléchargé vers Amazon S3. Il est recommandé de laisser ce paramètre sur.
7. Sélectionnez **Suivant**.
8. Pour **compartiment S3**, choisissez **configurer**.
9. Dans la boîte de dialogue Configurer le compartiment S3, effectuez l’une des opérations suivantes :
    1. Sélectionnez un compartiment existant dans la liste déroulante et choisissez **suivant**.
    2. Entrez un nom de compartiment et de la région où vous souhaitez créer un compartiment de nouveau et choisissez **suivant**.
10. Sélectionnez que j’ai vérifié que cette stratégie est corrige et cliquez sur Enregistrer.
11. (Facultatif) Pour le préfixe de chemin d’accès du rapport, entrez le préfixe de chemin d’accès de rapport que vous souhaitez ajouté au début le nom de votre rapport.
Si vous ne spécifiez pas un préfixe, le préfixe par défaut est le nom que vous avez spécifié pour le rapport à l’étape 4 et la plage de dates pour le rapport, dans le format suivant : `/report-name/date-range/`
12. Pour **unité de temps**, choisissez **horaire**.
13. Pour **le contrôle de version rapport**, choisissez si vous souhaitez que chaque version du rapport pour remplacer la version précédente du rapport ou pour être remis en plus des versions précédentes.
14. Pour **activer l’intégration de données pour**, aucune sélection n’est requise.
15. Pour **Compression**, sélectionnez **GZIP**.
16. Sélectionnez **Suivant**.
17. Une fois que vous avez passé en revue les paramètres pour votre rapport, sélectionnez **revue et terminer**.

    Notez le nom du rapport. Vous allez l’utiliser dans les étapes ultérieures.

Il peut prendre jusqu'à 24 heures avant que AWS démarrer la remise des rapports à votre compartiment Amazon S3. Après le démarrage de la remise, AWS met à jour les fichiers de rapport AWS coût et d’utilisation au moins une fois par jour. Vous pouvez poursuivre la configuration de votre environnement AWS sans attendre la livraison à démarrer.

## <a name="create-a-role-and-policy-in-aws"></a>Créer un rôle et une stratégie dans AWS

Gestion des coûts Azure accède au compartiment S3 où le rapport de coût et d’utilisation se trouve plusieurs fois par jour. Le service a besoin d’accéder aux informations d’identification dont vous recherchez de nouvelles données. Vous créez un rôle et une stratégie dans AWS pour permettre la gestion des coûts pour y accéder.

Pour activer l’accès en fonction du rôle à un compte AWS dans Cost Management, le rôle est créé dans la console AWS. Vous devez avoir le _ARN de rôle_ et _ID externe_ à partir de la console AWS. Plus tard, de les utiliser sur le **créer un connecteur AWS** page dans Gestion des coûts.

Utilisez l’Assistant Création d’un nouveau rôle :

1. Connectez-vous à votre console AWS et sélectionnez **Services**.
2. Dans la liste des services, sélectionnez **IAM**.
3. Sélectionnez **rôles** , puis sélectionnez **Create Role**.
4. Sur la page suivante, sélectionnez **autre compte AWS**.
5. Dans **ID de compte**, entrez **432263259397**.
6. Dans **Options**, sélectionnez **nécessite un ID externe (meilleure pratique lorsqu’une tierce partie suppose que ce rôle)**.
7. Dans **ID externe**, entrez l’ID externe. L’ID externe est un code secret partagé entre le rôle AWS et Azure Cost Management. Le même ID externe est également utilisé sur le **nouveau connecteur** page dans Gestion des coûts. Par exemple, un ID externe ressemble à _Companyname1234567890123_.

    > [!NOTE]
    > Ne modifiez pas la sélection pour **MFA nécessitent**. Il doit rester désactivée.
8. Sélectionnez **Suivant : Autorisations**.
9. Sélectionnez **créer une stratégie**. Un nouvel onglet de navigateur s'ouvre. Voilà où vous créez une stratégie.
10. Sélectionnez **choisir un service**.

Configurer les autorisations pour le rapport de coût et d’utilisation :

1. Entrez **coût et le rapport d’utilisation**.
2. Sélectionnez **niveau d’accès** > **en lecture** > **DescribeReportDefinitions**. Cette étape permet la gestion des coûts lire les rapports CUR sont définis et de déterminer s’ils correspondent à la condition préalable définition de rapport.
3. Sélectionnez **ajouter des autorisations supplémentaires**.

Configurer les autorisations pour votre compartiment S3 et les objets :

1. Sélectionnez **choisir un service**.
2. Entrez **S3**.
3. Sélectionnez **niveau d’accès** > **liste** > **ListBucket**. Cette action Obtient la liste des objets dans le compartiment S3.
4. Sélectionnez **niveau d’accès** > **en lecture** > **GetObject**. Cette action permet de télécharger des fichiers de facturation.
5. Sélectionnez **ressources**.
6. Sélectionnez **compartiment – ARN ajouter**.
7. Dans **nom du compartiment**, entrez le compartiment utilisé pour stocker les fichiers CUR.
8. Sélectionnez **objet – ARN ajouter**.
9. Dans **nom du compartiment**, entrez le compartiment utilisé pour stocker les fichiers CUR.
10. Dans **nom de l’objet**, sélectionnez **n’importe quel**.
11. Sélectionnez **ajouter des autorisations supplémentaires**.

Configurer les autorisations pour l’Explorateur de coût :

1. Sélectionnez **choisir un service**.
2. Entrez **coût Service Explorer**.
3. Sélectionnez **actions de tous les services de l’Explorateur de coût (ce :\*)**. Cette action valide le fait que la collection est correcte.
4. Sélectionnez **ajouter des autorisations supplémentaires**.

Ajoutez l’autorisation pour les organisations AWS :

1. Entrez **organisations**.
2. Sélectionnez **niveau d’accès** > **liste** > **ListAccounts**. Cette action Obtient les noms des comptes.
3. Dans **vérifier la stratégie**, entrez un nom pour la nouvelle stratégie. Vérifiez que vous avez entré les informations correctes, puis sélectionnez **créer une stratégie**.
4. Revenez à l’onglet précédent et actualisez la page Web de votre navigateur. Dans la barre de recherche, recherchez votre nouvelle stratégie.
5. Sélectionnez **suivant : révision**.
6. Entrez un nom pour le nouveau rôle. Vérifiez que vous avez entré les informations correctes, puis sélectionnez **Create Role**.

    Remarque : le rôle ARN et l’ID externe utilisée dans les étapes précédentes lorsque vous avez créé le rôle. Vous allez les utiliser ultérieurement lorsque vous configurez le connecteur Azure Cost Management.

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

Utilisez les informations suivantes pour créer un connecteur AWS et démarrer la surveillance de vos coûts AWS :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Accédez à **coût Management + facturation** > **gestion des coûts**.
3. Sous **paramètres**, sélectionnez **Cloud connecteurs (version préliminaire)**.  
    ![Exemple illustrant les connecteurs de Cloud (version préliminaire) paramètre)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Sélectionnez **+ ajouter** en haut de la page pour créer un connecteur.
5. Sur le **créer un connecteur AWS** page **nom d’affichage**, entrez un nom pour votre connecteur.  
    ![Exemple de la page pour la création d’un connecteur AWS](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Si vous le souhaitez, sélectionnez le groupe d’administration par défaut. Il stocke toutes les découvertes de comptes liés. Vous pouvez configurer ultérieurement.
7. Dans le **facturation** section, sélectionnez **débitons automatiquement le 1 % en disponibilité générale** si vous souhaitez garantir un fonctionnement continu lorsque la version d’évaluation arrive à expiration. Si vous sélectionnez l’option automatique, vous devez sélectionner un abonnement à la facturation.
8. Pour **Role ARN**, entrez la valeur que vous avez utilisé lorsque vous configurez le rôle dans AWS.
9. Pour **ID externe**, entrez la valeur que vous avez utilisé lorsque vous configurez le rôle dans AWS.
10. Pour **Nom_élément_rapport**, entrez le nom que vous avez créé dans AWS.
11. Sélectionnez **suivant** , puis sélectionnez **créer**.

Il peut prendre plusieurs heures pour les nouvelles étendues AWS, le AWS consolidés compte et comptes AWS lié et leurs affichage des données de coût.

Après avoir créé le connecteur, nous vous recommandons d’affecter le contrôle d’accès à celui-ci. Les utilisateurs reçoivent des autorisations sur les étendues nouvellement découvertes : AWS consolidés compte et comptes AWS lié. L’utilisateur qui crée le connecteur est le propriétaire du connecteur, le compte consolidé et tous les comptes liés.

Attribution d’autorisations de connecteur aux utilisateurs une fois la détection se produit n’affecte des autorisations pour les étendues AWS existantes. Au lieu de cela, les autorisations sont affectées uniquement les nouveaux comptes liés.

## <a name="take-additional-steps"></a>Prendre des mesures supplémentaires

- [Configurer des groupes d’administration](../governance/management-groups/index.md#initial-setup-of-management-groups), si vous n’avez pas déjà.
- Vérifiez que les nouvelles étendues sont ajoutés à votre sélecteur d’étendue. Sélectionnez **Actualiser** pour afficher les données les plus récentes.
- Sur le **Cloud connecteurs** page, sélectionnez votre connecteur et sélectionnez **accédez au compte de facturation** pour affecter le compte lié à des groupes d’administration.

## <a name="manage-cloud-connectors"></a>Gérer les connecteurs de cloud

Lorsque vous sélectionnez un connecteur sur le **Cloud connecteurs** page, vous pouvez :

- Sélectionnez **accédez au compte de facturation** pour afficher des informations pour le AWS compte consolidé.
- Sélectionnez **contrôle d’accès** pour gérer l’attribution de rôle pour le connecteur.
- Sélectionnez **modifier** pour mettre à jour le connecteur. Vous ne pouvez pas modifier le numéro de compte AWS, car il apparaît dans l’ARN de rôle. Mais vous pouvez créer un nouveau connecteur.
- Sélectionnez **Vérifiez** pour réexécuter le test de vérification pour vous assurer que Cost Management peut collecter des données en utilisant les paramètres de connecteur.

![Exemple de liste de connecteurs AWS créés](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Configurer des groupes de gestion Azure

Pour créer un emplacement unique pour afficher les informations de fournisseur de cloud de croisée, vous devez placer vos abonnements Azure et les comptes AWS lié dans le même groupe d’administration. Si vous n’avez pas déjà configuré votre environnement Azure avec des groupes d’administration, consultez [configuration des groupes d’administration initiale](../governance/management-groups/index.md#initial-setup-of-management-groups).

Si vous souhaitez séparer les coûts, vous pouvez créer un groupe d’administration qui contient seulement les comptes AWS lié.

## <a name="set-up-an-aws-consolidated-account"></a>Compte de configuration AWS consolidé

Le compte AWS consolidés combine la facturation et paiement pour plusieurs comptes AWS. Il joue également un compte AWS lié.

![Détails de l’exemple pour AWS consolidés compte](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Dans la page, vous pouvez :

- Sélectionnez **mettre à jour** pour mettre à jour l’association de AWS lié comptes avec un groupe d’administration.
- Sélectionnez **contrôle d’accès** pour définir l’attribution de rôle pour l’étendue.

### <a name="permissions-for-an-aws-consolidated-account"></a>Autorisations pour AWS consolidés compte

Par défaut, les autorisations pour un compte AWS consolidés sont définies lors de la création du compte, en fonction des autorisations de connecteur AWS. Le créateur de connecteur est le propriétaire.

Vous gérez le niveau d’accès en utilisant le **niveau d’accès** compte consolidé de page de l’AWS. Toutefois, AWS lié comptes n’héritent pas des autorisations pour le compte AWS consolidés.

## <a name="set-up-an-aws-linked-account"></a>Configurer un compte AWS lié

Le compte AWS lié est où les ressources AWS sont créés et gérés. Un compte lié agit également comme une limite de sécurité.

À partir de cette page, vous pouvez :

- Sélectionnez **mettre à jour** pour mettre à jour l’association de AWS lié au compte avec un groupe d’administration.
- Sélectionnez **contrôle d’accès** pour définir une attribution de rôle pour l’étendue.

![Exemple de la page compte lié AWS](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>Autorisations pour AWS lié compte

Par défaut, les autorisations pour un compte AWS lié sont définies lors de la création, en fonction des autorisations de connecteur AWS. Le créateur de connecteur est le propriétaire. Vous gérez le niveau d’accès en utilisant le **niveau d’accès** la page de l’AWS liée compte. AWS lié comptes n’héritent des autorisations à partir d’un compte AWS consolidés.

AWS les comptes liés toujours héritent des autorisations du groupe d’administration auquel ils appartiennent.

## <a name="next-steps"></a>Étapes suivantes

- Maintenant que vous avez configuré et configuré l’intégration de rapport de coût de AWS et d’utilisation, passez à [aux coûts AWS de gérer et utilisation](aws-integration-manage.md).
- Si vous n’êtes pas familiarisé avec l’analyse des coûts, consultez [Explorer et analyser les coûts avec analyse des coûts](quick-acm-cost-analysis.md) Guide de démarrage rapide.
- Si vous n’êtes pas familiarisé avec les budgets dans Azure, consultez [créer et gérer des budgets Azure](tutorial-acm-create-budgets.md).
