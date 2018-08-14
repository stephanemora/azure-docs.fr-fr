
## <a name="start-your-powershell-session"></a>Démarrer votre session PowerShell
Tout d’abord, la dernière version [d’Azure PowerShell](http://msdn.microsoft.com/library/mt619274.aspx) doit être installée et en cours d’exécution. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Les exemples de cette rubrique utilisent le [modèle de déploiement Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md), et donc les [applets de commande Azure Resource Manager](http://msdn.microsoft.com/library/azure/mt125356.aspx). 
> 
> 

Exécutez l’applet de commande [**Connect-AzureRmAccount**](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) pour faire apparaître un écran de connexion dans lequel vous pouvez entrer vos informations d’identification. Utilisez les informations d’identification dont vous disposez pour vous connecter au portail Azure.

    Connect-AzureRmAccount

Si vous possédez plusieurs abonnements, utilisez l’applet de commande [**Set-AzureRmContext**](https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext) pour sélectionner l’abonnement que votre session PowerShell doit utiliser. Pour savoir quel abonnement la session PowerShell en cours utilise, exécutez l’applet de commande [**Get-AzureRmContext**](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermcontext). Pour voir tous vos abonnements, exécutez l’applet de commande [**Get-AzureRmSubscription**](https://docs.microsoft.com/powershell/module/servicemanagement/azurerm.profile/get-azurermsubscription).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

