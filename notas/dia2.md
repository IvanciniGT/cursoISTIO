kind: Tipos de cosas que podemos crear en Kubernetes
    Pod: Conjunto de contenedor, que:
        - Comparten IP: localhost
        - Se ejecutan en la misma máquina
            - Comunicaciones RED: van guay !!!
            - Comparten filesystem? NO
                Pero... Escriben sobre el mismo host, y por tanto?
                    Puedo montar la misma carpeta del host a ambos contenedores: VOLUMEN
                        Compartir achivos entre ellos a traves de filesystem HDD
                        Pueden compartir RAM
                            Pod:
                                C1: Weblogic -> Oracle, BEA
                                    ---> Ficheros de log
                                        2 logs rotados 100Kb
                                C2: sidecar: Filebeat | Fluentd | Logstash
                                    ---> Ficheros de log -----VVV
                                                Cluster de ElasticSearch (guarda ficheros de log)
                                Monto un volumen compartido con el host y con ellos
                                    Ese volumen lo monto en RAM
    Deployment     |    Plantilla de Pod + Cuantos quiero de esa plantilla
    Statefulset    |
    Service:
        - Nombre para poder acceder a los puertos de los contenedores de los pods. DNS
        - Balanceo
    Ingress >>> Route (Openshift)
    namespace :    Tener diferentes entornos dentro del cluster
    configMap:     Guardar configuraciones y mandarlas luego a los pods
                        - Volumen
                        - Entorno
    secrets:       Guardar configuraciones securizadas
    serviceaaccount      
                   Autenticar un pod contra kubernetes?
                        
    DaemonSet:     |    Plantilla de pod + nada... porque se van a crear tantos pods como
                        nodos tenga en el cluster
                    
                    ? para que vales esto ? Monitorizacion
                        scrapper que lea las stats de hardware de la maquina
                            logstash
                            filebeat
                            metricbeat
                            heartbeat
    
    ########
    Instalar ISTIO:
        Cargar las librerias de ISTIO: CRD
        Generar el plano de control de ISTIO
            namespace: istio-system
        VVVVV
        Generar lo que llamamos el plano de datos de ISTIO: sidecars
            Contenedores que va a generar ISTIO dentro de cada POD que haya en Kubernetes
                Ese contenedor contiene un proxy MAN-IN-THE-MIDDLE
    Pasos:
        - Descargar ISTIO en local
            - cliente de istio:
                istioctl
                yml

    ########
    
kubectl apply -f fichero-descriptor-namespace.yaml
    ---> GIT  
kubectl create namespace   <<<<< RUINA MAS GRANDE QUE SE HA INVENTADO !!!!!
    NO DEJO TRAZA


HELM
chart_basico de Helm

App y Version

App1 - v1.3.4
App2 - v0.0.2

helm App1-v1.3.4 chart_basico + fichero_variables


# Lo que me controla el
Ingress - Quien puede acceder a donde de dentro del cluster
Egress - Mis pods de dentro del cluster a donde pueden ir a pedir información de fuera del cluster

                            Kubernetes
                --------------------------------------
Cliente   <>>>>    INGRESS <>>>> POD  <>>>>> EGRESS <>>>  BBDD EXTERNA



# Desarrollo de software
- Servicio
- Aplicacion
- script
- demonio

# Servicio en el mundo del software
Software que se ejecuta en segundo plano, 
de forma indefinida en el tiempo 
y que atiende peticiones de otros programas

# Demonio en el mundo del software
Software que se ejecuta en segundo plano, 
de forma indefinida en el tiempo 
y que no atiende peticiones de otros programas
sino que él sabe lo que tiene que hacer

# Aplicación
Software que se ejecuta en primer plano, 
de forma indefinida en el tiempo 
y que atiende peticiones de usuario

# Script 
Software que se ejecuta realizando una serie de tareas de 
forma finita en el tiempo
    Programa de instalación
    
    
Instalacion de ISTIO
- Library ISTIO -> CRD
- Control-plane ISTIO = NAMESPACE + Recursos ISTIOD
- INGRESS
- EGRESS

Activar la injección automatica de sidecars en un namespace bookinfo

Despliegue de una pp bookinfo en el ns bookinfo
Servicios:
    details: Mostrar los detalles de un libro
    ratings: Mostrar las valoraciones de un libro
    reviews: Mostrar las reseñas de un libro
    productpage: Mostrar toda la informacion
Deployments:
    details: v1
    ratings: v1
    reviews: v1 v2 v3
    productpage: v1


---> PRODUCTPAGE   ------> DETAILS
                   ------> REVIEWS
                               v1
                               v2  |
                               v3  |------> RATINGS  
                               
                               
CLUSTER IP
NODE PORT     <<<
LOAD BALANCER <<<





VIRTUALSERVICE es una capa por encima de nuestro SERVICE
---> Es el que me permite balancear la carga entre los pods asociados a un servicio



INGRESS ----> RUTA ----> SERVICE1
                            90% -----> POD v1
                            10% -----> POD v2
                            
INGRESS ----> RUTA ----> SERVICE1



                    En ISTIO Esas reglas se ejecutan por sidecar (proxy)? 

                    Reglas de NetFilter (IPTABLES) ME LAS FUMO. 
                    SERVICE1------------------
                       VV                    V                  
                      PROXY-----------
                       ^^                                           SERVICIO
GATEWAY -------> VIRTUALSERVICES                                       VV
                                      ---------> POD INFO v1   -----> PROXY -----> POD 17, 19, 28, 43
                                      ---------> POD INFO v1
                                      ---------> POD INFO v2
                                      ---------> POD INFO v2
                                      ---------> POD INFO v2
                  (ampliar la configuración que hago de el enrutamiento)
                    10% balanceo RR POD v1                    
                    90% balanceo LEAST POD v2                    

Que es un virtualService (Configuración de enrutamiento para el proxy)


    Quiero una cola de 1000 peticiones
    Quiero un timeout en cola de 10s


SERVICIO?
    - Nombre
    - Balanceo


SERVICE         <<<<< Desarrollador .... Información: Quien hace que
VIRTUALSERVICE <<<<<< No es responsabilidad del desarrollador:
                            Operador del cluster

Entorno de desarrollo: 
    100% trafico vaya a v2
    
Entorno de producción
    100% trafico vaya a v1
    90% trafico vaya a v1 - 10% trafico v2
    50% trafico vaya a v1 - 50% trafico v2
    100% trafico vaya a v2
    
Arquitectura de sistemas / Devops <<<< PRODUCCION ???