# Prometheus

<img src="../img/prometheus.png" alt="Prometheus" width="200px">

Prometheus es un sistema de monitorización que se integra por defecto con el clúster de Kubernetes en el que esté desplegado. De esta forma es capaz de descubrir automáticamente todos los objetos que forman parte del clúster y aplicar las reglas de monitorización que hayas creado a todos ellos.

Es la solución de monitorización más habitual para Kubernetes, no recomiendo experimentar con otras. Se ha convertido en el estándar de-facto y es un proyecto maduro, con un gran soporte y comunidad.

Una breve introducción a la arquitectura: [https://prometheus.io/docs/introduction/overview/](https://prometheus.io/docs/introduction/overview/)

## Instalación

Prometheus se despliega como cualquier otra aplicación, con Deployments, Services, Roles, ConfigMaps etc. En esta carpeta encontrarás el archivo `prometheus.yaml` con todos esos objetos.

Para levantarlo en un clúster simplemente ejecuta `kubectl apply -f prometheus.yaml`.

También puedes encontrarlo para Helm en [https://artifacthub.io/packages/helm/prometheus-community/prometheus](https://artifacthub.io/packages/helm/prometheus-community/prometheus) con más opciones de personalización. Es la forma típica de instalar Prometheus.

Una vez instalado puedes acceder a la UI web a través del serivice `prometheus-server`.

Prometheus utiliza unas piezas llamadas `exporters` para obtener métricas de cada aplicación, son algo así como los agentes de monitorización en otros sistemas. Por defecto despliega el `node-exporter` que se levanta con un DaemonSet en todos los nodos, y expone métricas desde cada nodo (red, memoria, CPU, disco, etc.), y también `kube-state-metrics`, que expone métricas relativas al clúster (estado de los deployments, de los pod, etc.). Puedes encontrar una lista con los exporter más utilizados aquí: [https://prometheus.io/docs/instrumenting/exporters/](https://prometheus.io/docs/instrumenting/exporters/)

Cualquier aplicación puede exponer métricas para que las lea Prometheus. Con esta instalación se incluyen también unas reglas por defecto que utiliza Prometheus para descubrir de qué aplicaciones puede obtener esas métricas, utilizando annotations en los objetos de Kubernetes. Los exporter anteriores vienen ya configurados para que Prometheus pueda descubrirlos. Puedes ver estas reglas en el configMap `prometheus-server`


## Queries

Tiene su propio lenguaje para consultas, llamado PromQL. Si la instalación ha ido bien deberías tener ya algunas métricas en el servidor con lo que puedes empezar a hacer consultas siguiendo esta guía:

[https://prometheus.io/docs/prometheus/latest/querying/basics/](https://prometheus.io/docs/prometheus/latest/querying/basics/)


## Configuración

Prometheus se configura a través de reglas sobre cómo conectarse a las aplicaciones que tiene métricas para él (exporters o tus propias aplicaciones). Puedes inspeccionar las reglas por defecto en el configMap `prometheus-server`. Para aprender más sobre cómo crear tus propias reglas, la documentación oficial es la mejor guía: [https://prometheus.io/docs/prometheus/latest/configuration/configuration/](https://prometheus.io/docs/prometheus/latest/configuration/configuration/)

## Alertas

Prometheus también tiene un motor para alertas y una aplicación aparte llamada alert-manager que se encarga de gestionar los avisos para esas alertas.

[https://prometheus.io/docs/alerting/latest/overview/](https://prometheus.io/docs/alerting/latest/overview/)


# Grafana

<img src="../img/grafana.png" alt="Grafana" width="200px">

Grafana es una interfaz web para visualizar métricas almacenadas en Prometheus (o en cualquier otro sistema de monitorización). Permite crear dashboards para tener todas las métricas importantes bajo control y configurar alertas. El potencial de Grafana reside en su capacidad para integrarse con sistemas como Kubernetes y Prometheus para que sea sencillo crear esos dashboard y alertas.

## Instalación 

Al igual que con Prometheus, he adjuntado el archivo `grafana.yaml` para desplegarlo con  `kubectl apply -f grafana.yaml` y también podéis encontrarlo para Helm en [https://artifacthub.io/packages/helm/grafana/grafana](https://artifacthub.io/packages/helm/grafana/grafana)

Puede acceder a la interfaz web a traveś del service `grafana`

## Features

Lo primero que debes hacer es configurar un `Data Source` para conectar Grafana con un sistema de monitorización como Prometheus.

A continuación puedes probar distintas queries de Prometheus en la sección  `Explore`. Grafana utiliza el lenguaje para consultas que corresponda al data source en cuestión. Con Prometheus utilizaremos PromQL de la misma manera que lo hacíamos en la propia interfaz web de Prometheus.

Y por fin crear dashboards. Cada dashboard se compone de distintos paneles independientes entre sí. En cada panel puedes visualizar una o varias métricas de Prometheus y configurar límites y alertas para las mismas. Al crear un panel lo único necesario es introducir la consulta para Prometheus que desees para obtener la métrica que quieres visualizar. A partir de ahí se puede configurar todo sobre el gráfico que mostrará esa métrica.

En la sección de configuración de un dashboard es interesante la posibilidad de crear variables, de forma que un mismo dashboard sirva por ejemplo para visualizar todos los nodos a la vez, en lugar de crear uno nuevo para cada nodo. Y también la posibilidad de obtener el código JSON que contiene todos los elementos de dashboard, para exportarlo o gestionarlo como código.

Por último, es posible encontrar dashboards de grafana mantenidos por la comunidad, la mayoría de ellos además utilizando Prometheus como data source. [https://grafana.com/grafana/dashboards](https://grafana.com/grafana/dashboards) Es muy sencillo importar cualquier dashboard que sea compatible con Prometheus, atuomáticamente se integrará con tu sistema y comenzará a mostrar tus métricas. Por ejemplo puedes encontrar varios para el `node-exporter` o `kube-state-metrics` que ya desplegamos con Prometheus en la sección anterior.

