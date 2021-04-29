2 contenedores de nginx fuera de KUBERNETES!!!!
    - Puertos 8081:80 -- curl ---> Nginx1
    - Puertos 8082:80 -- curl ---> Nginx2
    
sudo apt install docker-compose -y
cd curso/ejemplos/nginx
docker-compose up -d
curl -s $(curl -s ifconfig.me):8081
curl -s $(curl -s ifconfig.me):8082

## Obtengoi la ip public de mi maquina
curl -s ifconfig.me
54.194.187.143


NS:default                                   EGRESS                    FUERA DEL CLUSTER
   POD:                                       >>>                          nginx
   C1: nginx
        VVV 
   C2: envoy (sidecar)


Problemas?
 √ Fragil
    Que pasa si un dia cambia aquello y lo muevo a otro sitio.... dentro de mi cluster
    2 nginx. 1 d repuesto del otro... Balanceo 
    3 Seguridad... 1º Puedo acceder ahi?
    
Service Entry
    namespace proveedores
    
                            Configuración que se entregan a quien? A los sidecars
                        = ServiceEntry = VirtualService
NS:default             NS: proveedores                      EGRESS                    FUERA DEL CLUSTER
   POD:                  nginxexterno                         >>>                          nginx
   C1: nginx
        VVV nginx_externo
   C2: envoy (sidecar)
   
Que no se te olvide activar la inyeccion automatica de sidecars en el ns: proveedores
    Seguro? pa' que?
    
    La salida por el egress
    
    

servicio de kubernetes
namespace frontend  -> namespace backend


nginx(interno) curl nginxexterno:8081 CONTESTE