---
title: Se connecter aux applications 3270 sur des macroordinateurs IBM avec Azure - Azure Logic Apps
description: Intégrer et automatiser des applications basées sur l’écran 3270 avec Azure à l’aide du connecteur Azure Logic Apps et IBM 3270
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: ebf858ba86758b11ee896d745d70bdf2f0d0cde6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57878378"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Intégrer des applications basées sur l’écran 3270 sur des macroordinateurs IBM avec Azure à l’aide du connecteur Azure Logic Apps et IBM 3270

> [!NOTE]
> Ce connecteur est en [ *version préliminaire publique*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Avec Azure Logic Apps et le connecteur IBM 3270, vous pouvez accéder et exécuter des applications de macroordinateur IBM qui vous généralement le lecteur en parcourant les écrans émulateur 3270. De cette façon, vous pouvez intégrer vos applications de macroordinateur IBM avec Azure, Microsoft et autres applications, services et systèmes en créant des flux de travail automatisés avec Azure Logic Apps. Le connecteur communique avec les gros ordinateurs IBM en utilisant le protocole TN3270 et est disponible dans toutes les régions Azure Logic Apps à l’exception d’Azure Government et Azure China 21Vianet. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

Cet article décrit ces aspects de l’aide du connecteur 3270 : 

* Pourquoi utiliser le connecteur IBM 3270 dans Azure Logic Apps et la façon dont le connecteur s’exécute 3270 applications basées sur l’écran

* Les conditions préalables et le programme d’installation pour l’utilisation du connecteur 3270

* Les étapes d’ajout d’actions du connecteur 3270 à votre application logique

## <a name="why-use-this-connector"></a>Pourquoi utiliser ce connecteur ?

Pour accéder aux applications sur des macroordinateurs IBM, vous utilisez généralement un émulateur de terminal 3270, souvent appelé un « écran vert ». Cette méthode est un moyen éprouvé mais a des limitations. Bien que Host Integration Server (HIS) vous permet de que travailler directement avec ces applications, parfois, en séparant l’écran et la logique métier n’est pas possible. Ou bien, peut-être que vous n’avez plus d’informations pour le fonctionnement des applications hôtes.

Pour étendre ces scénarios, le connecteur IBM 3270 dans Azure Logic Apps fonctionne avec l’outil de conception 3270, vous utilisez pour l’enregistrement ou « capture », les écrans de l’hôte utilisés pour une tâche spécifique, définissez le flux de navigation pour cette tâche via votre application de macroordinateur et définir le méthodes avec des paramètres d’entrée et de sortie pour cette tâche. L’outil de conception convertit ces informations dans les métadonnées par le connecteur 3270 lors de l’appel d’une action qui représente cette tâche à partir de votre application logique.

Après avoir généré le fichier de métadonnées à partir de l’outil de conception, vous ajoutez ce fichier à un compte d’intégration dans Azure. De cette façon, votre application logique peut accéder aux métadonnées de votre application lorsque vous ajoutez une action de connecteur 3270. Le connecteur lit le fichier de métadonnées à partir de votre compte d’intégration, gère la navigation à travers les 3270 écrans et dynamiquement présente les paramètres pour l’action de connecteur 3270. Vous pouvez ensuite fournir des données à l’application hôte et le connecteur renvoie les résultats à votre application logique. De cette façon, vous pouvez intégrer vos applications héritées avec Azure, Microsoft et autres applications, services et systèmes Azure Logic Apps prend en charge.

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Recommandé : Un [environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Vous pouvez sélectionner cet environnement en tant que l’emplacement de création et exécution de votre application logique. Une fenêtre ISE permet d’accéder à partir de votre application logique à des ressources qui sont protégés à l’intérieur des réseaux virtuels Azure.

* L’application logique à utiliser pour l’automatisation et l’exécution de votre application contrôlée par l’écran de 3270

  Le connecteur IBM 3270 ne possède aucun déclencheur, utilisez un autre déclencheur pour démarrer votre application logique, telles que la **périodicité** déclencheur. Vous pouvez ensuite ajouter des actions du connecteur 3270. Prise en main, [créer une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Si vous utilisez une fenêtre ISE, sélectionnez ce ISE en tant qu’emplacement de votre application logique.

* [Téléchargez et installez l’outil de conception 3270](https://aka.ms/3270-design-tool-download).
La seule condition préalable est [Microsoft .NET Framework 4.6.1](https://aka.ms/net-framework-download).

  Cet outil vous permet de vous enregistrer les écrans, les chemins de navigation, les méthodes et les paramètres pour les tâches dans votre application que vous ajoutez et exécutez en tant qu’actions du connecteur 3270. L’outil génère un fichier de Host Integration Designer XML un fichier HIDX () qui fournit les métadonnées nécessaires pour le connecteur à utiliser pour la conduite de votre application de macroordinateur.
  
  Après le téléchargement et installation de cet outil, procédez comme suit pour la connexion à votre hôte :

  1. Ouvrez l’outil de conception 3270. À partir de la **Session** menu, sélectionnez **Sessions hôte**.
  
  1. Fournissent des informations sur le serveur de votre hôte TN3270.

* Un [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), qui est l’endroit où vous stockez votre fichier d’un fichier HIDX sous forme de carte pour votre application logique puisse accéder aux définitions de métadonnées et de la méthode dans ce fichier. 

  Assurez-vous que votre compte d’intégration est lié à l’application logique que vous utilisez. En outre, si vous utilisez une fenêtre ISE, veillez à ce que se trouve de votre compte d’intégration l’environnement ISE même qui utilise votre application logique.

* Accès au serveur qui héberge votre application de macroordinateur TN3270

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Créer la vue d’ensemble des métadonnées

Dans une application pilotée par écran 3270, les écrans et les champs de données sont uniques pour vos scénarios, par conséquent, le connecteur 3270 doit ces informations relatives à votre application, vous pouvez fournir en tant que métadonnées. Ces métadonnées décrivent les informations qui vous aide à votre application logique identifier et reconnaître les écrans, explique comment naviguer entre les écrans, où les données d’entrée et l’emplacement d’attendre des résultats. Pour spécifier et générer ces métadonnées, vous utilisez l’outil de conception 3270, qui vous guide à travers ces spécifique *modes*, ou des étapes, comme décrit plus loin dans plus de détails :

* **Capturer**: Dans ce mode, vous enregistrez les écrans requis pour terminer une tâche spécifique avec votre application de macroordinateur, par exemple, l’obtention d’un solde de compte.

* **Navigation**: Dans ce mode, vous spécifiez le plan ou le chemin d’accès pour savoir comment naviguer dans les écrans de votre application de macroordinateur pour la tâche spécifique.

* **Méthodes**: Dans ce mode, vous définissez la méthode, par exemple, `GetBalance`, qui décrit le chemin de navigation d’écran. Vous choisissez également les champs de chaque écran qui deviennent les entrées de la méthode et les paramètres de sortie.

### <a name="unsupported-elements"></a>Éléments non pris en charge

L’outil de conception ne prend pas en charge ces éléments :

* Mappages de prise en charge de mappage de base d’IBM (GBT) partielle : Si vous importez une carte GBT, l’outil de conception ignore les définitions d’écran partielle.
* Paramètres : In/Out Vous ne pouvez pas définir des paramètres In/Out.
* Traitement de menu : Non pris en charge la version préliminaire
* Traitement du tableau : Non pris en charge la version préliminaire

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Captures d’écran

Dans ce mode, vous marquez un élément sur chaque écran 3270 qui identifie de façon unique cet écran. Par exemple, vous pouvez spécifier une ligne de texte ou un ensemble plus complexe de conditions, telles qu’un texte spécifique et un champ non vide. Vous pouvez enregistrer ces écrans sur une connexion active au serveur hôte, ou importer ces informations à partir d’une carte de prise en charge de mappage base IBM (GBT). La connexion active utilise un émulateur TN3270 pour la connexion à l’hôte. Chaque action de connecteur doit correspondre à une seule tâche qui démarre avec la connexion à votre session et se termine avec la déconnexion de votre session.

1. Si vous n’avez pas déjà, ouvrez l’outil de conception 3270. Dans la barre d’outils, choisissez **capturer** afin que vous passez en mode de Capture.

1. Pour démarrer l’enregistrement, appuyez sur la touche F5, ou à partir de la **enregistrement** menu, sélectionnez **démarrer l’enregistrement**. 

1. À partir de la **Session** menu, sélectionnez **Connect**.

1. Dans le **capturer** volet, à partir du premier écran dans votre application, parcourez votre application pour la tâche spécifique que vous enregistrez.

1. Une fois que vous avez terminé la tâche, vous déconnecter de votre application comme vous le faites habituellement.

1. À partir de la **Session** menu, sélectionnez **déconnexion**.

1. Pour arrêter l’enregistrement, appuyez sur la touche MAJ + F5 clés, ou à partir de la **enregistrement** menu, sélectionnez **arrêter l’enregistrement**.

   Une fois que vous capturez les écrans pour une tâche, l’outil concepteur affiche des miniatures qui représentent ces écrans. Quelques remarques concernant ces vignettes :

   * Inclus avec vos écrans capturées, vous avez un écran qui est nommé « Vide ».

     Lorsque vous vous connectez à [CICS](https://www.ibm.com/it-infrastructure/z/cics), vous devez envoyer la clé « Effacer » avant d’entrer le nom de la transaction que vous souhaitez exécuter. L’écran où vous envoyez la clé « Effacer » ne possède aucune *les attributs de reconnaissance*, par exemple un titre de l’écran, vous pouvez ajouter à l’aide de l’éditeur de reconnaissance de l’écran. Pour représenter cet écran, les miniatures inclut un écran nommé « Vide ». Vous pouvez ultérieurement utiliser cet écran pour représenter l’écran où vous entrez le nom de la transaction.

   * Par défaut, le nom d’une capture d’écran utilise le premier mot à l’écran. Si ce nom existe déjà, l’outil de conception ajoute le nom avec un trait de soulignement et un nombre, par exemple, « WBGB » et « WBGB_1 ».

1. Pour donner un nom plus significatif à une capture d’écran, procédez comme suit :

   1. Dans le **hôte écrans** volet, sélectionnez l’écran que vous souhaitez renommer.

   1. Dans le même volet, vers le bas dans le même volet, recherchez le **nom de l’écran** propriété.

   1. Modifier le nom de l’écran actuel à un nom plus descriptif.

1. Maintenant spécifier les champs d’identification de chaque écran.

   Avec le flux de 3270 données, écrans n’ont des identificateurs de valeur par défaut, donc vous devez sélectionner le texte unique à chaque écran. Pour des scénarios complexes, vous pouvez spécifier plusieurs conditions, par exemple, texte unique et un champ avec une condition spécifique.

Une fois que vous avez terminé en sélectionnant les champs de reconnaissance, passer au mode suivant.

### <a name="conditions-for-identifying-repeated-screens"></a>Conditions de l’identification des écrans répétées

Pour le connecteur Parcourir et de faire la distinction entre les écrans, généralement par trouver texte unique sur un écran que vous pouvez utiliser en tant qu’identificateur entre les écrans capturées. Pour les écrans répétées, vous devrez peut-être plusieurs méthodes d’identification. Par exemple, supposons que vous avez deux écrans qui ressemblent à ceci près un seul écran retourne une valeur valide, tandis que l’autre écran renvoie un message d’erreur.

Dans l’outil de conception, vous pouvez ajouter *les attributs de reconnaissance*, par exemple, un titre d’écran tels que « Obtenir compte Balance », à l’aide de l’éditeur de reconnaissance de l’écran. Si vous avez un chemin d’accès comprenant des branches et les deux branches retournent le même écran, mais avec des résultats différents, vous avez besoin d’autres attributs de reconnaissance. Au moment de l’exécution, le connecteur utilise ces attributs pour déterminer la branche actuelle et la duplication. Voici les conditions que vous pouvez utiliser :

* Valeur spécifique : Cette valeur correspond à la chaîne spécifiée à l’emplacement spécifié.
* PAS une valeur spécifique : Cette valeur ne correspond pas à la chaîne spécifiée à l’emplacement spécifié.
* Vide : Ce champ est vide.
* NON vide : Ce champ n’est pas vide.

Pour plus d’informations, consultez le [exemple de plan de navigation](#example-plan) plus loin dans cette rubrique.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Définir des plans de navigation

Dans ce mode, vous définissez le flux ou les étapes pour parcourir les écrans de votre application de macroordinateur pour votre tâche spécifique. Par exemple, dans certains cas, vous pouvez avoir plusieurs chemins d’accès que votre application peut tirer où un seul chemin produit le résultat correct, tandis que le chemin d’accès génère une erreur. Pour chaque écran, spécifiez les séquences de touches nécessaires pour le passage à l’écran suivant, tel que `CICSPROD <enter>`.

> [!TIP]
> Si vous automatisez plusieurs tâches qui utilisent la même connexion et de déconnecter les écrans, l’outil de conception fournit les types de plan connecter et déconnecter spéciales. Lorsque vous définissez ces plans, vous pouvez les ajouter à votre plan de navigation de début et de fin.

### <a name="guidelines-for-plan-definitions"></a>Instructions pour les définitions de plan

* Inclure tous les écrans, en commençant à la connexion et se terminant par la déconnexion.

* Vous pouvez créer un plan autonome ou utiliser les plans de connecter et déconnecter, qui vous permettent de réutiliser une série d’écrans communs à toutes vos opérations.

  * Le dernier écran dans votre plan de connexion doit être le même écran en tant que le premier écran dans votre plan de navigation.

  * Le premier écran dans votre plan de déconnexion doit être le même écran en tant que le dernier écran dans votre plan de navigation.

* Vos écrans capturées peuvent contenir de nombreux écrans répétées, afin de le sélectionner et utiliser qu’une seule instance des écrans répétées dans votre plan. Voici quelques exemples d’écrans répétées :

  * L’écran de connexion dans, par exemple, le **MSG-10** écran
  * L’écran d’accueil pour CICS
  * « Clair » ou **vide** écran

<a name="create-plans"></a>

### <a name="create-plans"></a>Créer des plans

1. Dans la barre d’outils de l’outil de conception 3270, choisissez **Navigation** afin que vous passez en mode de Navigation.

1. Pour démarrer votre plan, dans le **Navigation** volet, choisissez **nouveau Plan**.

1. Sous **choisir un nouveau nom de Plan**, entrez un nom pour votre plan. À partir de la **Type** , sélectionnez le type de plan :

   | Type de plan | Description |
   |-----------|-------------|
   | **Processus** | Pour autonome ou combiné des plans |
   | **Connexion** | Pour les plans de connexion |
   | **Se déconnecter** | Pour les plans de déconnexion |
   |||

1. À partir de la **hôte écrans** volet, faites glisser les miniatures capturées au plan de navigation de surface dans le **Navigation** volet.

   Pour représenter l’écran vide où vous entrez le nom de la transaction, utilisez l’écran « Vide ».

1. Réorganisez les écrans dans l’ordre qui décrit la tâche que vous définissez.

1. Pour définir le chemin d’accès de flux entre les écrans, y compris les branches et les jointures, sur la barre d’outils de l’outil de conception, choisissez **flux**.

1. Choisissez le premier écran dans le flux. Faites glisser et dessiner une connexion à l’écran suivant dans le flux.

1. Pour chaque écran, indiquez les valeurs pour le **clé d’aide** propriété (Attention identificateur) et pour le **texte fixe** propriété, qui déplace le flux à l’écran suivant.

   Vous pouvez avoir uniquement la clé d’aide, ou à la fois la clé d’aide et texte fixe.

Une fois que vous avez terminé votre plan de navigation, vous pouvez [définir des méthodes dans le mode suivant](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Exemples

Dans cet exemple, supposons que vous exécutez une transaction CICS nommée « WBGB » qui a comme suit : 

* Dans le premier écran, vous entrez un nom et un compte.
* Sur le deuxième écran, vous obtenez le solde du compte.
* Vous quittez l’écran « Vide ».
* Vous vous déconnectez de CICS à l’écran « MSG-10 ».

Supposons également que vous répétez ces étapes, mais que vous entrez des données incorrectes, vous pouvez capturer l’écran qui affiche l’erreur. Voici les écrans que vous capturez :

* MSG-10
* Bienvenue dans CICS
* Vide
* WBGB_1 (entrée)
* WBGB_2 (erreur)
* Empty_1
* MSG-10_1

Bien que de nombreux écrans ici obtenir des noms uniques, certains écrans sont le même écran, par exemple, « MSG-10 » et « Vide ». Pour un écran répété, n'utiliser qu’une seule instance pour cet écran dans votre plan. Voici des exemples qui illustrent la façon dont un plan autonome, plan de Connect, Disconnect plan et un plan combiné peut se présenter :

* Plan autonome

  ![Plan de navigation autonome](./media/connectors-create-api-3270/standalone-plan.png)

* Connecter un plan

  ![Connecter un plan](./media/connectors-create-api-3270/connect-plan.png)

* Déconnecter le plan

  ![Déconnecter le plan](./media/connectors-create-api-3270/disconnect-plan.png)

* Plan combiné

  ![Plan combiné](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Exemple : Identifier les écrans répétées

Pour le connecteur Parcourir et de différencier les écrans, généralement par trouver texte unique sur un écran que vous pouvez utiliser en tant qu’identificateur entre les écrans capturées. Pour les écrans répétées, vous devrez peut-être plusieurs méthodes d’identification. L’exemple de plan a un branchement où vous pouvez obtenir des écrans ressemblent. Un seul écran retourne un solde de compte, tandis que l’autre écran renvoie un message d’erreur.

L’outil de conception vous permet d’ajouter des attributs de reconnaissance, par exemple, un titre de l’écran « Obtenir compte Balance », à l’aide de la reconnaissance de l’écran Éditeur nommé. Dans le cas des écrans similaires, vous avez besoin d’autres attributs. Au moment de l’exécution, le connecteur utilise ces attributs pour déterminer la branche et le branchement.

* Dans la branche qui retourne une entrée valide, qui est l’écran avec le solde du compte, vous pouvez ajouter un champ qui a une condition « non vide ».

* Dans la branche qui retourne une erreur, vous pouvez ajouter un champ qui a une condition « vide ».

<a name="define-method"></a>

## <a name="define-methods"></a>Définir des méthodes

Dans ce mode, vous définissez une méthode qui est associé à votre plan de navigation. Pour chaque paramètre de méthode, vous spécifiez le type de données, tels que chaîne, entier, date ou heure et ainsi de suite. Lorsque vous avez terminé, vous pouvez tester votre méthode sur l’ordinateur hôte en direct et vérifiez que la méthode fonctionne comme prévu. Puis, vous générez le fichier de métadonnées ou le fichier Host Integration Designer XML un fichier HIDX (), qui comporte à présent les définitions de méthode à utiliser pour la création et exécution d’une action pour le connecteur IBM 3270.

1. Dans la barre d’outils de l’outil de conception 3270, choisissez **méthodes** afin que vous passez en mode de méthodes. 

1. Dans le **Navigation** volet, sélectionnez l’écran qui a les champs d’entrée.

1. Pour ajouter le premier paramètre d’entrée pour votre méthode, procédez comme suit :

   1. Dans le **capturer** volet, sur l’écran de l’émulateur 3270, choisissez le champ entier, pas uniquement le texte dans le champ, que vous souhaitez que la première entrée.

      > [!TIP]
      > Pour afficher tous les champs et de vous assurer que vous sélectionnez le champ terminé, sur le **vue** menu, sélectionnez **tous les champs**.

   1. Dans la barre d’outils de l’outil de conception, choisissez **champ d’entrée**. 

   Pour ajouter plus de paramètres d’entrée, répétez les étapes précédentes pour chaque paramètre.

1. Pour ajouter le premier paramètre de sortie pour votre méthode, procédez comme suit :

   1. Dans le **capturer** volet, sur l’écran de l’émulateur 3270, choisissez le champ entier, pas uniquement le texte dans le champ, que vous souhaitez que la première sortie.

      > [!TIP]
      > Pour afficher tous les champs et de vous assurer que vous sélectionnez le champ terminé, sur le **vue** menu, sélectionnez **tous les champs**.

   1. Dans la barre d’outils de l’outil de conception, choisissez **champ de sortie**.

   Pour ajouter des paramètres de sortie, répétez les étapes précédentes pour chaque paramètre.

1. Après avoir ajouté tous vos paramètres de la méthode, définissez ces propriétés pour chaque paramètre :

   | Nom de la propriété | Valeurs possibles | 
   |---------------|-----------------|
   | **Type de données** | Byte, Date heure, Decimal, Int, Long, Short, String |
   | **Technique de remplissage de champ** | Paramètres prennent en charge ces types de remplissage, remplir avec des espaces à droite si nécessaire : <p><p>- **Type**: Entrez les caractères séquentiellement dans le champ. <p>- **Remplir**: Remplacez le contenu du champ avec des caractères, remplir avec des espaces à droite si nécessaire. <p>- **EraseEofType**: Effacez le champ, puis tapez les caractères de manière séquentielle dans le champ. |
   | **Chaîne de format** | Certains types de données de paramètre utilisent une chaîne de format, qui informe le connecteur 3270 comment convertir du texte à partir de l’écran en un type de données .NET : <p><p>- **Date/heure**: La chaîne de format de date/heure suit le [.NET date et heure personnalisées des chaînes de format](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Par exemple, la date `06/30/2019` utilise la chaîne de format `MM/dd/yyyy`. <p>- **Décimal**: Utilise la chaîne de format décimal le [clause COBOL Picture](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Par exemple, le nombre `100.35` utilise la chaîne de format `999V99`. |
   |||

## <a name="save-and-view-metadata"></a>Enregistrer et afficher les métadonnées

Après avoir défini votre méthode, mais avant de tester votre méthode, enregistrez toutes les informations que vous avez définies jusqu'à présent dans un fichier RAP (.rap).
Vous pouvez enregistrer ce fichier RAP à tout moment au cours de n’importe quel mode. L’outil de conception inclut également un exemple de fichier de stratégie que vous pouvez ouvrir et consulter en accédant au dossier d’installation de l’outil de conception à cet emplacement et l’ouverture du fichier « WoodgroveBank.rap » :

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Toutefois, si vous essayez d’enregistrer les modifications apportées à l’exemple de fichier RAP ou de la génération d’un fichier d’un fichier HIDX à partir de l’exemple de fichier RAP tandis que le fichier reste dans le dossier d’installation de l’outil de conception, vous pouvez obtenir une erreur « accès refusé ». Par défaut, l’outil de conception installé dans votre dossier Program Files sans autorisations élevées. Si vous obtenez une erreur, essayez l’une de ces solutions :

* Copiez l’exemple de fichier vers un autre emplacement.
* Exécutez l’outil de conception en tant qu’administrateur.
* Vérifiez vous-même le propriétaire du dossier du Kit de développement logiciel.

## <a name="test-your-method"></a>Votre méthode de test

1. Pour exécuter votre méthode sur l’hôte en direct, tout en conservant en mode de méthodes, appuyez sur la touche F5 ou à partir de la barre d’outils de l’outil de conception, choisissez **exécuter**.

   > [!TIP]
   > Vous pouvez modifier les modes à tout moment. Sur le **fichier** menu, sélectionnez **Mode**, puis sélectionnez le mode souhaité.

1. Entrez les valeurs de vos paramètres, puis choisissez **OK**.

1. Pour continuer à l’écran suivant, choisissez **suivant**.

1. Lorsque vous avez terminé, choisissez **fait**, qui affiche vos valeurs de paramètre de sortie.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Générer et charger un fichier HIDX fichier

Lorsque vous êtes prêt, générer le fichier d’un fichier HIDX pour pouvoir charger à votre compte d’intégration. L’outil de conception 3270 crée le fichier d’un fichier HIDX dans un nouveau sous-dossier où vous avez enregistré votre fichier RAP.

1. Dans la barre d’outils de l’outil de conception 3270, choisissez **générer le Code**.

1. Accédez au dossier qui contient votre fichier RAP, puis ouvrez le sous-dossier créé par l’outil après avoir généré votre fichier d’un fichier HIDX. Vérifiez que l’outil de création du fichier d’un fichier HIDX.

1. Se connecter à la [Azure portal](https://portal.azure.com)et recherchez votre compte d’intégration.

1. Ajouter votre fichier d’un fichier HIDX sous forme de carte à votre compte d’intégration par [suivez ces étapes pour ajouter des mappages similaires](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), mais lorsque vous sélectionnez le type de carte, sélectionnez **un fichier HIDX**.

Plus loin dans cette rubrique, lorsque vous ajoutez une action IBM 3270 à votre application logique pour la première fois, vous êtes invité à créer une connexion entre votre application logique et le serveur hôte en fournissant des informations de connexion, tels que les noms de votre intégration compte et le serveur hôte . Après avoir créé la connexion, vous pouvez sélectionner votre fichier d’un fichier HIDX précédemment ajouté, la méthode à exécuter et les paramètres à utiliser.

Lorsque vous avez terminé toutes ces étapes, vous pouvez utiliser l’action que vous créez dans votre application logique pour la connexion à votre macroordinateur IBM, d’écrans de lecteur pour votre application, entrer des données, retourner des résultats et ainsi de suite. Vous pouvez également continuer l’ajout d’autres actions à votre application logique pour l’intégration avec d’autres applications, les services et les systèmes.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Exécuter des actions d’IBM 3270

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Sous la dernière étape où vous souhaitez ajouter une action, choisissez **nouvelle étape**, puis sélectionnez **ajouter une action**. 

1. Sous la zone de recherche, choisissez **Enterprise**. Dans la zone de recherche, entrez « 3270 » comme filtre. Dans la liste des actions, sélectionnez cette action : **Exécute un programme de macroordinateur via une connexion de TN3270**

   ![Sélectionnez l’action de 3270](./media/connectors-create-api-3270/select-3270-action.png)

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. 
   Cliquez sur le signe plus (**+**) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Si aucune connexion n’existe encore, fournissez les informations nécessaires pour votre connexion, puis choisissez **créer**.

   | Propriété | Obligatoire | Value | Description |
   |----------|----------|-------|-------------|
   | **Nom de connexion** | Oui | <*connection-name*> | Nom de votre connexion |
   | **ID de compte d’intégration** | Oui | <*integration-account-name*> | Nom de votre compte d’intégration |
   | **URL SAS compte d’intégration** | Oui | <*integration-account-SAS-URL*> | URL de Signature d’accès partagé (SAS) de votre compte d’intégration, que vous pouvez générer à partir des paramètres de votre compte d’intégration dans le portail Azure. <p>1. Sur l’intégration de votre compte de menu, sous **paramètres**, sélectionnez **URL de rappel**. <br>2. Dans le volet de droite, copiez la **généré les URL de rappel** valeur. |
   | **Serveur** | Oui | <*TN3270-server-name*> | Le nom du serveur pour votre service TN3270 |
   | **Port** | Non  | <*TN3270-server-port*> | Le port utilisé par votre serveur TN3270. Si ce champ est vide, le connecteur utilise `23` comme valeur par défaut. |
   | **Type d’appareil** | Non  | <*IBM-terminal-model*> | Le nom du modèle ou le numéro pour le terminal IBM pour émuler. Si ce champ est vide, le connecteur utilise les valeurs par défaut. |
   | **Page de codes** | Non  | <*code-page-number*> | Le numéro de page de code pour l’hôte. Si ce champ est vide, le connecteur utilise `37` comme valeur par défaut. |
   | **Nom d’unité logique** | Non  | <*logical-unit-name*> | Le nom d’unité logique spécifique pour demander à partir de l’hôte |
   | **Activer le protocole SSL ?** | Non  | Activé ou désactivé | Activer ou désactiver le chiffrement SSL. |
   | **Valider le certificat ssl de l’hôte ?** | Non  | Activé ou désactivé | Activer ou désactiver la validation pour le certificat du serveur. |
   ||||

   Par exemple : 

   ![Propriétés de connexion](./media/connectors-create-api-3270/connection-properties.png)

1. Fournissez les informations nécessaires pour l’action :

   | Propriété | Obligatoire | Value | Description |
   |----------|----------|-------|-------------|
   | **Nom d’un fichier Hidx** | Oui | <*HIDX-file-name*> | Sélectionnez le fichier d’un fichier HIDX 3270 que vous souhaitez utiliser. |
   | **Nom de la méthode** | Oui | <*method-name*> | Sélectionnez la méthode dans le fichier d’un fichier HIDX que vous souhaitez utiliser. Une fois que vous sélectionnez une méthode, le **ajouter un nouveau paramètre** liste s’affiche afin que vous pouvez sélectionner les paramètres à utiliser avec cette méthode. |
   ||||

   Par exemple : 

   **Sélectionnez le fichier d’un fichier HIDX**

   ![Sélectionnez un fichier HIDX fichier](./media/connectors-create-api-3270/select-hidx-file.png)

   **Sélectionnez la méthode**

   ![Sélectionner la méthode](./media/connectors-create-api-3270/select-method.png)

   **Sélectionnez les paramètres**

   ![Sélectionnez les paramètres](./media/connectors-create-api-3270/add-parameters.png)

1. Lorsque vous avez terminé, enregistrez et exécutez votre application logique.

   Après votre logique d’application s’est terminée, les étapes à partir de l’exécution apparaissent. 
   Étapes réussies montrent des coches, alors que les étapes ayant échoués montrent la lettre « X ».

1. Pour passer en revue les entrées et sorties de chaque étape, développez cette étape.

1. Pour passer en revue les sorties, choisissez **afficher les sorties brutes**.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, actions et limite, qui sont décrits par OpenAPI du connecteur (anciennement Swagger) description, passez en revue la page de référence du connecteur : [ https://docs.microsoft.com/connectors/<replace-with-api-topic-file-name> ](/connectors/).

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
