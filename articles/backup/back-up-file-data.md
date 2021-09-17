---
title: Sauvegarder des données de fichier avec MABS
description: Vous pouvez sauvegarder des données de fichier sur les ordinateurs serveurs et clients avec MABS.
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: 71b12c98713dff2340bdda1baf486dcd5cfdb70a
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681676"
---
# <a name="back-up-file-data-with-mabs"></a>Sauvegarder des données de fichier avec MABS

Vous pouvez utiliser le Serveur de sauvegarde Microsoft Azure (MABS) pour sauvegarder les données de fichier sur les ordinateurs serveurs et clients.

## <a name="before-you-start"></a>Avant de commencer

1. **Déployer MABS** : Vérifiez que MABS est installé et déployé correctement. Nous vous recommandons de consulter les articles suivants :

   - [Installer MABS](backup-azure-microsoft-azure-backup.md)

   - [Que peut sauvegarder MABS ?](backup-mabs-protection-matrix.md)

   - [Nouveautés dans MABS](backup-mabs-whats-new-mabs.md)

   - [Notes de publication MABS](backup-mabs-release-notes-v3.md)

1. **Configurer le stockage** - Vous pouvez stocker les données sauvegardées sur disque, sur bande et dans le cloud avec Azure. Pour plus d’informations, consultez [Préparer le stockage des données](/system-center/dpm/plan-long-and-short-term-data-storage?view=sc-dpm-2019&preserve-view=trus).

1. **Configurer l’agent de protection MABS** : vous devrez installer l’agent de protection MABS sur chaque machine que vous souhaitez sauvegarder. Lisez [Déployer l’agent de protection MABS](backup-azure-microsoft-azure-backup.md).

## <a name="back-up-file-data"></a>Sauvegarder des données de fichier

Une fois que vous avez configuré votre infrastructure MABS, vous pouvez activer des machines de protection dont vous souhaitez sauvegarder les données de fichiers.

1. Pour créer un groupe de protection, cliquez sur **Protection** > **Actions** > **Créer un groupe de Protection** pour ouvrir l’Assistant **Création d’un nouveau groupe de protection** dans la console MABS.

1. Dans **Sélectionner le type de groupe de protection**, sélectionnez **Serveurs**.

