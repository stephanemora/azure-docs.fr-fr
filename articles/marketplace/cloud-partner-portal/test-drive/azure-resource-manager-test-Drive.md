---
title: Version d’évaluation d’Azure Resource Manager | Place de marché Azure
description: Générer une version d’évaluation de la Place de marché à l’aide d’Azure Resource Manager
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Patrick .Butler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 8b2a24b6f2d7df92f1c8ea1b22432471aa432011
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644900"
---
# <a name="azure-resource-manager-test-drive"></a>Version d’évaluation d’Azure Resource Manager

Cet article concerne les éditeurs ayant leur offre sur la Place de marché Azure, ou qui se trouvent sur AppSource mais souhaitent générer leur version d’évaluation avec des ressources Azure uniquement.

Un modèle Azure Resource Manager (Resource Manager) est un conteneur codé de ressources Azure que vous concevez pour représenter votre solution de la meilleure façon. Si vous n’êtes pas familiarisé avec les modèles Resource Manager, consultez les articles [Comprendre les modèles Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) et [Création de modèles Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) pour être sûr de savoir comment générer et tester vos propres modèles.

La version d’évaluation prend le modèle Resource Manager fourni et créer un déploiement de toutes les ressources requises à partir de ce modèle dans un groupe de ressources.

Si vous choisissez de générer une version d’évaluation d’Azure Resource Manager, vos exigences sont les suivantes :

- Générer, tester, puis charger votre modèle Resource Manager de la version d’évaluation.
- Configurer tous les paramètres et les métadonnées nécessaires pour activer votre version d’évaluation.
- Republier votre offre avec la version d’évaluation activée.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Comment générer une version d’évaluation d’Azure Resource Manager

Voici le processus de génération d’une version d’évaluation d’Azure Resource Manager :

1. Concevez les services à offrir à vos clients dans un diagramme de flux.
1. Définissez les expériences que vos clients vont générer.
1. Selon les définitions ci-dessus, décidez quels éléments et quelles ressources sont nécessaires pour les clients accomplir tâches (par exemple, instance D365 ou site Web avec une base de données).
1. Générez la conception localement et testez l’expérience.
1. Packagez l’expérience dans un déploiement de modèle ARM. Ensuite :
    1. Définissez quelles parties des ressources sont des paramètres d’entrée.
    1. Définissez les variables.
    1. Définissez les sorties données à l’expérience client.
1. Publiez, testez et mettez en ligne.

La partie la plus importante sur la création d’une version d’évaluation d’Azure Resource Manager consiste à définir le(s) scénario(s) que vous souhaitez fournir à vos clients. Votre produit est un pare-feu et vous souhaitez montrer la façon dont il gère les attaques par injection de script ? Votre produit est un produit de stockage et vous souhaitez montrer la rapidité et la facilité avec laquelle il compresse les fichiers ?

Veillez à prendre suffisamment de temps pour évaluer les meilleures façons de présenter votre produit. Particulièrement concernant les ressources dont vous aurez besoin, car cela facilite le packaging du modèle Resource Manager.

Pour continuer avec notre exemple de pare-feu, l’architecture peut être que vous avez besoin une URL d’adresse IP publique pour votre service et une autre URL d’adresse IP publique pour le site web protégé par votre pare-feu. Chaque adresse IP est déployée sur une machine virtuelle et interconnectée avec un groupe de sécurité réseau et une interface réseau.

Une fois que vous avez conçu le package de ressources désiré, l’écriture et la construction du modèle Resource Manager de la version d’évaluation sont les prochaines étapes.

## <a name="writing-test-drive-resource-manager-templates"></a>Écriture des modèles Resource Manager de la version d’évaluation

La version d’évaluation exécute des déploiements dans un mode entièrement automatisé et par conséquent, les modèles de version d’évaluation disposent de certaines restrictions décrites ci-dessous.

### <a name="parameters"></a>Paramètres

