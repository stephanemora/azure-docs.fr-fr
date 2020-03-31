---
title: Restaurer des machines virtuelles VMware avec le serveur de sauvegarde Azure
description: Utilisez le serveur de sauvegarde Azure (MABS) pour restaurer des machines virtuelles VMware s’exécutant sur un serveur VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: ab2fb4f8f79fa5a664f5cb0ba1bb537c1df658c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212356"
---
# <a name="restore-vmware-virtual-machines"></a>Restaurer les machines virtuelles VMware

Cet article explique comment utiliser le serveur de sauvegarde Microsoft Azure (MABS) pour restaurer les points de récupération de machines virtuelles VMware. Pour avoir une vue d’ensemble de l’utilisation de MABS pour récupérer des données, consultez [Récupérer des données protégées](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server). Dans la console d’administrateur MABS, il y a deux façons de rechercher des données récupérables – rechercher ou parcourir. Lorsque vous récupérez des données, vous pouvez vouloir ou non restaurer des données ou une machine virtuelle au même emplacement. Pour cette raison, MABS prend en charge trois options de récupération pour les sauvegardes de machines virtuelles VMware :

* **Récupération à l’emplacement d’origine (OLR)**  : utilisez la récupération à l’emplacement d’origine pour restaurer une machine virtuelle protégée à son emplacement d’origine. Vous ne pouvez restaurer une machine virtuelle à son emplacement d’origine que si aucun disque n’a été ajouté ou supprimé depuis la sauvegarde. Si des disques ont été ajoutés ou supprimés, vous devez utiliser un autre emplacement de récupération.

* **Récupération à un autre emplacement (ALR)**  : lorsque la machine virtuelle d’origine est manquante ou que vous ne souhaitez pas déranger la machine virtuelle d’origine, récupérez la machine virtuelle à un autre emplacement. Pour récupérer une machine virtuelle à un autre emplacement, vous devez indiquer l’emplacement d’un hôte ESXi, le pool de ressources, le dossier ainsi que le magasin de données de stockage et le chemin. Pour mieux différencier la machine virtuelle restaurée de la machine virtuelle d’origine, MABS ajoute « -Récupérée » au nom de la machine virtuelle.

* **Récupération de l’emplacement des fichiers individuels (ILR)**  : si la machine virtuelle protégée est une machine virtuelle Windows Server, des fichiers/dossiers individuels à l’intérieur de la machine virtuelle peuvent être récupérés à l’aide de la fonctionnalité ILR de MABS. Pour récupérer des fichiers individuels, reportez-vous à la procédure décrite plus loin dans cet article.

## <a name="restore-a-recovery-point"></a>Restaurer un point de récupération

1. Dans la console d’administrateur MABS, cliquez sur la vue Récupération.

