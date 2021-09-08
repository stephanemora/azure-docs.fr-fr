---
title: Surveiller la disponibilité avec des tests Ping d’URL – Azure Monitor
description: Configurez des tests ping dans Application Insights. Recevez des alertes si un site web devient indisponible ou répond lentement.
ms.topic: conceptual
ms.date: 07/13/2021
ms.reviewer: sdash
ms.openlocfilehash: 55e396f7d1af6bc05d722e90d7dad99b2ac8b2d7
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122563786"
---
# <a name="monitor-availability-with-url-ping-tests"></a>Superviser la disponibilité avec des tests Ping d’URL

Le nom *test ping d’URL* prête un peu à confusion. Ces tests n’utilisent pas le protocole ICMP (Internet Control Message Protocol) pour vérifier la disponibilité de votre site. Au lieu cela, ils se servent de fonctionnalités de requête HTTP plus avancées pour s’assurer qu’un point de terminaison répond bien. Ils mesurent les performances associées à cette réponse. Ils permettent aussi de définir des critères de réussite personnalisés à l’aide de fonctionnalités plus avancées, comme analyser des requêtes dépendantes et autoriser de nouvelles tentatives.

Pour créer un test de disponibilité, vous devez utiliser une ressource Application Insights existante ou [créer une ressource Application Insights](create-new-resource.md).

> [!NOTE]
> Les tests ping d’URL sont catégorisés en tant que tests classiques. Vous pouvez les retrouver sous **Ajouter un test classique**  dans le volet **Disponibilité**. Pour des fonctionnalités plus avancées, consultez [Tests standard (préversion)](availability-standard-tests.md).
 
## <a name="create-a-test"></a>Créer un test

Pour créer votre première demande de disponibilité :
1. Dans votre ressource Application Insights, ouvrez le volet **Disponibilité** et sélectionnez   **Ajouter un test classique**.

    :::image type="content" source="./media/monitor-web-app-availability/create-test.png" alt-text="Capture d’écran montrant le volet Disponibilité et le bouton permettant d’ajouter un test classique." lightbox ="./media/monitor-web-app-availability/create-test.png":::
1. Donnez un nom à votre test et sélectionnez **Ping URL** pour **SKU**.
1. Entrez l’URL que vous voulez tester.
1. Ajustez les paramètres (décrits dans le tableau ci-dessous) en fonction de vos besoins, puis sélectionnez **Créer**.

   |Paramètre| Explication |
   |----|----|
   |**URL** |  L’URL peut être n’importe quelle page web que vous souhaitez tester, mais elle doit être visible à partir de l’Internet public. L’URL peut contenir une chaîne de requête. Vous pouvez donc par exemple tester un peu votre base de données. Si l’URL correspond à une redirection, vous pouvez suivre jusqu’à 10 redirections.|
   |**Analyser les demandes dépendantes**| Le test demande des images, des scripts, des fichiers de style et d’autres fichiers qui font partie de la page web de test. Le temps de réponse enregistré inclut le temps qui a été nécessaire pour obtenir ces fichiers. Le test échoue si l’une de ces ressources ne peut pas être téléchargée dans le délai imparti pour l’ensemble du test. Si l’option n’est pas cochée, le test ne demande que le fichier à l’URL spécifiée. L’activation de cette option donne lieu à une vérification plus stricte. Le test peut échouer pour les cas qui ne sont pas perceptibles lors de la navigation manuellement sur le site.
   |**Autoriser les nouvelles tentatives**|Lorsque le test échoue, une nouvelle tentative est effectuée peu après. L’échec est signalé uniquement après trois tentatives infructueuses. Les tests suivants sont ensuite effectués selon la fréquence de test habituelle. La nouvelle tentative est temporairement suspendue jusqu’à la réussite de la tentative suivante. Cette règle est appliquée indépendamment à chaque emplacement de test. *Nous recommandons cette option.* En moyenne, environ 80 % des échecs disparaissent lors de la nouvelle tentative.|
   |**Fréquence de test**| Ce paramètre définit la fréquence selon laquelle le test est exécuté à partir de chaque emplacement de test. Avec, par défaut, une fréquence de cinq minutes et cinq emplacements de test, votre site sera testé en moyenne une fois par minute.|
   |**Emplacements du test**| Les valeurs de ce paramètre sont les emplacements à partir desquels les serveurs envoient des requêtes web à votre URL. *Nous recommandons au moins cinq emplacements de test* pour pouvoir faire la distinction entre les problèmes propres à votre site web et les problèmes de réseau. Vous pouvez sélectionner jusqu’à 16 emplacements.

