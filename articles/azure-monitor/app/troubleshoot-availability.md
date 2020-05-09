---
title: Résoudre les problèmes de vos tests de disponibilité Azure Application Insights
description: Résolvez des problèmes relatifs aux tests web dans Azure Application Insights. Recevez des alertes si un site web devient indisponible ou répond lentement.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 04/28/2020
ms.reviewer: sdash
ms.openlocfilehash: 8f03099cf2890882a1c1d4ba9d69fcb64d0db600
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233956"
---
# <a name="troubleshooting"></a>Dépannage

Cet article va vous aider à résoudre les problèmes courants qui peuvent se produire lorsque vous utilisez la surveillance de la disponibilité.

## <a name="ssltls-errors"></a>Erreurs SSL/TLS

|Symptôme/message d’erreur| Causes possibles|
|--------|------|
|Impossible de créer un canal sécurisé SSL/TLS  | Version de SSL. Seules les versions TLS 1.0, 1.1 et 1.2 sont prises en charge. **La version SSLv3 n’est pas prise en charge.**
|Couche d’enregistrements TLSv1.2 : alerte (niveau : fatal, description : code d’authentification de message d’enregistrement incorrect)| Consultez la conversation StackExchange pour [plus d’informations](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake).
|L’URL qui échoue est sur un réseau de distribution de contenu (CDN) | Cela peut être dû à une configuration incorrecte sur votre CDN |  

### <a name="possible-workaround"></a>Solution de contournement possible

* Si les URL qui rencontrent le problème sont toujours sur des ressources dépendantes, il est recommandé de désactiver les **requêtes dépendantes de l’analyse** pour le test web.

## <a name="test-fails-only-from-certain-locations"></a>Le test échoue uniquement à partir de certains emplacements

|Symptôme/message d’erreur| Causes possibles|
|----|---------|
|Une tentative de connexion a échoué, car la partie connectée n’a pas répondu convenablement après une certaine durée  | Dans certains emplacements, les agents de test sont bloqués par un pare-feu.|
|    |Le réacheminement de certaines adresses IP se produit via (Load Balancer, gestionnaires de trafic géographique, Azure Express Route.) 
|    |Si vous utilisez Azure ExpressRoute, il existe des scénarios où les paquets peuvent être ignorés si un [routage asymétrique se produit](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing).|

## <a name="test-failure-with-a-protocol-violation-error"></a>Échec de test avec une erreur de violation de protocole

|Symptôme/message d’erreur| Causes possibles| Résolutions possibles |
|----|---------|-----|
|Le serveur a valider une violation de protocole. Section=ResponseHeader Detail=CR doit être suivi par LF | Cela se produit lorsque des en-têtes mal formés sont détectés. Plus précisément, certains en-têtes peuvent ne pas utiliser CRLF pour indiquer la fin de ligne, ce qui viole la spécification HTTP. Application Insights applique cette spécification HTTP et fait échouer les réponses contenant des en-têtes mal formés.| a. Contactez le fournisseur CDN/de l’hôte du site web pour corriger les serveurs défectueux. <br> b. Si les demandes ayant échoué sont des ressources (par exemple, des fichiers de style, des images, des scripts), vous pouvez envisager de désactiver l’analyse des demandes dépendantes. Gardez à l’esprit que si vous procédez ainsi, vous perdrez la possibilité de superviser la disponibilité de ces fichiers.