2. À l’aide du volet Parcourir, parcourez ou filtrez pour rechercher la machine virtuelle que vous souhaitez récupérer. Une fois que vous sélectionnez une machine virtuelle ou un dossier, les points de récupération du volet affichent les points de récupération disponibles.

    ![Points de récupération disponibles](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. Dans le champ **Points de récupération**, utilisez le calendrier et les menus déroulants pour sélectionner une date à laquelle un point de récupération a été pris. Les dates du calendrier en gras ont des points de récupération disponibles.

4. Dans la barre d’outils, cliquez sur **Récupérer** pour ouvrir l’**Assistant Récupération**.

    ![Assistant Récupération, vérifier la sélection à restaurer](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. Cliquez sur **Suivant** pour passer à l’écran **Spécifier les options de récupération**.

6. Dans l'écran **Spécifier les options de récupération**, si vous souhaitez activer la limitation de bande passante réseau, cliquez sur **Modifier**. Pour désactiver la limitation de réseau, cliquez sur **Suivant**. Aucune autre option de cet écran de l’Assistant n’est disponible pour les machines virtuelles VMware. Si vous choisissez de modifier la limitation de bande passante réseau, dans la boîte de dialogue Limitation, sélectionnez **Activer la limitation de l’utilisation de la bande passante réseau** pour l’activer. Une fois la limitation activée, configurez les **Paramètres** et la **Planification des tâches**.

7. Dans l'écran **Sélectionner le type de récupération**, choisissez si vous souhaitez récupérer vers l’instance d’origine ou vers un nouvel emplacement, puis cliquez sur **Suivant**.

     * Si vous choisissez **Récupérer sur l’instance d’origine**, vous n’avez plus besoin d’effectuer d’autres choix dans l’Assistant. Les données de l’instance d’origine sont utilisées.

     * Si vous choisissez **Récupérer en tant que machine virtuelle sur n’importe quel hôte**, sur l’écran **Spécifier la destination**, fournissez les informations pour **l’hôte ESXi, le pool de ressources, le dossier** et le **Chemin**.

      ![Sélectionner le type de récupération](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. Dans l'écran **Résumé**, passez en revue vos paramètres et cliquez sur **Récupérer** pour démarrer le processus de récupération. L'écran **État de la récupération** affiche la progression de l’opération de récupération.

## <a name="restore-an-individual-file-from-a-vm"></a>Restaurer un fichier individuel à partir d’une machine virtuelle

Vous pouvez restaurer des fichiers individuels à partir d’un point de récupération de machine virtuelle protégé. Cette fonctionnalité est uniquement disponible pour les machines virtuelles Windows Server. La restauration de fichiers individuels est similaire à la restauration de l’ensemble de la machine virtuelle, sauf que vous accédez à VMDK et que vous recherchez les fichiers souhaités avant de commencer le processus de récupération. Pour récupérer un fichier individuel ou sélectionner des fichiers à partir d’une machine virtuelle Windows Server :

>[!NOTE]
>La restauration d’un fichier individuel à partir d’une machine virtuelle est disponible uniquement pour les points de récupération de disque et de machine virtuelle Windows.

1. Dans la console d’administrateur MABS, cliquez sur la vue **Récupération**.

2. À l’aide du volet **Parcourir**, parcourez ou filtrez pour rechercher la machine virtuelle que vous souhaitez récupérer. Une fois que vous sélectionnez une machine virtuelle ou un dossier, les points de récupération du volet affichent les points de récupération disponibles.

    ![Points de récupération disponibles](./media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

3. Dans le volet **Points de récupération pour :** , utilisez le calendrier pour sélectionner la date qui contient le ou les points de récupération souhaités. Selon la façon dont la stratégie de sauvegarde a été configurée, les dates peuvent avoir plusieurs points de récupération. Une fois que vous avez sélectionné le jour de la prise du point de récupération, vérifiez que vous avez choisi la bonne **Heure de récupération**. Si la date sélectionnée comporte plusieurs points de récupération, choisissez votre point de récupération en le sélectionnant dans le menu déroulant Heure de récupération. Une fois que vous avez choisi le point de récupération, la liste des éléments récupérables s’affiche dans le volet **Chemin**.

4. Pour rechercher les fichiers que vous souhaitez récupérer, dans le volet **Chemin**, double-cliquez sur l'élément dans la colonne **Élément récupérable** pour l’ouvrir. Sélectionnez le fichier, les fichiers ou les dossiers que vous souhaitez récupérer. Pour sélectionner plusieurs éléments, appuyez sur la touche **Ctrl** tout en sélectionnant chaque élément. Utilisez le volet **Chemin** pour effectuer une recherche dans la liste des fichiers ou des dossiers figurant dans la colonne **Élément récupérable**. **Rechercher dans la liste ci-dessous** n’effectue pas de recherche dans les sous-dossiers. Pour effectuer une recherche dans des sous-dossiers, double-cliquez sur le dossier. Utilisez le bouton **Haut** pour vous déplacer d’un dossier enfant vers le dossier parent. Vous pouvez sélectionner plusieurs éléments (fichiers et dossiers), mais ils doivent se trouver dans le même dossier parent. Vous ne pouvez pas récupérer d’éléments de dossiers différents lors de la même tâche de récupération.

    ![Vérifier la sélection à restaurer](./media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

5. Une fois que vous avez sélectionné le ou les éléments à récupérer, dans la barre d’outils Console d’administrateur, cliquez sur **Récupérer**  pour ouvrir l’**Assistant Récupération**. Dans l’Assistant Récupération, l'écran **Vérifier la récupération sélectionnée** affiche les éléments sélectionnés à récupérer.

6. Dans l'écran **Spécifier les options de récupération**, si vous souhaitez activer la limitation de bande passante réseau, cliquez sur **Modifier**. Pour désactiver la limitation de réseau, cliquez sur **Suivant**. Aucune autre option de cet écran de l’Assistant n’est disponible pour les machines virtuelles VMware. Si vous choisissez de modifier la limitation de bande passante réseau, dans la boîte de dialogue Limitation, sélectionnez **Activer la limitation de l’utilisation de la bande passante réseau** pour l’activer. Une fois la limitation activée, configurez les **Paramètres** et la **Planification des tâches**.
7. Dans l'écran **Sélectionner le type de récupération**, cliquez sur **Suivant**. Vous ne pouvez récupérer votre ou vos fichiers ou dossiers que dans un dossier réseau.
8. Dans l'écran **Spécifier la destination**, cliquez sur **Parcourir** afin de rechercher un emplacement réseau pour vos fichiers ou dossiers. MABS crée un dossier où tous les éléments récupérés sont copiés. Le nom du dossier a le préfixe MABS_day-month-year. Lorsque vous sélectionnez un emplacement pour les fichiers ou le dossier récupérés, les détails de cet emplacement (destination, chemin de destination et espace disponible) sont fournis.

    ![Spécifier l’emplacement de récupération des fichiers](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. Dans l’écran **Spécifier les options de récupération**, choisissez quels paramètres de sécurité appliquer. Vous pouvez choisir de modifier la limitation de l’utilisation de la bande passante réseau, mais la limitation est désactivée par défaut. Par ailleurs, **Récupération SAN** et la **Notification** ne sont pas activées.
10. Dans l'écran **Résumé**, passez en revue vos paramètres et cliquez sur **Récupérer** pour démarrer le processus de récupération. L'écran **État de la récupération** affiche la progression de l’opération de récupération.

## <a name="next-steps"></a>Étapes suivantes

Pour résoudre les problèmes lors de l’utilisation du serveur de sauvegarde Azure, consultez le [guide de dépannage du serveur de sauvegarde Azure](./backup-azure-mabs-troubleshoot.md).