1. Dans **Sélectionner les membres du groupe**, vous allez ajouter les ordinateurs dont vous voulez sauvegarder les données de fichier dans le groupe de protection. Sur ces ordinateurs, vous sélectionnez les emplacements, partages et dossiers à protéger.  [Déployez des groupes de protection](backup-support-matrix-mabs-dpm.md). Vous pouvez sélectionner différents types de dossiers (comme le Bureau), un autre fichier ou la totalité du volume. Vous pouvez également exclure des emplacements spécifiques de la protection.

   >[!NOTE]
   > Si vous protégez le volume sur lequel la déduplication est activée, assurez-vous que le rôle serveur de [Déduplication des données](/windows-server/storage/data-deduplication/install-enable) est installé sur le serveur MABS. Pour plus d’informations sur la déduplication, consultez la [matrice de prise en charge](backup-support-matrix-mabs-dpm.md#deduplicated-volumes-support).   

1. Dans **Sélectionner la méthode de protection des données**, spécifiez le mode de gestion de la sauvegarde à court terme et à long terme. La sauvegarde à court terme s’effectue toujours sur disque en premier lieu, avec la possibilité de sauvegarder depuis le disque vers le cloud Azure avec Sauvegarde Azure (à court ou long terme). Une alternative à la sauvegarde à long terme dans le cloud consiste également à configurer une sauvegarde à long terme sur un appareil de bandes ou une bibliothèque de bandes autonomes connectés au serveur MABS.

1. Dans **Sélectionner les objectifs à court terme**, spécifiez comment vous voulez sauvegarder dans le stockage à court terme sur le disque.  Dans **Durée de rétention**, spécifiez la durée pendant laquelle vous souhaitez conserver les données sur disque. Dans **Fréquence de synchronisation**, vous spécifiez la fréquence à laquelle exécuter une sauvegarde incrémentielle sur le disque. Si vous ne souhaitez pas définir d’intervalle de sauvegarde, vous pouvez activer l’option **Juste avant un point de récupération** afin que MABS exécute une sauvegarde complète rapide juste avant la planification de chaque point de récupération.

1. Si vous voulez stocker des données sur bande à long terme, dans **Spécifier les objectifs à long terme**, indiquez combien de temps vous voulez conserver les données sur bande (entre 1 et 99 ans). Dans Fréquence de sauvegarde, spécifiez la fréquence d’exécution des sauvegardes sur bande. La fréquence se base sur la durée de rétention que vous avez spécifiée :

   - Quand la durée de rétention est comprise entre 1 et 99 ans, vous pouvez sélectionner des sauvegardes quotidiennes, hebdomadaires, bihebdomadaires, mensuelles, trimestrielles, semestrielles ou annuelles.

   - Quand la durée de rétention est comprise entre 1 et 11 mois, vous pouvez sélectionner des sauvegardes quotidiennes, hebdomadaires, bihebdomadaires ou mensuelles.

   - Quand la durée de rétention est comprise entre 1 et 4 semaines, vous pouvez sélectionner des sauvegardes quotidiennes ou hebdomadaires.

   Sur un lecteur de bande autonome, pour un groupe de protection unique, MABS utilise la même bande pour les sauvegardes quotidiennes jusqu'à ce qu'il n'y ait plus assez de place sur la bande. Vous pouvez aussi colocaliser des données de différents groupes de protection sur bande.

   Dans la page **Sélectionner les détails de bande et de bibliothèque**, spécifiez la bande/bibliothèque à utiliser et indiquez si les données doivent être compressées et chiffrées sur bande.

1. Dans la page **Vérifier l’allocation de disque**, consultez l’espace disque du pool de stockage alloué au groupe de protection.

   **Taille totale des données** correspond à la taille des données que vous souhaitez sauvegarder et **Disk space to be provisioned on MABS** (Espace disque à provisionner sur MABS) correspond à l’espace recommandé par MABS pour le groupe de protection. MABS choisit le volume de sauvegarde idéal en fonction des paramètres. Toutefois, vous pouvez modifier les choix de volume de sauvegarde dans **Détails de l’allocation de disque**. Pour les charges de travail, sélectionnez le stockage préféré dans le menu déroulant. Vos modifications changent les valeurs de **Stockage Total** et **Stockage libre** dans le volet **Stockage sur disque disponible**. L’espace sous-provisionné est la quantité de stockage que MABS vous suggère d’ajouter au volume pour continuer à procéder à des sauvegardes de façon fluide à l’avenir.

1. Dans **Choisir la méthode de création de réplica**, sélectionnez la manière de gérer la réplication initiale complète des données.  Si vous choisissez de répliquer sur le réseau, nous vous recommandons de choisir une heure creuse. Pour de grandes quantités de données ou des conditions de réseau peu optimales, envisagez de répliquer les données hors connexion à l’aide d’un support amovible.

1. Sous **Sélectionner les options de vérification de cohérence**, indiquez comment vous voulez automatiser les vérifications de cohérence. Vous pouvez activer une vérification pour qu’elle s’exécute uniquement lorsque les données de réplication deviennent incohérentes ou selon une planification. Si vous ne souhaitez pas configurer la vérification de cohérence automatique, vous pouvez exécuter une vérification manuelle à tout moment en cliquant avec le bouton droit sur le groupe de protection dans la zone **Protection** de la console MABS et en sélectionnant **Effectuer une vérification de cohérence**.

1. Si vous avez choisi de sauvegarder dans le cloud à l’aide de Sauvegarde Azure, sur la page **Indiquer les données de protection en ligne**, veillez à sélectionner les charges de travail que vous souhaitez sauvegarder dans Azure.

1. Dans **Spécifier une planification de sauvegarde en ligne**, spécifiez la fréquence des sauvegardes incrémentielles sur Azure. Vous pouvez planifier des sauvegardes quotidiennes, hebdomadaires, mensuelles et annuelles, ainsi que sélectionner la date et l’heure de leur exécution. Les sauvegardes peuvent avoir lieu jusqu’à deux fois par jour. Chaque fois qu’une sauvegarde s’exécute, un point de récupération de données est créé dans Azure à partir de la copie des données sauvegardées stockées sur le disque MABS.

1. Dans **Spécifier une stratégie de rétention en ligne**, vous pouvez spécifier la façon dont les points de récupération créés à partir des sauvegardes quotidiennes/hebdomadaires/mensuelles/annuelles sont conservés dans Azure.

1. Dans **Choisir la réplication en ligne**, spécifiez la manière dont se produit la réplication initiale complète des données. Vous pouvez répliquer sur le réseau ou effectuer une sauvegarde en mode hors connexion (essaimage hors connexion). La sauvegarde en mode hors connexion utilise la fonctionnalité Azure Import. [En savoir plus](/azure/backup/backup-azure-backup-import-export).

1. Dans la page **Résumé**, vérifiez vos paramètres. Une fois que vous avez cliqué sur **Créer un groupe**, la réplication initiale des données se produit. Quand elle est terminée, l’état du groupe de protection indique **OK** dans la page **État**. Ensuite, la sauvegarde a lieu conformément aux paramètres du groupe de protection.

## <a name="recover-backed-up-file-data"></a>Récupérer des données de fichier sauvegardées

Vous pouvez récupérer des données à l'aide de l'Assistant Récupération. Lorsque vous double-cliquez sur un volume protégé dans le volet **Données protégées** de l'Assistant, MABS affiche les données qui appartiennent à ce volume dans le volet des résultats. Vous pouvez filtrer les noms des serveurs protégés par ordre alphabétique en cliquant sur **Filtre**. Une fois la source de données sélectionnée dans l'arborescence pour la récupération, vous pouvez sélectionner un point de récupération spécifique. Pour cela, cliquez sur les dates qui figurent en gras dans le calendrier. Lorsque vous cliquez sur **Récupérer** dans le volet **Actions**, MABS lance la tâche de récupération.

## <a name="recover-data"></a>Récupérer des données
Récupérez des données à partir de la console MABS comme suit :

1. Dans la console MABS, cliquez sur **Récupération** dans la barre de navigation. Recherchez ensuite les données à récupérer. Dans le volet des résultats, sélectionnez les données.

1. Les points de récupération disponibles sont indiqués en gras dans le calendrier, à la section des points de récupération. Sélectionnez la date en gras pour le point de récupération que vous souhaitez récupérer.

1. Dans le volet **Élément récupérable**, sélectionnez l'élément à récupérer.

1. Dans le volet **Actions**, cliquez sur **Récupérer** pour ouvrir l’Assistant Récupération.

1. Vous pouvez récupérer des données comme suit :

   1. **Récupérer à l'emplacement d'origine**. Notez que cela ne fonctionne pas si l’ordinateur client est connecté via un VPN. Dans ce cas, utilisez un autre emplacement, puis copiez les données à partir de cet emplacement.

   1. **Récupérer à un autre emplacement**.

   1. **Copier sur bande**. Cette option permet de copier  le volume qui contient les données sélectionnées sur une bande dans la bibliothèque MABS. Vous pouvez également choisir de compresser ou de chiffrer les données sur la bande.

1. Spécifiez les options de récupération :

   1. **Comportement existant de récupération de version**. Sélectionnez **Créer une copie**, **Ignorer** ou **Remplacer**. Cette option est activée uniquement quand vous effectuez la récupération à l’emplacement d’origine.

   1. **Restaurer la sécurité**. Sélectionnez **Appliquer les paramètres de sécurité de l'ordinateur de destination** ou **Appliquer les paramètres de sécurité de la version de point de récupération**.

   1. **Limitation de l'utilisation de la bande passante réseau**. Cliquez sur **Modifier** pour activer la limitation d'utilisation du réseau :

   1. **Activer la récupération SAN au moyen de captures instantanées matérielles**. Sélectionnez cette option pour utiliser les captures instantanées matérielles basées sur SAN pour accélérer la récupération.
      
      Cette option est valide uniquement lorsque vous disposez d'un réseau de stockage SAN dans lequel la fonctionnalité de captures instantanées matérielles est activée. En effet, le réseau SAN a la possibilité de créer un clone qu'il fractionne pour le rendre inscriptible, tandis que l'ordinateur protégé et le serveur MABS sont connectés au même réseau SAN.

   1. **Notification**. Cliquez sur **Envoyer un e-mail lorsque cette récupération est terminée**, puis indiquez les destinataires de la notification. Séparez les adresses de messagerie par des virgules.

1. Cliquez sur **Suivant** après avoir sélectionné les options précédentes.

1. Vérifiez vos paramètres de récupération, puis cliquez sur **Récupérer**. Notez que toute tâche de synchronisation lancée pour l’élément de récupération sélectionné est annulée quand la récupération est en cours.

Si vous utilisez le Stockage de sauvegarde moderne (MBS), la récupération par l’utilisateur final (EUR) du serveur de fichiers n’est pas prise en charge. La récupération par l’utilisateur du serveur de fichiers dépend du service VSS, qui n’est pas utilisé par le stockage de sauvegarde moderne. Si la récupération par l’utilisateur est activée, alors procédez comme suit pour récupérer des données :

1. Accédez au fichier de données protégées. Cliquez avec le bouton droit sur le nom du fichier > **Propriétés**.

1. Dans **Propriétés** > **Versions précédentes**, sélectionnez la version à partir de laquelle effectuer la récupération.
