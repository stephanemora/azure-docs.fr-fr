---
title: Configurer les rapports Power BI
description: Configurez les rapports Power BI pour la Sauvegarde Azure à l’aide d’un coffre Recovery Services.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 4f8a2dd927f996ea09e40c7db2e43b46c17f6258
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978370"
---
# <a name="configure-azure-backup-reports"></a>Configurer les rapports de la Sauvegarde Azure

Cet article explique les étapes à suivre pour configurer les rapports pour la Sauvegarde Azure à l’aide d’un coffre Recovery Services. Il montre également comment accéder aux rapports à l’aide de Power BI. Une fois que vous avez terminé ces étapes, vous pouvez accéder directement à Power BI pour afficher, personnaliser et créer des rapports.

> [!IMPORTANT]
> Depuis le 1er novembre 2018, certains clients peuvent remarquer des problèmes dans le chargement des données dans l’application de sauvegarde Azure dans Power BI, disant « Nous avons trouvé des caractères en trop à la fin de l’entrée JSON. L’exception a été déclenchée par l’interface IDataReader. »
Cela est dû à une modification du format avec lequel les données sont chargées dans le compte de stockage.
Téléchargez la dernière application (version 1.8) pour éviter ce problème.
>
>

## <a name="supported-scenarios"></a>Scénarios pris en charge

- Les rapports de la Sauvegarde Azure sont pris en charge pour la sauvegarde de machines virtuelles Azure et de fichiers/dossiers dans le cloud à l’aide de l’agent Azure Recovery Services.
- Les rapports pour la base de données Azure SQL, les partages de fichiers Azure, Data Protection Manager et le serveur de sauvegarde Azure ne sont pas pris en charge pour l’instant.
- Vous pouvez afficher des rapports sur différents coffres et abonnements, à condition que le même compte de stockage soit configuré pour chacun des coffres. Le compte de stockage sélectionné doit se trouver dans la même région que le coffre Recovery Services.
- La fréquence d’actualisation planifiée des rapports est de 24 heures dans Power BI. Vous pouvez également effectuer une actualisation à la demande des rapports dans Power BI. Dans ce cas, les données les plus récentes dans le compte de stockage client sont utilisées pour restituer des rapports.

## <a name="prerequisites"></a>Conditions préalables requises

