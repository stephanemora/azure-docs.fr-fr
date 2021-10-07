---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 09/08/2021
ms.author: alkohli
ms.openlocfilehash: 4359d8288627f425a4a61fc0d4bf91d63bea174f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378686"
---
|    Modèle d’URL |    Composant ou fonctionnalité  |
|-----------------------------------------------------|-----------------------------------------|
|    https://\*.databoxedge.azure.com/\*<br>https://\*.servicebus.windows.net/\*<br>https://login.microsoftonline.com |    Service Azure Stack Edge<br>Azure Service Bus<br>Service d’authentification – Azure Active Directory                           |
|    http:\//crl.microsoft.com/pki/\*<br>http:\//www.microsoft.com/pki/\*                                                                                                                                                                                                                                                                                                                                                                                                  |    Révocation de certificat                                                                               |
|    https://\*.core.windows.net/\*<br>https://\*.data.microsoft.com<br>http://\*.msftncsi.com<br>https://www.msftconnecttest.com/connecttest.txt |    Comptes de stockage Azure et surveillance |
|    http:\//windowsupdate.microsoft.com<br>http://\*.windowsupdate.microsoft.com<br>https://\*.windowsupdate.microsoft.com<br>http://\*.update.microsoft.com<br>https://\*.update.microsoft.com<br>http://\*.windowsupdate.com<br>http://download.microsoft.com<br>http://\*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://\*.ws.microsoft.com<br>https://\*.ws.microsoft.com<br>http://\*.mp.microsoft.com |    Serveurs Microsoft Update                                                                             |
|    http://\*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                                          |    CDN Akamai                                                                                           |
|    http://\*.data.microsoft.com     |    Service de télémétrie dans Windows, consultez l’article Mise à jour de l’expérience client et du diagnostic de la télémétrie |
|    `http://<vault-name>.vault.azure.net:443`     |    Key Vault |
<!--|    http://www.msftconnecttest.com/connecttest.txt  |    Pour les diagnostics     ||  |-->   

