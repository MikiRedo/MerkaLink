MerkaLink:

*apuntes de los pasos que he hecho y los problemas que he ido encontrando*


1: Descragamos los binarios en el repo y ejecutamos el ./install-fabric.sh -b

2: Entramos en “merca-chain” y arrancamos la red (./network.sh up -ca). Comprobamos que los contenedores esten arrancados (docker ps).

3: Me doy cuenta que algunos contenedores no han arrancado (docker ps -a), ya que el de “orderer” y uno de los “peer node start” estan parados.

4: Uso el comando docker logs (“id”) del contenedor “orderer” para intentar detectar donde petardea. Parece que es un “PANIC” al arrancar el admin server.

5: Miramos el docker logs y me doy cuenta que hay problemas en el "compose-test-net.yaml". En el documento nos damos cuenta que en un peer y el orderer tienen errors de escritura en la palabra "server" y "hyperledger". Lo corregimos y ya arranca correctamente.

6: Empezamos con el chaincode. Primero modificamos los atributos de los archivos que hay dentro de Scripts: packageCC.sh, deployCC.sh, etc. para que puedan ser ejecutables (chmod +x) ya que a priori no lo son y daba error. Una vez desplegamos el chaincode vemos que hay un error (docker logs "id"), resulta que falta una llave{} en una de las funciones.

7: Nos piden cambiar los logs, así que vamos al archivo "compose-test-net.yaml" y modificamos tal y como nos piden para el orderer y org1 / org2.

8: Descargamos el Softhsm via apt install y creamos 3 tokens, uno para el orderer, uno para el org.1 y otro para el org.2. Habia que mapear la ruta en los volumenes del archivo "compose-ca.yaml" y cambiar los valores con los del token, pero no consigo encontrar donde modificar y que ruta mapear.

No sabia como configurar una vez creados los tokens :s

Available slots:
Slot 722596617
    Slot info:
        Description:      SoftHSM slot ID 0x2b11f309                                      
        Manufacturer ID:  SoftHSM project                 
        Hardware version: 2.6
        Firmware version: 2.6
        Token present:    yes
    Token info:
        Manufacturer ID:  SoftHSM project                 
        Model:            SoftHSM v2      
        Hardware version: 2.6
        Firmware version: 2.6
        Serial number:    55c087152b11f309
        Initialized:      yes
        User PIN init.:   yes
        Label:            Miki                            
Slot 855940702
    Slot info:
        Description:      SoftHSM slot ID 0x33049e5e                                      
        Manufacturer ID:  SoftHSM project                 
        Hardware version: 2.6
        Firmware version: 2.6
        Token present:    yes
    Token info:
        Manufacturer ID:  SoftHSM project                 
        Model:            SoftHSM v2      
        Hardware version: 2.6
        Firmware version: 2.6
        Serial number:    1841011e33049e5e
        Initialized:      yes
        User PIN init.:   yes
        Label:            fabric                          
Slot 2
    Slot info:
        Description:      SoftHSM slot ID 0x2                                             
        Manufacturer ID:  SoftHSM project                 
        Hardware version: 2.6
        Firmware version: 2.6
        Token present:    yes
    Token info:
        Manufacturer ID:  SoftHSM project                 
        Model:            SoftHSM v2      
        Hardware version: 2.6
        Firmware version: 2.6
        Serial number:                    
        Initialized:      no
        User PIN init.:   no
        Label:  

Debido a que no tenia bien seteado el tema de los tokens, no he podido hacer el punto 5, así que he seguid con el punto 6.

8: Nos piden usar Prometheus-Grafana, así qque entramos en la carpeta, arrancamos el contenedor (docker compose up -d) y lo abrimos en el navegador con el id de la maquina de ubuntu y sus puertos. Vemos que funciona correctamente y cacharreamos un poco con los browsers.

9: Investigamos un poco sobre el porque es interesante realizar un despliegue distribuido. Las principales ventajas de este són la escalabilidad (ya que puedes seguir agregando nodos y puedes mantener un mayor número de transacciones) y la seguridad (ya que si un nodo falla o recibe ataques otros nodos pueden seguir en marcha). Aspectos como la resilencia o la eficiencia también se ven mejorados, ya que un mayor número de nodos activos permite una mayor adaptación a los diferentes ataques o errores que puedan surgir.

Fuentes: 
Stackoverflow, 
https://chat.openai.com/, 
https://hyperledger-fabric.readthedocs.io/