- Créez un [compte de stockage Azure](../storage/common/storage-account-create.md) afin de le configurer pour les rapports. Il est utilisé pour stocker des données associées aux rapports.
- [Créez un compte Power BI](https://powerbi.microsoft.com/landing/signin/) pour afficher, personnaliser et créer vos propres rapports à l’aide du portail Power BI.
- Inscrivez le fournisseur de ressources **Microsoft.insights**, s’il n’est pas déjà inscrit. Utilisez les abonnements pour le compte de stockage et le coffre Recovery Services afin que les données de rapports puissent être transmises au compte de stockage. Pour faire cette étape, accédez au Portail Azure, sélectionnez **Abonnement** > **Fournisseurs de ressources**, et cochez ce fournisseur pour l’inscrire.

## <a name="configure-storage-account-for-reports"></a>Configurer le compte de stockage pour les rapports

Suivez ces étapes afin de configurer le compte de stockage pour un coffre Recovery Services à l’aide du Portail Azure. Il s’agit d’une configuration unique. Une fois le compte de stockage configuré, vous pouvez accéder directement à Power BI pour afficher l’application modèle et utiliser des rapports.

1. Si l’un de vos coffres Recovery Services est déjà ouvert, passez à l’étape suivante. Si vous n’avez aucun coffre Recovery Services ouvert, dans le portail Azure, sélectionnez **Tous les services**.

   - Dans la liste des ressources, saisissez **Recovery Services**.
   - Au fur et à mesure de la saisie, la liste est filtrée. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.
   - La liste des archivages de Recovery Services s’affiche. Dans la liste des archivages de Recovery Services, sélectionnez un archivage.

     Le tableau de bord de l’archivage sélectionné s'ouvre.
2. Dans la liste des éléments qui s’affiche sous le coffre, sous la section **Analyse et rapports**, sélectionnez **Rapports de sauvegarde** afin de configurer le compte de stockage pour les rapports.

      ![Sélectionner l’élément de menu Rapports de sauvegarde - Étape 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. Dans le panneau **Rapports de sauvegarde**, sélectionnez le lien **Paramètres de diagnostic**. Ce lien ouvre l’interface utilisateur des **paramètres de diagnostic**, utilisée pour effectuer une transmission de données de type push vers un compte de stockage client.

      ![Permettre les diagnostics - Étape 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Sélectionnez **Activer les diagnostics** pour ouvrir une interface utilisateur à utiliser pour configurer un compte de stockage.

      ![Activer les diagnostics - Étape 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Dans la zone **Nom**, entrez un nom de paramètre. Sélectionnez la case à cocher **Archiver vers un compte de stockage** pour que les données de rapports puissent commencer à arriver dans le compte de stockage.

      ![Permettre les diagnostics - Étape 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Sélectionnez **Compte de stockage** puis sélectionnez l’abonnement et le compte de stockage appropriés dans la liste pour stocker les données de rapports, puis sélectionnez **OK**.

      ![Sélectionner le compte de stockage - Étape 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Sous la section **Journal**, sélectionnez la case à cocher **AzureBackupReport**. Déplacez le curseur pour sélectionner une période de rétention pour ces données de création de rapports. Les données de rapports du compte de stockage sont conservées pendant la période sélectionnée avec ce curseur.

      ![Enregistrer le compte de stockage - Étape 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Passez en revue toutes les modifications, puis sélectionnez **Enregistrer**. Cette action garantit que toutes vos modifications sont enregistrées et que le compte de stockage est maintenant configuré pour stocker les données de rapports.

9. Le tableau **Paramètres de diagnostic** affiche désormais le nouveau paramètre activé pour l’archivage. S’il n’apparaît pas, actualisez le tableau pour afficher le paramètre mis à jour.

      ![Afficher le paramètre de diagnostic - Étape 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Une fois que vous configurez des rapports en enregistrant le compte de stockage, *attendez 24 heures* pour que le push de données initial se termine. Importez l’application de sauvegarde Azure dans Power BI uniquement après cette heure. Pour plus d’informations, visitez la [section FAQ](backup-azure-monitor-alert-faq.md).
>
>

## <a name="view-reports-in-power-bi"></a>Afficher les rapports dans Power BI

Une fois que vous avez configuré un compte de stockage pour les rapports à l’aide du coffre Recovery Services, il faut environ 24 heures pour que les données de rapports commencent à arriver. Après 24 heures de configuration d’un compte de stockage, suivez ces étapes pour afficher les rapports dans Power BI.
Pour personnaliser et partager le rapport, créez un espace de travail et procédez comme suit.

1. [Connectez-vous](https://powerbi.microsoft.com/landing/signin/) à Power BI.
2. Accédez à **Applications > Obtenir plus d’applications de Microsoft AppSource** . Suivez les étapes de la [documentation Power BI pour la connexion à un service](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

3. Dans la barre de **recherche**, entrez **sauvegarde Azure** et sélectionnez **Obtenir maintenant**.

      ![Obtenir l’application modèle](./media/backup-azure-configure-reports/template-app-get.png)
4. Entrez le nom du compte de stockage configuré à l’étape 5 précédente, puis sélectionnez **Suivant**.

    ![Entrer le nom du compte de stockage](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)
5. À l’aide de la méthode d'authentification « Clé », entrez la clé de ce compte de stockage. Vous pouvez trouver les clés d’accès de votre compte de stockage dans le portail Azure. Pour plus d’informations, consultez [Gérer les clés d’accès au compte de stockage](../storage/common/storage-account-keys-manage.md).

     ![Entrer le compte de stockage](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>

6. Sélectionnez **Connexion**. Lorsque la connexion est établie, vous voyez une notification Importation de données.

    ![Importer un pack de contenu](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>

    Une fois l’importation terminée, vous voyez une notification de **réussite**. Si le volume de données dans le compte de stockage est important, l’importation de l’application modèle peut prendre un peu plus longtemps.

    ![Importer le pack de contenu - Réussite](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>

7. Une fois les données importées, l’application modèle **Sauvegarde Azure** est visible dans **Applications** dans le volet de navigation. Sous **Tableaux de bord**, **Rapports** et **Jeux de données**, la liste affiche maintenant la sauvegarde Azure.

8. Sous **Tableaux de bord**, sélectionnez **Sauvegarde Azure**, affichant un ensemble de rapports clés épinglés.

      ![Tableau de bord de la Sauvegarde Azure](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Pour afficher l’ensemble des rapports, sélectionnez l’un des rapports du tableau de bord.

      ![Intégrité des travaux de la Sauvegarde Azure](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Sélectionnez chaque onglet des rapports pour afficher les rapports du domaine associé.

      ![Onglets des rapports de la Sauvegarde Azure](./media/backup-azure-configure-reports/reports-tab-view.png)

## <a name="troubleshooting-errors"></a>Dépannage des erreurs

| Détails de l’erreur | Résolution |
| --- | --- |
| Une fois que vous avez configuré le compte de stockage pour les rapports de sauvegarde, **Compte de stockage** indique toujours **Non configuré**. | Si vous avez configuré correctement un compte de stockage, vos données de rapport arrivent malgré ce problème. Pour résoudre ce problème, accédez au portail Azure et sélectionnez **Tous les services** > **Paramètres de diagnostic** > **Coffre Recovery Services** > **Modifier le paramètre**. Supprimez le paramètre configuré et créez un paramètre sur le même panneau. Cette fois, dans la case **Nom**, sélectionnez **service**. Le compte de stockage configuré s’affiche maintenant. |
|Après avoir importé l’application modèle Sauvegarde Azure dans Power BI, un message d’erreur « 404 - Conteneur introuvable » s’affiche. | Comme indiqué précédemment, vous devez attendre 24 heures pour afficher correctement les rapports dans Power BI après les avoir configurés dans le coffre Recovery Services. Si vous essayez d’accéder aux rapports avant le terme de ce délai, vous obtenez ce message d’erreur, car il manque des données pour afficher des rapports valides. |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré le compte de stockage et importé l’application modèle Sauvegarde Azure, les prochaines étapes consistent à personnaliser des rapports et à utiliser un modèle de données de rapports pour créer des rapports. Pour plus d'informations, consultez les articles suivants.

- [Utiliser un modèle de données de rapports de la Sauvegarde Azure](backup-azure-reports-data-model.md)
- [Filtrer des rapports dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
- [Créer des rapports dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
