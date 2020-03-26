---
title: Activer l’accès privé aux sites pour Azure Functions
description: Découvrez comment configurer l’accès privé aux sites depuis un réseau virtuel Azure pour Azure Functions.
author: mcollier
ms.author: mcollier
ms.service: azure-functions
ms.topic: tutorial
ms.date: 02/15/2020
ms.openlocfilehash: ada08de182791c6ecb2b83ef3b924bf40975e1ee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78852014"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Tutoriel : Établir l’accès privé aux sites avec Azure Functions

Ce tutoriel vous montre comment activer l’[accès privé aux sites](./functions-networking-options.md#private-site-access) avec Azure Functions. En utilisant l’accès privé aux sites, vous pouvez exiger que le code de votre fonction soit déclenché uniquement à partir d’un réseau virtuel spécifique.

L’accès privé aux sites est utile dans les scénarios où l’accès à l’application de fonction doit être limité à un réseau virtuel spécifique. Par exemple, l’application de fonction peut être applicable uniquement aux employés d’une organisation spécifique ou aux services qui se trouvent dans le réseau virtuel spécifié (par exemple, une autre fonction Azure, une machine virtuelle Azure ou un cluster AKS).

Si une application Functions doit accéder aux ressources Azure au sein du réseau virtuel ou connectées via des [points de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md), l’[intégration au réseau virtuel](./functions-create-vnet.md) est nécessaire.

Dans ce tutoriel, vous allez apprendre à configurer l’accès privé aux sites pour votre application de fonction :

> [!div class="checklist"]
> * Création d'une machine virtuelle
> * Créer un service Azure Bastion
> * Créer une application Azure Functions
> * Configurer un point de terminaison de service de réseau virtuel
> * Créer et déployer une fonction Azure
> * Appeler la fonction à partir de l’extérieur et dans le réseau virtuel

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="topology"></a>Topologie

Le diagramme suivant représente l’architecture de la solution à créer :

![Diagramme d’architecture général pour la solution d’accès privé aux sites](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Prérequis

Pour ce tutoriel, il est important de comprendre l’adressage IP et la gestion des sous-réseaux. Vous pouvez commencer par [cet article qui couvre les principes fondamentaux de l’adressage et de la gestion de sous-réseaux](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). De nombreux autres articles et vidéos sont disponibles en ligne.

## <a name="sign-in-to-azure-portal"></a>Se connecter au portail Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

La première étape de ce tutoriel consiste à créer une machine virtuelle à l’intérieur d’un réseau virtuel.  La machine virtuelle sera utilisée pour accéder à votre fonction une fois que vous aurez rendu cette dernière accessible uniquement depuis le réseau virtuel.

1. Sélectionnez le bouton **Créer une ressource**.

2. Dans le champ de recherche, tapez `Windows Server`, puis sélectionnez **Windows Server** dans les résultats de la recherche.

3. Sélectionnez **Windows Server 2019 Datacenter** dans la liste des options de Windows Server, puis appuyez sur le bouton **Créer**.

4. Sous l’onglet **De base**, utilisez les paramètres de machine virtuelle de la manière spécifiée dans le tableau sous l’image :

    >[!div class="mx-imgBorder"]
    >![Onglet De base pour une nouvelle machine virtuelle Windows](./media/functions-create-private-site-access/create-vm-3.png)

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Abonnement** | Votre abonnement | Abonnement sous lequel vos ressources sont créées. |
    | [**Groupe de ressources**](../azure-resource-manager/management/overview.md) | myResourceGroup | Choisissez le groupe de ressources qui doit contenir toutes les ressources de ce tutoriel.  Le fait d’utiliser le même groupe simplifie la suppression des ressources à la fin de ce tutoriel. |
    | **Nom de la machine virtuelle** | myVM | Le nom de la machine virtuelle doit être unique dans le groupe de ressources |
    | [**Région**](https://azure.microsoft.com/regions/) | (États-Unis) USA Centre Nord | Choisissez une région proche de chez vous ou près des fonctions qui doivent être accessibles. |
    | **Ports d’entrée publics** | None | Sélectionnez **Aucun** pour qu’il n’existe aucune connectivité entrante à la machine virtuelle à partir d’Internet. L’accès à distance à la machine virtuelle sera configuré par le biais du service Azure Bastion. |

5. Choisissez l’onglet **Mise en réseau** et sélectionnez **Créer nouveau** pour configurer un nouveau réseau virtuel.

    >[!div class="mx-imgBorder"]
    >![Créer un réseau virtuel pour la nouvelle machine virtuelle](./media/functions-create-private-site-access/create-vm-networking.png)

6. Dans **Créer un réseau virtuel**, utilisez les paramètres figurant dans le tableau sous l’image :

    >[!div class="mx-imgBorder"]
    >![Créer un réseau virtuel pour la nouvelle machine virtuelle](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Nom** | myResourceGroup-vnet | Vous pouvez utiliser le nom par défaut généré pour votre réseau virtuel. |
    | **Plage d’adresses** | 10.10.0.0/16 | Utilisez une plage d’adresses unique pour le réseau virtuel. |
    | **Nom du sous-réseau** | Didacticiel | Nom du sous-réseau. |
    | **Plage d’adresses** (sous-réseau) | 10.10.1.0/24 | La taille du sous-réseau définit le nombre d’interfaces qui peuvent être ajoutées au sous-réseau. Ce sous-réseau est utilisé par la machine virtuelle. Un sous-réseau `/24` fournit 254 adresses d’hôte. |

7. Sélectionnez **OK** pour créer le réseau virtuel.
8. De retour sous l’onglet **Mise en réseau**, assurez-vous que l’option **Aucune** est sélectionnée pour **Adresse IP publique**.
9. Choisissez l’onglet **Gestion**, puis dans **Compte de stockage des diagnostics**, choisissez **Créer nouveau** pour créer un compte de stockage.
10. Laissez les valeurs par défaut pour les sections **Identité**, **Arrêt automatique** et **Sauvegarde**.
11. Sélectionnez **Revoir + créer**. Une fois la validation terminée, sélectionnez **Créer**. Le processus de création d’une machine virtuelle prend quelques minutes.

## <a name="configure-azure-bastion"></a>Configurer Azure Bastion

[Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) est un service Azure complètement managé qui fournit un accès RDP et SSH sécurisé aux machines virtuelles directement à partir du portail Azure. L’utilisation du service Azure Bastion supprime la nécessité de configurer les paramètres réseau liés à l’accès RDP.

1. Dans le portail, choisissez **Ajouter** en haut de la vue de groupe de ressources.
2. Dans le champ de recherche, entrez « Bastion ».  Sélectionnez « Bastion ».
3. Sélectionnez **Créer** pour commencer le processus de création d’une ressource Azure Bastion. Vous remarquerez un message d’erreur dans la section **Réseau virtuel**, car il n’existe pas encore de sous-réseau `AzureBastionSubnet`. Le sous-réseau est créé au cours des étapes suivantes. Utilisez les paramètres indiqués dans le tableau sous l’image ci-après :

    >[!div class="mx-imgBorder"]
    >![Début de la création de la ressource Azure Bastion](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Nom** | myBastion | Nom de la nouvelle ressource Bastion |
    | **Région** | Centre-Nord des États-Unis | Choisissez une [région](https://azure.microsoft.com/regions/) près de chez vous ou près d’autres services auxquels ont accès vos fonctions. |
    | **Réseau virtuel** | myResourceGroup-vnet | Réseau virtuel dans lequel la ressource Bastion est créée |
    | **Sous-réseau** | AzureBastionSubnet | Sous-réseau dans votre réseau virtuel auquel la nouvelle ressource d’hôte Bastion est déployée. Vous devez créer un sous-réseau à l’aide de la valeur de nom `AzureBastionSubnet`. Cette valeur permet à Azure de savoir dans quel sous-réseau déployer les ressources Bastion. Vous devez utiliser un sous-réseau de `/27` ou plus (`/27`, `/26`, etc.). |

    > [!NOTE]
    > Pour obtenir un guide pas à pas détaillé de la création d’une ressource Azure Bastion, reportez-vous au tutoriel [Créer un hôte Azure Bastion](../bastion/bastion-create-host-portal.md).

4. Créez un sous-réseau dans lequel Azure peut provisionner l’hôte Azure Bastion. Le fait de choisir **Gérer la configuration du sous-réseau** ouvre un nouveau volet dans lequel vous pouvez définir un nouveau sous-réseau.  Choisissez **+ Sous-réseau** pour créer un sous-réseau.
5. Le sous-réseau doit avoir pour nom `AzureBastionSubnet` et pour préfixe au minimum `/27`.  Sélectionnez **OK** pour créer le sous-réseau.

    >[!div class="mx-imgBorder"]
    >![Créer un sous-réseau pour l’hôte Azure Bastion](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

6. Dans la page **Créer un bastion**, sélectionnez le `AzureBastionSubnet` récemment créé dans la liste des sous-réseaux disponibles.

    >[!div class="mx-imgBorder"]
    >![Créer un hôte Azure Bastion avec un sous-réseau spécifique](./media/functions-create-private-site-access/create-bastion-basics-2.png)

7. Sélectionnez **Vérifier & créer**. Une fois la validation terminée, sélectionnez **Créer**. La création de la ressource Azure Bastion prendra quelques minutes.

## <a name="create-an-azure-functions-app"></a>Créer une application Azure Functions

L’étape suivante consiste à créer une application de fonction dans Azure à l’aide du [plan de consommation](functions-scale.md#consumption-plan). Vous déploierez le code de votre fonction sur cette ressource ultérieurement dans le tutoriel.

1. Dans le portail, choisissez **Ajouter** en haut de la vue de groupe de ressources.
2. Sélectionnez **Compute > Function App**.
3. Dans la section **De base**, utilisez les paramètres d’application de fonction comme indiqué dans le tableau.

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Groupe de ressources** | myResourceGroup | Choisissez le groupe de ressources qui doit contenir toutes les ressources de ce tutoriel.  L’utilisation du même groupe de ressources pour l’application de fonction et la machine virtuelle facilitera le nettoyage des ressources quand vous aurez terminé ce tutoriel. |
    | **Nom de l’application de fonction** | Nom globalement unique | Nom qui identifie votre nouvelle Function App. Les caractères valides sont a à z (insensible à la casse), 0 à 9 et -. |
    | **Publier** | Code | Option permettant de publier des fichiers de code ou un conteneur Docker. |
    | **Pile d’exécution** | Langage préféré | Choisissez un runtime qui prend en charge votre langage de programmation de fonction favori. |
    | **Région** | Centre-Nord des États-Unis | Choisissez une [région](https://azure.microsoft.com/regions/) près de chez vous ou près d’autres services auxquels ont accès vos fonctions. |

    Sélectionnez le bouton **Suivant : Hébergement >** .
4. Pour la section **Hébergement**, sélectionnez le **Compte de stockage**, le **Système d’exploitation** et le **Plan** comme indiqué dans le tableau suivant.

    | Paramètre      | Valeur suggérée  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Compte de stockage** | Nom globalement unique | Créez un compte de stockage utilisé par votre application de fonction. Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres. Vous pouvez également utiliser un compte existant qui doit répondre aux [exigences relatives aux comptes de stockage](./functions-scale.md#storage-account-requirements). |
    | **Système d’exploitation** | Système d’exploitation préféré | Un système d’exploitation est présélectionné pour vous en fonction de la sélection de votre pile d’exécution, mais vous pouvez modifier le paramètre si nécessaire. |
    | **Planification** | Consommation | Le [plan d’hébergement](./functions-scale.md) régit la mise à l’échelle de l’application de fonction est la mise à disposition des ressources pour chaque instance. |
5. Sélectionnez **Vérifier + créer** pour passer en revue les sélections de configuration d’application.
6. Sélectionnez **Créer** pour configurer et déployer l’application de fonction.

## <a name="configure-access-restrictions"></a>Configurer des restrictions d’accès

L’étape suivante consiste à configurer des [restrictions d’accès](../app-service/app-service-ip-restrictions.md) pour que seules les ressources sur le réseau virtuel puissent appeler la fonction.

Vous activez l’[accès privé aux sites](functions-networking-options.md#private-site-access) en créant un [point de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md) de réseau virtuel Azure entre l’application de fonction et le réseau virtuel spécifié. Les restrictions d’accès sont implémentées par le biais de points de terminaison de service. Les points de terminaison de service garantissent que seul le trafic provenant du réseau virtuel spécifié peut accéder à la ressource désignée. Dans ce cas, la ressource désignée est la fonction Azure.

1. Au sein de l’application de fonction, passez à l’onglet **Fonctionnalités de la plateforme**. Sélectionnez le lien **Mise en réseau** sous l’en-tête de section *Mise en réseau* pour ouvrir la section État de la fonctionnalité réseau.
2. La page **État de la fonctionnalité réseau** est le point de départ de la configuration d’Azure Front Door, d’Azure CDN et également des restrictions d’accès. Sélectionnez **Configurer des restrictions d’accès** pour configurer l’accès privé aux sites.
3. Dans la page **Restrictions d’accès**, vous voyez uniquement la restriction par défaut en place. La configuration par défaut ne place aucune restriction sur l’accès à l’application de fonction.  Sélectionnez **Ajouter une règle** pour créer une configuration de restriction d’accès privé aux sites.
4. Dans le volet **Ajouter une restriction d’accès**, sélectionnez **Réseau virtuel** dans la zone de liste déroulante **Type**, puis sélectionnez le réseau virtuel et le sous-réseau créés.
5. La page **Restrictions d’accès** montre maintenant qu’il existe une nouvelle restriction. Quelques secondes peuvent être nécessaires pour que l’**État du point de terminaison** passe de `Disabled` à `Provisioning`, puis à `Enabled`.

    >[!IMPORTANT]
    > Chaque application de fonction a un [site Kudu (outil avancé)](../app-service/app-service-ip-restrictions.md#scm-site) permettant de gérer les déploiements d’applications de fonction. Ce site est accessible à partir d’une URL telle que : `<FUNCTION_APP_NAME>.scm.azurewebsites.net`. Étant donné que les restrictions d’accès n’ont pas été activées sur ce site de déploiement, vous pouvez toujours déployer le code de votre projet à partir d’une station de travail de développeur locale ou d’un service de build sans avoir à provisionner un agent dans le réseau virtuel.

## <a name="access-the-functions-app"></a>Accéder à l’application Functions

1. Revenez à l’application de fonction créée.  Dans la section **Présentation**, copiez l’URL.

    >[!div class="mx-imgBorder"]
    >![Obtenir l’URL de l’application de fonction](./media/functions-create-private-site-access/access-function-overview.png)

2. Si vous essayez d’accéder à l’application de fonction maintenant à partir de votre ordinateur en dehors de votre réseau virtuel, vous obtenez une page HTTP 403 indiquant que l’application est arrêtée.  L’application n’est pas arrêtée. La réponse est en fait un état HTTP 403 d’adresse IP interdite.
3. Vous allez maintenant accéder à votre fonction à partir de la machine virtuelle créée, qui est connectée à votre réseau virtuel. Pour accéder au site à partir de la machine virtuelle, vous devez vous connecter à celle-ci via le service Azure Bastion.  Sélectionnez **Se connecter**, puis choisissez **Bastion**.
4. Indiquez le nom d’utilisateur et le mot de passe requis pour vous connecter à la machine virtuelle.  Sélectionnez **Connecter**. Une nouvelle fenêtre de navigateur s’affiche pour vous permettre d’interagir avec la machine virtuelle.
5. Étant donné que cette machine virtuelle accède à la fonction via le réseau virtuel, il est possible d’accéder au site à partir du navigateur web sur la machine virtuelle.  Il est important de noter que même si l’application de fonction n’est accessible qu’à partir du réseau virtuel désigné, une entrée DNS publique est conservée. Comme indiqué ci-dessus, toute tentative d’accès au site génère une réponse HTTP 403.

## <a name="create-a-function"></a>Créer une fonction

L’étape suivante de ce tutoriel consiste à créer une fonction Azure déclenchée par HTTP. L’appel de la fonction via une requête HTTP GET ou POST doit aboutir à une réponse « Hello, {nom} ».  

1. Suivez l’un des guides de démarrage rapide suivants pour créer et déployer votre application Azure Functions.

    * [Visual Studio Code](./functions-create-first-function-vs-code.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Ligne de commande](./functions-create-first-azure-function-azure-cli.md)
    * [Maven (Java)](./functions-create-first-java-maven.md)

2. Lors de la publication de votre projet Azure Functions, choisissez la ressource d’application de fonction que vous avez créée plus tôt dans ce tutoriel.
3. Vérifiez que la fonction est déployée.

    >[!div class="mx-imgBorder"]
    >![Fonction déployée dans la liste des fonctions](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>Appeler la fonction directement

1. Pour tester l’accès à la fonction, vous devez copier l’URL de la fonction. Sélectionnez la fonction déployée, puis sélectionnez **</> Obtenir l’URL de fonction**. Ensuite, cliquez sur le bouton **Copier** pour copier l’URL dans le Presse-papiers.

    >[!div class="mx-imgBorder"]
    >![Copier l’URL de la fonction](./media/functions-create-private-site-access/get-function-url.png)

    > [!NOTE]
    > Quand la fonction s’exécute, une erreur d’exécution s’affiche dans le portail, indiquant que le runtime de fonction ne peut pas démarrer. En dépit du texte du message, l’application de fonction est en cours d’exécution. L’erreur est due aux nouvelles restrictions d’accès, qui empêchent le portail d’effectuer une requête de vérification du runtime.

2. Collez l’URL dans un navigateur web. Quand vous essayez maintenant d’accéder à l’application de fonction à partir d’un ordinateur en dehors de votre réseau virtuel, vous obtenez une réponse HTTP 403 indiquant que l’application est arrêtée.

## <a name="invoke-the-function-from-the-virtual-network"></a>Appeler la fonction à partir du réseau virtuel

L’accès à la fonction via un navigateur web (à l’aide du service Azure Bastion) sur la machine virtuelle configurée sur le réseau virtuel est couronné de succès !

>[!div class="mx-imgBorder"]
>![Accéder à la fonction Azure via Azure Bastion](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Étapes suivantes


> [!div class="nextstepaction"]
> [En savoir plus sur les options de mise en réseau dans Functions](./functions-networking-options.md)
