---
title: Résolution des problèmes d’affinité de Session Azure Application Gateway
description: Cet article fournit des informations sur la façon de résoudre les problèmes d’affinité de session dans Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: 157cbd9b05f7f2af58df732a1ca0329926a200da
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123213"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Résoudre les problèmes d’affinité Azure Application Gateway Session

Découvrez comment diagnostiquer et résoudre les problèmes d’affinité de session avec Azure Application Gateway.

## <a name="overview"></a>Présentation

La fonctionnalité d’affinité de session basée sur les cookies est utile lorsque vous souhaitez conserver une session utilisateur sur le même serveur. En utilisant des cookies gérés de passerelle, la passerelle Application Gateway peut diriger le trafic pour traitement à partir d’une session utilisateur vers le même serveur. Ceci est important lorsque l’état de la session est enregistré localement sur le serveur pour une session utilisateur.

## <a name="possible-problem-causes"></a>Causes possibles de problème

Le problème à la gestion de l’affinité de session basée sur les cookies peut se produire en raison des raisons principales suivantes :

- « Affinité basée sur les cookies » n’est pas activé
- Votre application ne peut pas gérer l’affinité basée sur les cookies
- Application à l’aide d’affinité basée mais demandes toujours rebondissent pas entre les serveurs principaux

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Vérifier si le paramètre « affinité basée sur les cookies » est activé.

Les problèmes d’affinité de session peuvent parfois se produire si vous oubliez d’activer le paramètre « Affinité basée sur les cookies ». Pour déterminer si vous avez activé le paramètre « Affinité basée sur les cookies » sous l’onglet Paramètres HTTP dans le portail Azure, suivez les instructions :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

2. Dans le **barre de navigation gauche** volet, cliquez sur **toutes les ressources**. Cliquez sur le nom de passerelle d’application dans le panneau toutes les ressources. Si l’abonnement que vous avez déjà sélectionné comporte plusieurs ressources, vous pouvez entrer le nom de passerelle d’application dans le **filtrer par nom...** pour accéder facilement à la passerelle d’application.