> [!NOTE]
> L’URL peut ne pas être en échec sur les navigateurs qui présentent une validation approximative des en-têtes HTTP. Consultez ce billet de blog pour obtenir une explication détaillée de ce problème : http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Questions courantes relatives à la résolution des problèmes

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Le site me semble OK, mais j’observe des échecs de tests. Pourquoi Application Insights m’envoie-t-il des alertes ?

   * Est-ce que l’option **Analyser les requêtes dépendantes** est activée pour le test ? Cela entraîne une vérification stricte des ressources telles que les scripts, les images, etc. Ces types de d’échecs peuvent être difficiles à remarquer sur un navigateur. Vérifiez toutes les images, les scripts, les feuilles de style et tout autre fichier chargé par la page. Si l’un d’eux échoue, le test signale une erreur, même si la page HTML principale se charge sans problème. Pour désensibiliser le test à ces échecs de ressource, il vous suffit de décocher la case Analyser les requêtes dépendantes dans la configuration du test.

   * Pour réduire la probabilité de bruit des spots réseau temporaires, etc., vérifiez que la case à cocher de configuration Permettre les nouvelles tentatives pour les échecs des tests web est activée. Vous pouvez également procéder aux tests à partir de plusieurs emplacements et gérer le seuil de règle d'alerte en conséquence afin d'éviter que des problèmes spécifiques à un emplacement ne provoquent des alertes injustifiées.

   * Cliquez sur un des points rouges à partir de l’expérience de disponibilité, ou sur tout échec de disponibilité à partir du navigateur de recherche pour afficher les détails de la raison pour laquelle nous avons signalé l’échec. Le résultat du test, ainsi que les données de télémétrie côté serveur corrélées (si activées), doivent aider à comprendre pourquoi le test a échoué. Les causes courantes des problèmes temporaires proviennent du réseau ou de la connexion.

   * Est-ce que le délai d’attente du test est arrivé à expiration ? Nous abandonnons les tests après 2 minutes. Si votre test Ping ou multiétape prend plus de 2 minutes, nous le signalerons comme un échec. Pensez à diviser le test en plusieurs parties qui peuvent être effectuées dans des délais plus courts.

   * Est-ce qu’un échec est signalé pour tous vos emplacements, ou seulement certains d’entre eux ? Si des échecs ne sont signalés que pour certains, cela peut être dû à des problèmes de réseau/CDN. Là encore, cliquer sur les points rouges peut aider à comprendre pourquoi l’emplacement a signalé des échecs.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Je n’ai pas reçu d’e-mail lorsque l’alerte s’est déclenchée, a été résolue, ou les deux.

Vérifiez la configuration des alertes classiques pour confirmer que votre adresse e-mail est répertoriée directement, ou que vous êtes sur une liste de distribution qui est configurée pour recevoir des notifications. Le cas échéant, vérifiez ensuite la configuration de la liste de distribution pour confirmer qu’elle peut recevoir des e-mails externes. Vérifiez également si votre administrateur de messagerie dispose de stratégies configurées qui pourraient provoquer ce problème.

### <a name="i-did-not-receive-the-webhook-notification"></a>Je n’ai pas reçu la notification webhook.

Vérifiez que l’application qui reçoit la notification webhook est disponible et traite correctement les requêtes webhook. Pour plus d’informations, consultez [ceci](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook).

### <a name="i-am-getting--403-forbidden-errors-what-does-this-mean"></a>Je reçois des erreurs 403 Interdit. Qu’est-ce que cela signifie ?

Cette erreur indique que vous devez ajouter des exceptions de pare-feu pour autoriser les agents de disponibilité à tester votre URL cible. Pour obtenir la liste complète des adresses IP d’agent à autoriser, consultez l’[article sur les exceptions IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Échecs intermittents des tests avec une erreur de violation de protocole.

L’erreur (« violation de protocole... CR doit être suivi par LF ») indique un problème lié au serveur (ou aux dépendances). Il se produit lorsque des en-têtes mal formés sont définis dans la réponse. Ce problème peut être provoqué par des équilibreurs de charge ou des réseaux de distribution de contenu (CDN). Plus précisément, certains en-têtes peuvent ne pas utiliser CRLF pour indiquer la fin de ligne, ce qui enfreint la spécification HTTP et entraîne donc l’échec de la validation au niveau WebRequest .NET. Examinez la réponse pour repérer les en-têtes qui peuvent ne pas être conformes.

> [!NOTE]
> L’URL peut ne pas être en échec sur les navigateurs qui présentent une validation approximative des en-têtes HTTP. Consultez ce billet de blog pour obtenir une explication détaillée de ce problème : http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Je ne vois pas les données de télémétrie côté serveur associées pour diagnostiquer les échecs des tests.*

Si Application Insights est défini pour votre application côté serveur, cela peut être en raison d’un [échantillonnage](../../azure-monitor/app/sampling.md) en cours. Sélectionnez un résultat de disponibilité différent.

### <a name="can-i-call-code-from-my-web-test"></a>Puis-je appeler du code à partir de mon test web ?

Non. Les étapes du test doivent se trouver dans le fichier .webtest. Et vous ne pouvez pas appeler d’autres tests web ou utiliser des boucles. En revanche, il existe un certain nombre de plug-ins qui peuvent s’avérer utiles.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Quelle est la différence entre les « tests web » et les « tests de disponibilité » ?

Les deux conditions peuvent être référencées indifféremment. « Tests de disponibilité » est un terme plus générique qui inclut les tests ping d’URL uniques en plus des tests web à plusieurs étapes.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>J’aimerais utiliser les tests de disponibilité sur notre serveur interne qui s’exécute derrière un pare-feu.

   Il existe deux solutions possibles :

   * Configurez votre pare-feu pour autoriser les demandes entrantes provenant des [adresses IP de nos agents de test web](../../azure-monitor/app/ip-addresses.md).
   * Écrivez votre propre code pour tester régulièrement votre serveur interne. Exécutez le code en tant que processus en arrière-plan sur un serveur test derrière votre pare-feu. Le processus de test peut envoyer ses résultats à Application Insights à l’aide de l’API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) dans le package du kit de développement logiciel (SDK) principal. Cela nécessite que votre serveur de test ait un accès sortant au point de terminaison d’ingestion Application Insights, mais ce risque de sécurité est beaucoup plus faible que l’alternative, qui consiste à autoriser les demandes entrantes. Les résultats s’affichent dans les panneaux de tests web de disponibilité, même si l’expérience sera légèrement simplifiée par rapport à ce qui est disponible pour les tests créés via le portail. Les tests de disponibilité personnalisés s’affichent également comme résultats de disponibilité dans Analytics, Search et Metrics.

