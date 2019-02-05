---
title: Copier des données sur votre Microsoft Azure Data Box par le biais de SMB| Microsoft Docs
description: Découvrez comment copier des données sur votre Azure Data Box par le biais du service de copie de données.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: alkohli
ms.openlocfilehash: 9d271642a432d8a149fbe468087a0598c91e7c36
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902377"
---
# <a name="tutorial-use-data-copy-service-to-directly-ingest-data-into-azure-data-box-preview"></a>Didacticiel : Utiliser le service de copie de données pour ingérer directement des données dans Azure Data Box (préversion)

Ce tutoriel explique comment ingérer des données par l’intermédiaire du service de copie de données, sans avoir besoin d’hôte intermédiaire. Ce service s’exécute localement sur l’instance Data Box, il se connecte à votre appareil NAS via SMB et copie les données sur Data Box.

Utilisez le service de copie de données :

- Dans les environnements NAS (Network-Attached Storage) où les hôtes intermédiaires ne sont pas disponibles.
- Avec de petits fichiers dont l’ingestion et le chargement de données prend des semaines. Ce service améliore considérablement le temps d’ingestion et de chargement.

Ce tutoriel vous fournira des informations sur :

> [!div class="checklist"]
> * Prérequis
> * Copier des données sur Data Box


## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que :

1. Vous avez terminé le [Tutoriel : Configurer Azure Data Box](data-box-deploy-set-up.md).
2. Vous avez reçu votre Data Box et que l’état de la commande dans le portail est **Remis**.
3. Vous disposez des informations d’identification de l’appareil NAS source auquel vous vous connectez pour la copie des données.
4. Vous êtes connecté à un réseau haut débit. Nous vous recommandons vivement d’utiliser au minimum une connexion 10 GbE. Si vous ne disposez pas d’une connexion 10 GbE, utilisez une liaison de données 1 GbE. Cependant, les vitesses de copie seront affectées.

## <a name="copy-data-to-data-box"></a>Copier des données sur Data Box

Une fois que vous êtes connecté au NAS, l’étape suivante consiste à copier les données. Avant de commencer la copie des données, passez en revue les considérations suivantes :

- Quand vous copiez des données, vérifiez que leur taille est conforme aux limites de taille spécifiées dans l’article [Limitations relatives au Stockage Azure et à Data Box](data-box-limits.md).
- Si les données chargées par Data Box le sont aussi simultanément par d’autres applications en dehors de Data Box, l’échec du chargement ou l’altération de données peuvent en découler.
- Si les données sont en cours d’évolution au moment où elles sont lues par le service de copie de données, vous pouvez constater des échecs ou une altération des données.

Pour copier des données par l’intermédiaire du service de copie de données, vous devez créer une tâche. Suivez ces étapes lorsque vous voulez créer une tâche de copie de données.