3. Sélectionnez **paramètres HTTP** onglet sous **paramètres**.

   ![troubleshoot-session-affinity-issues-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Cliquez sur **appGatewayBackendHttpSettings** sur le côté droit pour vérifier si vous avez sélectionné **activé** pour affinité basée sur les cookies.

   ![troubleshoot-session-affinity-issues-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Vous pouvez également vérifier la valeur de la «**CookieBasedAffinity**» est définie sur *activé*sous «**backendHttpSettingsCollection**» en utilisant l’une des méthodes suivantes :

- Exécutez [Get-AzureRmApplicationGatewayBackendHttpSettings](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermapplicationgatewaybackendhttpsettings?view=azurermps-4.1.0) dans PowerShell
- Examinez le fichier JSON en utilisant le modèle Azure Resource Manager

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>L’application ne peut pas gérer l’affinité basée sur les cookies

#### <a name="cause"></a>Cause :

La passerelle d’application peut uniquement effectuer affinité basée sur une session en utilisant un cookie.

#### <a name="workaround"></a>Solution de contournement

Si l’application ne peut pas gérer l’affinité basée sur les cookies, vous devez utiliser un équilibreur de charge azure interne ou externe ou une autre solution de tiers.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Application à l’aide d’affinité basée mais demandes toujours rebondissent pas entre les serveurs principaux

#### <a name="symptom"></a>Symptôme

Vous avez activé le paramètre d’affinité basée sur les cookies, lorsque vous accédez à la passerelle d’Application à l’aide d’une URL de nom court dans Internet Explorer, par exemple : [ http://website ](http://website/) , la demande est toujours rebondissent pas entre les serveurs principaux.

Pour identifier ce problème, suivez les instructions :

1. Prendre une trace de débogueur web sur le « Client » qui se connecte à l’application derrière le Gateway(We are using Fiddler in this example) Application.
    **Conseil** si vous ignorez comment utiliser le Fiddler, activez l’option «**je souhaite collecter le trafic réseau et de les analyser à l’aide du débogueur web**» en bas.

2. Vérifier et analyser les journaux de session, pour déterminer si les cookies fournis par le client ont les détails ARRAffinity. Si vous ne trouvez pas les détails ARRAffinity, tel que «**ARRAffinity =** *ARRAffinityValue*» dans le jeu de cookie, ce qui signifie que le client n’est pas répondre avec le cookie disposer, qui est fourni par le Passerelle d’application.
    Par exemple : 

    ![troubleshoot-session-affinity-issues-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

        ![troubleshoot-session-affinity-issues-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

L’application continue à essaie de définir le cookie à chaque demande jusqu'à ce qu’il obtienne la réponse.

#### <a name="cause"></a>Cause :

Ce problème se produit car Internet Explorer et autres navigateurs ne peuvent pas stocker ou utiliser le cookie avec une URL de nom court.

#### <a name="resolution"></a>Résolution :

Pour résoudre ce problème, vous devez accéder à la passerelle d’Application à l’aide d’un nom de domaine complet. Par exemple, utilisez [ http://website.com ](http://website.com/) ou [ http://appgw.website.com ](http://appgw.website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>Autres journaux pour résoudre les problèmes

Vous pouvez collecter des journaux supplémentaires et les analyser pour résoudre les problèmes de l’affinité de session basée sur les cookies connexes de problèmes

### <a name="analyze-application-gateway-logs"></a>Analyser les journaux de passerelle d’Application

Pour collecter les journaux de passerelle d’Application, suivez les instructions :

Activation de la journalisation avec le portail Azure

1. Dans le [Azure portal](https://portal.azure.com/), recherchez votre ressource, puis sur **journaux de Diagnostic**.

   Pour Application Gateway, trois journaux d’audit sont disponibles : Journal d’accès, de journal de performances, de journal du pare-feu

2. Pour commencer à collecter des données, cliquez sur **activer les diagnostics**.

   ![troubleshoot-session-affinity-issues-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. Le panneau **Paramètres de diagnostic** contient les paramètres des journaux de diagnostic. Dans cet exemple, Log Analytics stocke les journaux. Cliquez sur **configurer** sous **Analytique de journal** pour définir votre espace de travail. Vous pouvez également utiliser des concentrateurs d’événements et un compte de stockage pour enregistrer les journaux de diagnostic.

   ![troubleshoot-session-affinity-issues-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Confirmez les paramètres, puis cliquez sur **enregistrer**.

   ![troubleshoot-session-affinity-issues-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Afficher et analyser les journaux d’accès Application Gateway

1. Dans le portail Azure sous la vue de ressource de passerelle d’Application, sélectionnez **les journaux de diagnostic** dans le **surveillance** section.

   ![troubleshoot-session-affinity-issues-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. Sur le côté droit, sélectionnez «**ApplicationGatewayAccessLog**» dans la liste déroulante sous **catégories de journaux.**  

   ![troubleshoot-session-affinity-issues-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Dans la liste d’accès journal passerelle d’Application, cliquez sur le journal que vous souhaitez analyser et d’exportation et exportez le fichier JSON.

4. Convertir le fichier JSON que vous avez exporté à l’étape 3 dans un fichier CSV et les afficher dans Excel, Power BI ou tout autre outil de visualisation des données.

5. Vérifiez les données suivantes :

- **ClientIP**– c’est l’adresse IP du client à partir du client qui se connecte.
- **ClientPort** -il s’agit du port source à partir du client qui se connecte pour la demande.
- **RequestQuery** – indique le serveur de destination que la demande est reçue.
- **Acheminée par le serveur** : Instance de pool back-end que la demande est reçue.
- **X-AzureApplicationGateway-LOG-ID** : ID de corrélation utilisé pour la requête. Peut être utilisée pour résoudre les problèmes de trafic sur les serveurs principaux. Par exemple :  X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **ÉTAT DU SERVEUR** : code de réponse HTTP reçu par Application Gateway à partir du back-end.

  ![troubleshoot-session-affinity-issues-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Si vous voyez deux éléments proviennent de la même ClientIP et Port du Client, et ils sont envoyés sur le même serveur principal, cela signifie que la passerelle d’Application configuré correctement.

Si vous voyez deux éléments proviennent de la même ClientIP et Port du Client, et ils sont envoyés aux serveurs principaux différents, cela signifie que la demande est rebondissent pas entre les serveurs principaux, sélectionnez «**Application utilise l’affinité basée sur les cookies, mais les demandes toujours rebondissent pas entre les serveurs principaux**« en bas de dépannage.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Utiliser le débogueur web pour capturer et analyser le trafic HTTP ou HTTPS

Web des outils de débogage tel que Fiddler, peut vous aider à déboguer des applications web en capturant le trafic réseau entre les ordinateurs Internet et de test. Ces outils permettent d’inspecter les données entrantes et sortantes comme le navigateur reçoit/envoie les. Fiddler, dans cet exemple, a l’option de relecture HTTP qui peut vous aider à résoudre les problèmes côté client avec les applications web, en particulier pour le type de problème d’authentification.

Utilisez le débogueur web de votre choix. Dans cet exemple, nous allons utiliser Fiddler pour capturer et analyser le trafic http ou https, suivez les instructions :

1. Télécharger l’outil Fiddler sur <https://www.telerik.com/download/fiddler>.

    > [!NOTE]
    > Choisissez Fiddler4 si l’ordinateur de capture a .NET 4 est installé. Sinon, choisissez Fiddler2.

2. Cliquez avec le bouton droit sur l’exécutable d’installation et exécuter en tant qu’administrateur pour installer.

            ![troubleshoot-session-affinity-issues-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Lorsque vous ouvrez Fiddler, il doit démarrer automatiquement la capture du trafic (Notez que la capture à l’angle inférieur gauche). Appuyez sur F12 pour démarrer ou arrêter la capture du trafic.

        ![troubleshoot-session-affinity-issues-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Très probablement, vous serez intéressé par le trafic HTTPS décrypté, et vous pouvez activer le déchiffrement HTTPS en sélectionnant **outils** > **Fiddler Options**, puis cochez la case « **déchiffrer Le trafic HTTPS**».

        ![troubleshoot-session-affinity-issues-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Vous pouvez supprimer les sessions non liées précédentes avant de reproduire le problème en cliquant sur **X** (icône) > **supprimer tout** comme suivent la capture d’écran : 

        ![troubleshoot-session-affinity-issues-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Une fois que vous avez reproduit le problème, enregistrez le fichier pour la révision en sélectionnant **fichier** > **enregistrer** > **toutes les Sessions...** . 

        ![troubleshoot-session-affinity-issues-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Vérifier et analyser les journaux de session pour déterminer la cause du problème.

    Exemples :

- **Exemple a :** Vous recherchez un journal de session qui la demande est envoyée à partir du client, et il accède à l’adresse IP publique de la passerelle d’Application, que vous cliquez sur ce fichier journal pour afficher les détails.  Sur le côté droit, les données dans la zone inférieure sont ce que la passerelle d’Application renvoie au client. Sélectionnez l’onglet « RAW » et de déterminer si le client reçoit une «**Set-Cookie : ARRAffinity=** *ARRAffinityValue*." S’il n’existe aucun cookie, affinité de session n’est pas définie, ou la passerelle d’Application n’est pas appliquer le cookie au client.

   > [!NOTE]
   > Cette valeur ARRAffinity est l’id de cookie, qui définit de la passerelle d’Application pour le client à envoyer à un serveur principal particulier.

    ![troubleshoot-session-affinity-issues-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Exemple b :** Le journal de session suivant suivie de la précédente est le client répond à la passerelle d’Application, qui a défini le ARRAAFFINITY. Si l’id de cookie ARRAffinity correspond à, le paquet doit être envoyé sur le même serveur principal qui a été utilisé précédemment. Vérifier les lignes suivantes de communication http pour voir si le cookie du client ARRAffinity change.

    ![troubleshoot-session-affinity-issues-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Pour la même session de communication, le cookie ne doit ne pas pour modifier. Vérifiez la zone supérieure sur le côté droit, sélectionnez l’onglet « Cookies » pour voir si le client est en utilisant le cookie et renvoyer à la passerelle d’Application. Si ce n’est pas le cas, le navigateur client n’est pas en conservant et en utilisant le cookie pour les conversations. Parfois, le client peut se trouver.

 

## <a name="next-steps"></a>Étapes suivantes

Si les étapes précédentes ne vous permettent pas de résoudre le problème, ouvrez un [ticket d’incident](https://azure.microsoft.com/support/options/).