La plupart des modèles possèdent un ensemble de paramètres. Les paramètres définissent les noms de ressources, les tailles de ressources (par exemple, les types de comptes de stockage ou les tailles de machine virtuelle), les noms d’utilisateur et les mots de passe, les noms DNS et ainsi de suite. Lorsque vous déployez des solutions à l’aide du portail Azure, vous pouvez manuellement remplir tous ces paramètres, choisir des noms DNS disponibles ou des noms de compte de stockage et ainsi de suite.

![Liste des paramètres dans Azure Resource Manager](./media/azure-resource-manager-test-drive/param1.png)

Toutefois, la version d’évaluation fonctionne dans un mode entièrement automatique, sans intervention humaine, elle prend donc en charge un ensemble limité de catégories de paramètres. Si un paramètre dans le modèle Resource Manager de la version d’évaluation n’entre pas dans l’une des catégories prises en charge, vous devez **le remplacer par une valeur variable ou constante.**

Vous pouvez utiliser n’importe quel nom valide pour vos paramètres, la version d’évaluation reconnaît la catégorie d’un paramètre à l’aide de valeurs de type métadonnées. Si vous ne **spécifiez pas le type de métadonnées pour chaque paramètre de modèle**, votre modèle ne passera pas l’étape de validation :

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

Il est également important de noter que **tous les paramètres sont facultatifs**, vous n’êtes donc pas obligé d’en utiliser si vous le désirez.

### <a name="accepted-parameter-metadata-types"></a>Types de métadonnées de paramètres acceptés

