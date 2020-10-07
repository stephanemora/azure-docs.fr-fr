---
title: Personnaliser l’interface utilisateur de la solution de supervision à distance – Azure | Microsoft Docs
description: Cet article fournit des informations sur la façon dont vous pouvez accéder au code source pour l’interface utilisateur de l’accélérateur de solution de supervision à distance et effectuer quelques personnalisations.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 2789ed642979616a4491a61d146d8468552ec2e5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318464"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Personnaliser l’accélérateur de solution de supervision à distance

Cet article fournit des informations sur la façon dont vous pouvez accéder au code source et personnaliser l’interface utilisateur de l’accélérateur de solution de supervision à distance.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Préparer un environnement de développement local pour l’interface utilisateur

Le code de l’interface utilisateur de l’accélérateur de solution de supervision à distance est implémenté à l’aide du framework React.js. Vous pouvez trouver le code source dans le dépôt GitHub [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui).

Pour apporter des modifications à l’interface utilisateur, vous pouvez en exécuter une copie localement. Pour effectuer des actions comme la récupération des données de télémétrie, la copie locale se connecte à une instance déployée de la solution.

Les étapes suivantes décrivent le processus de configuration d’un environnement local pour le développement de l’interface utilisateur :

1. Déployez une instance de **base** de l’accélérateur de solution à l’aide de l’interface CLI **pcs**. Notez le nom de votre déploiement et les informations d’identification que vous avez fournies pour la machine virtuelle. Pour plus d’informations, consultez [Déployer à l’aide de l’interface CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Pour activer l’accès SSH à la machine virtuelle qui héberge les microservices de votre solution, utilisez le Portail Azure ou Azure Cloud Shell. Par exemple :

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Activez uniquement l’accès SSH durant les phases de développement et de test. Si vous activez SSH, [désactivez-le dès que vous avez fini de l’utiliser](../security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Utilisez le Portail Azure ou Azure Cloud Shell pour rechercher le nom et l’adresse IP publique de votre machine virtuelle. Par exemple :

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Utilisez SSH pour vous connecter à votre machine virtuelle. Servez-vous de l’adresse IP de l’étape précédente et des informations d’identification que vous avez fournies en exécutant **pcs** pour déployer la solution. La commande `ssh` est disponible dans Azure Cloud Shell.

1. Pour permettre la connexion de l’expérience utilisateur locale, exécutez les commandes suivantes au niveau du shell bash dans la machine virtuelle :

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Quand l’exécution de la commande est terminée et que le site web démarre, vous pouvez vous déconnecter de la machine virtuelle.

1. Dans votre copie locale du dépôt [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui), modifiez le fichier **.env** pour ajouter l’URL de votre solution déployée :

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. Dans l’invite de commandes, accédez à votre copie locale du dossier `azure-iot-pcs-remote-monitoring-webui`.

1. Pour installer les bibliothèques requises et exécuter l’interface utilisateur localement, exécutez les commandes suivantes :

    ```cmd/sh
    npm install
    npm start
    ```

1. La commande précédente exécute l’interface utilisateur localement sur http:\//localhost:3000/dashboard. Vous pouvez modifier le code pendant que le site est en cours d’exécution et voir sa mise à jour dynamique.

## <a name="customize-the-layout"></a>Personnaliser la disposition

Chaque page de la solution de supervision à distance est composée d’un ensemble de contrôles, appelés *panneaux* dans le code source. La page **Tableau de bord** se compose de cinq panneaux : Vue d’ensemble, Carte, Alertes, Télémétrie et Analytique. Vous trouverez le code source qui définit chaque page et ses panneaux dans le dépôt GitHub [pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui). Par exemple, le code qui définit la page **Tableau de bord**, sa disposition et ses panneaux se trouve dans le dossier [src/components/pages/dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard).

Étant donné que les panneaux gèrent leurs propres disposition et dimensionnement, vous pouvez facilement modifier la disposition d’une page. Apportez les modifications suivantes à l’élément **PageContent** du fichier `src/components/pages/dashboard/dashboard.js` pour :

* permuter les positions des panneaux Carte et les Télémétrie ;
* modifier les largeurs relatives des panneaux Carte et Analyse.

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-6">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

![Modifier la disposition des panneaux](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Vous pouvez également ajouter plusieurs instances du même panneau, ou plusieurs versions si vous [dupliquez et personnalisez un panneau](#duplicate-and-customize-an-existing-control). L’exemple suivant montre comment ajouter deux instances du panneau Télémétrie. Pour effectuer ces modifications, modifiez le fichier `src/components/pages/dashboard/dashboard.js` :

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

Vous pouvez ensuite afficher différentes données de télémétrie dans chaque panneau :

![Plusieurs panneaux Télémétrie](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Dupliquer et personnaliser un contrôle existant

Les étapes suivantes expliquent comment dupliquer un panneau existant, le modifier, puis utiliser la version modifiée. Elles prennent l’exemple du panneau **Alertes** :

1. Dans votre copie locale du référentiel, faites une copie du dossier **Alertes** dans le dossier `src/components/pages/dashboard/panels`. Nommez la nouvelle copie **cust_alerts**.

1. Dans le dossier **cust_alerts** du fichier **alertsPanel.js**, remplacez le nom de la classe par **CustAlertsPanel** :

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Ajoutez la ligne suivante au fichier `src/components/pages/dashboard/panels/index.js` :

    ```javascript
    export * from './cust_alerts';
    ```

1. Remplacez `alertsPanel` par `CustAlertsPanel` dans le fichier `src/components/pages/dashboard/dashboard.js` :

    ```javascript
    import {
      OverviewPanel,
      CustAlertsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlertsPanel
        alerts={currentActivealertsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Le panneau **Alertes** d’origine a été remplacé par une copie appelée **CustAlerts**. Cette copie est identique à l’original. Vous pouvez maintenant modifier la copie. Par exemple, pour modifier l’ordre des colonnes dans le panneau **Alertes** :

1. Ouvrez le fichier `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js` .

1. Modifiez les définitions de colonnes comme illustré dans l’extrait de code suivant :

    ```javascript
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

La capture d’écran suivante montre la nouvelle version du panneau **Alertes** :

![Panneau Alertes mis à jour](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Personnaliser le graphique de télémétrie

Les fichiers du dossier `src/components/pages/dashboard/panels/telemtry` définissent le graphe de télémétrie sur la page **Tableau de bord**. L’interface utilisateur récupère les données de télémétrie du backend de solution dans le fichier `src/services/telemetryService.js`. Les étapes suivantes indiquent comment remplacer la période de 15 minutes affichée dans le graphe de télémétrie par une période de 5 minutes :

1. Dans le fichier `src/services/telemetryService.js`, recherchez la fonction appelée **getTelemetryByDeviceIdP15M**. Effectuez une copie de cette fonction et modifiez la copie comme suit :

    ```javascript
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Pour utiliser cette nouvelle fonction afin de remplir le graphique de télémétrie, ouvrez le fichier `src/components/pages/dashboard/dashboard.js`. Recherchez la ligne qui initialise le flux de données de télémétrie et modifiez-la comme suit :

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Le graphique de télémétrie affiche maintenant la période de cinq minutes de données de télémétrie :

![Graphique de télémétrie illustrant une journée](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Ajouter un nouvel indicateur de performance clé

La page **Tableau de bord** affiche les indicateurs de performance clés dans le panneau **Analyse**. Ces indicateurs de performance clés sont calculés dans le fichier `src/components/pages/dashboard/dashboard.js`. Les indicateurs de performance clés sont affichés par le fichier `src/components/pages/dashboard/panels/analytics/analyticsPanel.js`. Les étapes suivantes décrivent comment calculer et afficher une nouvelle valeur d’indicateur de performance clé dans la page **Tableau de bord**. L’exemple fourni consiste à ajouter un nouveau changement de pourcentage dans l’indicateur de performance clé des alertes d’avertissement :

1. Ouvrez le fichier `src/components/pages/dashboard/dashboard.js` . Modifiez l’objet **initialState** en lui ajoutant une propriété **warningAlertsChange** :

    ```javascript
    const initialState = {
      ...

      // Analytics data
      analyticsVersion: 0,
      currentActiveAlerts: [],
      topAlerts: [],
      alertsPerDeviceId: {},
      criticalAlertsChange: 0,
      warningAlertsChange: 0,
      analyticsIsPending: true,
      analyticsError: null

      ...
    };
    ```

1. Modifiez l’objet **currentAlertsStats** en lui ajoutant une propriété **totalWarningCount** :

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Calculez le nouvel indicateur de performance clé. Recherchez le calcul du nombre d’alertes critiques. Dupliquez le code et modifiez la copie comme suit :

    ```javascript
    // ================== Warning Alerts Count - START
    const currentWarningAlerts = currentAlertsStats.totalWarningCount;
    const previousWarningAlerts = previousAlerts.reduce(
      (cnt, { severity }) => severity === Config.ruleSeverity.warning ? cnt + 1 : cnt,
      0
    );
    const warningAlertsChange = ((currentWarningAlerts - previousWarningAlerts) / currentWarningAlerts * 100).toFixed(2);
    // ================== Warning Alerts Count - END
    ```

1. Ajoutez le nouvel indicateur de performance clé **warningAlertsChange** au flux d’indicateurs :

    ```javascript
    return ({
      analyticsIsPending: false,
      analyticsVersion: this.state.analyticsVersion + 1,

      // Analytics data
      currentActiveAlerts,
      topAlerts,
      criticalAlertsChange,
      warningAlertsChange,
      alertsPerDeviceId: currentAlertsStats.alertsPerDeviceId,

      ...
    });
    ```

1. Ajoutez le nouvel indicateur de performance clé **warningAlertsChange** aux données d’état utilisées pour restituer l’interface utilisateur :

    ```javascript
    const {
      ...

      analyticsVersion,
      currentActiveAlerts,
      topAlerts,
      alertsPerDeviceId,
      criticalAlertsChange,
      warningAlertsChange,
      analyticsIsPending,
      analyticsError,

      ...
    } = this.state;
    ```

1. Mettez à jour les données passées au panneau Indicateurs de performance clés :

    ```javascript
    <AnalyticsPanel
      timeSeriesExplorerUrl={timeSeriesParamUrl}
      topAlerts={topAlertsWithName}
      alertsPerDeviceId={alertsPerDeviceType}
      criticalAlertsChange={criticalAlertsChange}
      warningAlertsChange={warningAlertsChange}
      isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || analyticsError}
      theme={theme}
      colors={chartColorObjects}
      t={t} />
    ```

Vous avez apporté toutes les modifications nécessaires au fichier `src/components/pages/dashboard/dashboard.js`. Les étapes suivantes décrivent les changements à effectuer dans le fichier `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` pour afficher le nouvel indicateur de performance clé :

1. Modifiez la ligne de code suivante pour récupérer la nouvelle valeur d’indicateur de performance clé comme suit :

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Modifiez le balisage pour afficher la nouvelle valeur d’indicateur de performance clé comme suit :

    ```javascript
    <div className="analytics-cell">
      <div className="analytics-header">{t('dashboard.panels.analytics.criticalAlerts')}</div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(criticalAlertsChange) ? criticalAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(warningAlertsChange) ? warningAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

La page **Tableau de bord** affiche maintenant la nouvelle valeur d’indicateur de performance clé :

![Indicateur de performance clé d’avertissement](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Personnaliser la carte

Pour obtenir des informations détaillées sur des composants de la carte de la solution, consultez la page GitHub [Customize map](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) (Personnaliser la carte).

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Autres options de personnalisation

Pour affiner la couche de présentation et de visualisations dans la solution de supervision à distance, vous pouvez modifier le code. Les dépôts GitHub pertinents sont les suivants :

* [Microservice de configuration pour solutions Azure IoT (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [Microservice de configuration pour solutions Azure IoT (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Interface utilisateur web de surveillance à distance de solutions préconfigurées Azure IoT](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les ressources disponibles pour personnaliser l’interface utilisateur web dans l’accélérateur de solution de supervision à distance. Pour en savoir plus sur la personnalisation de l’interface utilisateur, consultez les articles suivants :

* [Ajouter une page personnalisée à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-page.md)
* [Ajouter un service personnalisé à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-service.md)
* [Ajouter une grille personnalisée à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-grid.md)
* [Ajouter un menu volant personnalisé à l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Ajouter un panneau personnalisé au tableau de bord dans l’interface utilisateur web de l’accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-customize-panel.md)

Pour plus d’informations conceptuelles sur l’accélérateur de solution de supervision à distance, consultez [Architecture de la supervision à distance](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Pour plus d’informations sur la personnalisation des microservices de la solution de supervision à distance, consultez [Personnaliser et redéployer un microservice](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
