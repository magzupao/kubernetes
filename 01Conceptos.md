Kubernetes

El Plano de control (toma desiciones sobre el cluster y detecta y responde a los eventos del cluster) es la parte principal de Kubernetes.
Un ejemplo del trabajo del plano de control es reaccionar a un evento creando nuevos recursos cuando se despliegua una nueva aplicacion.

EL plano de control esta compuesto de:
- Kube api server, usado para ver o cambiar el estado del cluster
- Kube scheduler, asignar nuevos objetos, como pods, a los Nodos. Durante el proceso de agendado-programación, las decisiones se toman según el estado actual del clúster de Kubernetes y los requisitos del nuevo objeto.
- Etcd, almacen clave/valor que tiene los datos de disponibilidad del cluster
- Kube controller manager, supervisa el estado del cluster
- Cloud controller manager, permite la gestion de la API en la nube