| Type de métadonnées   | Type de paramètre  | Description     | Exemple de valeur    |
|---|---|---|---|
| **baseuri**     | string          | URI de base de votre package de déploiement| https:\//\<\..\>.blob.core.windows.net/\<\..\> |
| **username**    | string          | Nouveau nom d’utilisateur aléatoire.| admin68876      |
| **mot de passe**    | chaîne sécurisée    | Nouveau mot de passe aléatoire | Lp!ACS\^2kh     |
| **ID de la session**   | string          | ID de session unique de la version d’évaluation (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="username"></a>username

La version d’évaluation initialise ce paramètre avec un **URI de base** de votre package de déploiement, vous pouvez donc utiliser ce paramètre pour construire l’URI de tous les fichiers inclus dans votre package.

```json
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

À l’intérieur de votre modèle, vous pouvez utiliser ce paramètre pour construire un URI pour n’importe quel fichier à partir du package de déploiement de votre version d’évaluation. L’exemple ci-dessous montre comment construire un URI du modèle lié :

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>username

La version d’évaluation initialise ce paramètre avec un nouveau nom d’utilisateur aléatoire :

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

Exemple de valeur :

    admin68876

Vous pouvez utiliser des noms d’utilisateur aléatoires ou constants pour votre solution.

#### <a name="password"></a>password

La version d’évaluation initialise ce paramètre avec un nouveau mot de passe aléatoire :

```json
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

Exemple de valeur :

    Lp!ACS^2kh

Vous pouvez utiliser des mots de passe aléatoires ou constants pour votre solution.

#### <a name="session-id"></a>ID de la session

La version d’évaluation initialise ce paramètre avec un GUID unique représentant l’ID de session de la version d’évaluation :

```json
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique Test Drive session id."
    }
  },
  ...
}
```

Exemple de valeur :

    b8c8693e-5673-449c-badd-257a405a6dee

Vous pouvez utiliser ce paramètre pour identifier de façon unique la session de la version d’évaluation, si nécessaire.

### <a name="unique-names"></a>Noms uniques

Certaines ressources Azure, comme les comptes de stockage ou les noms DNS, exigent des noms globalement uniques.

Autrement dit, chaque fois que la version d’évaluation déploie le modèle Resource Manager, elle crée un **groupe de ressources avec un nom unique** pour toutes ses\' ressources. Par conséquent, il est nécessaire d’utiliser la fonction concaténée [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) avec vos noms variables sur les ID de groupe de ressources pour générer des valeurs uniques aléatoires :

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Assurez-vous que vous concaténez les chaînes de votre paramètre/variable (\'contosovm\') avec une sortie de chaîne unique (\'resourceGroup ().id\'), car cela garantit l’unicité et la fiabilité de chaque variable.

Par exemple, la plupart des noms de ressources ne peuvent pas commencer par un chiffre, mais la fonction de chaîne unique peut retourner une chaîne commençant par un chiffre. Par conséquent, si vous utilisez une sortie de chaîne unique brute, vos déploiements échoueront. 

Vous trouverez des informations supplémentaires sur les règles et les restrictions d’affectation de noms de ressources dans [cet article](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="deployment-location"></a>Emplacement de déploiement

Vous pouvez rendre votre version d’évaluation disponible dans plusieurs régions Azure. L’idée est de permettre à un utilisateur de choisir la région la plus proche, afin de fournir la meilleure expérience utilisateur possible.

Lorsque la version d’évaluation crée une instance du laboratoire, elle crée toujours un groupe de ressources dans la région choisie par un utilisateur et elle exécute ensuite votre modèle de déploiement dans le contexte de ce groupe. Par conséquent, votre modèle doit choisir l’emplacement de déploiement à partir du groupe de ressources :

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

Utilisez ensuite cet emplacement pour chaque ressource d’une instance de laboratoire spécifique :

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

Vous devez vous assurer que votre abonnement a l’autorisation de déployer toutes les ressources que vous souhaitez déployer dans chacune des régions sélectionnées. Ainsi, vous devez vous assurer que vos images de machine virtuelle sont disponibles dans toutes les régions que vous vous apprêtez à activer, sinon votre modèle de déploiement ne fonctionnera pas dans certaines régions.

### <a name="outputs"></a>Outputs

Normalement, avec des modèles Resource Manager, vous pouvez effectuer un déploiement sans produire de sortie. La raison est que vous connaissez toutes les valeurs que vous utilisez pour remplir les paramètres de modèle et vous pouvez toujours inspecter les propriétés de n’importe quelle ressource manuellement.

Toutefois, pour les modèles Resource Manager de la version d’évaluation, il est important de retourner toutes les informations à la version d’évaluation, elles sont exigées pour obtenir un accès au laboratoire (URI de site Web, noms d’hôte de machine virtuelle, noms d’utilisateur et mots de passe). \' Assurez-vous que vos noms de sortie sont lisibles, car ces variables sont présentées au client.

Il n’existe aucune restriction liée aux sorties de modèle. N’oubliez pas que la version d’évaluation convertit toutes les valeurs de sortie en **chaînes**, donc si vous envoyez un objet vers la sortie, un utilisateur verra une chaîne JSON.

Exemple :

```json
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>Limites d’abonnement

L’abonnement et les limites de service sont d’autres éléments à prendre en considération. Par exemple, si vous souhaitez déployer jusqu’à dix machines virtuelles à 4 cœurs, vous devez vous assurer que l’abonnement que vous utilisez pour votre laboratoire vous permet d’utiliser 40 cœurs.

Vous trouverez plus d’informations concernant l’abonnement Azure et les limites de service dans [cet article](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Étant donné que plusieurs versions d’évaluation peuvent être faites en même temps, vérifiez que votre abonnement peut gérer le  de cœurs multiplié par le nombre total de versions d’évaluation simultanées pouvant être effectuées.\#

### <a name="what-to-upload"></a>Les éléments à télécharger

Le modèle Resource Manager de la version d’évaluation est chargé sous la forme d’un fichier zip, pouvant inclure des artefacts de déploiement différents, mais il doit inclure un fichier nommé **main-template.json**. Ce fichier est le modèle de déploiement Azure Resource Manager et la version d’évaluation l’utilise pour instancier un laboratoire.

Si vous avez des ressources supplémentaires en plus de ce fichier, vous pouvez les référencer comme des ressources externes à l’intérieur du modèle, ou les inclure dans le fichier zip.

Au cours de la certification de publication, la version d’évaluation décompresse votre package de déploiement et place son contenu dans un conteneur d’objets blob interne de la version d’évaluation. La structure du conteneur reflète la structure de votre package de déploiement :

| package.zip                       | Conteneur d’objets blob de la version d’évaluation         |
|---|---|
| main-template.json                | https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json  |
| templates/solution.json           | https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json |
| scripts/warmup.ps1                | https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1  |


Un URI de ce conteneur d’objets blob est appelé URI de Base. Chaque révision de votre laboratoire a son propre conteneur d’objets blob et, par conséquent, chaque révision de votre laboratoire a son propre URI de Base. La version d’évaluation peut passer un URI de base de votre package de déploiement décompressé dans votre modèle via des paramètres de modèle.

## <a name="transforming-template-examples-for-test-drive"></a>Transformation des exemples de modèle pour la version d’évaluation

Le processus de transformation d’une architecture de ressources en modèle Resource Manager de la version d’évaluation peut être décourageant. Afin de faciliter ce processus, nous avons préparé des exemples sur la façon optimale [de transformer les modèles de déploiement actuels](./transforming-examples-for-test-drive.md).

## <a name="how-to-publish-a-test-drive"></a>Publication d’une version d’évaluation

Maintenant que vous avez créé votre version d’évaluation, cette section décrit en détail chacun des champs obligatoires pour pouvoir la publier.

![Activation de la version d’évaluation dans l’interface utilisateur](./media/azure-resource-manager-test-drive/howtopub1.png)

Le premier champ, et également le plus important, consiste à activer/désactiver la version d’évaluation pour votre offre. Lorsque vous sélectionnez **Oui,** le reste du formulaire avec tous les champs obligatoires vous est présenté pour être rempli. Lorsque vous sélectionnez **Non**, le formulaire est désactivé et si vous republiez avec la version d’évaluation désactivée, votre version d’évaluation est supprimée de la production.

Remarque : si des versions d’évaluation sont employées par des utilisateurs, elles continuent à être exécutées jusqu’à ce que leur session expire.

### <a name="details"></a>Détails

La prochaine section à remplir concerne les détails de votre version d’évaluation.

![Informations détaillées de la version d’évaluation](./media/azure-resource-manager-test-drive/howtopub2.png)

**Description :** *requis* C’est ici que vous rédigez la description principale sur le contenu de votre version d’évaluation. Le client consulte ici les scénarios qui sont couverts par la version d’évaluation de votre produit. 

**Manuel utilisateur :** *requis* Il s’agit de la procédure approfondie de l’expérience de votre version d’évaluation. Le client l’ouvre et peut savoir exactement ce que vous voulez qu’il fasse dans sa version d’évaluation. Il est important que ce contenu soit facile à comprendre et à suivre ! (fichier PDF uniquement)

**Vidéo de démonstration de la version d’évaluation :** *recommandée* Comme le manuel de l’utilisateur, il est préférable d’inclure un didacticiel vidéo sur l’expérience de votre version d’évaluation. Le client la visionne avant et pendant la version d’évaluation et peut savoir exactement ce que vous voulez qu’il fasse dans sa version d’évaluation. Il est important que ce contenu soit facile à comprendre et à suivre !

- **Nom** : titre de votre vidéo.
- **Lien** : doit être une URL incorporée à partir de votre tube ou vidéo. Un exemple illustrant l’obtention de l’URL incorporée figure ci-dessous :
- **Miniature** : ce doit être une image de haute qualité (533 x 324 pixels). Il est recommandé de prendre ici une capture d’écran de certaines parties de votre version d’évaluation.

Voici comment ces champs s’affichent pour votre client lors de son expérience de version d’évaluation.

![Emplacement des champs de la version d’évaluation dans l’offre de la Place de marché](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Configuration technique

La section suivante à remplir est l’emplacement où vous chargez le modèle Resource Manager de votre version d’évaluation et où vous définissez comment les instances de votre version d’évaluation fonctionnent spécifiquement.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Instances :** *requis* Voici où vous configurez le nombre d’instances que vous désirez, leur(s) région(s), et la vitesse à laquelle vos clients peuvent obtenir la version d’évaluation.

- **Instances** : le modèle Resource Manager de votre version d’évaluation est déployé dans les régions choisies. Il est recommandé de choisir une seule région, celle où la majorité de vos clients se trouvent.
- **Chaud** : nombre d’instances de version d’évaluation déjà déployées et en attente d’accès par région sélectionnée. Les clients peuvent accéder instantanément à ces versions d’évaluation au lieu d’attendre un déploiement. L’inconvénient est que ces instances sont toujours en cours d’exécution sur votre abonnement Azure, entraînant des coûts de fonctionnement plus importants. Il est vivement recommandé d’avoir **au moins une instance à chaud**, étant donné que la plupart de vos clients ne souhaitent pas attendre la fin des déploiements complets, et il y a donc une chute dans l’utilisation du client.
- **Tiède** : nombre d’instances de version d’évaluation par région ayant été déployées, avant que la machine virtuelle ne soit arrêtée, puis stockée dans le stockage Azure. Le délai d’attente pour les instances tièdes est plus lent que les instances à chaud, mais le coût de fonctionnement du stockage est également moindre.
- **Froid** : nombre d’instances de version d’évaluation pouvant être déployées par région. Les instances à froid nécessitent le modèle Resource Manager entier de la version d’évaluation pour passer par un déploiement lorsqu’un client demande la version d’évaluation, elles sont donc plus lentes que les instances à chaud ou tièdes. Toutefois, la différence est que vous payez uniquement la durée de la version d’évaluation.

À ce stade, calculez le nombre total de versions d’évaluation simultanées potentielles que vous vous apprêtez à rendre disponible et vérifiez que la limite de quota de votre abonnement est capable de gérer cette quantité simultanée :

**(Nombre de régions sélectionnées x instances à chaud) + (nombre de régions sélectionnées x instances tièdes) + (nombre de régions sélectionnées x instances à froid)**

**Durée de la version d’évaluation (heures) :** *requis* Durée pendant laquelle la version d’évaluation reste active, en \# d’heures. La version d’évaluation se termine automatiquement à la fin de cette période.

**Modèle Resource Manager de la version d’évaluation :** *requis* Chargez votre modèle Resource Manager ici. C’est le fichier que vous avez créé dans la section précédente. Nommez le fichier de modèle principal « main template.json » et assurez-vous que votre modèle Resource Manager contient des paramètres de sortie pour les variables clés nécessaires. (Doit être un fichier zip)

**Informations d’accès :** *requis* Lorsqu’un client obtient sa version d’évaluation, les informations d’accès lui sont présentées. Ces instructions sont destinées à partager les paramètres de sortie utiles à partir du modèle Resource Manager de votre version d’évaluation. Pour inclure les paramètres de sortie, utilisez des accolades doubles (par exemple, **{{nom_sortie}}** ) afin de les insérer correctement à l’emplacement. (Une mise en forme de chaîne HTML est recommandée ici pour l’affichage dans le serveur frontal).

### <a name="test-drive-deployment-subscription-details"></a>Détails d’abonnement du déploiement de la version d’évaluation

La dernière section à remplir vise à permettre le déploiement automatique de versions d’évaluation en connectant votre abonnement Azure et Azure Active Directory (AD).

![Détails de l’abonnement de déploiement de la version d’évaluation](./media/azure-resource-manager-test-drive/subdetails1.png)

**ID d’abonnement Azure :** *requis* Ce paramètre accorde l’accès aux services Azure et au portail Azure. C’est dans l’abonnement que l’utilisation des ressources est signalée et que les services sont facturés. Si vous n’avez pas encore un abonnement Azure **distinct** uniquement pour les versions d’évaluation, continuez et créez-en un. Pour trouver les ID d’abonnement Azure, connectez-vous au portail Azure et accédez aux abonnements dans le menu de gauche. (Exemple : « a83645ac-1234-5ab6-6789-1h234g764ghty »)

![Abonnements Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**ID de locataire Azure AD :** *requis* Si vous avez un ID client déjà disponible, vous pouvez le trouver ci-dessous dans les propriétés :\> ID de répertoire.

![Propriétés Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Dans le cas contraire, créez un locataire dans Azure Active Directory.

![Liste des locataires Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

![Définissez l’organisation, le domaine et le pays/la région du locataire Azure AD.](./media/azure-resource-manager-test-drive/subdetails5.png)

![Confirmez la sélection](./media/azure-resource-manager-test-drive/subdetails6.png)

**ID d’application Azure AD :** *requis* L’étape suivante consiste à créer et inscrire une nouvelle application. Nous allons utiliser cette application pour effectuer des opérations sur l’instance de votre version d’évaluation.

1. Accédez au répertoire que vous venez de créer ou au répertoire déjà existant et sélectionnez le répertoire Azure Active Directory dans le volet de filtre.
2. Recherchez « Inscriptions d’applications » et cliquez sur « Ajouter »
3. Entrez un nom d’application.
4. Sélectionnez le type « Application/API web ».
5. Indiquez une valeur quelconque dans le champ URL de connexion ; nous n’utiliserons pas ce champ.
6. Cliquez sur Créer.
7. Une fois l’application créée, accédez à Propriétés -\> Définir l’application comme étant mutualisée, puis appuyez sur Enregistrer.

Cliquez sur Enregistrer. La dernière étape consiste à récupérer l’ID de cette application inscrite et à le coller dans le champ de la version d’évaluation ici.

![Détails de l’ID d’application Azure AD](./media/azure-resource-manager-test-drive/subdetails7.png)

Étant donné que nous utilisons l’application à déployer vers l’abonnement, nous devons ajouter cette application en tant que contributeur dans l’abonnement. Voici les instructions pour le faire :

1. Accédez au panneau Abonnements et sélectionnez l’abonnement approprié que vous utilisez uniquement pour la version d’évaluation.
1. Cliquez sur **Contrôle d’accès (IAM)** .
1. Cliquez sur l’onglet **Attributions de rôles**.  ![Ajouter un nouveau principal Access Control](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Cliquez sur **Ajouter une attribution de rôle**.
1. Définissez le rôle **Contributeur**.
1. Tapez le nom de l’application Azure AD et sélectionnez cette dernière pour assigner le rôle.
    ![Ajouter les autorisations](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Cliquez sur **Enregistrer**.

**Clé d’application Azure AD :** *requis* Le champ final consiste à générer une clé d’authentification. Sous Clés, ajoutez une description de la clé, définissez la durée afin qu’elle n’expire jamais, puis sélectionnez Enregistrer. Il est **important** d’éviter d’avoir une clé expirée, car cela arrête votre version d’évaluation en production. Copiez cette valeur et collez-la dans le champ obligatoire de votre version d’évaluation.

![Affiche les clés de l’application Azure AD](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que tous les champs de la version d’évaluation sont remplis, parcourez et **republiez** votre offre. Une fois que votre version d’évaluation a réussi la certification, vous devez tester avec soin l’expérience du client dans la **préversion** de votre offre. Démarrez une version d’évaluation dans l’interface utilisateur et ouvrez votre abonnement Azure à l’intérieur du portail Azure puis vérifiez que vos versions d’évaluation sont déployées correctement.

![Portail Azure](./media/azure-resource-manager-test-drive/subdetails9.png)

Il est important de noter que vous ne supprimez aucune instance de la version d’évaluation, car elles sont approvisionnées pour vos clients, donc le service de version d’évaluation nettoiera automatiquement ces groupes de ressources une fois qu’un client a terminé de l’utiliser.

Une fois que vous vous sentez à l’aise avec votre offre en préversion, il est temps de la **mettre en service**. Une fois que l’offre a été publiée, un processus de vérification finale de Microsoft permet de vérifier l’expérience de bout en bout complète. Si l’offre est rejetée pour une raison quelconque, nous envoyons une notification au support technique de votre offre, en expliquant les éléments à corriger.

Si vous avez d'autres questions, si vous cherchez des conseils de résolution des problèmes ou si vous souhaitez améliorer votre version d'évaluation, consultez l'article [Forum aux questions, résolution des problèmes et meilleures pratiques](./marketing-and-best-practices.md).
