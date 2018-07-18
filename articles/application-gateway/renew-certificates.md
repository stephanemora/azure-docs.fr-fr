---
title: Renouveler un certificat Azure Application Gateway
description: Découvrez comment renouveler un certificat associé à un écouteur de passerelle Application Gateway.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/18/2018
ms.author: victorh
ms.openlocfilehash: b44a57fe8ebcc985d3ab66ea04936a1558d00863
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598264"
---
# <a name="renew-application-gateway-certificates"></a>Renouveler des certificats Application Gateway

À un moment donné, vous devrez renouveler vos certificats si vous avez configuré votre passerelle Application Gateway pour le chiffrement SSL.

Vous pouvez renouveler un certificat associé à un écouteur à l’aide du Portail Azure, d’Azure PowerShell ou d’Azure CLI :

## <a name="azure-portal"></a>Portail Azure

Pour renouveler un certificat d’écouteur à partir du portail, accédez à vos écouteurs Application Gateway. Cliquez sur l’écouteur dont vous devez renouveler le certificat, puis cliquez sur **Renew or edit selected certificate** (Renouveler ou modifier le certificat sélectionné).

![Renouvellement de certificat](media/renew-certificate/ssl-cert.png)

Chargez votre nouveau certificat PFX, attribuez-lui un nom, tapez le mot de passe, puis cliquez sur **Enregistrer**.

## <a name="azure-powershell"></a>Azure PowerShell

Pour renouveler votre certificat à l’aide d’Azure PowerShell, utilisez l’applet de commande suivante :

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-azureRmApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password
```
## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment configurer le déchargement SSL avec la passerelle Azure Application Gateway, consultez la [configuration du déchargement SSL](application-gateway-ssl-portal.md)
