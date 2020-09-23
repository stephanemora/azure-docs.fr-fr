---
title: Supervision d’intégrité de fichier dans Azure Security Center | Microsoft Docs
description: Découvrez comment configurer la fonctionnalité Monitoring d’intégrité de fichier (FIM) dans Azure Security Center à l’aide de cette procédure pas à pas.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: memildin
ms.openlocfilehash: 9e8bd56655adfa1f7cdb769ac6cd282193b1bcf2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90901376"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Supervision d’intégrité de fichier dans Azure Security Center
Découvrez comment configurer la fonctionnalité Monitoring d’intégrité de fichier (FIM) dans Azure Security Center à l’aide de cette procédure pas à pas.


## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale (GA)|
|Prix :|Nécessite [Azure Defender pour les serveurs](defender-for-servers-introduction.md)|
|Rôles et autorisations obligatoires :|Le **propriétaire de l’espace de travail** peut activer/désactiver FIM (pour plus d’informations, consultez [Rôles Azure pour Log Analytics](https://docs.microsoft.com/services-hub/health/azure-roles#azure-roles)).<br>Le **lecteur** peut visualiser les résultats.|
|Clouds :|![Oui](./media/icons/yes-icon.png) Clouds commerciaux<br>![Oui](./media/icons/yes-icon.png) Gouvernement des États-Unis<br>![Non](./media/icons/no-icon.png) Chine Gov, autres Gov|
|||





## <a name="what-is-fim-in-security-center"></a>En quoi consiste la fonctionnalité FIM dans Security Center ?
Le Monitoring d’intégrité de fichier (FIM), également appelé Monitoring des modifications, recherche les modifications qui sont apportées aux fichiers et registres du système d’exploitation, ainsi qu’aux logiciels d’application et autres systèmes, et qui peuvent indiquer une attaque. Une méthode de comparaison est utilisée pour déterminer si l’état actuel du fichier est différent de la dernière analyse du fichier. Vous pouvez tirer parti de cette comparaison pour vérifier si des modifications suspectes ou valides ont été apportées à vos fichiers.

Le Monitoring d’intégrité de fichier dans Security Center valide l’intégrité des fichiers Windows, le Registre Windows et les fichiers Linux. Vous sélectionnez les fichiers que vous souhaitez analyser en activant la fonctionnalité FIM. À l’aide de la fonctionnalité FIM, Security Center recherche différentes activités sur les fichiers, notamment :

- La suppression et la création de fichiers et d’entrées de registre
- Les modifications de fichiers (modifications apportées à la taille du fichier, aux listes de contrôle d’accès et au hachage du contenu)
- Les modifications de registre (modifications apportées à la taille du registre, aux listes de contrôle d’accès, au type d’entrées et au contenu)

Security Center vous recommande des entités à surveiller, pour lesquelles vous pouvez facilement activer la fonctionnalité FIM. Vous pouvez également définir vos propres stratégies FIM ou entités à surveiller. Cette procédure pas à pas vous explique comment procéder.

> [!NOTE]
> La fonctionnalité FIM (File Integrity Monitoring) fonctionne sur les ordinateurs et les machines virtuelles Windows et Linux et est disponible uniquement lorsque **Azure Defender pour les serveurs** est activé. Consultez [Tarification](security-center-pricing.md) pour en savoir plus. La fonctionnalité FIM charge des données dans l’espace de travail Log Analytics. Des frais de données seront appliqués en fonction de la quantité de données que vous téléchargez. Pour en savoir plus, consultez l’article [Tarification - Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

La fonctionnalité FIM utilise la solution Azure Change Tracking pour identifier les modifications apportées dans votre environnement. Lorsque la fonctionnalité FIM est activée, vous disposez d’une ressource **Change Tracking** de type **Solution**. Pour plus d’informations sur la fréquence de collecte de données, consultez [Détails de la collecte de données de suivi des modifications](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) pour Azure Change Tracking.

> [!NOTE]
> Si vous supprimez la ressource **Change Tracking**, vous désactivez également la fonctionnalité FIM dans Security Center.

## <a name="which-files-should-i-monitor"></a>Quels fichiers dois-je surveiller ?
Sélectionnez les fichiers qui sont essentiels au fonctionnement de votre système et de vos applications. Nous vous recommandons de choisir des fichiers qui ne sont pas susceptibles d’être modifiés sans planification. La sélection de fichiers qui sont fréquemment modifiés par des applications ou le système d’exploitation (par exemple, les fichiers journaux et les fichiers texte) va surcharger le processus et compromettre la détection des attaques.

Security Center fournit la liste suivante d’éléments recommandés à surveiller en fonction de modèles d’attaque connus. Il s’agit notamment des fichiers et clés de Registre Windows. Toutes les clés sont sous HKEY_LOCAL_MACHINE (« HKLM » dans la table).

|**Fichiers Linux**|**Fichiers Windows**|**Clés de Registre Windows**|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*.conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini\boot|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|/sbin|C:\Windows\System32\userinit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders|
|/boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Program Files\Microsoft Security Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini\boot|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|


## <a name="using-file-integrity-monitoring"></a>Utilisation du Monitoring d’intégrité de fichier

1. Utiliser le tableau de bord **Azure Defender**.

1. Dans le zone **Protection avancée**, sélectionnez **Analyse de l’intégrité du fichier**.

    :::image type="content" source="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png" alt-text="Lancement FIM" lightbox="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png":::


    La fenêtre **Monitoring d’intégrité de fichier** s’affiche.
    ![Tableau de bord Security Center][2]

Chaque espace de travail contient les informations suivantes :

- Nombre total de modifications apportées au cours de la dernière semaine (un trait d’union « - » apparaît lorsque la fonctionnalité FIM n’est pas activée dans l’espace de travail)
- Nombre total d’ordinateurs et de machines virtuelles envoyant des rapports à l’espace de travail
- Emplacement géographique de l’espace de travail
- Abonnement Azure auquel appartient l’espace de travail

Les boutons suivants peuvent également apparaître dans certains espaces de travail :

- ![Icône Activer][3] Indique que la fonctionnalité FIM n’est pas activée pour l’espace de travail. Sélectionnez l’espace de travail pour activer la fonctionnalité FIM sur tous les ordinateurs associés à l’espace de travail.
- ![Mettre à niveau l’icône du plan][4] Indique que l’espace de travail ou l’abonnement n’est pas protégé par Azure Defender. Pour utiliser la fonctionnalité FIM, votre abonnement doit être protégé par Azure Defender.  La sélection de l’espace de travail vous permet de mettre à niveau.
- Si aucun bouton n’apparaît, la fonctionnalité FIM est déjà activée dans l’espace de travail.

Sous **Analyse de l’intégrité du fichier**, vous pouvez sélectionner un espace de travail pour y activer la fonctionnalité FIM, afficher le tableau de bord Analyse de l’intégrité du fichier pour cet espace de travail ou [mettre à niveau](security-center-pricing.md) l’espace de travail pour utiliser Azure Defender.

## <a name="enable-fim"></a>Activer la fonctionnalité FIM
Pour activer la fonctionnalité FIM dans un espace de travail :

1. Sous **Monitoring d’intégrité de fichier**, sélectionnez un espace de travail avec le bouton **Activer**.

1. La fenêtre **Activer le Monitoring d’intégrité de fichier** qui s’ouvre affiche le nombre d’ordinateurs Windows et Linux appartenant à l’espace de travail.

   ![Activer le Monitoring d’intégrité de fichier][5]

   Les paramètres recommandés pour Windows et Linux sont également affichés.  Développez les champs **Fichiers Windows**, **Registre** et **fichiers Linux** pour afficher la liste complète des éléments recommandés.

1. Désactivez toutes les entités recommandées pour lesquelles vous ne souhaitez pas activer FIM.

1. Sélectionnez **Appliquer le Monitoring d’intégrité de fichier** pour activer la fonctionnalité FIM.

> [!NOTE]
> Vous pouvez modifier les paramètres à tout moment. Consultez la section [Modifier des entités surveillées](#edit-monitored-entities) ci-dessous pour en savoir plus.


## <a name="view-the-fim-dashboard"></a>Afficher le tableau de bord FIM
Le tableau de bord **Monitoring d’intégrité de fichier** affiche les espaces de travail sur lesquels la fonctionnalité FIM est activée. Le tableau de bord FIM s’ouvre lorsque vous avez activé la fonctionnalité FIM dans un espace de travail ou que vous sélectionnez un espace de travail dans la fenêtre **Monitoring d’intégrité de fichier** pour lequel la fonctionnalité est activée.

![Tableau de bord Monitoring d’intégrité de fichier][6]

Le tableau de bord FIM d’un espace de travail affiche les détails suivants :

- Nombre total d’ordinateurs connectés à l’espace de travail
- Nombre total de modifications apportées au cours de la période sélectionnée
- Détails des types de modification (fichiers, registre)
- Détails des catégories de modification (modification, ajout, suppression)

Sélectionnez le filtre en haut du tableau de bord pour définir la période de temps pour laquelle vous souhaitez afficher les modifications.

![Filtre Période][7]

L’onglet **Ordinateurs** (illustré ci-dessus) répertorie tous les ordinateurs envoyant des rapports à cet espace de travail. Pour chaque ordinateur, le tableau de bord affiche :

- Le nombre total de modifications apportées au cours de la période sélectionnée
- Une répartition des modifications selon leur type (modifications de fichier ou de registre)

La fonctionnalité **Recherche dans les journaux** s’affiche lorsque vous entrez un nom d’ordinateur dans le champ de recherche ou que vous sélectionnez un ordinateur répertorié dans l’onglet Ordinateurs. Cette fonctionnalité affiche toutes les modifications apportées à l’ordinateur pendant la période sélectionnée. Vous pouvez développer chaque modification pour afficher des informations supplémentaires.

![Recherche dans les journaux][8]

L’onglet **Modifications** (illustré ci-dessous) répertorie toutes les modifications associées à l’espace de travail pendant la période sélectionnée. Pour chaque entité qui a été modifiée, le tableau de bord affiche les informations suivantes :

- L’ordinateur sur lequel la modification a été apportée
- Le type de modification (registre ou fichier)
- La catégorie de modification (modification, ajout, suppression)
- La date et l’heure de modification

![Les modifications apportées à l’espace de travail][9]

La fenêtre **Détails des modifications** s’ouvre lorsque vous saisissez une modification dans le champ de recherche ou que vous sélectionnez une entité répertoriée dans l’onglet **Modifications**.

![Détails des modifications][10]

## <a name="edit-monitored-entities"></a>Modifier des entités surveillées

1. Revenez au **tableau de bord Monitoring d’intégrité de fichier** et sélectionnez **Paramètres**.

   ![Paramètres][11]

   La page **Configuration de l’espace de travail** s’ouvre. Elle contient trois onglets : **Registre Windows**, **Fichiers Windows** et **Fichiers Linux**. Chaque onglet répertorie les entités que vous pouvez modifier dans cette catégorie. Pour chaque entité répertoriée, Security Center identifie si la fonctionnalité FIM est activée (true) ou désactivée (false).  La modification de l’entité vous permet d’activer ou de désactiver la fonctionnalité FIM.

   ![Configuration de l’espace de travail][12]

2. Sélectionnez une protection d’identités. Dans cet exemple, nous avons sélectionné un élément dans le Registre Windows. La fenêtre **Modification pour Change Tracking** s’affiche.

   ![Modification pour Change Tracking][13]

Sous **Modification pour Change Tracking**, vous pouvez :

- Activer (True) ou désactiver (False) le Monitoring d’intégrité de fichier
- Saisir ou modifier le nom de l’entité
- Saisir ou modifier la valeur ou le chemin d’accès
- Supprimer l’entité, ignorer la modification ou enregistrer les modifications

## <a name="add-a-new-entity-to-monitor"></a>Ajouter une nouvelle entité à surveiller
1. Revenez au **tableau de bord Monitoring d’intégrité de fichier**, puis sélectionnez **Paramètres** en haut de la page. La fenêtre **Configuration de l’espace de travail** s’affiche.
2. Sous **Configuration de l’espace de travail**, sélectionnez l’onglet correspondant au type d’entité que vous souhaitez ajouter : Registre Windows, Fichiers Windows ou Fichiers Linux. Dans cet exemple, nous avons sélectionné **Fichiers Linux**.

   ![Ajoutez un nouvel élément à surveiller][14]

3. Sélectionnez **Ajouter**. La fenêtre **Ajout pour Change Tracking** s’affiche.

   ![Saisir les informations requises][15]

4. Sur la page **Ajouter**, saisissez les informations requises, puis sélectionnez **Enregistrer**.

## <a name="disable-monitored-entities"></a>Désactiver des entités surveillées
1. Revenez au tableau de bord **Monitoring d’intégrité de fichier**.
2. Sélectionnez un espace de travail pour lequel la fonctionnalité FIM est actuellement activée. Un espace de travail pour lequel la fonctionnalité FIM est activée ne contient pas de bouton Activer ou Mettre à niveau le plan.

   ![Sélectionner un espace de travail pour lequel la fonctionnalité FIM est activée][16]

3. Sous Monitoring d’intégrité de fichier, cliquez sur **Paramètres**.

   ![Sélectionner les paramètres][17]

4. Sous **Configuration de l’espace de travail**, sélectionnez un groupe dans lequel la valeur **Activé** est définie sur true.

   ![Configuration de l’espace de travail][18]

5. Dans la fenêtre **Modification pour Change Tracking**, définissez la valeur **Activé** sur false.

   ![Définir la valeur Activé sur false][19]

6. Sélectionnez **Enregistrer**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Supervision de dossiers et chemins d’accès à l’aide de caractères génériques

Utilisez des caractères génériques pour simplifier le suivi au sein des répertoires. Les règles suivantes s’appliquent lorsque vous configurez la supervision d’un dossier à l’aide de caractères génériques :
-   Les caractères génériques sont requis pour effectuer le suivi de plusieurs fichiers.
-   Les caractères génériques ne peuvent être utilisés que dans le dernier segment d’un chemin d’accès, comme C:\folder\file ou /etc/*.conf
-   Si le chemin d’accès d’une variable d’environnement n’est pas valide, la validation réussit, mais le chemin d’accès échoue lors de l’exécution de l’inventaire.
-   Lors de la définition du chemin d’accès, évitez les chemins d’accès généraux comme c:\*.*, sinon un trop grand nombre de dossiers sont parcourus.

## <a name="disable-fim"></a>Désactiver la fonctionnalité FIM
Vous pouvez désactiver la fonctionnalité FIM. La fonctionnalité FIM utilise la solution Azure Change Tracking pour identifier les modifications apportées dans votre environnement. En la désactivant, vous supprimez la solution Change Tracking de l’espace de travail sélectionné.

1. Pour désactiver la fonctionnalité FIM, revenez au tableau de bord **Monitoring d’intégrité de fichier**.
2. Sélectionnez un espace de travail.
3. Sous **Monitoring d’intégrité de fichier**, cliquez sur **Désactiver**.

   ![Désactiver la fonctionnalité FIM][20]

4. Sélectionnez **Supprimer** pour confirmer la désactivation.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser la fonctionnalité Monitoring d’intégrité de fichier (FIM) dans Security Center. Pour plus d’informations sur Security Center, consultez les pages suivantes :

* [Définition des stratégies de sécurité](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Blog sur la sécurité Azure](https://docs.microsoft.com/archive/blogs/azuresecurity/): découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png