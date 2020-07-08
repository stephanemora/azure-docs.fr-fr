---
title: Guide de contrôle du comportement d’arrêt de Windows dans Azure Lab Services | Microsoft Docs
description: Procédure d'arrêt automatique d’une machine virtuelle Windows inactive et de suppression de la commande d’arrêt Windows.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e17f6e79c3d18d82dd206954dcfb0e06b02b4d53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445166"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Guide de contrôle du comportement d’arrêt de Windows

Azure Lab Services propose plusieurs contrôles de coût pour veiller à ce que les machines virtuelles Windows ne s’exécutent pas de manière inattendue :
 - [Définir un calendrier](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Définir des quotas pour les utilisateurs](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [Activer l’arrêt automatique lors de la déconnexion](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

Même avec ces contrôles de coût, il arrive qu'une machine virtuelle Windows continuer de s’exécuter de manière inattendue et entraîne une déduction du quota de l’étudiant :

- **Fenêtre RDP ouverte**
  
    Lorsqu’un étudiant se connecte à sa machine virtuelle avec RDP, il peut, par inadvertance, laisser la fenêtre RDP ouverte.  Tant que la fenêtre RDP est ouverte, le paramètre d'**arrêt automatique lors de la déconnexion** n'est pas activé puisqu’il se déclenche uniquement après déconnexion de la session RDP.

- **La commande d’arrêt de Windows est utilisée pour désactiver la machine virtuelle**
  
    Un étudiant peut utiliser la commande d’arrêt de Windows, ou d’autres mécanismes d’arrêt fournis dans Windows, pour éteindre la machine virtuelle plutôt que d'utiliser le [bouton d'arrêt d'Azure Lab Services](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm).  Dans ce cas, du point de vue d'Azure Lab Services, la machine virtuelle est toujours en cours d’utilisation.
    
Pour éviter de telles situations, ce guide propose une procédure permettant d'arrêter automatiquement une machine virtuelle Windows inactive et de supprimer la commande d’arrêt de Windows à partir du menu **Démarrer**.  

> [!NOTE]
> Une machine virtuelle peut également être déduite de manière inattendue du quota lorsque l’étudiant démarre sa machine virtuelle, mais ne se connecte jamais à cette dernière avec RDP.  Actuellement, ce guide ne répond *pas* à un tel scénario.  Il convient plutôt de rappeler aux étudiants de se connecter immédiatement à leur machine virtuelle avec RDP après démarrage, à défaut de quoi ils doivent arrêter la machine virtuelle.

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Déconnexion et arrêt automatiques de RDP pour une machine virtuelle inactive

Windows propose des paramètres de **stratégie de groupe locale** à utiliser pour définir un délai avant de déconnecter automatiquement une session RDP inactive.  Une session est considérée comme inactive en l'*absence* d'entrée de souris/clavier.  Toutes les activités de longue durée n'impliquant pas d’entrée de souris/clavier entraînent un état d’inactivité de la machine virtuelle.  Cela englobe l’exécution d’une requête longue, la diffusion vidéo en continu, la compilation, etc.  En fonction des besoins de votre classe, vous pouvez choisir de définir un délai d’inactivité suffisamment long pour gérer ces différentes activités.  Par exemple, vous pouvez définir le délai d’inactivité sur une ou plusieurs heures, si nécessaire.

Voici l’expérience de l’étudiant lorsque vous configurez la **limite de session inactive** en combinaison avec le paramètre d'[**arrêt automatique lors de la déconnexion**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect) :
 1. L’étudiant se connecte à sa machine virtuelle Windows avec RDP.
 2. Lorsque l’étudiant laisse sa fenêtre RDP ouverte et que la machine virtuelle est inactive jusqu'à la **limite de session inactive** que vous avez spécifiée (par exemple, 5 minutes), la boîte de dialogue suivante s'affiche :

    ![Boîte de dialogue de délai d'inactivité expiré](./media/how-to-windows-shutdown/idle-time-expired.png)

1. Si l’étudiant ne clique *pas* sur **OK**, sa session RDP se déconnecte automatiquement au bout de 2 minutes.
2. Après déconnexion de la session RDP, une fois le délai spécifié pour le paramètre d'**arrêt automatique lors de la déconnexion** atteint, Azure Lab Services arrête automatiquement la machine.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>Définir le délai d'inactivité de la session RDP sur le modèle de machine virtuelle

Pour définir le délai d’inactivité de la session RDP, vous pouvez vous connecter au modèle de machine virtuelle et exécuter le script PowerShell ci-dessous.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 1000

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
Vous pouvez également suivre cette procédure manuelle utilisant le modèle de machine virtuelle :

1. Appuyez sur la touche Windows, entrez **gpedit**, puis sélectionnez **Modifier la stratégie de groupe (Panneau de configuration)** .

1. Accédez à **Configuration de l'ordinateur > Modèles d’administration > Composants Windows > Services Bureau à distance > Hôte de session Bureau à distance > Délais d'expiration des sessions**.  

    ![Éditeur de stratégie de groupe locale](./media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Cliquez avec le bouton droit sur **Définir le délai d’expiration des sessions de services Bureau à distance ouvertes mais inactives**, puis cliquez sur **Modifier**.

1. Entrez les paramètres suivants, puis cliquez sur **OK** :
   1. Sélectionnez **Enabled**.
   1. Sous **Options**, spécifiez la **Limite de session inactive**.

    ![Limite de session inactive](./media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Enfin, pour combiner ce comportement avec le paramètre d'**arrêt automatique lors de la déconnexion**, vous devez suivre les étapes décrites dans l'article relatif à la procédure : [Activez l’arrêt automatique des machines virtuelles lors de la déconnexion](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect).

> [!WARNING]
> Après avoir configuré ce paramètre, soit avec PowerShell pour modifier le paramètre de Registre directement, soit manuellement avec l’éditeur de stratégie de groupe, vous devez d’abord redémarrer la machine virtuelle pour que les paramètres prennent effet.  En outre, si vous configurez le paramètre à l’aide du Registre, l’éditeur de stratégie de groupe ne s’actualise pas toujours pour refléter les modifications apportées au paramètre du Registre. Toutefois, le paramètre du Registre prend toujours effet comme prévu et la session RDP est déconnectée pendant la durée d’inactivité spécifiée.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Supprimer la commande d'arrêt de Windows à partir du menu Démarrer

Les paramètres **Stratégie de groupe locale** Windows vous permettent également de supprimer la commande d'arrêt à partir du menu **Démarrer**.

Pour supprimer la commande d’arrêt, vous pouvez vous connecter au modèle de machine virtuelle et exécuter le script PowerShell ci-dessous.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

Vous pouvez également suivre cette procédure manuelle utilisant le modèle de machine virtuelle :

1. Appuyez sur la touche Windows, entrez **gpedit**, puis sélectionnez **Modifier la stratégie de groupe (Panneau de configuration)** .

1. Accédez à **Configuration de l'ordinateur > Modèles d’administration > Menu Démarrer et Barre des tâches**.  

    ![Éditeur de stratégie de groupe locale](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Cliquez avec le bouton droit sur **Supprimer et empêcher l’accès aux commandes Arrêter, Redémarrer, Mettre en veille et Mettre en veille prolongée**, puis cliquez sur **Modifier**.

1. Sélectionnez le paramètre **Activé**, puis cliquez sur **OK** :
 
   ![Paramètre d’arrêt](./media/how-to-windows-shutdown/edit-shutdown.png)

1. Notez que la commande d'arrêt n'apparaît plus sous le menu **Démarrer** de Windows ; seule la commande **Déconnecter** s’affiche.

    ![Commande d'arrêt](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Étapes suivantes
Consultez l’article relatif à la préparation d’un modèle de machine virtuelle Windows : [Guide de configuration d’un modèle de machine Windows dans Azure Lab Services](how-to-prepare-windows-template.md)