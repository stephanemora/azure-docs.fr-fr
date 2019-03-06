---
title: 'Tutoriel : Simuler un échec d’accès au stockage redondant avec accès en lecture dans Azure | Microsoft Docs'
description: Simuler une erreur d’accès au stockage géoredondant avec accès en lecture
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.openlocfilehash: 0cbb4d2bc6449dc1cf12a374085b429743224995
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872877"
---
# <a name="tutorial-simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Tutoriel : Simuler un échec d’accès au stockage redondant avec accès en lecture

Ce tutoriel est le deuxième d’une série. Il vous permet de découvrir les avantages d’un [stockage géographiquement redondant avec accès en lecture](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) en simulant une défaillance.

Pour simuler une défaillance, vous pouvez utiliser [Fiddler](#simulate-a-failure-with-fiddler) ou un [routage statique](#simulate-a-failure-with-an-invalid-static-route). Les deux méthodes vous permettent de simuler l’échec des demandes d’accès au point de terminaison principal de votre compte de stockage [géographiquement redondant avec accès en lecture](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS), pour que l’application lise plutôt le point de terminaison secondaire.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

Dans ce deuxième volet, vous apprenez à :

> [!div class="checklist"]
> * Exécuter et interrompre l’application
> * Simuler une défaillance avec [Fiddler](#simulate-a-failure-with-fiddler) ou [un itinéraire statique non valide](#simulate-a-failure-with-an-invalid-static-route) 
> * Simuler la restauration du point de terminaison principal

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel, suivez le tutoriel précédent : [Rendre vos données d’application hautement disponibles avec Stockage Azure][previous-tutorial].

Pour simuler une défaillance à l’aide de Fiddler : 

* Télécharger et [installer Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-fiddler"></a>Simuler une défaillance à l’aide de Fiddler

Pour simuler une défaillance avec Fiddler, vous injectez un échec de réponse vis-à-vis des demandes d’accès au point de terminaison principal de votre compte de stockage RA-GRS.

Les sections suivantes décrivent la manière de simuler une défaillance ainsi que la restauration du point de terminaison principal avec Fiddler.

### <a name="launch-fiddler"></a>Lancer Fiddler

Ouvrez Fiddler, sélectionnez **Rules** (Règles) et **Customize Rules** (Personnaliser les règles).

![Personnaliser les règles de Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Fiddler ScriptEditor se lance et affiche le fichier **SampleRules.js**. Ce fichier sert à personnaliser Fiddler.

Collez l’exemple de code suivant dans la fonction `OnBeforeResponse`. Le nouveau code est commenté pour que la logique qu’il crée ne soit pas implémentée immédiatement.

Une fois l’opération effectuée, sélectionnez **File** (Fichier) et **Save** (Enregistrer) pour enregistrer les changements apportés.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![Coller la règle personnalisée](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="interrupting-the-application"></a>Interruption de l’application

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python et Java v7](#tab/dotnet-python-java-v7)

Exécutez l’application dans votre environnement de développement intégré ou interpréteur de commandes.

Une fois que l’application a commencé sa lecture du point de terminaison principal, appuyez sur **une touche** dans la fenêtre de console pour mettre en pause l’application.

![Application de scénario](media/storage-simulate-failure-ragrs-account-app/scenario.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Exécutez l’application dans votre environnement de développement intégré ou interpréteur de commandes.

Étant donné que vous contrôlez l’exemple, vous n’avez pas besoin de l’interrompre pour simuler une défaillance. Vérifiez simplement que le fichier a été chargé dans votre compte de stockage en exécutant l’exemple et en entrant **P**.

![Application de scénario](media/storage-simulate-failure-ragrs-account-app/Java-put-list-output.png)

---

### <a name="simulate-failure"></a>Simuler une défaillance

Pendant que l’application est interrompue, décommentez la règle personnalisée que nous avons enregistrée précédemment dans Fiddler.

Cet exemple de code recherche les demandes d’accès au compte de stockage RA-GRS. Si le chemin contient le nom du fichier `HelloWorld`, il retourne le code de réponse `503 - Service Unavailable`.

Accédez à Fiddler, puis sélectionnez **Rules** (Règles) -> **Customize Rules** (Personnaliser les règles).

Décommentez les lignes suivantes, remplacez `STORAGEACCOUNTNAME` par le nom de votre compte de stockage. Sélectionnez **File** (Fichier) -> **Save** (Enregistrer) pour enregistrer les changements apportés. 

> [!NOTE]
> Si vous exécutez l’exemple d’application sur Linux, vous devez redémarrer Fiddler chaque fois que vous modifiez le fichier **CustomRule.js** afin que Fiddler puisse installer la logique personnalisée.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python et Java v7](#tab/dotnet-python-java-v7)

Pour permettre à l’application de reprendre son exécution, appuyez sur **une touche**.

Une fois que l’application a repris son exécution, les demandes d’accès au point de terminaison principal n’aboutissent plus. L’application tente de se reconnecter au point de terminaison principal à 5 reprises. Après cinq tentatives non réussies, elle demande l’image au point de terminaison secondaire en lecture seule. Une fois que l’application a réussi à récupérer l’image à 20 reprises à partir du point de terminaison secondaire, elle tente de se connecter au point de terminaison principal. Si le point de terminaison principal est toujours inaccessible, l’application reprend sa lecture du point de terminaison secondaire.

Ce modèle correspond au modèle [Disjoncteur](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) décrit dans le didacticiel précédent.

![Coller la règle personnalisée](media/storage-simulate-failure-ragrs-account-app/figure3.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Maintenant que vous avez introduit la défaillance, entrez **G** pour la tester.

Vous êtes informé que le pipeline secondaire est utilisé au lieu du pipeline principal.

---

### <a name="simulate-primary-endpoint-restoration"></a>Simuler la restauration du point de terminaison principal

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python et Java v7](#tab/dotnet-python-java-v7)

Une fois que vous avez défini la règle personnalisée Fiddler à l’étape précédente, les demandes d’accès au point de terminaison principal cessent d’aboutir.

Pour simuler le retour à un fonctionnement normal du point de terminaison principal, supprimez la logique d’injection de l’erreur `503`.

Pour interrompre l’application, appuyez sur **une touche**.

Accédez à Fiddler, puis sélectionnez **Rules** (Règles) et **Customize Rules** (Personnaliser les règles). 

Commentez ou supprimez la logique personnalisée dans la fonction `OnBeforeResponse`, en laissant la fonction par défaut.

Sélectionnez **File** (Fichier) et **Save** (Enregistrer) pour enregistrer les changements apportés.

![Supprimer la règle personnalisée](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Une fois l’opération effectuée, appuyez sur **une touche** pour permettre à l’application de reprendre son exécution. L’application poursuit sa lecture du point de terminaison principal jusqu’à ce qu’elle atteigne 999 lectures.

![Reprendre l’exécution de l’application](media/storage-simulate-failure-ragrs-account-app/figure4.png)

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Une fois que vous avez défini la règle personnalisée Fiddler à l’étape précédente, les demandes d’accès au point de terminaison principal cessent d’aboutir.

Pour simuler le retour à un fonctionnement normal du point de terminaison principal, supprimez la logique d’injection de l’erreur `503`.

Accédez à Fiddler, puis sélectionnez **Rules** (Règles) et **Customize Rules** (Personnaliser les règles).  Commentez ou supprimez la logique personnalisée dans la fonction `OnBeforeResponse`, en laissant la fonction par défaut.

Sélectionnez **File** (Fichier) et **Save** (Enregistrer) pour enregistrer les changements apportés.

Lorsque vous avez terminé, entrez **G** pour tester le téléchargement. L’application signale qu’elle réutilise maintenant le pipeline principal.

---

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simuler une défaillance avec un itinéraire statique non valide

Vous pouvez créer un itinéraire statique non valide pour toutes les demandes d’accès au point de terminaison principal de votre compte de stockage RA-GRS [(géoredondant avec accès en lecture)](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage). Dans ce didacticiel, l’hôte local sert de passerelle pour acheminer les demandes de routage vers le compte de stockage. Le recours à l’hôte local en tant que passerelle amène toutes les demandes d’accès au point de terminaison principal de votre compte de stockage à effectuer une boucle dans l’hôte, ce qui engendre une défaillance. Suivez les étapes ci-après pour simuler une défaillance ainsi que la restauration du point de terminaison principal avec un itinéraire statique non valide. 

### <a name="start-and-pause-the-application"></a>Démarrer et interrompre l’application

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python et Java v7](#tab/dotnet-python-java-v7)

Exécutez l’application dans votre environnement de développement intégré ou interpréteur de commandes. Une fois que l’application a commencé sa lecture du point de terminaison principal, appuyez sur **une touche** dans la fenêtre de console pour mettre en pause l’application.

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Étant donné que vous contrôlez l’exemple, vous n’avez pas besoin de l’interrompre pour tester une défaillance.

Vérifiez que le fichier a été chargé dans votre compte de stockage en exécutant l’exemple et en entrant **P**.

---

### <a name="simulate-failure"></a>Simuler une défaillance

Une fois l’application suspendue, démarrez l’invite de commandes sur Windows en tant qu’administrateur ou exécutez le terminal en tant qu’utilisateur racine sur Linux.

Pour obtenir plus d’informations sur le domaine du point de terminaison principal du compte de stockage, entrez la commande suivante dans une invite de commandes ou un terminal.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 Remplacez `STORAGEACCOUNTNAME` par le nom de votre compte de stockage. Copiez l’adresse IP de votre compte de stockage dans un éditeur de texte pour une utilisation ultérieure.

Pour obtenir l’adresse IP de l’hôte local, entrez `ipconfig` dans l’invite de commandes Windows ou `ifconfig` dans le terminal Linux. 

Pour ajouter un itinéraire statique relatif à un hôte de destination, entrez la commande suivante dans une invite de commandes Windows ou un terminal Linux. 

#### <a name="linux"></a>Linux

`route add <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a> Windows

`route add <destination_ip> <gateway_ip>`

Remplacez `<destination_ip>` par l’adresse IP du compte de stockage, et `<gateway_ip>` par l’adresse IP de l’hôte local.

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python et Java v7](#tab/dotnet-python-java-v7)

Pour permettre à l’application de reprendre son exécution, appuyez sur **une touche**.

Une fois que l’application a repris son exécution, les demandes d’accès au point de terminaison principal n’aboutissent plus. L’application tente de se reconnecter au point de terminaison principal à cinq reprises. Après cinq tentatives non réussies, elle demande l’image au point de terminaison secondaire en lecture seule. Une fois que l’application a réussi à récupérer l’image à 20 reprises à partir du point de terminaison secondaire, elle tente de se connecter au point de terminaison principal. Si le point de terminaison principal est toujours inaccessible, l’application reprend sa lecture du point de terminaison secondaire. Ce modèle correspond au modèle [Disjoncteur](/azure/architecture/patterns/circuit-breaker) décrit dans le didacticiel précédent.

# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Maintenant que vous avez introduit la défaillance, entrez **G** pour la tester. Vous êtes informé que le pipeline secondaire est utilisé au lieu du pipeline principal.

---

### <a name="simulate-primary-endpoint-restoration"></a>Simuler la restauration du point de terminaison principal

Pour simuler la reprise du fonctionnement du point de terminaison principal, supprimez l’itinéraire statique du point de terminaison principal à partir de la table de routage. Ainsi, toutes les demandes d’accès au point de terminaison principal peuvent être acheminées au moyen de la passerelle par défaut.

Pour supprimer l’itinéraire statique d’un hôte de destination, le compte de stockage, entrez la commande suivante dans une invite de commandes Windows ou un terminal Linux.

#### <a name="linux"></a>Linux

`route del <destination_ip> gw <gateway_ip>`

#### <a name="windows"></a> Windows

`route delete <destination_ip>`

# <a name="net-python-and-java-v7tabdotnet-python-java-v7"></a>[.NET, Python et Java v7](#tab/dotnet-python-java-v7)

Pour permettre à l’application de reprendre son exécution, appuyez sur **une touche**. L’application poursuit sa lecture du point de terminaison principal jusqu’à ce qu’elle atteigne 999 lectures.

![Reprendre l’exécution de l’application](media/storage-simulate-failure-ragrs-account-app/figure4.png)


# <a name="java-v10tabjava-v10"></a>[Java v10](#tab/Java-v10)

Entrez **G** pour tester le téléchargement. L’application signale qu’elle réutilise maintenant le pipeline principal.

![Reprendre l’exécution de l’application](media/storage-simulate-failure-ragrs-account-app/java-get-pipeline-example-v10.png)

---

## <a name="next-steps"></a>Étapes suivantes

Dans ce deuxième volet, vous avez appris à simuler une défaillance pour tester le stockage géoredondant avec accès en lecture.

Pour découvrir comment fonctionne le stockage géographiquement redondant avec accès en lecture (RA-GRS) ainsi que les risques qui y sont liés, lisez l’article suivant :

> [!div class="nextstepaction"]
> [Conception d’applications à haute disponibilité à l’aide de RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