Si votre URL n’est pas visible à partir de l’Internet public, vous pouvez choisir une ouverture sélective de votre pare-feu pour autoriser uniquement les transactions entrantes à des fins de test. Pour en savoir plus sur les exceptions de pare-feu applicables aux agents de test de disponibilité, consultez le [guide des adresses IP](./ip-addresses.md#availability-tests).

> [!NOTE]
> Nous vous recommandons vivement de tester à partir de plusieurs emplacements avec un minimum de cinq emplacements. Cette approche aide à éviter les fausses alarmes qui peuvent provenir de problèmes temporaires rencontrés avec un emplacement spécifique. Nous avons aussi constaté que la configuration optimale est d’avoir un *nombre d’emplacements de test égal au seuil d’emplacement de l’alerte + 2*.

## <a name="success-criteria"></a>Critères de réussite

|Paramètre| Explication |
|----|----|
| **Délai d’expiration du test** |diminuez cette valeur pour être averti des réponses lentes. Le test est compté comme une erreur si des réponses de votre site n’ont pas été reçues pendant cette période. Si vous avez sélectionné **Analyser les demandes dépendantes**, l’ensemble des images, fichiers de style, scripts et autres ressources dépendantes ont dû être reçus pendant cette période.|
| **Réponse HTTP** | Le code d’état retourné est comptabilisé comme un succès. Le code qui indique qu’une page web normale a été retournée est le 200.|
| **Correspondance du contenu** | Nous vérifions qu’une correspondance exacte respectant la casse est présente dans chaque réponse. Il doit s’agir d’une chaîne standard sans caractère générique (par exemple « Bienvenue ! »). N’oubliez pas que si votre contenu change, vous devrez peut-être l’actualiser. *La correspondance de contenu prend en charge uniquement des caractères anglais.* |

## <a name="alerts"></a>Alertes

|Paramètre| Explication |
|----|----|
|**Quasi-temps réel (préversion)** | Nous vous recommandons d’utiliser des alertes qui fonctionnent quasiment en temps réel. Vous configurez ce type d’alerte une fois que vous avez créé votre test de disponibilité.  |
|**Seuil d’emplacement de l’alerte**| La relation optimale entre le seuil d’emplacement de l’alerte et le nombre d’emplacements de test est *seuil d’emplacement de l’alerte = nombre d’emplacements de test - 2*, avec un minimum de cinq emplacements de test.|

## <a name="location-population-tags"></a>Étiquettes de remplissage d’emplacement

Vous pouvez utiliser les étiquettes de remplissage suivantes pour l’attribut de géolocalisation lorsque vous déployez un test Ping d’URL de disponibilité à l’aide d’Azure Resource Manager.

### <a name="azure-government"></a>Azure Government

| Nom d’affichage   | Nom du remplissage     |
|----------------|---------------------|
| USGov Virginia | usgov-va-azr        |
| Gouvernement des États-Unis - Arizona  | usgov-phx-azr       |
| Gouvernement des États-Unis - Texas    | usgov-tx-azr        |
| USDoD Est     | usgov-ddeast-azr    |
| US DoD - Centre  | usgov-ddcentral-azr |

### <a name="azure-china"></a>Azure Chine

| Nom d’affichage   | Nom du remplissage     |
|----------------|---------------------|
| Chine orientale     | mc-cne-azr          |
| Chine orientale 2   | mc-cne2-azr         |
| Chine du Nord    | mc-cnn-azr          |
| Chine Nord 2  | mc-cnn2-azr         |

### <a name="azure"></a>Azure

| Nom d’affichage                           | Nom du remplissage   |
|----------------------------------------|-------------------|
| Australie Est                         | emea-au-syd-edge  |
| Brésil Sud                           | latam-br-gru-edge |
| USA Centre                             | us-fl-mia-edge    |
| Asie Est                              | apac-hk-hkn-azr   |
| USA Est                                | us-va-ash-azr     |
| France Sud (anciennement France Centre) | emea-ch-zrh-edge  |
| France Centre                         | emea-fr-pra-edge  |
| Japon Est                             | apac-jp-kaw-edge  |
| Europe Nord                           | emea-gb-db3-azr   |
| Centre-Nord des États-Unis                       | us-il-ch1-azr     |
| États-Unis - partie centrale méridionale                       | us-tx-sn1-azr     |
| Asie Sud-Est                         | apac-sg-sin-azr   |
| Ouest du Royaume-Uni                                | emea-se-sto-edge  |
| Europe Ouest                            | emea-nl-ams-azr   |
| USA Ouest                                | us-ca-sjc-azr     |
| Sud du Royaume-Uni                               | emea-ru-msa-edge  |

## <a name="see-your-availability-test-results"></a>Consulter les résultats des tests de disponibilité

Vous pouvez visualiser les résultats des tests de disponibilité à l’aide des vues ligne et nuage de points.

Au bout de quelques minutes, sélectionnez **Actualiser** pour voir les résultats de vos tests.

:::image type="content" source="./media/monitor-web-app-availability/refresh.png" alt-text="Capture d’écran montrant la page de disponibilité avec le bouton Actualiser mis en surbrillance." lightbox="./media/monitor-web-app-availability/refresh.png":::

La vue en nuage de points montre des exemples de résultats de test contenant des détails de l’étape de test de diagnostic. Le moteur de test stocke les détails de diagnostic pour les tests qui présentent des erreurs. Pour les tests réussis, les détails de diagnostic sont stockés pour un sous-ensemble des exécutions. Pointez sur les points verts ou rouges pour voir le nom du test et son emplacement.

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="Capture d’écran qui montre la vue ligne." border="false":::

Sélectionnez un test ou emplacement ou réduisez la période pour voir plus de résultats autour de la période d’intérêt. Utilisez l’Explorateur de recherche pour voir les résultats de toutes les exécutions, ou utilisez les requêtes Analytics pour exécuter des rapports personnalisés sur ces données.

## <a name="inspect-and-edit-tests"></a>Examiner et modifier des tests

Pour modifier, désactiver temporairement ou supprimer un test, sélectionnez les points de suspension ( **...** ) en regard du nom du test. Les modifications de la configuration peuvent prendre jusqu’à 20 minutes pour se propager dans tous les agents de test une fois la modification effectuée.

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="Capture d’écran qui présente les détails du test, avec les options pour modifier et désactiver un test." border="false":::

Vous souhaiterez peut-être désactiver les tests de disponibilité ou les règles d’alerte associées lorsque vous effectuez la maintenance de votre service.

## <a name="actions-if-you-see-failures"></a>Actions en cas d’erreurs

Sélectionnez un point rouge.

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="Capture d’écran de l’onglet des détails de transaction de bout en bout." border="false":::

À partir d’un résultat de test de disponibilité, vous pouvez voir les détails de la transaction pour tous les composants. Vous pouvez ensuite :

* Examiner le rapport de résolution des problèmes pour déterminer ce qui a pu provoquer l’échec de votre test tant que votre application est toujours disponible.
* Vérifier la réponse reçue à partir de votre serveur.
* Diagnostiquer une défaillance à l’aide des données de télémétrie côté serveur corrélées qui ont été collectées pendant le traitement du test de disponibilité en échec.
* Enregistrer un problème ou un élément de travail dans Git ou Azure Boards pour suivre le problème. Le bogue contient un lien vers cet événement.
* Ouvrir le résultat du test web dans Visual Studio.

Pour en savoir plus sur les diagnostics des transactions de bout en bout, consultez la [documentation relative aux diagnostics des transactions](./transaction-diagnostics.md).

Sélectionnez la ligne d’une exception pour afficher les détails de l’exception côté serveur qui a provoqué l’échec du test de disponibilité synthétique. Vous pouvez également obtenir la [capture instantanée de débogage](./snapshot-debugger.md) pour des diagnostics de niveau code plus riches.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="Capture d’écran montrant les diagnostics côté serveur.":::

Outre les résultats bruts, vous pouvez examiner deux mesures essentielles de la disponibilité dans [Metrics Explorer](../essentials/metrics-getting-started.md) :

- **Disponibilité** : pourcentage des tests qui ont réussi, sur toutes les exécutions de test.
- **Durée du test** : durée moyenne du test sur toutes les exécutions de test.

## <a name="automation"></a>Automatisation

* [Utilisez des scripts PowerShell pour configurer un test de disponibilité](./powershell.md#add-an-availability-test) automatiquement.
* Configurez un [webhook](../alerts/alerts-webhooks.md) qui est appelé lorsqu’une alerte est déclenchée.


## <a name="next-steps"></a>Étapes suivantes

* [Alertes de disponibilité](availability-alerts.md)
* [Tests web à plusieurs étapes](availability-multistep.md)
* [Dépannage](troubleshoot-availability.md)
* [Modèle Azure Resource Manager pour les tests web](/azure/templates/microsoft.insights/webtests?tabs=json)
