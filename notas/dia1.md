# Qué es Kubernetes/Openshift?
Orquestador de Contenedores.
    Orquestación a nivel de cluster. Gestiono los procesos de muchas máquinas.
    Para qué quiero un cluster?
        HA: Poder garantizar que el servicio este funcionando 24x7x365(6)
            Ni de coña... Yo no lo firmo
            Lo que si firmamos es que vamos a poner todos nuestros medios y mejores intenciones para conseguir 
                que unos servivios que estamos ofreciendo estén disponibles una determinada cantidad de tiempo
                    90% del servicio. RUINA GRANDE !!!! 10 dias, 1 puedo estar caido... 36,5 dias caido al año
                    99% del servicio. RUINA !!! 100 dias 1 puede estar caido... 3 dias al año 
                    99.9
                    99.99
                Esto es solo una declaracion de intenciones
            Las estragias más comunes pasan por montar un cluster:
                Activo-Pasivo. Tengo 2 copias del sistema, identicas o no..., pero solo una esta en funcionamiento.
                    Cuando deja de funcionar, corriendo levanto la otra
                    Deployment: ReplicaSet # de replicas de 1.
                    
                Activo-Activo. Tengo N copias del sistema, identicas o no..., todas estan en funcionamiento.
                    Cuando deja una de funcionar, no pasa nada ¿? ya que tengo otras copias.
                    Montar por delante un balanceador de carga (HAProxy, nginx, apache).
                    Deployment: ReplicaSet # de replicas de 4-6.
                    
        Escalabilidad. Capacidad de un sistema a adaptarse a "Incrementos" o decrementos.
            Cluster Activo-Activo.
            

    Servicio


## Qué es un contenedor?
Entorno AISLADO donde podemos ejecutar procesos en un sistema operativo Linux.
    - Tiene su propio Filesystem
    - Tiene limitaciones en cuanto a los recursos a los que puede acceder del host
    - Tiene su propia configuración de red.

MacOS - UNIX - Especificación de un SO (SUS + Posix)
HP-UX
AIX
Solaris

Linux
BSD

Es una aplicación + librerias + dependencias paquetizadas para poder ser ejecutadas de forma independiente
Capa de abstracción que tiene lo justo y necesario que me hace falta para ejecutar la aplicación
Entorno de ejecución donde dentro podemos tener aplicaciones en un entorno configurado ad-hoc
Sistema de ficheros AISLADO

App1   |   App2
---------------
C1     |   C2
---------------
Gestor de Contenedores (docker, containerd, crio, podman)
---------------
      SO
---------------
    HIERRO
    
    

Kubernetes: Es la base <<<< Google
    VVVV 
Openshift: Añadidos sobre kubernetes
    Origin:                       Opensource y gratuito !!!!
    Openshift Container Platform: OpenSource y de pago
    OS Cloud
    
    
-----------------------------------------------------------
Que es una arquitectura de Microservicios?
- Divido el código de un programa en muchas funcionalidades más pequeñas que son 
  capaces de funcionar autonomamente, resilente
- Esos trozos independientes de programa, se comunican entre si, para montar un sistema más complejo, con un proposito mas grande, mayor

Arquitectura basada en Servicios - SOA - SOAP, WSDL, UDDI

Un sistema completo va a estar formado por muchos servicios/microservicios
Cómo instalamos un servicio/microservicio (en un contenedor)
    Ejecutarlo de forma aislada
    Esta abstraido del entorno
    Facilidad de distribución del software. Esto es importante? 
    
Kubernernetes


DEVOPS: Cultura AUTOMATIZACION. Que quiero automatizar: TODO
Jenkins... Integra todo:
    Desarrollo: No es automatizable
    Empaquetarlo y Distribuirlo: Automatiza: GIT, Mercurial, Subversion, maven, msbuild, npm
        Quien automatiza esto? Desarrollador
    Probarlo: 
        QA - Testers. Ruina !!!! Eso era antes
        Programas que son realizados por Testers
    Instala un sistema?
        Administrador de sistemas. RUINA !!!! Eso era antes
        Programa. Administradores de sistemas (devops)
    Operación y monitorización de mi sistema
        Kubernetes/Openshift
        
ALM:    Lifecycle agil del proyecto de software . Uyyyy.... Casi !!!!! 

-----
Vamos a acabar con una cantidad de contenedores... (programas, aplicaciones, servicios) ENORME !!!!
    Arquitecturas de microservicios

Service mess
Service Mesh

----------------------
Contenedor vs Servicio
----------------------
Servicio es una abstracción y el contenedor es una instancia del servicio.
Características de los servicios:
- Un servicio tiene su propia IP... igual que un contenedor


docker run --name minginx -d -p 192.168.17.98:8080:80 nginx


docker run --name minginx -d nginx
    pull
    container create
    start
    