1. Dans l’interface utilisateur web locale de votre Data Box, accédez à **Gérer > Copier des données**.
2. Dans la page **Copier des données**, cliquez sur **Créer**.

    ![Cliquez sur **Créer** dans la page **Copier des données**](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. Dans la boîte de dialogue **Configurer et démarrer**, renseignez les entrées suivantes.
    
    |Champ                          |Valeur    |
    |-------------------------------|---------|
    |Nom du travail                       |Nom unique contenant moins de 230 caractères pour le travail. Ces caractères ne sont pas autorisés dans le nom d’un travail - \<, \>, \|, \?, \*, \\, \:, \/ et \\\.         |
    |Emplacement source                |Indiquez le chemin SMB vers la source de données au format : `\\<ServerIPAddress>\<ShareName>` ou `\\<ServerName>\<ShareName>`.        |
    |Nom d’utilisateur                       |Nom d’utilisateur au format `\\<DomainName><UserName>` pour accéder à la source de données.        |
    |Mot de passe                       |Mot de passe pour accéder à la source de données.           |
    |Compte de stockage de destination    |Sélectionnez le compte de stockage cible sur lequel charger les données à partir de la liste déroulante.         |
    |Type de stockage de destination       |Sélectionnez le type de stockage cible à partir d’un objet blob de blocs, d’un objet blob de pages ou d’Azure Files.        |
    |Conteneur/partage de destination    |Entrez le nom du conteneur ou du partage afin de charger les données dans votre compte de stockage de destination. Le nom peut être un nom de partage ou un nom de conteneur. Par exemple, `myshare` ou `mycontainer`. Vous pouvez également indiquer ces informations au format `sharename\directory_name` ou `containername\virtual_directory_name` dans le cloud.        |
    |Copier les fichiers correspondant à un modèle    | Entrez le modèle de correspondance de nom de fichier des deux manières suivantes.<ul><li>**Utiliser des expressions génériques**. Seuls `*` et `?` sont pris en charge dans les expressions génériques. Par exemple, cette expression `*.vhd` correspond à tous les fichiers dotés de l’extension .vhd. De la même façon, `*.dl?` correspond à tous les fichiers dont l’extension est soit `.dl`, soit `.dll`. Quant à `*foo`, il correspond à tous les fichiers dont les noms se terminent par `foo`.<br>Vous pouvez entrer l’expression générique directement dans le champ. Par défaut, la valeur entrée dans le champ est traitée comme expression générique.</li><li>**Utiliser des expressions régulières**. Les expressions régulières POSIX sont prises en charge. Par exemple, une expression régulière `.*\.vhd` correspond à tous les fichiers dotés de l’extension `.vhd`. Pour une expression régulière, fournissez l’élément `<pattern>` directement en tant que `regex(<pattern>)`. <li>Pour plus d’informations concernant les expressions régulières, consultez [Langage des expressions régulières - Aide-mémoire](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |Optimisation de fichier              |Quand cette option est activée, les fichiers de moins de 1 Mo sont compressés à l’ingestion. La copie des petits fichiers est ainsi accélérée. Des gains de temps significatifs sont constatés quand le nombre de fichiers dépasse largement le nombre de répertoires.        |
 
4. Cliquez sur **Start**. Les entrées sont validées, et si la validation aboutit, une tâche démarre. Le démarrage de la tâche peut prendre quelques minutes.

    ![Démarrage d’une tâche à partir de la boîte de dialogue Configurer le travail et démarrer](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Une tâche est créée avec les paramètres spécifiés. Activez la case à cocher pour pouvoir ensuite suspendre et reprendre, annuler ou redémarrer une tâche.

    ![Gérer une tâche avec la page Copier des données](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Vous pouvez suspendre cette tâche si elle impacte sur les ressources NAS pendant les heures de pointe.

        ![Suspension d’une tâche](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Vous pouvez reprendre la tâche plus tard en dehors des heures de pointe.

        ![Reprise d’une tâche](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Vous pouvez annuler une tâche à tout moment.

        ![Annulation d’une tâche](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png) Une confirmation est demandée lorsque vous annulez une tâche.

        ![Confirmation de l’annulation d’une tâche](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Si vous décidez d’annuler une tâche, les données déjà copiées ne sont pas supprimées. Pour supprimer toutes les données que vous avez copiées sur votre Data Box, réinitialisez l’appareil.

        ![Réinitialisation de l’appareil](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Si vous annulez ou suspendez une tâche, les fichiers volumineux qui sont en cours de copie peuvent être laissés à moitié copiés. Ces fichiers sont chargés dans le même état sur Azure. Lorsque vous essayez d’annuler ou de suspendre une opération, validez les fichiers qui sont correctement copiés. Pour valider les fichiers, examinez les partages SMB ou téléchargez le fichier de marque d'ordre d'octet.

    - Vous pouvez redémarrer une tâche en cas d’échec brusque dû à une erreur temporaire, telle qu’un problème réseau. Vous ne pouvez pas redémarrer une tâche si elle a atteint un état terminal, tel que l’achèvement correct ou l’achèvement avec des erreurs. Les échecs peuvent être dus à des problèmes de taille ou de nommage de fichiers. Ces erreurs sont enregistrées, mais la tâche ne peut pas être redémarrée une fois terminée.

        ![Redémarrage d’une tâche ayant échoué](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Si vous rencontrez un problème et que la tâche ne peut pas être redémarrée, téléchargez les journaux d’erreurs et analysez l’échec dans les fichiers journaux. Après avoir corrigé le problème, vous pouvez créer une autre tâche de copie des fichiers. Vous pouvez également [copier les fichiers sur SMB](data-box-deploy-copy-data.md).
    
    - Dans cette version, vous ne pouvez pas supprimer de tâche.
    
    - Vous pouvez créer un nombre illimité de tâches, mais exécuter un maximum de 10 tâches en parallèle à la fois.
    - Si l’optimisation des fichiers est activée, les fichiers de petite taille sont compressés à l’ingestion pour améliorer les performances de copie. Dans ce cas, vous voyez un fichier compressé (GUID en tant que nom). Ne supprimez pas ce fichier car il sera décompressé pendant le chargement.

6. Pendant que la tâche est en cours d’exécution, dans la page **Copier des données** :

    - Dans la colonne **État**, vous pouvez voir l’état du travail de copie. L’état peut être :
        - **Exécution**
        - **Échec**
        - **Réussi**
        - **Suspension**
        - **En pause**
        - **Annulation**
        - **Canceled**
        - **Terminé avec des erreurs**
    - Dans la colonne **Fichiers**, vous pouvez voir le nombre de fichiers et la taille totale des fichiers en cours de copie.
    - Dans la colonne **Traités**, vous pouvez voir le nombre de fichiers et la taille totale des fichiers qui sont traités.
    - Dans la colonne **Détails**, cliquez sur **Afficher** pour voir les détails de la tâche.
    - Si des erreurs se produisent pendant le processus de copie, comme indiqué dans la colonne **Nombre d’erreurs**, accédez à la colonne **Journal des erreurs** et téléchargez les journaux d’erreurs pour résoudre les problèmes.

Attendez la fin des tâches de copie. Comme certaines erreurs ne sont consignées que dans la page **Connexion et copie**, assurez-vous que les tâches de copie se sont terminées sans erreur avant de passer à l’étape suivante.

![Aucune erreur dans la page **Connexion et copie**](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Pour garantir l’intégrité des données, la somme de contrôle est calculée par le biais d’une fonction inline lors de la copie des données. Une fois la copie terminée, vérifiez l’espace utilisé et l’espace libre sur votre appareil.
    
![Vérifier l’espace libre et l’espace utilisé sur le tableau de bord](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

Une fois la tâche de copie terminée, vous pouvez accéder à **Préparer l’expédition**.

>[!NOTE]
> La préparation de l’expédition ne peut pas s’effectuer pendant que les tâches de copie s’exécutent.

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a apporté des connaissances concernant Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
> * Prérequis
> * Copier des données sur Data Box


Passez au tutoriel suivant pour découvrir comment renvoyer votre Data Box à Microsoft.

> [!div class="nextstepaction"]
> [Ship your Azure Data Box to Microsoft](./data-box-deploy-picked-up.md) (Expédier votre Azure Data Box à Microsoft)