### <a name="uploading-a-multi-step-web-test-fails"></a>Le chargement d’un test web à plusieurs étapes échoue

Certaines raisons à cause desquelles cela peut se produire :
   * La limite de taille est de 300 Ko.
   * Les boucles ne sont pas prises en charge.
   * Les références à d’autres tests web ne sont pas prises en charge.
   * Les sources de données ne sont pas prises en charge.

### <a name="my-multi-step-test-doesnt-complete"></a>Mon test à plusieurs étapes ne se termine pas

Chaque test possède une limite de 100 demandes. En outre, le test s’arrête s’il s’exécute pendant plus de deux minutes.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Comment puis-je exécuter un test avec des certificats clients ?

Non pris en charge actuellement.

## <a name="who-receives-the-classic-alert-notifications"></a>Qui reçoit les notifications d'alerte (classiques) ?

Cette section ne s'applique qu'aux alertes classiques et vous aidera à optimiser vos notifications d'alerte afin que seuls les destinataires de votre choix les reçoivent. Pour mieux comprendre la différence entre les [alertes classiques](../platform/alerts-classic.overview.md) et la nouvelle expérience d'alerte, reportez-vous à l'[article de présentation des alertes](../platform/alerts-overview.md). Pour contrôler la notification des alertes dans la nouvelle expérience d'alerte, utilisez des [groupes d'actions](../platform/action-groups.md).

* Nous recommandons l'utilisation de destinataires spécifiques pour les notifications d'alertes classiques.

* Pour les alertes relatives aux échecs de X sur Y emplacements, l'option **En bloc/groupe**, si elle est activée, envoie une notification aux utilisateurs dotés du rôle d'administrateur/co-administrateur.  Grosso modo, _tous_ les administrateurs de l'_abonnement_ recevront des notifications.

* Pour les alertes de métrique de disponibilité, l’option **En bloc/groupe**, si elle est activée, envoie des alertes aux utilisateurs ayant des rôles de propriétaire, contributeur ou lecteur dans l’abonnement. Dans les faits, _tous_ les utilisateurs ayant accès à la ressource Application Insights sont concernés et recevront des notifications. 

> [!NOTE]
> Si vous utilisez actuellement l'option **En bloc/groupe** et que vous la désactivez, vous ne pourrez pas annuler la modification.

Utilisez la nouvelle expérience d'alerte ou les alertes en temps quasi-réel si vous devez notifier les utilisateurs en fonction de leur rôle. Avec les [groupes d'actions](../platform/action-groups.md), vous pouvez configurer des notifications par e-mail à l'intention des utilisateurs dotés du rôle de contributeur, de propriétaire ou de lecteur (rôles non combinés en une même option).

## <a name="next-steps"></a>Étapes suivantes

* [Tests web à plusieurs étapes](availability-multistep.md)
* [Tests ping d’URL](monitor-web-app-availability.md)