Puedo acceder al nginx?
SI .... solo desde las maquinas que esten conectadas a esa red virtual:
    - Host
    - C Nginx
    - Otros contenedores que tenga por ahi

Docker
    En mi maquina monta un red virtual 172.17.0.0/16
    
Cuantas redes hay en mi maquina


Kubernetes
    Crear una red virtual (igual que docker), si bien la red virtual de Kubernetes
    engloba los contenedores que tenga ejecutandose en cualquier nodo del cluster.
    La red virtual une todos los nodos del cluster... todas las máquinas.
    
    POD: Un grupo de contenedores que:
        - Se ejecutan en la misma MAQUINA FISICA/VIRTUAL
        - Comparten IP. (se refieren como localhost)
    
    Contenedor Weblogic en un POD ejecutandose en el NODO 1
        La IP Del contenedor de MySQL:3306
        --> Otro Contenedor MySQL en otro PO ejecutandose en un nodo 2
        
    ¿Pueden comunicarse entre si, tal cual, sin necesidad de nada mas?
        SI: Siempre que conozcan la IP uno del otro
    
    ¿Que IP le va a asignar Kubernetes al POD donde está corriendo el contenedor de MYSQL?
        NPI
    ¿Tengo garantía de que esa IP no va a cambiar? 
        NO
        
    No trabajamos con IPS.... Trabajamos con nombres de DNS
        ---> Que ofecen los servicios en Kubernetes
                Es una IP, con un nombre DNS asociado, que tiene establecida una redirección de puertos.
                DESCUBRIMIENTO DE SERVICIOS.
        ---> Encolamiento de peticiones > Balanceo de carga. 
        

Weblogic POD1 >>>>> COLA           >>> MySQL POD2 PUFF
                     I5 Q4 Q3 Q2 | Q1(puff)
                                >>>>>> MySQL POD2'
                                >>>>>> MySQL POD3'
                                >>>>>> MySQL POD4'
                                >>>>>> MySQL POD5'

Servicio es un NGINX, HAProxy, Apache?
    NO. Solo simples reglas de NetFilter

Ingress -> Kubernetes
    Route -> Openshift
        Forma de exponer un servicio fuera de la red del cluster
No es la única manera de exponerlos. De que otras forma lo puedo exponer?
    Servicio de tipo ClusterIP (este no vale para exponer algo al publico, solo dentro del cluster)
    Servicio de tipo NodePort >30000
    Servicio de tipo LoadBalancer. Generar una IP Publica que redirecciona a los Nodeport 
    
Usamos mucho los servicios de tipo load balancer?
    o preferimos los ingress? MÁS BARATO !!!! No tengo que pagar al proveedor del cluster otra IP PUBLICA

http://miingres.micluster.es/app2 ---> Servicio ClusterIP ---> Contenedor 
http://miingres.micluster.es/app1 ---> servicio ClusterIP ---> Contenedor

http://publicip1.micluster.es ---> servicio NodePort ---> servicio ClusterIP ---> Contenedor

Al final... ni el uno ni el otro! 
Ingress de ISTIO


Si todo esto ya lo hace kubernetes... para que necesito ISTIO?
    - Limitar cominicaciones entre pods.
    - Securizar comunicaciones
    - Monitorización de los servicios ---> Prometheus / Grafana
    - Distruir la carga entre PODs... Para que me vale esto?
        - Pruebas y depliegues Montar el servicio en v2: 100% del trafico 
        
        
Securizar las comunicaciones?
    Evitar 2 tipos de ataques:
        - No adulteración de los datos: Man in the middle
        - Suplantación de identidad: Phishing
        - No repudio
        
        
Clientes finales ----- lan publica --- cluster ---- red privada
                                                            - S1 (C1)
                                                            - S2 (C2, C3)
                                                            - S3 (C4)
Necesito securizar la red privada?
    Certificados <- Phishing => Autenticación
        DNI <<< Policia Nacional (Entidad certificadora)
        
        Emisor   >>>>>>>> Receptor
        Cliente  >>>>>>>> Servicio
        (Weblogic)    >>>> Weblogic

        Al menos receptor
        Para más seguridad que querria yo... que los 2 presenten certificado
CA - Citadel


Como funciona ISTIO?
    sidecar -> Un contenedor que meto en el mismo POD donde se ejecuta el contenedor que ofrece el servicio
    
POD1: Contenedor NGINX:80
      Proxy ISTIO ^
      
      
YAML
YAML aint markup language
XML
    HTML
    SGML
    
    Markup Language Tags
        <etiqueta> contenido </etiqueta>
    
JSON


GNU/Linux
GNU is not Unix

386BSD

Que es un Deployment?
    Agupa un pod y un replicaset

Que es un Statefulset?
    Agupa un pod y un replicaset
    Las replicas tienen un identificador

Que es un replicaset
    Quien controla el escalado
    
    
