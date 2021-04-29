Kubernetes
    
INGRESS 
(NGINX)         (reglas netfilter)
rutas1    >>>>> servicio1                >>>> pod 1s1 >>> IP servicio3 (esta llamada es interceptada por netfliter-SO) y 
                                                          acaba mandandolo a la IP de pod 1s2
                                         >>>> pod 2s1

rutas2    >>>>> servicio2                >>>> pod 1s2
                                         >>>> pod 2s2

rutas3    >>>>> servicio3 NP             >>>> pod 1s3     
                    En cada nodo, en su red publica se abre un puerto (+30000) que redirige al servicio interno

rutas4    >>>>> servicio4 LB             >>>> pod 1s4 >>> IP servicio3 (esta llamada es interceptada por netfliter-SO) y 
                                                          acaba mandandolo a la IP de pod 1s2     
                                         >>>> pod 2s4
                    Es una IP publica que redirige al servicio interno
                    ¿Como configuro esa IP publica?
                        Esto no lo hago yo... Cuando trabajo en un cloud, el proveedor de cloud me da una IP PUBLICA con
                        redirecciones (balanceo) a mis servicios internos

La gracia de tener un servicio: Nombre+balanceo

------------------------------------------------------
Kubernetes
    
 
          G1>ruta >>>>> vservicio1        >>>> pod 1s1       + sidecar > contenedor (proxy)
                                         >>>> pod 2s1       + sidecar > contenedor (proxy)
                                         -
          >>>>> servicio2                >>>> pod 1s2       + sidecar > contenedor (proxy)
                                         >>>> pod 2s2       + sidecar > contenedor (proxy)
                                         -
          >>>>> servicio3 NP             >>>> pod 1s3       + sidecar > contenedor (proxy)
                                         >>>> pod 2s3       + sidecar > contenedor (proxy)
                    En cada nodo, en su red publica se abre un puerto (+30000) que redirige al servicio interno
                    -
          >>>>> servicio4 LB             >>>> pod 1s3       + sidecar > contenedor (proxy)
                                         >>>> pod 2s3       + sidecar > contenedor (proxy) 
                    Es una IP publica que redirige al servicio interno
                    ¿Como configuro esa IP publica?
                        Esto no lo hago yo... Cuando trabajo en un cloud, el proveedor de cloud me da una IP PUBLICA con
                        redirecciones (balanceo) a mis servicios internos
                     -   
          >>> servicio ingress istio (LB) >>>> istio-ingressgateway
                    Va a pillar una IP de donde (del proveedor cloud)
                                                


La gracia de tener un servicio: Nombre+balanceo

pod 1s1 >>> servicio3

curl servicio3

GATEWAY (servicio + PROXY )

VirtualService  ----> Configuración de enrutamiento para los sidecars ¿Donde?
DestinationRule ----> Configuración de control del trafico para los sidecars ¿Como?
                        Balanceo, Cola
                        
Proxis <<< Gateway

http://34.251.136.107:32439/productpage



>>>>> Reviews   >>> PODS (app: reviews) = 3
>>>>> Details   >>> PODS (app: details) = 1

Cuando se produzca una llamada a details => details



http://reviews.prod.svc.cluster.local/newcatalog

apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: reviews-route
spec:
  hosts:
  - reviews.prod.svc.cluster.local
  http:
  - name: "reviews-v2-routes"
    match:
    - uri:
        prefix: "/wpcatalog"
    - uri:
        prefix: "/consumercatalog"
    rewrite:
      uri: "/newcatalog"
    route:
    - destination:
        host: reviews.prod.svc.cluster.local
        subset: v2
  - name: "reviews-v1-route"
    route:
    - destination:
        host: reviews.prod.svc.cluster.local
        subset: v1




Connection pool:
    # Esto sirve para proteger el/los POD(s)
    maxConnections: 50 x número máximo de replicas  (autoescalado 1-10)
                                >>>>>> + Numero de replicas del POD >>> + recursos
                               Si no hay mas recursos porque estoy al limite, peto el POD >>>>
                                    En este caso a quien atiendo?A nadie... lo he petao... pues a nadie <<<<<<
                                        PELIGRO !!!!!! NO QUIERO ESTAR AQUI
    Cuando configuro autoescalado, lo hago en base a metricas HoritontalPodAutoscaler metrica CPU > 70% MAX=10 pods
    
    
    
Que significa el 5?
    Maximo numero de conexiones concurrentes que voy a mandar a un servicio S1
        De que depende?
            Numero de PODS que tengo por detrás

S1. Tarda un tiempo. Malamente !!!
    Recursos para el procesamiento. Monitorización: Cuantoas peticiones es capaz de resolver un pod
-------------------------
PNS1: 10 peticiones por segundo:
    - Recursos. Esto lo puedo tocar... hasta lo que pueda
    - Lo que haga la app. Lo puedo tocar? NO
Cuanto tardo en procesar una peticion: 500ms

Cuantas conexiones me aguanta el pod? 5

10 peticiones  
    ---------    x 0,5 s = 5 peticiones en paralelo
    segundo

Me interesa que el valor real que yo tengo cuando lo mido esté en 5 abiertas siempre?
Cuanto tardan las que se encolan... cuanto tienen que esperar <<<<<<

Supermercado:
    maxConnections? Número de cajeros que tengo:

Quiero que se genere una cola? Si siempre que sea razonable
Si no tengo cola que significa? Desperdicio de recursos


Hago una peticion a un servicio
    Y el servicio llama a una BBDD <<<< Tarda mucho en contestar
    Y el servicio quiere leer un fichero del disco
    
    
    
Pod
    Contenedor
        Prueba de Vida <<<<< 5 minutos   <<<<< Si esto.. que SI ES RESPONSABILIDAD DEL DESARROLLADOR.. 
                                                me lo ha hecho bien
                                                curl puerto 500
        Esta operación puedes ser muy agresiva!!!
        
        
        ES:   GREEN    |    YELLOW   |    RED
                Nodo 1   <<<< 10000 documentos <<<<<< copiar los 500 doc del nodo 2 que no tiene respaldo
                Nodo 2   <<<< 5000 documentos copia de los del nodo 1 y 5000 nuevos  <<<<< copia 5000  docs del nodo 1
                Nodo 3   <<<< 5000 documentos copia del nodo 1 y otros 5000 copia del nodo 2 PUFFF
            Maestro. Que se ha caido uno!
                Calma... no os precipiteis... vamos a esperar un poco 5 minutitos 
        Si se cae un nodo... voy a estar como el del video... Como se me caiga otro....
        
                Microservicio que hace una query a una bbdd y devuelve un json
        Tienes pasta?
        
        
    outlierDetection:
      consecutive5xxErrors: 7
      interval: 5s
      baseEjectionTime: 10s